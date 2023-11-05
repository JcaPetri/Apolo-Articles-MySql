## Apolo-Articles-MySql
CREATE DATABASE `articlesdb` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_as_cs */ /*!80016 DEFAULT ENCRYPTION='N' */;

Main Structure of all Microservices
Group: com.apolo
Name: system / articles / persons / users

========================================================================================================================================================================================
#### Importan Definitions:
	System Microservice contains the system structure of all the software. In this module you need to create all the databases and java classes, enums, interfaces, entities, etc.
	The main tables of this module will be in the other databases such as mirrors. 
 	These mirror tables will be updated via Kafka topics, but for the only record that are used by this Articles Microservices.
	With this mechanism each microservice becames independent of the others. Example: SysCompanies_Mir, SysBusinessUnits_Mir, etc.
	One important thing, is you make a mirror only of the records that need in the other microservice, not all the records that exists in the System Microservice.
	The are three types of tables.
		Tli is the list table, in which only enable the IDNum for one Microservice and BusinessUnit. This kind of table do not have any relation with others.
		Tbl is the normal table, where stores the specific software data. This kind of table have relation with others tables of the same kind.
  				Tables with their owns ID and IDNum. These are the common tables.
	  			Tables without owns ID and IDNum, It is generated in ArtDataElements_Tbl.
	  				Microservice_Tbl has not their own ID and IDNum.
    	Mir is a mirror table, theses tables are updated throw kafka.
	All tables have the key for each record:
		ID		--> is the uniqueidentifier auto generated.
		IDNum	--> is the autoincrement number auto generated.

	--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	The Apolo Software Structure are defined by:
 		Frontend
   			This is the user interface, which call the backend to get the information and business logic.
	  		Example to create an invoice, you need the information of:
				Clients -> call the PersonsMicroservices, which has all the information about the clients/supplier/both.
						The parameters request are: 
									Endpoint: this is the  address of the Microservice.
									Group: this can be Clients types, BusinessUnits, etc. This can be an array of Groups.
									Others parameters defined by the microservice, needed to comply the request.
				Articles -> call the ArticlesMicroservices, which has all information about the articles (things and services) and theirs relations.
						The parameters request are: 
								Endpoint: this is the  address of the Microservice.
								Group: this can be articles types, BusinessUnits, etc. This can be an array of Groups.
								Others parameters defined by the microservice, needed to comply the request.
				Taxes -> call the TaxesMicrosevices, which has all infomation about the taxes subject.
						The parameters request are: 
								Endpoint: this is the  address of the Microservice.
								GeneratorID: the seller ID.
								DestiantionID: the client ID.
								Others parameters defined by the microservice, needed to comply the request.
			
		Backend
   			This is the logic and where the permanent information are stored.
	  		Each Microservice specializes in a specific task. 
	 		Next, the Apolo Microcervices Structure are defined by:
		 		SystemsMicroSs
		   			This microservices has the information about:
						The main dictionary of the software.
	  					The software structure, in java and databases, entities, tables, datatypes, etc.
	  					The companies and theirs structures.
						The microservices and the relations with the companies and business units.
	  					Most of this tables are going to be part of other microservices like mirror tables. But only with data that they need.
	  				The microservices structure are defined by:
			  			Java project called ApoloSystemsMicroSs, this a restfull webservice java project.
			  			MySql database called SystemsDB, this contain the permanent information of all the System software.
			   			Kafka Topics Producer/Consumer, to update all the other Microservices.
				 						
				UsersMicroSs
		   			This microservices has the information about:
						The users, groups of users.
	  					The permition and authorizations.
	  				The microservices structure are defined by:
			  			Java project called ApoloUsersMicroSs, this a restfull webservice java project.
			  			MySql database called UsersDB, this contain the permanent information of all the Users.
			   			Kafka Topics Producer/Consumer, to update all the other Microservices.
			 	PersonsMicroSs
		   			This microservices has the information about the persons (legal and real).
	  				Concept: Traders are the real link between the companies and theirs Clientes or Suppliers.
			 							 The reason is that the Customer can change the cuit, the business name, or want to charge the bill to another cuit. But the Customer/Supplier is the same.
										 To fix that, the relationship between Business and theirs customers or suppliers, are throw the traders. Are not direct with the real person.
					 					 Importan: If the trader has only one persons relation, the software automaticaly make the selection.
	  													 If the trade has two or more persons relations, the user must select, to wich person want to charge the invoice.				  
	  				The microservices structure are defined by:
			  			Java project called ApoloPersonsMicroSs, this a restfull webservice java project.
			  			MySql database called PersonssDB, this contain the permanent information of all the Persons.
			   			Kafka Topics Producer/Consumer, to update all the other Microservices.
  				ArticlesMicroSs
		   			This microservices has the information about Articles (things or services):
						The microservice can specialize:
									1.- In branch of articles (Library, Car Supplier, Gift, etc). These articles are created by the System, and are common to all business units.
				 					2.- In the articles of one company (can have the mix of things and services that sell). These articles are created by each users.
									3.- In a mix of 1 and 2. You can have some common and custom articles.
				 				Concept: The common articles are those that are unique ID in the world, they have standar barcode (EAN) and description.
				 								 The custom articles are non standar, and the company can change them without advise.
	  				The microservices structure are defined by:
			  			Java project called ApoloArticlesMicroSs, this a restfull webservice java project.
			  			MySql database called ArticlesDB, this contain the permanent information of all articles.
			   			Kafka Topics Producer/Consumer, to update all the other Microservices.

