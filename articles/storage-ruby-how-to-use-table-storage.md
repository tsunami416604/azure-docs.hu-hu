<properties urlDisplayName="Table Service" pageTitle="How to use table storage (Ruby) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="Learn how to use the table storage service in Azure. Code samples are written using the Ruby API." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Table Service from Ruby" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# How to Use the Table Service from Ruby

This guide shows you how to perform common scenarios using the Windows
Azure Table service. The samples are written written using the
Ruby API. The scenarios covered include **creating and deleting a
table, inserting and querying entities in a table**. For more
information on tables, see the [Next Steps][Next Steps] section.

## Table of Contents

-   [What is the Table Service?][What is the Table Service?]
-   [Concepts][Concepts]
-   [Create an Azure Storage Account][Create an Azure Storage Account]
-   [Create a Ruby application][Create a Ruby application]
-   [Configure your Application to Access Storage][Configure your Application to Access Storage]
-   [Setup an Azure Storage Connection][Setup an Azure Storage Connection]
-   [How to: Create a Table][How to: Create a Table]
-   [How to: Add an Entity to a Table][How to: Add an Entity to a Table]
-   [How To: Update an Entity][How To: Update an Entity]
-   [How to: Work with Groups of Entities][How to: Work with Groups of Entities]
-   [How to: Query for an Entity][How to: Query for an Entity]
-   [How to: Query a Set of Entities][How to: Query a Set of Entities]
-   [How To: Query a Subset of Entity Properties][How To: Query a Subset of Entity Properties]
-   [How To: Delete an Entity][How To: Delete an Entity]
-   [How to: Delete a Table][How to: Delete a Table]
-   [Next Steps][Next Steps]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="create-a-windows-azure-storage-account"></span></a>Create an Azure Storage Account

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="create-a-ruby-application"></span></a>Create a Ruby Application

Create a Ruby application. For instructions,
see [Create a Ruby Application on Azure][Create a Ruby Application on Azure].

## <span id="configure-your-application-to-access-storage"></span></a>Configure Your Application to Access Storage

To use Azure storage, you need to download and use the Ruby azure package,
which includes a set of convenience libraries that communicate with the storage REST services.

### Use RubyGems to obtain the package

1.  Use a command-line interface such as **PowerShell** (Windows), **Terminal** (Mac), or **Bash** (Unix).

2.  Type **gem install azure** in the command window to install the gem and dependencies.

### Import the package

Use your favorite text editor, add the following to the top of the Ruby file where you intend to use storage:

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Setup an Azure Storage Connection

The azure module will read the environment variables **AZURE\_STORAGE\_ACCOUNT** and **AZURE\_STORAGE\_ACCESS\_KEY**
for information required to connect to your Azure storage account. If these environment variables are not set, you must specify the account information before using **Azure::TableService** with the following code:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

To obtain these values:

1.  Log into the [Azure Management Portal][Azure Management Portal].

2.  Navigate to the storage account you want to use.

3.  Click **MANAGE KEYS** at the bottom of the navigation pane.

4.  In the pop up dialog, you will see the storage account name, primary access key and secondary access key. For access key, you can either the primary one or the secondary one.

## <span id="how-to-create-a-table"></span></a>How to Create a Table

The **Azure::TableService** object lets you work with tabls and entities. To create a table, use the **create\_table()** method. The following example creates a table or print out the error if there is any.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <span id="how-to-add-an-entity-to-a-table"></span></a>How to Add an Entity to a Table

