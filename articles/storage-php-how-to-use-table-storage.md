<properties urlDisplayName="Table Service" pageTitle="How to use table storage (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Learn how to use the Table service from PHP to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="PHP" title="How to use the Table service from PHP" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# How to use the Table service from PHP

This guide will show you how to perform common scenarios using the Azure Table service. The samples are written in PHP and use the [Azure SDK for PHP][Azure SDK for PHP]. The scenarios covered include **creating and deleting a table, and inserting, deleting, and querying entities in a table**. For more information on the Azure Table service, see the [Next Steps][Next Steps] section.

## Table of contents

-   [What is the Table Service][What is the Table Service]
-   [Concepts][Concepts]
-   [Create an Azure storage account][Create an Azure storage account]
-   [Create a PHP application][Create a PHP application]
-   [Configure your application to access the Table service][Configure your application to access the Table service]
-   [Setup an Azure storage connection][Setup an Azure storage connection]
-   [How to: Create a table][How to: Create a table]
-   [How to: Add an entity to a table][How to: Add an entity to a table]
-   [How to: Retrieve a single entity][How to: Retrieve a single entity]
-   [How to: Retrieve all entities in a partition][How to: Retrieve all entities in a partition]
-   [How to: Retrieve a subset of entities in a partition][How to: Retrieve a subset of entities in a partition]
-   [How to: Retrieve a subset of entity properties][How to: Retrieve a subset of entity properties]
-   [How to: Update an entity][How to: Update an entity]
-   [How to: Batch table operations][How to: Batch table operations]
-   [How to: Delete a table][How to: Delete a table]
-   [Next Steps][Next Steps]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="CreateAccount"></span></a>Create an Azure storage account

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>Create a PHP application

The only requirement for creating a PHP application that accesses the Azure Table service is the referencing of classes in the Azure SDK for PHP from within your code. You can use any development tools to create your application, including Notepad.

In this guide, you will use Table service features which can be called from within a PHP application locally, or in code running within an Azure web role, worker role, or website.

## <span id="GetClientLibrary"></span></a>Get the Azure Client Libraries

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>Configure your application to access the Table service

To use the Azure Table service APIs, you need to:

1.  Reference the autoloader file using the [require\_once][require\_once] statement, and
2.  Reference any classes you might use.

The following example shows how to include the autoloader file and reference the **ServicesBuilder** class.

> [WACOM.NOTE]
> This example (and other examples in this article) assume you have installed the PHP Client Libraries for Azure via Composer. If you installed the libraries manually or as a PEAR package, you will need to reference the `WindowsAzure.php` autoloader file.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

In the examples below, the `require_once` statement will be shown always, but only the classes necessary for the example to execute will be referenced.

## <span id="ConnectionString"></span></a>Setup an Azure storage connection

To instantiate an Azure Table service client you must first have a valid connection string. The format for the table service connection string is:

For accessing a live service:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

For accessing the emulator storage:

    UseDevelopmentStorage=true

To create any Azure service client you need to use the **ServicesBuilder** class. You can:

-   pass the connection string directly to it or
-   use the **CloudConfigurationManager (CCM)** to check multiple external sources for the connection string:

    -   by default it comes with support for one external source - environmental variables
    -   you can add new sources by extending the **ConnectionStringSource** class

For the examples outlined here, the connection string will be passed directly.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

## <span id="CreateTable"></span></a>How to: create a table

A **TableRestProxy** object lets you create a table with the **createTable** method. When creating a table, you can set the Table Service timeout. (For more information about the table service timeout, see [Setting Timeouts for Table Service Operations][Setting Timeouts for Table Service Operations].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
    }

For information about restrictions on Table names, see [Understanding the Table Service Data Model][Understanding the Table Service Data Model].

## <span id="AddEntity"></span></a>How to: Add an entity to a table

To add an entity to a table, create a new **Entity** object and pass it to **TableRestProxy-\>insertEntity**. Note that when you create an entity you must specify a `PartitionKey` and `RowKey`. These are the unique identifiers for an entity and are values that can be queried much faster than other entity properties. The system uses `PartitionKey` to automatically distribute the table’s entities over many storage nodes. Entities with the same `PartitionKey` are stored on the same node. (Operations on multiple entities stored on the same node will perform better than on entities stored across different nodes.) The `RowKey` is the unique ID of an entity within a partition.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", 
                         EdmType::DATETIME, 
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

For information about Table properties and types, see [Understanding the Table Service Data Model][Understanding the Table Service Data Model].

The **TableRestProxy** class offers two alternative methods for inserting entities: **insertOrMergeEntity** and **insertOrReplaceEntity**. To use these methods, create a new **Entity** and pass it as a parameter to either method. Each method will insert the entity if it does not exist. If the entity already exists, **insertOrMergeEntity** will update property values if the properties already exist and add new properties if they do not exist, while **insertOrReplaceEntity** completely replaces an existing entity. The following example shows how to use **insertOrMergeEntity**. If the entity with `PartitionKey` "tasksSeattle" and `RowKey` "1" does not already exist, it will be inserted. However, if it has previously been inserted (as shown in the example above), the `DueDate` property will be updated and the `Status` property will be added. The `Description` and `Location` properties are also updated, but with values that effectively leave them unchanged. If these latter two properties were not added as shown in the example, but existed on the target entity, their existing values would remain unchanged.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
       