========================================================================================================================================================================================
Structure are as follows:
	Used to create the main elements
		ArtDataElements_Tbl	--> Contains the diccionary of all articles data elements of the Microservice.
		ArtDataElementLanguages_Tbl	--> Contains the meaning of the diccionary in another languages than the default.
		ArtDataElementComments_Tbl	--> Contains one or more comments/details/explains of each record of the diccionary.
	Used to create multiples tables
		ArtRootElements_Tli	--> This is a List Table that contains the other data element of the system. Enable the IDNum element to a Microservice.
    Used to create the general properties of the articles. Before create an article, it is necesary create an ArtGeneralProperty.
      ArtGeneral_Tbl    --> Contains the general information of the articles. 
      ArtGeneralProperties_Tbl	--> Contains the properties of each general information.
      ArtGeneralPropertyOptionalFields_Tbl	--> Contains the optional fields/columns of the general property.
    Used to create the articles
			Articles_Tbl	--> Contains the articles informations. This table has the article for each Microservice. 
      ArticleOptionalFields_Tbl  --> Contains the optional fields/columns of the articles informations.
      ArtRelations_Tbl  --> Contains the relation between articles. It could be substitutes, complementary, etc.
    Used to create the Mirror Tables
			Concept: When some value changed in the main table (SysBaseElements_Tbl), a Kaftka producer send this changed to a queue name SysBaseElements+BusinessUnitIDn+ScopeIDn.
	 						 After the item microservices take this change from the queue, the update procedure is triggered, only for the values that are in this microservice.
			ArtSysBaseElements_Mir	--> Contains the MIRROR of the diccionary of all system elements of the Microservice.
			ArtSysBaseElementLanguages_Tbl	--> Contains the MIRROR of the meaning of the diccionary in other languages.
			ArtSysBaseElementComments_Tbl	--> Contains the MIRROR of the comments/details/explains of each record of the diccionary.
			ArtSysRootElements_Tli	--> This is the MIRROR of the List Table that contains the other element of the system. Enable the IDNum element to a Microservice and BusinessUnit.
      ArtSysCompanies_Mir  --> Contain the MIRROR of the SysCompanies_Tbl information.