To add an entity, first create a hash object that defines your entity properties. Note that for every entity you mustspecify a **PartitionKey** and **RowKey**. These are the unique identifiers of your entities, and are values that can be queried much faster than your other properties. Azure Storage Service uses **PartitionKey** to automatically distribute the table's entities over many storage nodes. Entities with the same **PartitionKey** are stored on the same node. The **RowKey** is the unique ID of the entity within the partition it belongs to.

    entity = { "content" => "test entity", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <span id="how-to-update-an-entity"></span></a>How To: Update an Entity

There are multiple methods available to update an existing entity:

-   **update\_entity():** Update an existing entity by replacing it.
-   **merge\_entity():** Updates an existing entity by merging new property values into the existing entity.
-   **insert\_or\_merge\_entity():** Updates an existing entity by replacing it. If no entity exists, a new one will be inserted:
-   **insert\_or\_replace\_entity():** Updates an existing entity by merging new property values into the existing entity. If no entity exists, a new one will be inserted.

The following example demonstrates updating an entity using **update\_entity()**:

    entity = { "content" => "test entity with updated content", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

With **update\_entity()** and **merge\_entity()**, if the entity that is being updated doesn't exist then the update operation will fail. Therefore if you wish to store an entity regardless of whether it already exists, you should instead use **insert\_or\_replace\_entity()** or **insert\_or\_merge\_entity()**.

## <span id="how-to-work-with-groups-of-entities"></span></a>How to: Work with Groups of Entities

Sometimes it makes sense to submit multiple operations together in a batch to ensure atomic processing by the server. To accomplish that, you first create a **Batch** object and then use the **execute\_batch()** method on **TableService**. The following example demonstrates submitting two entities with RowKey 2 and 3 in a batch. Notice that it only works for entities with the same PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable", 
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <span id="how-to-query-for-an-entity"></span></a>How to: Query for an Entity

To query an entity in a table, use the **get\_entity()** method, by passing the table name, **PartitionKey** and **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key", 
      "1")

## <span id="how-to-query-a-set-of-entities"></span></a>How to: Query a Set of Entities

To query a set of entities in a table, create a query hash object and use the **query\_entities()** method. The following example demonstrates getting all the entities with the same **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

**Notice** that if the result set is too large for a single query to return, a continuation token will be returned which you can use to retrieve subsequent pages.

## <span id="how-to-query-a-subset-of-entity-properties"></span></a>How To: Query a Subset of Entity Properties

A query to a table can retrieve just a few properties from an entity. This technique, called "projection", reduces bandwidth and can improve query performance, especially for large entities. Use the select clause and pass the names of the properties you would like to bring over to the client.

    query = { :filter => "PartitionKey eq 'test-partition-key'", 
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <span id="how-to-delete-an-entity"></span></a>How To: Delete an Entity

To delete an entity, use the **delete\_entity()** method. You need to pas in the name of the table which contains the entity, the PartitionKey and RowKey of the entity.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <span id="how-to-delete-a-table"></span></a>How to: Delete a Table

To delete a table, use the **delete\_table()** method and pass in the name of the table you want to delete.

        azure_table_service.delete_table("testtable")

## <span id="next-steps"></span></a>Next Steps

Now that you've learned the basics of table storage, follow these links to learn how to do more complex storage tasks.

-   See the MSDN Reference: [Storing and Accessing Data in Azure][Storing and Accessing Data in Azure]
-   Visit the [Azure Storage Team Blog][Azure Storage Team Blog]
-   Visit the [Azure SDK for Ruby][Azure SDK for Ruby] repository on GitHub

  [Next Steps]: #next-steps
  [What is the Table Service?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-a-windows-azure-storage-account
  [Create a Ruby application]: #create-a-ruby-application
  [Configure your Application to Access Storage]: #configure-your-application-to-access-storage
  [Setup an Azure Storage Connection]: #setup-a-windows-azure-storage-connection
  [How to: Create a Table]: #how-to-create-a-table
  [How to: Add an Entity to a Table]: #how-to-add-an-entity-to-a-table
  [How To: Update an Entity]: #how-to-update-an-entity
  [How to: Work with Groups of Entities]: #how-to-work-with-groups-of-entities
  [How to: Query for an Entity]: #how-to-query-for-an-entity
  [How to: Query a Set of Entities]: #how-to-query-a-set-of-entities
  [How To: Query a Subset of Entity Properties]: #how-to-query-a-subset-of-entity-properties
  [How To: Delete an Entity]: #how-to-delete-an-entity
  [How to: Delete a Table]: #how-to-delete-a-table
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Create a Ruby Application on Azure]: /en-us/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure Management Portal]: https://manage.windowsazure.com/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: http://github.com/WindowsAzure/azure-sdk-for-ruby