## <span id="RetrieveEntity"></span></a>How to: Retrieve a single entity

The **TableRestProxy-\>getEntity** method allows you to retrieve a single entity by querying for its `PartitionKey` and `RowKey`. In the example below, the partition key `tasksSeattle` and row key `1` are passed to the **getEntity** method.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <span id="RetEntitiesInPartition"></span></a>How to: Retrieve all entities in a partition

Entity queries are constructed using filters (for more information, see [Querying Tables and Entities][Querying Tables and Entities]). To retrieve all entities in partition, use the filter "PartitionKey eq *partition\_name*". The following example shows how to retrieve all entities in the `tasksSeattle` partition by passing a filter to the **queryEntities** method.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetrieveSubset"></span></a>How to: Retrieve a subset of entities in a partition

The same pattern used in the previous example can be used to retrieve any subset of entities in a partition. The subset of entities you retrieve will be determined by the filter you use (for more information, see [Querying Tables and Entities][Querying Tables and Entities]).The following example shows how to use a filter to retrieve all entities with a specific `Location` and a `DueDate` less than a specified date.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetPropertiesSubset"></span></a>How to: Retrieve a subset of entity properties

A query can retrieve a subset of entity properties. This technique, called *projection*, reduces bandwidth and can improve query performance, especially for large entities. To specify a property to be retrieved, pass the name of the property to the **Query-\>addSelectField** method. You can call this method multiple times to add more properties. After executing **TableRestProxy-\>queryEntities**, the returned entities will only have the selected properties. (If you want to return a subset of Table entities, use a filter as shown in the queries above.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether 
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <span id="UpdateEntity"></span></a>How to: Update an entity

An existing entity can be updated by using the **Entity-\>setProperty** and **Entity-\>addProperty** methods on the entity, and then calling **TableRestProxy-\>updateEntity**. The following example retrieves an entity, modifies one property, removes another property, and adds a new property. Note that removing a property is done by setting its value to **null**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteEntity"></span></a>How to: Delete an entity

To delete an entity, pass the table name, and the entity's `PartitionKey` and `RowKey` to the **TableRestProxy-\>deleteEntity** method.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Note that for concurrency checks, you can set the Etag for an entity to be deleted by using the **DeleteEntityOptions-\>setEtag** method and passing the **DeleteEntityOptions** object to **deleteEntity** as a fourth parameter.

## <span id="BatchOperations"></span></a>How to: Batch table operations

The **TableRestProxy-\>batch** method allows you to execute multiple operations in a single request. The pattern here involves adding operations to **BatchRequest** object and then passing the **BatchRequest** object to the **TableRestProxy-\>batch** method. To add an operation to a **BatchRequest** object, you can call any of the following methods multiple times:

-   **addInsertEntity** (adds an insertEntity operation)
-   **addUpdateEntity** (adds an updateEntity operation)
-   **addMergeEntity** (adds a mergeEntity operation)
-   **addInsertOrReplaceEntity** (adds an insertOrReplaceEntity operation)
-   **addInsertOrMergeEntity** (adds an insertOrMergeEntity operation)
-   **addDeleteEntity** (adds a deleteEntity operation)

The following example shows how to execute **insertEntity** and **deleteEntity** operations in a single request:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\Entity;
    use WindowsAzure\Table\Models\EdmType;
    use WindowsAzure\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate", 
                          EdmType::DATETIME, 
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

For more information about batching Table operations, see [Performing Entity Group Transactions][Performing Entity Group Transactions].

## <span id="DeleteTable"></span></a>How to: Delete a table

Finally, to delete a table, pass the table name to the **TableRestProxy-\>deleteTable** method.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Next steps

Now that you’ve learned the basics of the Azure Table Service, follow these links to learn how to do more complex storage tasks.

-   See the MSDN Reference: [Storing and Accessing Data in Azure][Storing and Accessing Data in Azure]
-   Visit the Azure Storage Team Blog: <http://blogs.msdn.com/b/windowsazurestorage/>

  [Azure SDK for PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Next Steps]: #NextSteps
  [What is the Table Service]: #what-is
  [Concepts]: #concepts
  [Create an Azure storage account]: #CreateAccount
  [Create a PHP application]: #CreateApplication
  [Configure your application to access the Table service]: #ConfigureStorage
  [Setup an Azure storage connection]: #ConnectionString
  [How to: Create a table]: #CreateTable
  [How to: Add an entity to a table]: #AddEntity
  [How to: Retrieve a single entity]: #RetrieveEntity
  [How to: Retrieve all entities in a partition]: #RetEntitiesInPartition
  [How to: Retrieve a subset of entities in a partition]: #RetrieveSubset
  [How to: Retrieve a subset of entity properties]: #RetPropertiesSubset
  [How to: Update an entity]: #UpdateEntity
  [How to: Batch table operations]: #BatchOperations
  [How to: Delete a table]: #DeleteTable
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [get-client-libraries]: ../includes/get-client-libraries.md
  [require\_once]: http://php.net/require_once
  [Setting Timeouts for Table Service Operations]: http://msdn.microsoft.com/en-us/library/windowsazure/dd894042.aspx
  [Understanding the Table Service Data Model]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx
  [Querying Tables and Entities]: http://msdn.microsoft.com/en-us/library/windowsazure/dd894031.aspx
  [Performing Entity Group Transactions]: http://msdn.microsoft.com/en-us/library/windowsazure/dd894038.aspx
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
