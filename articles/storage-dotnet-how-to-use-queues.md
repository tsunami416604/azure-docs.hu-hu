<properties urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# How to use Queue Storage from .NET

This guide will show you how to perform common scenarios using the
Azure Queue storage service. The samples are written in C# code
and use the Azure Storage Client for .NET. The scenarios covered include **inserting**,
**peeking**, **getting**, and **deleting** queue messages, as well as
**creating and deleting queues**. For more information on queues, refer
to the [Next steps][Next steps] section.

> [WACOM.NOTE] This guide targets the Azure .NET Storage Client Library 2.x and above. The recommended version is Storage Client Library 4.x, which is available via [NuGet][NuGet] or as part of the [Azure SDK for .NET][Azure SDK for .NET]. See [How to: Programmatically access Queue storage][How to: Programmatically access Queue storage] below for more details on obtaining the Storage Client Library.

## Table of contents

-   [What is Queue Storage][What is Queue Storage]
-   [Concepts][Concepts]
-   [Create an Azure Storage Account][Create an Azure Storage Account]
-   [Setup an Azure Storage connection string][Setup an Azure Storage connection string]
-   [How to: Programmatically access Queue storage][How to: Programmatically access Queue storage]
-   [How to: Create a queue][How to: Create a queue]
-   [How to: Insert a message into a queue][How to: Insert a message into a queue]
-   [How to: Peek at the next message][How to: Peek at the next message]
-   [How to: Change the contents of a queued message][How to: Change the contents of a queued message]
-   [How to: Dequeue the next message][How to: Dequeue the next message]
-   [How to: Leverage additional options for dequeuing messages][How to: Leverage additional options for dequeuing messages]
-   [How to: Get the queue length][How to: Get the queue length]
-   [How to: Delete a queue][How to: Delete a queue]
-   [Next steps][Next steps]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Create an account</span>Create an Azure Storage account

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Setup a connection string</span>Setup an Azure Storage Connection String

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Access programmatically</span>How to: Programmatically access Queue storage

### Obtaining the assembly

You can use NuGet to obtain the `Microsoft.WindowsAzure.Storage.dll` assembly. Right-click your project in **Solution Explorer** and choose **Manage NuGet Packages**. Search online for "WindowsAzure.Storage" and click **Install** to install the Azure Storage package and dependencies.

