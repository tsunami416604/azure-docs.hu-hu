---
title: Ismerkedés az Azure üzenetsor-tárolóval a Visual Studióval (ASP.NET)
description: Az Azure üzenetsor-tárolás használatának első lépései egy ASP.NET-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980759"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure üzenetsor-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Az Azure üzenetsor-tárolója Felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET-kódokat néhány gyakori forgatókönyvhöz az Azure üzenetsor-tárolási entitások használatával. Ezek a forgatókönyvek olyan általános feladatokat foglalnak magukba, mint például az Azure-üzenetsor létrehozása, valamint üzenetsor-üzenetek hozzáadása, módosítása, olvasása és eltávolítása.

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **megoldáskezelő**kattintson a jobb gombbal a **vezérlők**elemre, és a helyi menüben válassza a **Hozzáadás – > vezérlő**elemet.

    ![Vezérlő hozzáadása egy ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Az **állvány hozzáadása** párbeszédpanelen válassza az **MVC 5 vezérlő – üres**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![MVC-vezérlő típusának megadása](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. A **vezérlő hozzáadása** párbeszédpanelen nevezze el a vezérlő *QueuesController*, és válassza a **Hozzáadás**lehetőséget.

    ![Az MVC-vezérlő neve](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adja hozzá az *alábbi utasításokat* a `QueuesController.cs` fájlhoz:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Üzenetsor létrehozása

A következő lépések bemutatják, hogyan hozhat létre üzenetsor-t:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt. 

1. Adjon hozzá egy **CreateQueue** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **CreateQueue** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Szerezzen be egy **CloudQueue** objektumot, amely a kívánt várólista nevére mutató hivatkozást jelöl. A **CloudQueueClient. GetQueueReference** metódus nem hajt végre kérelmet a várólista-tárolón. A hivatkozás azt adja vissza, hogy létezik-e a várólista. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ha még nem létezik, hívja meg a **CloudQueue. createifnotexists metódust** metódust a várólista létrehozásához. A **CloudQueue. createifnotexists metódust** metódus **igaz** értéket ad vissza, ha a várólista nem létezik, és a létrehozása sikeresen megtörtént. Ellenkező esetben a rendszer **Hamis értéket** ad vissza.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Frissítse a **ViewBag** a várólista nevével.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **CreateQueue** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `CreateQueue.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és kattintson az **üzenetsor létrehozása** elemre az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Üzenetsor létrehozása](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Ahogy azt korábban említettük, a **CloudQueue. createifnotexists metódust** metódus **igaz** értéket ad vissza, ha a várólista nem létezik, és létrejön. Ezért ha az alkalmazást akkor futtatja, amikor a várólista létezik, a metódus **hamis**értéket ad vissza. Ha többször szeretné futtatni az alkalmazást, törölnie kell a várólistát az alkalmazás újbóli futtatása előtt. A várólista törlése a **CloudQueue. Delete** metódussal végezhető el. A várólistát a [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használatával is törölheti.  

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Miután [létrehozta a várólistát](#create-a-queue), hozzáadhat üzeneteket az adott várólistához. Ez a szakasz bemutatja, hogyan adhat hozzá üzenetet egy üzenetsor *-tesztelési várólistához*. 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **AddMessage** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **AddMessage** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Szerezzen be egy, a várólistára mutató hivatkozást képviselő **CloudQueueContainer** objektumot. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hozza létre a várólistára felvenni kívánt üzenetet jelképező **CloudQueueMessage** objektumot. **CloudQueueMessage** objektum hozható létre egy sztringből (UTF-8 formátumban) vagy egy bájtos tömbből.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Hívja meg a **CloudQueue. AddMessage** metódust, és adja hozzá az üzenetet a várólistához.

    ```csharp
    queue.AddMessage(message);
    ```

1. Hozzon létre és állítson be néhány **ViewBag** -tulajdonságot a nézetben való megjelenítéshez.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **AddMessage** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `AddMessage.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza az **üzenet hozzáadása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Üzenet hozzáadása](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

A két rész – üzenet [elolvasása egy várólistából az eltávolítása nélkül](#read-a-message-from-a-queue-without-removing-it) , és egy üzenetsor [üzenetének olvasása és eltávolítása](#read-and-remove-a-message-from-a-queue) – bemutatjuk, hogyan lehet üzeneteket olvasni egy várólistából.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Üzenet elolvasása egy várólistából eltávolítás nélkül

Ez a szakasz azt szemlélteti, hogyan lehet megtekinteni egy várólistán lévő üzenetet (az első üzenet eltávolítása nélkül).  

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **PeekMessage** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **PeekMessage** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Szerezzen be egy, a várólistára mutató hivatkozást képviselő **CloudQueueContainer** objektumot. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. A **CloudQueue. PeekMessage** metódus meghívásával olvassa be a várólista első üzenetét anélkül, hogy el kellene távolítani a várólistáról. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Frissítse a **ViewBag** két értékkel: a várólista nevét és az olvasott üzenetet. A **CloudQueueMessage** objektum két tulajdonságot tesz elérhetővé az objektum értékének beolvasásához: **CloudQueueMessage. AsBytes** és **CloudQueueMessage. AsString**. A **AsString** (ebben a példában használt) karakterláncot ad vissza, míg a **AsBytes** egy bájt tömböt ad vissza.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **PeekMessage** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `PeekMessage.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

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

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és kattintson a **betekintés üzenet** elemre az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Betekintés üzenet](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Üzenet olvasása és eltávolítása egy várólistából

Ebből a szakaszból megtudhatja, hogyan olvashatja el és távolíthatja el az üzeneteket egy várólistából.   

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **ReadMessage** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **ReadMessage** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Szerezzen be egy, a várólistára mutató hivatkozást képviselő **CloudQueueContainer** objektumot. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. A várólista első üzenetének olvasásához hívja meg a **CloudQueue. GetMessage** metódust. A **CloudQueue. GetMessage** metódus az üzenetet 30 másodpercig (alapértelmezés szerint) jeleníti meg minden más kód olvasásakor, hogy más kód ne módosítsa vagy törölje az üzenetet a feldolgozás során. Az üzenet láthatatlan időtartamának módosításához módosítsa a **CloudQueue. GetMessage** metódusnak átadott **visibilityTimeout** paramétert.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Hívja meg a **CloudQueueMessage. Delete** metódust az üzenet várólistából való törléséhez.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Frissítse a **ViewBag** a törölt üzenettel és a várólista nevével.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **ReadMessage** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `ReadMessage.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

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

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és az **üzenet olvasása/törlése** elemre kattintva tekintse meg az alábbi képernyőképhez hasonló eredményeket:
  
    ![Üzenet olvasása és törlése](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Ez a szakasz bemutatja, hogyan kérhető le a várólista hossza (üzenetek száma). 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **GetQueueLength** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **ReadMessage** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Szerezzen be egy, a várólistára mutató hivatkozást képviselő **CloudQueueContainer** objektumot. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue. FetchAttributes** metódust a várólista attribútumainak beolvasásához (a hosszát is beleértve). 

    ```csharp
    queue.FetchAttributes();
    ```

1. A várólista hosszának beolvasásához nyissa meg a **CloudQueue. ApproximateMessageCount** tulajdonságot.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Frissítse a **ViewBag** a várólista nevével és annak hosszával.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **GetQueueLength** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `GetQueueLengthMessage.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **várólista-hossz beolvasása** lehetőséget az alábbi képernyőképhez hasonló eredmények megjelenítéséhez:
  
    ![Várólista hosszának beolvasása](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Üzenetsor törlése
Ez a szakasz azt szemlélteti, hogyan lehet törölni a várólistát. 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy végrehajtotta a [fejlesztési környezet beállításának](#set-up-the-development-environment)lépéseit. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **DeleteQueue** nevű metódust, amely egy **ActionResult**ad vissza.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **DeleteQueue** metódusban szerezzen be egy **CloudStorageAccount** objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában: (módosítsa *&lt;Storage-Account-name >t* az elérni kívánt Azure Storage-fiók nevére.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **CloudQueueClient** -objektum beolvasása üzenetsor-kezelési ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Szerezzen be egy, a várólistára mutató hivatkozást képviselő **CloudQueueContainer** objektumot. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue. Delete** metódust a **CloudQueue** objektum által jelölt várólista törléséhez.

    ```csharp
    queue.Delete();
    ```

1. Frissítse a **ViewBag** a várólista nevével és annak hosszával.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. A **megoldáskezelő**bontsa ki a **nézetek** mappát, kattintson a jobb gombbal a **várólisták**elemre, majd a helyi menüben válassza a **Hozzáadás – > nézet**lehetőséget.

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a **DeleteQueue** nevet a nézet neveként, majd válassza a **Hozzáadás**lehetőséget.

1. Nyissa meg `DeleteQueue.cshtml`, és módosítsa úgy, hogy az a következő kódrészlethez hasonlítson:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. A **megoldáskezelő**bontsa ki a **nézetek-> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó **HTML. ActionLink**után adja hozzá a következő **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **várólista-hossz beolvasása** lehetőséget az alábbi képernyőképhez hasonló eredmények megjelenítéséhez:
  
    ![Üzenetsor törlése](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Következő lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure Table Storage és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
