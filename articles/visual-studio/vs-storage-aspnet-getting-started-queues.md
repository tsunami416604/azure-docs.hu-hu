---
title: Ismerkedés az Azure várólista-tárterülettel a Visual Studio használatával (ASP.NET)
description: Az Azure-várólista-tárolás használatának első lépései egy ASP.NET projektben a Visual Studióban, miután a Visual Studio Connected Services használatával egy tárfiókhoz csatlakoztak
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980759"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Ismerkedés az Azure várólista-tárolásával és a Visual Studio Connected Services szolgáltatással (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Az Azure-várólista-tárolás felhőalapú üzenetküldést biztosít az alkalmazás-összetevők között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Ez az oktatóanyag bemutatja, hogyan írhat ASP.NET kódot néhány gyakori forgatókönyvhez az Azure-várólista-tároló entitások használatával. Ezek a forgatókönyvek közé tartozik a gyakori feladatok, például egy Azure-várólista létrehozása, és hozzáadása, módosítása, olvasása és eltávolítása várólista-üzenetek.

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure tárfiók](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC-vezérlő létrehozása 

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Vezérlők parancsra,** és a helyi menüben válassza a **Hozzáadás >vezérlő parancsot.**

    ![Vezérlő hozzáadása ASP.NET MVC-alkalmazáshoz](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Az **Állvány hozzáadása** párbeszédpanelen válassza az **MVC 5 vezérlő – Üres**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![MVC-vezérlő típusának megadása](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. A **Vezérlő hozzáadása** párbeszédpanelen nevezze el a Controller *QueuesController*nevet, és válassza **a Hozzáadás**lehetőséget.

    ![Az MVC-vezérlő elnevezése](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adja hozzá a következő `QueuesController.cs` *irányelveket* a fájlhoz:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Üzenetsor létrehozása

A következő lépések bemutatják, hogyan hozhat létre várólistát:

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt. 

1. Adjon hozzá egy **CreateQueue** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. A **CreateQueue** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Beszerezni egy **CloudQueue** objektumot, amely a kívánt várólista nevére mutató hivatkozást jelöli. A **CloudQueueClient.GetQueueReference** metódus nem kér kérést a várólista-tárolóra. A hivatkozás attól függetlenül megjelenik, hogy a várólista létezik-e. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue.CreateIfNotExists** metódust a várólista létrehozásához, ha még nem létezik. A **CloudQueue.CreateIfNotExists** metódus **igaz** értéket ad vissza, ha a várólista nem létezik, és sikeresen létrehozva. Ellenkező esetben **a rendszer hamis** értéket ad vissza.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Frissítse a **ViewBag-ot** a várólista nevével.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a nézet nevéhez a **CreateQueue** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `CreateQueue.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **Várólista létrehozása lehetőséget** az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Várólista létrehozása](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Mint korábban említettük, a **CloudQueue.CreateIfNotExists** metódus csak akkor ad vissza **igaz,** ha a várólista nem létezik, és létrejön. Ezért ha az alkalmazást akkor futtatja, amikor a várólista létezik, a metódus **hamis**értéket ad vissza. Az alkalmazás többszöri futtatásához törölnie kell a várólistát, mielőtt újra futtatna az alkalmazást. A várólista törlése a **CloudQueue.Delete** metódussal végezhető el. A várólista az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040) vagy a Microsoft Azure Storage Explorerben is törölheti a [várólistát.](../vs-azure-tools-storage-manage-with-storage-explorer.md)  

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása várólistához

Miután [létrehozott egy várólistát,](#create-a-queue)üzeneteket adhat a várólistához. Ez a szakasz végigvezeti egy üzenet hozzáadásán a várólista *tesztvárólistáihoz.* 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **AddMessage** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Az **AddMessage** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. A várólistára mutató hivatkozást jelző **CloudQueueContainer** objektum bekésése. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hozza létre a várólistához hozzáadni kívánt üzenetet képviselő **CloudQueueMessage** objektumot. A **CloudQueueMessage** objektum létrehozható karakterláncból (UTF-8 formátumban) vagy bájttömbből.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Hívja meg a **CloudQueue.AddMessage** metódust az üzenet várólistához való hozzáadásához.

    ```csharp
    queue.AddMessage(message);
    ```

1. Hozzon létre és állítson be néhány **ViewBag** tulajdonságot a nézetben való megjelenítéshez.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be az **AddMessage** parancsot a nézet nevéhez, és válassza a **Hozzáadás gombot.**

1. Nyissa `AddMessage.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és az **üzenet hozzáadása gombra** a következő képernyőképhez hasonló eredmények megtekintéséhez válassza az üzenet hozzáadása lehetőséget:
  
    ![Üzenet hozzáadása](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

A két szakasz – [Üzenet olvasása a várólistából anélkül, hogy eltávolítaná,](#read-a-message-from-a-queue-without-removing-it) és [olvassa el és távolítsa el az üzenetet a várólistából](#read-and-remove-a-message-from-a-queue) - bemutatja, hogyan lehet üzeneteket olvasni egy várólistából.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Üzenet olvasása várólistából eltávolítás nélkül

Ez a szakasz bemutatja, hogyan lehet betekinteni egy sorba állított üzenetbe (az első üzenetet eltávolítás nélkül olvassa el).  

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **PeekMessage** nevű metódust, amely **ActionResult eredményt**ad vissza.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **PeekMessage** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. A várólistára mutató hivatkozást jelző **CloudQueueContainer** objektum bekésése. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue.PeekMessage** metódust, hogy olvassa el az első üzenetet a várólistában anélkül, hogy eltávolítaná azt a várólistából. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Frissítse a **ViewBag-ot** két értékkel: a várólista nevével és az olvasott üzenettel. A **CloudQueueMessage** objektum két tulajdonságot tár fel az objektum értékének megszerzéséhez: **CloudQueueMessage.AsBytes** és **CloudQueueMessage.AsString**. **Az AsString** (ebben a példában használatos) egy karakterláncot ad vissza, míg **az AsBytes** egy bájttömböt ad vissza.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a **PeekMessage** parancsot a nézet nevéhez, és válassza a **Hozzáadás gombot.**

1. Nyissa `PeekMessage.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és a **Betekintés üzenet** lehetőséget választva az alábbi képernyőképhez hasonló eredményeket szeretne látni:
  
    ![Betekintő üzenet](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Üzenet olvasása és eltávolítása a várólistából

Ebben a szakaszban megtudhatja, hogyan olvashat el és távolíthat el egy üzenetet a várólistából.   

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **ReadMessage** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **ReadMessage** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. A várólistára mutató hivatkozást jelző **CloudQueueContainer** objektum bekésése. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue.GetMessage** metódust a várólista első üzenetének olvasásához. A **CloudQueue.GetMessage** metódus az üzenetet 30 másodpercig láthatatlanná teszi bármely más kódolvasó üzenet számára, így más kód nem módosíthatja vagy törölheti az üzenetet a feldolgozás során. Ha módosítani szeretné, hogy az üzenet mennyi ideig legyen láthatatlan, módosítsa a **cloudTimeout** paraméterát a **CloudQueue.GetMessage** metódusnak.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Hívja meg a **CloudQueueMessage.Delete** metódust az üzenet várólistából való törléséhez.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Frissítse a **ViewBag-ot** úgy, hogy az üzenet törlődik, és a várólista neve.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez a **ReadMessage** értéket, és válassza a **Hozzáadás gombot.**

1. Nyissa `ReadMessage.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

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

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza **az Üzenet olvasása/törlése** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Üzenet olvasása és törlése](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Ez a szakasz bemutatja, hogyan lehet letenni a várólista hosszát (az üzenetek számát). 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **GetQueueLength** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **ReadMessage** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. A várólistára mutató hivatkozást jelző **CloudQueueContainer** objektum bekésése. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue.FetchAttributes** metódust a várólista attribútumainak (beleértve a hosszát is) beolvasásához. 

    ```csharp
    queue.FetchAttributes();
    ```

1. A **CloudQueue.ApproximateMessageCount** tulajdonság elérése a várólista hosszának leérkezéséhez.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Frissítse a **ViewBag-ot** a várólista nevével és hosszával.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen adja meg a nézet nevéhez a **GetQueueLength** értéket, és válassza a **Hozzáadás gombot.**

1. Nyissa `GetQueueLengthMessage.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **Várólista hosszának lefutása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Várólista hosszának beszereznie](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Üzenetsor törlése
Ez a szakasz a várólista törlését mutatja be. 

> [!NOTE]
> 
> Ez a szakasz feltételezi, hogy elvégezte a [fejlesztési környezet beállítása című](#set-up-the-development-environment)lépéseket. 

1. Nyissa meg az `QueuesController.cs` fájlt.

1. Adjon hozzá egy **DeleteQueue** nevű metódust, amely **ActionResult**eredményt ad vissza.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. A **DeleteQueue** metóduson belül szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával leszeretné szerezni a tárolási kapcsolat ihpedig a tárfiók adatait az Azure-szolgáltatás konfigurációjából: (A * &lt;tárfiók nevének módosítása>* az Azure-tárfiók nevére,)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. A **CloudQueueClient** objektum bekésése egy várólista-szolgáltatás ügyfelet jelöl.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. A várólistára mutató hivatkozást jelző **CloudQueueContainer** objektum bekésése. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Hívja meg a **CloudQueue.Delete** metódust a **CloudQueue** objektum által képviselt várólista törléséhez.

    ```csharp
    queue.Delete();
    ```

1. Frissítse a **ViewBag-ot** a várólista nevével és hosszával.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. A **Megoldáskezelőben**bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal a **Várólisták parancsra,** és a helyi menüben válassza a **Hozzáadás >nézet parancsot.**

1. A **Nézet hozzáadása** párbeszédpanelen írja be a Nézet nevéhez a **DeleteQueue** parancsot, és válassza a **Hozzáadás gombot.**

1. Nyissa `DeleteQueue.cshtml`meg a megnyitását, és módosítsa úgy, hogy a következő kódrészletnek tűnjön:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. A **Megoldáskezelőben**bontsa ki a Megosztott `_Layout.cshtml`nézetek **>** mappát, és nyissa meg a programot.

1. Miután az utolsó **Html.ActionLink**, add hozzá a következő **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Futtassa az alkalmazást, és válassza a **Várólista hosszának lefutása** lehetőséget az alábbi képernyőképhez hasonló eredmények megtekintéséhez:
  
    ![Várólista törlése](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>További lépések
Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.

  * [Ismerkedés az Azure blob storage-szal és a Visual Studio Connected Services szolgáltatással (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Ismerkedés az Azure table storage és a Visual Studio Connected Services használatával (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
