---
title: Az Azure queue storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET) használatának első lépései |} A Microsoft Docs
description: Ismerkedés az Azure queue storage használata az ASP.NET-projektben a Visual Studióban egy storage-fiók használata a Visual Studio csatlakoztatott szolgáltatásain való csatlakozást követően
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 5234f5c82e98fcb402cadf9a8a469a15bbb7ac6d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250815"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure queue storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Az Azure queue storage felhőbeli üzenetkezelést alkalmazás-összetevők közötti biztosít. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Ez az oktatóanyag bemutatja, hogyan írhat kódot ASP.NET az Azure queue storage entitások használata néhány gyakori forgatókönyvet. Ezek a forgatókönyvek között megtalálható a gyakori feladatokat, mint létrehozása az Azure-üzenetsort, és hozzáadása, módosítása, olvasó és üzenetsorbeli üzenetek eltávolítása.

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-fiók](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Hozzon létre egy MVC-vezérlő 

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **tartományvezérlők**, és a helyi menüből válassza az **Hozzáadás -> vezérlő**.

    ![Vezérlő hozzáadása egy ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. A a **hozzáadása Scaffold** párbeszédpanelen válassza **MVC 5 vezérlő - üres**, és válassza ki **Hozzáadás**.

    ![Adja meg az MVC-vezérlő típusa](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Az a **vezérlő hozzáadása** párbeszédpanelen adja a vezérlő *QueuesController*, és válassza ki **Hozzáadás**.

    ![Az MVC-vezérlő neve](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adja hozzá a következő *használatával* irányelveket a `QueuesController.cs` fájlt:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Üzenetsor létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy várólista:

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt. 

1. Adjon meg egy metódust nevű **CreateQueue** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Belül a **CreateQueue** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Get- **CloudQueue** egy hivatkozást a kívánt üzenetsor neve képviselő objektum. A **CloudQueueClient.GetQueueReference** metódus nem derül egy kérelmet a queue storage. A referencia-e a várólista létezik adja vissza. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.CreateIfNotExists** metódust hozzon létre egy üzenetsort, ha még nem található. A **CloudQueue.CreateIfNotExists** metódus visszatért **igaz** Ha az üzenetsor nem létezik, és sikeresen létrehozva. Ellenkező esetben **hamis** adja vissza.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Frissítés a **ViewBag** az üzenetsor nevére.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **CreateQueue** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `CreateQueue.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **üzenetsor létrehozása** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Üzenetsor létrehozása](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Ahogy korábban említettük a **CloudQueue.CreateIfNotExists** metódus visszatért **igaz** csak az üzenetsor nem létezik a és jön létre. Ezért, ha az alkalmazás futtatása során a várólista létezik, a metódus adja vissza **hamis**. Az alkalmazás futtatásához többször, az alkalmazás újbóli futtatása előtt kell törölnie a várólistát. A várólista törlése keresztül lehetséges az **CloudQueue.Delete** metódust. Az üzenetsor használatával is törölhet az [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adjon meg egy üzenetet egy üzenetsorba

Miután [létrehozott egy üzenetsort](#create-a-queue), üzenetet az üzenetsornak is hozzáadhat. Ez a szakasz végigvezeti a felvesz egy üzenetet egy üzenetsorba *test-várólista*. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon meg egy metódust nevű **AddMessage** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **AddMessage** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Get- **CloudQueueContainer** egy hivatkozást a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hozzon létre a **CloudQueueMessage** az üzenetet az üzenetsorba hozzáadni kívánt képviselő objektum. A **CloudQueueMessage** objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Hívja a **CloudQueue.AddMessage** metódus hozzáadása a messaged az üzenetsorba.

    ```csharp
    queue.AddMessage(message);
    ```

1. Hozzon létre, és állítsa be néhány **ViewBag** a nézet tulajdonságait.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **AddMessage** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `AddMessage.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **üzenet hozzáadása** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Üzenet hozzáadása](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

A két szakasz - [üzenet olvasása az üzenetsorból nélkül lehet inaktiválni](#read-a-message-from-a-queue-without-removing-it) és [olvasási és a egy üzenetet az üzenetsorból remove](#read-and-remove-a-message-from-a-queue) -bemutatják, hogyan üzenetek olvasása az üzenetsorból.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Üzenet olvasása az üzenetsorból nélkül lehet inaktiválni

Ez a szakasz bemutatja, hogyan betekintés az üzenetsorban található üzenet (olvassa el az első üzenet eltávolítása nélkül).  

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon meg egy metódust nevű **PeekMessage** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **PeekMessage** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Get- **CloudQueueContainer** egy hivatkozást a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.PeekMessage** módszer, olvassa el a várólista első üzenetébe anélkül, hogy eltávolítaná az üzenetsorból. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Frissítés a **ViewBag** a két érték: a várólista nevét és az üzenetet, amely lett beolvasva. A **CloudQueueMessage** objektum esetében az objektum érték első két tulajdonságok közzététele: **CloudQueueMessage.AsBytes** és **CloudQueueMessage.AsString**. **AsString** (ebben a példában használt) egy karakterláncot ad vissza, miközben **AsBytes** Bájttömbbel adja vissza.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **PeekMessage** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `PeekMessage.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **Peek message** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Belepillantás üzenetbe](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Olvassa el, és távolítsa el az üzenetet az üzenetsorból

Ebben a szakaszban megismerheti, hogyan olvassa el, és távolítsa el az üzenetet egy üzenetsorból.   

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon meg egy metódust nevű **ReadMessage** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **ReadMessage** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Get- **CloudQueueContainer** egy hivatkozást a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.GetMessage** módszer, olvassa el a várólista első üzenetébe. A **CloudQueue.GetMessage** módszer lehetővé teszi az üzenet nem látható (alapértelmezés szerint), hogy nincs más kód módosíthatja vagy törölheti az üzenet feldolgozásakor a, az üzenetek olvasásához többi kód 30 másodpercig. Az üzenet nem látható idő megváltoztatásához módosítsa a **visibilityTimeout** paramétert, hogy a rendszer a **CloudQueue.GetMessage** metódust.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Hívja a **CloudQueueMessage.Delete** metódus használatával törölheti az üzenetet az üzenetsorból.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Frissítés a **ViewBag** törli az üzenetet, és az üzenetsor nevére.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **ReadMessage** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `ReadMessage.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

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

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **olvasása/törlése üzenet** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Olvassa el, és az üzenet törlése](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Ez a szakasz mutatja be, hogyan tehet szert az üzenetsor hossza (üzenetek száma). 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon meg egy metódust nevű **GetQueueLength** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **ReadMessage** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Get- **CloudQueueContainer** egy hivatkozást a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.FetchAttributes** metódussal lehet bekérni az üzenetsor-attribútumok (beleértve a hossza). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Hozzáférés a **CloudQueue.ApproximateMessageCount** tulajdonság az üzenetsor hosszának lekérése.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Frissítés a **ViewBag** a nevét, valamint az üzenetsor hossza.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **GetQueueLength** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `GetQueueLengthMessage.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **üzenetsor hosszának lekérése** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Első sor hossza](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Üzenetsor törlése
Ez a szakasz bemutatja, hogyan várólistát töröl. 

> [!NOTE]
> 
> Ez a szakasz azt feltételezi, hogy végrehajtotta a lépéseket [a fejlesztési környezet beállítása](#set-up-the-development-environment). 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon meg egy metódust nevű **DeleteQueue** , amely adja vissza egy **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Belül a **DeleteQueue** metódus első egy **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure szolgáltatás konfigurációja a következő kód használatával: (módosítása  *&lt;storage-fiók-neve >* Ön az Azure storage-fiók neve fér hozzá.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Get- **CloudQueueClient** objektum egy várólista szolgáltatásügyfél jelöli.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Get- **CloudQueueContainer** egy hivatkozást a várólista képviselő objektum. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja a **CloudQueue.Delete** metódus használatával törölheti az üzenetsor által képviselt a **CloudQueue** objektum.

    ```csharp
    queue.Delete();
    ```

1. Frissítés a **ViewBag** a nevét, valamint az üzenetsor hossza.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. A a **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal **üzenetsorok**, és a helyi menüből válassza ki a **Hozzáadás -> nézet**.

1. Az a **nézet hozzáadása** párbeszédpanelen adja meg **DeleteQueue** a nézet nevét, és válassza a **Hozzáadás**.

1. Nyissa meg `DeleteQueue.cshtml`, és módosítsa, hogy a következő kódrészletet hasonlóan néz ki:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Az a **Megoldáskezelőben**, bontsa ki a **Nézet -> megosztott** mappát, és nyissa meg `_Layout.cshtml`.

1. Az utolsó után **Html.ActionLink**, adja hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza ki **üzenetsor hosszának lekérése** eredmények az alábbi képernyőfelvételhez hasonló megtekintéséhez:
  
    ![Üzenetsor törlése](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure table storage és a Visual Studio csatlakoztatott szolgáltatások (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