========================================================================================================================================================================================
Detailed explanation of each table.
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the main elements
		ArtDataElements_Tbl
			Contains the diccionary of all articles data elements of the Microservice.
			The rest of the tables only have the IDNum. To determine what a code means, you should consult this table.
			In order for the same IDName word to have different meanings depending on its use, it is defined for a Scope, BusinessUnit and Language.
			To respect all the rules the unique value must be the combination of: Name/Scope/BusinessUnit/Language.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  IDName     		-> is the readable code by the user.
			  ScopeIDn     		-> the Name must be unique for the application Scope, usually a Table.
			  GroupIDn 			-> the Name must be unique for the Group.
			  LanguageIDn 		-> the Name must be unique for Language. This dictionary has a default language defined.
			Important: when you create the element/object in this table, this element does not exits for the software. This table is like a dictionary.
						Only exist when you create the code in the specific table.
				Example: the pampa article is created in the dictionary, but it does not exist until it is created in the Articles table.
			Modification Rules:
				You can change the Name if there is a spelling error. Example you have an spelling error in a invoce and must be invoice.
				Warning: If I change the code that represents the word Invoice and it is an afip receipt. And I put food, everywhere the code is, food will start to appear.
				If you want to change the code and it is in many places, the system must generate another code for the new value
				To change this value, it must be done by the administrator. 
				It is best to never change it.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		Comments:
				In each microservice and database, you have one DataElements_Tbl. It work as a specific dictionary for it.
				Example:
					In the System Microservice you have the meaning of all databases tables, columns, stored procedures, views, java entities, classes, etc.
					In the Person Microservice you have the meaning of all person (legal or natural) whom can interact with the system.
					In the Users Microservice you have the meaning of all person who can enter in the system to work with. 
					In the Articles Microservice you have the meaning of all the articles that the campany can handle, to sell, buy, or have to use. 
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.

		ArtDataElementLanguages_Tbl	
			Contains the meaning of the diccionary in another languages than the default.
			In this table you have to comply with the same rules as the ArtBaseElements_Tbl.
			Important Clarification: the values IdNum, ScopeIDn, CompanyIDn = are always equal to the ArtDataElements_Tbl. 
									 These columns are put in this table only to ensure integrity and that there are no duplicates.
		    The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  	-- This three values are defined by the user.
			  	DataElementLanguageIDn	--> the IdNum of the element that has another languages meaning. It is created in the ArtDataElements_Tbl.
			  	NameID     		-> is the readable code by the user.
			  	LanguageIDn 	-> the LanguagesIDn must be diferent from the default language.
			  	-- This two values are set by the system automaticaly, and are the same as the ArtDataElements_Tbl. For do that use the IdNum.
			  	ScopeIDn     	-> the Name must be unique for the application Scope, usually a Table.
			  	BusinessUnitIDn	-> the Name must be unique for the BusinessUnit.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.
	
		ArtDataElementComments_Tbl	
			Contains one or more descriptions/comments/details/explains of each record of the diccionary.
			It has a defined language, an order when there is more than one description, a type of text format (mimetype), a status and the date of the last update.
			If the element has not been recorded in this table, it means the element has not clarifications.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			  	This table have not unique key because one IDNum can have none, one or more comments.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.			
			Tips:
				The ScopeIDn + BusinessUnitIDn + TableIDn combination can be the Kafka/rabbitMq topic.
	
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 	Used to create the Companies
		SysCompanies_Mir
			Contains the Mirror of the companies that use the software. 
			Each record is updated from SysCompanies_Tbl, which belongs to the SystemsDB database.
   			The Companies exists since you create them in this table. Its information, of what are they, are in the SysBaseElements_Mir table.
			The key for each record:
				This table has not its own key, because in this table you only enable the company to all the system.
			The unique Key is the union of:
				CompanyIDn		--> The Company can not be duplicated. Link with the ArtDataElements_Tbl.
			Common Field/Columns for all tables
				This table do not have another field, because the store critical information for the system and the record history are set in SysBaseElements_Tbl.

  	Used to create the Microservices
		SysMicroservices_Mir
			Contains the Mirro of the microservices that use the software. 
   			Each record is updated from SysMicroservices_Tbl, which belongs to the SystemsDB database.
			The Microservices exists since you create them in this table. Its information, of what are they, are in the SysBaseElements_Mir table.
			The key for each record:
				This table has not its own key, because in this table you only enable the microservice to all the system.
			The unique Key is the union of:
				MicroserviceIDn		--> The Microservice can not be duplicated. Link with the SysCompanies_Tbl.
			Common Field/Columns for all tables
				This table do not have another field, because the store critical information for the system and the record history are set in ArtDataElements_Tbl.

 	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create multiples tables
		ArtRootElements_Tli
   			This Is a List Table that contains the other element of the system. Enable the IDNum element to a Microservice.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable the element to a Microservice.
			The unique Key is the union of:
				RootElementIDn 		--> the IdNum of the entity
				MicroserviceIDn		--> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices of the BusinessUnit have access to them.
			Example: In the BaseElement_Tbl you have been created all values of the SysContries or SysLangueges, etc. This table is the diccionary and this values can not use it.
					 To make real and enable these values, we must to create a specific table for its. 
					 But if you are going to use only somes record of each tables, is bether have one table with all small tables. This table is called SysRootElement_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
   			Tips:
				The RootElementIDn + MicroserviceIDn combination can be the Kafka/rabbitMq topic.		 
					 
	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the software structure
		ArtGeneral_Tbl 
			Contains the general information of the articles.
   This is a List Table that contains the entities of the system, they can be database tables or java classes. 
			Enable the IDNum element to a Microservice.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable the element to a Microservice.
			The unique Key is the union of:
				EntityIDn 		--> the IdNum of the entity
				MicroserviceIDn --> the IdNum of the Microservice that the Entity belong. When is equal System, all microservices hava access to them.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		Important:
				The Entity is enable in the SysEntities_Tli for a Microservice.
				The data is created in the ArtDataElements_Tbl and is the same to all the BusinessUnit.
				When I assign the entity, I define the business unit owner and the microservice it belongs to.
			Kafka/rabbitMq Topic:
				The combination of EntityIDn + MicroserviceIDn combination can be used.	 