`Microsoft.WindowsAzure.Storage.dll` is also included in the Azure SDK for .NET, which can be downloaded from the [.NET Developer Center][.NET Developer Center]. The assembly is installed to the `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code> directory.

### Namespace declarations

Add the following code namespace declarations to the top of any C# file
in which you wish to programmatically access Azure Storage:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Make sure you reference the `Microsoft.WindowsAzure.Storage.dll` assembly.

### Retrieving your connection string

You can use the **CloudStorageAccount** type to represent
your Storage Account information. If you are using a Windows
Azure project template and/or have a reference to
Microsoft.WindowsAzure.CloudConfigurationManager, you
can you use the **CloudConfigurationManager** type
to retrieve your storage connection string and storage account
information from the Azure service configuration:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

If you are creating an application with no reference to Microsoft.WindowsAzure.CloudConfigurationManager and your connection string is located in the `web.config` or `app.config` as show above, then you can use **ConfigurationManager** to retrieve the connection string. You will need to add a reference to System.Configuration.dll to your project, and add another namespace declaration for it:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib dependencies

ODataLib dependencies in the Storage Client Library for .NET are resolved through the ODataLib (version 5.0.2) packages available through NuGet and not WCF Data Services. The ODataLib libraries can be downloaded directly or referenced by your code project through NuGet. The specific ODataLib packages are [OData][OData], [Edm][Edm], and [Spatial][Spatial].

## <a name="create-queue"></a><span class="short-header">Create a queue</span>How to: Create a queue

A **CloudQueueClient** object lets you get reference objects for queues.
The following code creates a **CloudQueueClient** object. All code in
this guide uses a storage connection string stored in the Azure
application's service configuration. There are also other ways to create
a **CloudStorageAccount** object. See [CloudStorageAccount][CloudStorageAccount]
documentation for details.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use the **queueClient** object to get a reference to the queue you want
to use. You can create the queue if it doesn't exist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Insert a message</span>How to: Insert a message into a queue

To insert a message into an existing queue, first create a new
**CloudQueueMessage**. Next, call the **AddMessage** method. A
**CloudQueueMessage** can be created from either a string (in UTF-8
format) or a **byte** array. Here is code which creates a queue (if it
doesn't exist) and inserts the message 'Hello, World':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-message"></a><span class="short-header">Peek at the next message</span>How to: Peek at the next message

You can peek at the message in the front of a queue without removing it
from the queue by calling the **PeekMessage** method.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-contents"></a><span class="short-header">Change message contents</span>How to: Change the contents of a queued message

You can change the contents of a message in-place in the queue. If the
message represents a work task, you could use this feature to update the
status of the work task. The following code updates the queue message
with new contents, and sets the visibility timeout to extend another 60
seconds. This saves the state of work associated with the message, and
gives the client another minute to continue working on the message. You
could use this technique to track multi-step workflows on queue
messages, without having to start over from the beginning if a
processing step fails due to hardware or software failure. Typically,
you would keep a retry count as well, and if the message is retried more
than *n* times, you would delete it. This protects against a message
that triggers an application error each time it is processed.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="get-message"></a><span class="short-header">De-queue the next message</span>How to: De-queue the next message

Your code de-queues a message from a queue in two steps. When you call
**GetMessage**, you get the next message in a queue. A message returned
from **GetMessage** becomes invisible to any other code reading messages
from this queue. By default, this message stays invisible for 30
seconds. To finish removing the message from the queue, you must also
call **DeleteMessage**. This two-step process of removing a message
assures that if your code fails to process a message due to hardware or
software failure, another instance of your code can get the same message
and try again. Your code calls **DeleteMessage** right after the message
has been processed.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="advanced-get"></a><span class="short-header">More de-queueing options</span>How to: Leverage additional options for de-queuing messages

There are two ways you can customize message retrieval from a queue.
First, you can get a batch of messages (up to 32). Second, you can set a
longer or shorter invisibility timeout, allowing your code more or less
time to fully process each message. The following code example uses the
**GetMessages** method to get 20 messages in one call. Then it processes
each message using a **foreach** loop. It also sets the invisibility
timeout to five minutes for each message. Note that the 5 minutes starts
for all messages at the same time, so after 5 minutes have passed since
the call to **GetMessages**, any messages which have not been deleted
will become visible again.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-queue-length"></a><span class="short-header">Get the queue length</span>How to: Get the queue length

You can get an estimate of the number of messages in a queue. The
**FetchAttributes** method asks the Queue service to
retrieve the queue attributes, including the message count. The **ApproximateMethodCount**
property returns the last value retrieved by the
**FetchAttributes** method, without calling the Queue service.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-queue"></a><span class="short-header">Delete a queue</span>How to: Delete a queue

To delete a queue and all the messages contained in it, call the
**Delete** method on the queue object.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Next steps

Now that you've learned the basics of Queue storage, follow these links
to learn how to do more complex storage tasks.

-   View the Queue service reference documentation for complete details about available APIs:
    -   [Storage Client Library for .NET reference][Storage Client Library for .NET reference]
    -   [REST API reference][REST API reference]
-   Learn about more advanced tasks you can perform with Azure Storage at [Storing and Accessing Data in Azure][Storing and Accessing Data in Azure].
-   Learn how to work with Azure Storage in backend processes for Azure Websites at [Get Started with the Azure WebJobs SDK][Get Started with the Azure WebJobs SDK].
-   View more feature guides to learn about additional options for storing data in Azure.
    -   Use [Table Storage][Table Storage] to store structured data.
    -   Use [Blob Storage][Blob Storage] to store unstructured data.
    -   Use [SQL Database][SQL Database] to store relational data.

  [Next steps]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK for .NET]: /en-us/downloads/
  [How to: Programmatically access Queue storage]: #configure-access
  [What is Queue Storage]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Setup an Azure Storage connection string]: #setup-connection-string
  [How to: Create a queue]: #create-queue
  [How to: Insert a message into a queue]: #insert-message
  [How to: Peek at the next message]: #peek-message
  [How to: Change the contents of a queued message]: #change-contents
  [How to: Dequeue the next message]: #get-message
  [How to: Leverage additional options for dequeuing messages]: #advanced-get
  [How to: Get the queue length]: #get-queue-length
  [How to: Delete a queue]: #delete-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [.NET Developer Center]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Storage Client Library for .NET reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Get Started with the Azure WebJobs SDK]: /en-us/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Table Storage]: /en-us/documentation/articles/storage-dotnet-how-to-use-tables/
  [Blob Storage]: /en-us/documentation/articles/storage-dotnet-how-to-use-blobs/
  [SQL Database]: /en-us/documentation/articles/sql-database-dotnet-how-to-use/
