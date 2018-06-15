---
title: Ismerkedés az Azure várólista-tároló és a Visual Studio kapcsolódó szolgáltatások (ASP.NET) |} Microsoft Docs
description: Ismerkedés az Azure üzenetsorának tárhelyet használ egy ASP.NET-projekt, a Visual Studio egy tárfiókot, a Visual Studio kapcsolódó szolgáltatások használatával történő kapcsolódás után
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: d06fde0dc6c289a09b9fe4c9e2ffbb50c3027490
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798321"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure várólista-tároló és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Az Azure várólista-tároló alkalmazás-összetevők közötti üzenetküldési felhő biztosít. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET olyan gyakori forgatókönyveket tartalmaz Azure üzenetsor-kezelési tárolási entitások használata. Ilyen például a gyakori feladatokat, mint egy Azure-üzenetsorba, létrehozása és hozzáadása, módosítása, olvasása, és eltávolítása a üzenetsor-üzeneteket.

##<a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Hozzon létre az MVC-vezérlő 

1. A a **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**, és a helyi menüből válassza ki a **Hozzáadás -> tartományvezérlő**.

    ![Vezérlő hozzáadása az ASP.NET MVC alkalmazások számára](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. A a **hozzáadása Scaffold** párbeszédablakban válassza **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Adja meg az MVC-vezérlő típusa](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen, a tartományvezérlő nevét *QueuesController*, és válassza ki **Hozzáadás**.

    ![Neve az MVC-vezérlő](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adja hozzá a következő *használatával* irányelvek a `QueuesController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Üzenetsor létrehozása

A következő lépések bemutatják, hogyan várólista létrehozása:

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt. 

1. Adja hozzá a hívott metódus **CreateQueue** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **CreateQueue** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Első egy **CloudQueue** hivatkozni kell a kívánt sor nevét képviselő objektum. A **CloudQueueClient.GetQueueReference** metódus nem tesz egy kérelmet a queue storage. A referencia-e a várólista létezik adja vissza. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.CreateIfNotExists** módszer a várólista létrehozására, ha még nem létezik. A **CloudQueue.CreateIfNotExists** metódus beolvasása **igaz** , ha a várólista nem létezik, és sikeresen létrejött. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Frissítés a **ViewBag** a várólista nevét.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateQueue** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `CreateQueue.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **létrehozás várólista** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Üzenetsor létrehozása](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Ahogy korábban említettük a **CloudQueue.CreateIfNotExists** metódus beolvasása **igaz** csak a várólista nem létezik és jön létre. Ezért amikor a várólista létezik-e, futtassa az alkalmazást, ha a metódus visszaadja **hamis**. Az alkalmazás többször is lefuthat, az alkalmazás ismételt futtatása előtt kell törölnie a várólistát. A sor törlése megteheti a **CloudQueue.Delete** metódust. A várólista használatával is törölheti a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>A várólista üzenet hozzáadása

Miután megismerte [egy sor](#create-a-queue), erre a várólistára üzenetet is hozzáadhat. Ez a szakasz bemutatja, hogyan üzenet ad hozzá egy várólista *teszt-várólista*. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adja hozzá a hívott metódus **AddMessage** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **AddMessage** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Első egy **CloudQueueContainer** hivatkozni kell a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hozzon létre a **CloudQueueMessage** az üzenetet a várólistában hozzá szeretné képviselő objektum. A **CloudQueueMessage** objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Hívja a **CloudQueue.AddMessage** a várakozási sorba a messaged adható hozzá.

    ```csharp
    queue.AddMessage(message);
    ```

1. Néhány létrehozásáról és **ViewBag** a Nézet tulajdonságai.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddMessage** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `AddMessage.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Hozzáadás üzenet** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Üzenet hozzáadása](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

A két szakasz - [üzenet olvasása az üzenetsorból eltávolítása nélkül](#read-a-message-from-a-queue-without-removing-it) és [olvasási és eltávolítása egy üzenetet az üzenetsorból](#read-and-remove-a-message-from-a-queue) -bemutatják, hogyan lehet üzenetek olvasása az üzenetsorból.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Egy üzenet olvasása az üzenetsorból eltávolítása nélkül

Ez a szakasz bemutatja, hogyan való aszinkron üzenet (az első üzenet olvasása eltávolítása nélkül).  

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adja hozzá a hívott metódus **PeekMessage** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **PeekMessage** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Első egy **CloudQueueContainer** hivatkozni kell a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.PeekMessage** módszer a várólista első üzenetébe olvasni a eltávolítása nélkül. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Frissítés a **ViewBag** két értékekkel: a várólista nevét és az üzenetet, amely lett beolvasva. A **CloudQueueMessage** vezérlőnek két tulajdonságait az objektum értékének lekérését: **CloudQueueMessage.AsBytes** és **CloudQueueMessage.AsString**. **AsString** (ebben a példában használt) egy karakterláncot ad vissza, miközben **AsBytes** adja vissza egy bájttömböt.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **PeekMessage** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `PeekMessage.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **betekintés üzenet** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Üzenet megtekintése](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Olvassa el, és távolítsa el az üzenetet az üzenetsorból

Ebben a szakaszban megismerheti, hogyan kiolvasni, és távolítsa el az üzenetet az üzenetsorból.   

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adja hozzá a hívott metódus **ReadMessage** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **ReadMessage** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Első egy **CloudQueueContainer** hivatkozni kell a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.GetMessage** metódus olvasni a várólista első üzenetébe. A **CloudQueue.GetMessage** módszer lehetővé teszi az üzenet nem látható üzeneteket olvasó, így nincs más kód módosíthatja vagy törölheti az üzenet feldolgozásakor a program a azt többi kód számára (alapértelmezés) 30 másodpercig. Az üzenet nem látható idő megváltoztatásához módosítsa a **visibilityTimeout** átadott paraméter a **CloudQueue.GetMessage** metódust.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Hívja a **CloudQueueMessage.Delete** metódus az üzenet törlése az üzenetsorból.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Frissítés a **ViewBag** törli az üzenetet, és a várólista nevét.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **ReadMessage** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `ReadMessage.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **olvasás/törlés üzenet** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Olvasási és törlési üzenet](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Ez a szakasz bemutatja az beszerzése a várólista hossza (üzenetek száma). 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adja hozzá a hívott metódus **GetQueueLength** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **ReadMessage** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Első egy **CloudQueueContainer** hivatkozni kell a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.FetchAttributes** metódusának segítéségével lekérheti a várólista attribútumok (beleértve a hossza). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Hozzáférés a **CloudQueue.ApproximateMessageCount** tulajdonság használatával beolvassa a várólista hossza.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Frissítés a **ViewBag** a nevét, valamint a várólista hossza.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetQueueLength** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `GetQueueLengthMessage.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **várólista hosszának lekérése** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Get-várólista hossza](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Üzenetsor törlése
Ez a szakasz bemutatja, hogyan várólista törlése. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a [beállította a fejlesztőkörnyezetet](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adja hozzá a hívott metódus **DeleteQueue** , amely visszaadja az **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **DeleteQueue** módszer, lekérni egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs az alábbi kód használatával: (módosítása  *&lt;tárfióknév >* elérni az Azure storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Első egy **CloudQueueClient** objektum által jelképezett várólista szolgáltatás ügyfél.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Első egy **CloudQueueContainer** hivatkozni kell a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.Delete** metódus által képviselt a várólista törlése a **CloudQueue** objektum.

    ```csharp
    queue.Delete();
    ```

1. Frissítés a **ViewBag** a nevét, valamint a várólista hossza.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. A a **Solution Explorer**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **várólisták**, és válassza a helyi menüben a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **DeleteQueue** a nézet nevét, majd válassza a **Hozzáadás**.

1. Nyissa meg `DeleteQueue.cshtml`, és módosítsa úgy, hogy például a következő kódrészletet:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, majd nyissa meg `_Layout.cshtml`.

1. Után utolsó **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **várólista hosszának lekérése** az alábbi képernyőfelvételhez hasonló eredmények megtekintése érdekében:
  
    ![Üzenetsor törlése](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure table storage és a Visual Studio kapcsolódó szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