ArtGeneral_Tbl    --> Contains the general information of the articles. 
 
		SysEntityFields_Tli	
			This is a List Table that contains the fields or the columns of the system. Enable the IDNum element to a Microservice.
			The fields exits to the Microservice, since you create it in this table. The information, of what are they, are in the ArtDataElements_Tbl table.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
				This table has its own key only for update it.
				The system does not use this key because, here we only enable one field/column to a Microservice.
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field. Link with the diccionary table - SysBaseElement.
				MicroserviceIDn --> The MicroserviceIDn is the IDNum of the microservice to which the field belongs. If its value is System all microservices have access to it.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		SysEntityStructures_Tbl	
			Contains the system structure. In this table you build the tables and java entities structures, with thiers column and fields.
			You must define all the properties of the entities.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				FieldIDn 		--> The FieldIDn is the IDNum of the field/column of the entity. Link with the ArtDataElements_Tbl.
				EntityIDn 		--> The EntityIDn is the IDNum of the entity. Link with the ArtDataElements_Tbl.
				MicroserviceIDn --> the IdNum of the Microservice that the Field and Entity belong. When is equal System, all microservices hava access to them.
				When an entity is selected from the Entity_Tbl, the Microservice is set, this parameter define the possible fields to be selected.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 
		SysEntityStructureFieldProperties_Tbl
			Contains the properties of each column/table or field/entity.
			Each field/column can have many properties, like DataType, Lenght, IsPrimaryKey, IsNotNull, etc.
			The key for each record:
			    ID		--> is the uniqueidentifier auto generated.
			    IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
			    FieldPropertyIDn	--> The FieldPropertyIDn is the IDNum of the field property type. Link with the ArtDataElements_Tbl.
			    EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
			                            The EntityStructureIDn has a FieldIDn + EntityIDn + MicroserviceIDn unique key.
			Common Field/Columns for all tables
			    The objective of these are to store critical information for the system and the record history.
			        StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
			        CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
			        LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
			        OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
			        DateCreated			--> The DateCreated is the record creation datetime UTC.
			        DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
			        TableHistory		-->	The TableHistory contain then change history of each column.
			The table operation:
			    To enter the data, you need the combination of two field, they are:
			        EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure (Microservice + Entity + Field). Link with the SysEntityStructurea_Tbl.
			        FieldPropertyIDn	--> The FieldPropertyIDn is the IDNum of the field property (DataType, Length, IsNotNull, etc). Link with the SysBaseElementa_Tbl.
			        FieldValueTypeIDn	--> The FieldValueTypeIDn is the IDNum of the type property. ILink with the SysBaseElementa_Tbl.
			        FieldValue			--> The FieldValue is a numeric/text/etc value of the property, this value is not standard and can not be multilanguage.
			                                If the value is an IDNumType, the meaning of it, is in the SysBaseElments_Tbl.
			    Example: You have a Name column. You can set the datatype as string, and the length of the string in 20.
			        First you add one record to define the datatype.
			            EntityStructureIDn = IDNum, represent Entity Person, Column = StateIDn. Link with the SysEntityStructurea_Tbl.
			            FieldPropertyIDn = IDNum, represent DataType, the property of the field. Link with the SysBaseElementa_Tbl.
			            FieldValueTypeIDn = IDNum, represent table state data type. Link with the SysBaseElementa_Tbl.
			            FieldValue = 372, IDNum, represent enable value. Link with the SysBaseElementa_Tbl.
			        Second you add another record to define the lenght.
			            EntityStructureIDn = IDNum, represent Entity Person, Column = StateIDn. Link with the SysEntityStructurea_Tbl.
			            FieldPropertyIDn = IDNum, represent Length, the property of the field. Link with the SysBaseElementa_Tbl.
			            FieldValueTypeIDn = IDNum, represent smallint, the value of the FieldValue is numeric. Link with the SysBaseElementa_Tbl.
			            FieldValue = 20, is the max length of the column. This value are not linked with anythings.
			
		SysEntityStructureFieldDefaultValues_Tbl
			Contains the default value of each field or column. When you set a value for a column, if the user do not enter, the system get the value from this table.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the ArtDataElements_Tbl.
		  		EntityStructureIDn	--> The EntityStructureIDn is the IDNum of the entity structure. Link with the SysEntityStructure_Tbl.
			                            The EntityStructureIDn has a FieldIDn + EntityIDn + MicroserviceIDn unique key.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.
	 		The table operation:
				After you select the EntityStructure (FieldIDn + EntityIDn + MicroserviceIDn) you need to define this two fields:
					DefaultVersionIDn	--> The DefaultVersionIDn is the IDNum of the DefaultVersion, defined in the ArtDataElements_Tbl.
					FieldDefaultValue	--> The FieldDefaultValue is a value that must be the same DataType of the field defined in the SysEntityStructures_Tbl.
				You can define more than one version. 
				Example: If you have to import some data from two diferent suppliers, you can define two diferent version. 
						 In the field supplierIDn, you set the specific number of the supplier.

	---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
	Used to create the Companies and theis Structures
		SysCompanyMicroservice_Tbl
			Contains the relation between Companies and Microservices. 
			This is One Company can have multiples Microservices and One Microservices can be used by multiples Companies.
			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
				CompanyIDn	--> The CompanyIDn is the IDNum. Link with the SysCompanies_Tbl.
		  		MicroserviceIDn	--> The MicroserviceIDn is the IDNum. Link with the SysMicroservices_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		SysCompanyRelations_Tbl
			Contains the relations between companies. If you have a economic group in this table can build this structure.
   			Here you can build the tree of the groups companies using the father/son schema.
			This type of structure allow you to get a individual or group report.
			But this table does not build the internal structure of the company.
   			The key for each record:
				ID		--> is the uniqueidentifier auto generated.
				IDNum	--> is the autoincrement number auto generated.
			The unique Key is the union of:
   				RelationID	--> The ID of the relation (uniqueidentifier). Link with the SysCompanyRelations_Tbl ID Value.
	   			RelationLevel	--> The level of the relation, all start with the number 1. 
	   			RelationOrden	--> The Orden inside the level, all level start with the number 1.
				CompanyIDn	--> The CompanyIDn is the IDNum of the Company involved in the relation. Link with the SysCompanies_Tbl.
			Common Field/Columns for all tables
				The objective of these are to store critical information for the system and the record history.
					StatedIDn 			--> The StatedIDn is the IDNum that define if the record is enable or not.
					CreatedByIDn		--> The CreatedByIDn is the IDNum of the user who created the record.
					LastModifiedByIDn	--> The LastModifiedByIDn is the IDNum of the last user who modified the record.
					OwnerIDn			--> The OwnerIDn is the IDNum of the record owner.
					DateCreated			--> The DateCreated is the record creation datetime UTC.
					DateTimeStamp		--> The DateTimeStamp is the datetime UTC of the last modification.
					TableHistory		-->	The TableHistory contain then change history of each column.

		
