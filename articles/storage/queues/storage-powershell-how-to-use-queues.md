---
title: Azure üzenetsor-tárolási műveletek végrehajtása a PowerShellben
description: Műveleteket hajthat végre az Azure üzenetsor-tárolóban a PowerShell használatával. Az Azure üzenetsor-tárolóval nagy számú, HTTP/HTTPS által elérhető üzenetet tárolhat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f45a4de2e13f936556f8dd99aa107110edc6e91
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077925"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure Queue Storage-műveletek végrehajtása az Azure PowerShell-lel

Az Azure üzenetsor-tároló egy olyan szolgáltatás, amely a világ bármely pontjáról HTTP vagy HTTPS használatával elérhető, nagy mennyiségű üzenetet tárol. Részletes információ: [Bevezetés az Azure Queues](storage-queues-introduction.md)szolgáltatásba. Ez a cikk a közös üzenetsor-tárolási műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * Üzenetsor beolvasása
> * Üzenet hozzáadása
> * Üzenet elolvasása
> * Üzenet törlése
> * Üzenetsor törlése

Ez a útmutató a Azure PowerShell modul az 0,7-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

A várólisták adatsíkja nem tartalmaz PowerShell-parancsmagokat. Az adatsík-műveletek, például az üzenetek hozzáadása, az üzenet olvasása és az üzenet törlése után a .NET Storage ügyféloldali kódtárat kell használnia, mivel az elérhető a PowerShellben. Hozzon létre egy üzenet objektumot, majd a AddMessage parancsok használatával hajtsa végre a műveleteket az üzeneten. Ez a cikk bemutatja, hogyan teheti meg.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyszínek listájának beolvasása

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ez a gyakorlat a **eastus**-t fogja használni. Ezt a változót a későbbi használat érdekében a változó **helyén** tárolja.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevét egy változóban tárolja későbbi használatra. Ebben a példában egy *howtoqueuesrg* nevű erőforráscsoportot hoznak létre a *eastus* régióban.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy szabványos általános célú Storage-fiókot a helyileg redundáns tárolással (LRS) a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)használatával. Szerezze be a használni kívánt Storage-fiókot definiáló Storage-fiók környezetét. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Az alábbi példa először kapcsolatot létesít az Azure Storage-val a Storage-fiók környezetével, amely magában foglalja a Storage-fiók nevét és a hozzá tartozó hozzáférési kulcsot. Ezután meghívja a [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) parancsmagot egy "howtoqueue" nevű várólista létrehozásához.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

További információ az Azure Queue szolgáltatás elnevezési szabályairól: [várólisták és metaadatok elnevezése](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Üzenetsor beolvasása

Lekérdezheti és lekérhet egy adott várólistát vagy egy Storage-fiókban lévő összes várólistát tartalmazó listát. Az alábbi példák azt mutatják be, hogyan kérhető le az összes várólista a Storage-fiókban, valamint egy adott várólista; mindkét parancs a [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) parancsmagot használja.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Az üzenetsor tényleges üzeneteit befolyásoló műveletek a .NET Storage ügyféloldali kódtárat használják a PowerShellben elérhetőként. Ha üzenetet szeretne hozzáadni egy várólistához, hozzon létre egy új példányt a [Microsoft. Azure. Storage. üzenetsor. CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) osztályhoz. Ezután hívja meg az [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) módszert. A CloudQueueMessage egy sztringből (UTF-8 formátumban) vagy egy bájttömbből hozható létre.

Az alábbi példa bemutatja, hogyan adhat hozzá egy üzenetet a várólistához.

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

Ha a [Azure Storage Explorer](https://storageexplorer.com)használja, kapcsolódhat az Azure-fiókjához, és megtekintheti a Storage-fiókban lévő várólistákat, és részletesen megtekintheti a várólistán lévő üzeneteket.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Üzenet olvasása a sorból, majd a törlés

Az üzenetek beolvasása a legjobb – első kijelentkezési sorrendben. Ez nem garantált. Ha elolvassa az üzenetet a várólistából, a rendszer láthatatlanná válik a várólistában megtekintett összes többi folyamatnál. Ez biztosítja, hogy ha a kód hardver vagy szoftver meghibásodása miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra.  

Ez a **láthatósági időkorlát** azt határozza meg, hogy az üzenet hányszor maradjon láthatatlan, mielőtt újra elérhetővé válik a feldolgozáshoz. Az alapértelmezett érték 30 mp.

A kód két lépésben beolvas egy üzenetet a várólistából. A [Microsoft. Azure. Storage. üzenetsor. CloudQueue. GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) metódus meghívásakor a következő üzenet jelenik meg a várólistán. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Az üzenet üzenetsorből való eltávolításának befejezéséhez hívja meg a [Microsoft. Azure. Storage. üzenetsor. CloudQueue. DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metódust.

Az alábbi példában elolvashatja a három üzenetsor-üzenetet, majd várjon 10 másodpercet (a láthatatlanság időtúllépését). Ezután olvassa el újra a három üzenetet, és törölje az üzeneteket a **DeleteMessage**meghívásával. Ha az üzenetek törlése után megpróbálja beolvasni a várólistát, $queueMessage a rendszer NULL értéket ad vissza.

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

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a Remove-AzStorageQueue parancsmagot. Az alábbi példa bemutatja, hogyan törölheti a gyakorlatban használt adott várólistát a Remove-AzStorageQueue parancsmag használatával.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyakorlatban létrehozott összes eszköz eltávolításához távolítsa el az erőforráscsoportot. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott Storage-fiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta az alapszintű üzenetsor-tárolás kezelését a PowerShell használatával, beleértve a következőket:

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * Üzenetsor beolvasása
> * Üzenet hozzáadása
> * A következő üzenet elolvasása
> * Üzenet törlése
> * Üzenetsor törlése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-parancsmagok

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
