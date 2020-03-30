---
title: Azure Queue storage-műveletek végrehajtása a PowerShellben
description: Műveletek végrehajtása az Azure Queue storage-ban a PowerShell használatával
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: bd2f372bdcb949b64f748d186a9b060bb9cbec4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087067"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure Queue Storage-műveletek végrehajtása az Azure PowerShell-lel

Az Azure Queue storage olyan szolgáltatás, amely a világ bármely pontjáról http-n vagy HTTPS-en keresztül elérhető üzenetek nagy számának tárolására szolgál. Részletes információt az [Azure-várólisták bemutatása című témakörben talál.](storage-queues-introduction.md) Ez az útmutató cikk a várólista tárolási gyakori műveleteit ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * Várólista beolvasása
> * Üzenet hozzáadása
> * Üzenet elolvasása
> * Üzenet törlése
> * Üzenetsor törlése

Ehhez az útmutatóhoz az Azure PowerShell-modul, az Az 0.7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Nincsenek PowerShell-parancsmagok a várólisták adatsíkhoz. Adatsík-műveletek, például üzenet hozzáadása, elolvasása és üzenet törlése érdekében a .NET tárolóügyfél-kódtárat kell használnia, mivel az elérhető a PowerShellben. Létrehoz egy üzenetobjektumot, majd az AddMessage parancsokkal műveleteket hajthat végre az üzeneten. Ez a cikk bemutatja, hogyan kell ezt csinálni.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának lekérése

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ez a gyakorlat az **eastus programot**fogja használni. Tárolja ezt a változó **helyen** későbbi használatra.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevét egy változóban tárolhatja későbbi használatra. Ebben a példában egy *howtoqueuesrg* nevű erőforráscsoport jön létre az *eastus* régióban.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy általános célú szabványos tárfiókot helyileg redundáns tárral (LRS) a [New-AzStorageAccount használatával.](/powershell/module/az.storage/New-azStorageAccount) A tárfiók környezetének beszereznie, amely meghatározza a használandó tárfiókot. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A következő példa először létrehoz egy kapcsolatot az Azure Storage-hoz a tárfiók környezethasználatával, amely tartalmazza a tárfiók nevét és a hozzáférési kulcsot. Ezután meghívja a [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) parancsmast egy "howtoqueue" nevű várólista létrehozásához.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Az Azure Queue Service elnevezési konvencióiról az [Elnevezési várólisták és metaadatok című](https://msdn.microsoft.com/library/azure/dd179349.aspx)témakörben talál további információt.

## <a name="retrieve-a-queue"></a>Várólista beolvasása

Lekérdezheti és lekérheti egy adott várólistát, vagy egy storage-fiók összes várólistájának listáját. Az alábbi példák bemutatják, hogyan lehet beolvasni a tárfiók összes várólistáját és egy adott várólistát; mindkét parancs a [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) parancsmagját használja.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása várólistához

A várólistában lévő tényleges üzeneteket befolyásoló műveletek a .NET storage-ügyfélkódtárban vannak elérhetővé téve a PowerShellben. Üzenet várólistához való hozzáadásához hozzon létre egy új példányt a [Microsoft.Azure.Storage.Queue.CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) osztályból. Ezután hívja meg az [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage) módszert. A CloudQueueMessage egy sztringből (UTF-8 formátumban) vagy egy bájttömbből hozható létre.

A következő példa bemutatja, hogyan adhat hozzá üzenetet a várólistához.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Ha az [Azure Storage Explorer](https://storageexplorer.com)használatával csatlakozik az Azure-fiókjához, és megtekintheti a tárfiók várólistáit, és leáshat egy várólistába a várólistán lévő üzenetek megtekintéséhez.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Üzenet olvasása a várólistából, majd törlés

Az üzenetek olvasása a legjobb első-in-first-out sorrendben. Ez nem garantált. Amikor elolvassa az üzenetet a várólistából, láthatatlanná válik az összes többi folyamat számára, amely a várólistát nézi. Ez biztosítja, hogy ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya megkapja ugyanazt az üzenetet, és próbálkozzon újra.  

Ez **a láthatatlansági időtúltávolság** határozza meg, hogy az üzenet mennyi ideig marad láthatatlan, mielőtt újra elérhetővé válik feldolgozásra. Az alapértelmezett érték 30 mp.

A kód két lépésben olvas be egy üzenetet a várólistából. A [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) metódus hívásakor a következő üzenet jelenik meg a várólistában. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Az üzenet várólistából való eltávolításának befejezéséhez hívja meg a [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metódust.

A következő példában végigolvassa a három várólista-üzenetet, majd várjon 10 másodpercet (a láthatatlanság időtúlára). Ezután újra elolvasta a három üzenetet, és a **DeleteMessage**hívásával törölte az üzeneteket, miután elolvasta őket. Ha az üzenetek törlése után megpróbálja elolvasni a várólistát, $queueMessage null értékűlesz.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Egy várólista és a benne lévő összes üzenet törléséhez hívja meg az Remove-AzStorageQueue parancsmac-ot. A következő példa bemutatja, hogyan törölheti az adott várólista ebben a gyakorlatban az Remove-AzStorageQueue parancsmag használatával.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyakorlatban létrehozott összes eszköz eltávolításához távolítsa el az erőforráscsoportot. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatócikkben megismerkedett az alapvető várólista-tárolási felügyelettel a PowerShell használatával, többek között a következőkkel:

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * Várólista beolvasása
> * Üzenet hozzáadása
> * A következő üzenet elolvasása
> * Üzenet törlése
> * Üzenetsor törlése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-parancsmagok

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
