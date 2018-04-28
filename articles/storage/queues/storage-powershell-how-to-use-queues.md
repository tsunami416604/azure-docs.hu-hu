---
title: Azure Queue storage PowerShell műveleteket |} Microsoft Docs
description: Hogyan hajthat végre műveleteket a Azure Queue storage a PowerShell használatával
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: bad9f1f3fd5737e865a8f4d1d15ab3d5eb68b4cb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure Queue storage műveleteket az Azure PowerShell

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Részletes információkért lásd: [Bevezetés az Azure várólisták](storage-queues-introduction.md). A cikkben található útmutató közös várólista tárolási műveletet ismertet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Üzenetsor létrehozása
> * A várólista beolvasása
> * Egy üzenet hozzáadása
> * Egy üzenet olvasása
> * Üzenetek törlése 
> * Üzenetsor törlése

Ez az útmutató az Azure PowerShell 3,6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Nincsenek az adatok vezérlősík várólisták nem PowerShell-parancsmagok. Adatok végrehajtásához vezérlősík műveletek, mint hozzáadása egy üzenet olvashatja az üzeneteket, és üzenet törléséhez rendelkeznie ahhoz, hogy ki van téve a PowerShellben használható a .NET a storage ügyféloldali kódtára. Message objektumot hoz létre, és majd parancsok például AddMessage használhatja ezt az üzenetet műveletek végrehajtásához. Ez a cikk bemutatja, hogyan ehhez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának beolvasása

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ebben a gyakorlatban használandó **eastus**. Ez a változó tárolja **hely** későbbi használatra.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport neve tárolható egy változóban későbbi használatra. Ebben a példában az erőforráscsoport neve *howtoqueuesrg* jön létre a *eastus* régióban.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy szabványos általános célú tárfiók helyileg redundáns tárolás (LRS) használatával [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A tárfiók környezetét, amely definiálja a tárfiókot használni beolvasása. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Az alábbi példa első Azure Storage használata a tárfiók környezetét, amely tartalmazza a tárfiók nevét és a hozzáférési kulcsot kapcsolatot létesít. Ezután meghívja [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) parancsmag segítségével hozzon létre egy "queuename" nevű várólistát.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Azure Queue szolgáltatás elnevezési konvencióira vonatkozó információkért lásd: [elnevezési üzenetsorok és metaadatok](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>A várólista beolvasása

Lekérdezi, és egy konkrét várólistába helyezi vagy a tárfiókban lévő összes várólistán listájának beolvasása. Az alábbi példák bemutatják, hogyan lehet lekérni a tárfiókban lévő összes várólisták, és egy konkrét várólistába helyezi; mindkét a parancsok a [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) parancsmag.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>A várólista üzenet hozzáadása

A tényleges üzenetet a várólistában befolyásoló műveleteket a .NET a storage ügyféloldali kódtára PowerShell felfedett használja. Üzenet hozzáadása egy üzenetsort, hozzon létre egy új példányát message objektumot [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) osztály. Ezután hívja meg az [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) módszert. Egy CloudQueueMessage egy karakterláncból (UTF-8 formátumban), illetve egy bájttömböt hozhatók létre.

A következő példa bemutatja, hogyan adja hozzá a sor üzenetet.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Ha használja a [Azure Tártallózó](http://storageexplorer.com), csatlakozzon az Azure-fiókjával és a várólisták tekintse meg a tárfiók, és részletekbe menően tárhatják fel az üzeneteket megtekintheti a várólista várólista. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Olvassa el egy üzenetet az üzenetsorból, majd törölje

Üzenetek legjobb-próbálja első-first out sorrendben olvasható. Nem garantált. Az üzenet olvasása az üzenetsorból, hogy láthatóvá váljon a megnézi a várólista minden más folyamatokkal. Ez biztosítja, hogy a kód nem tudja feldolgozni az üzenetet, hardver- vagy szoftverhiba miatt, ha a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálja meg újból.  

Ez **láthatatlansági időkorlátot** határozza meg, mennyi ideig az üzenet marad láthatatlan előtt ismét feldolgozási érhető el. Az alapértelmezett érték 30 másodperc. 

A kód két lépésben a várólista olvassa be az üzenetet. A hívás esetén a [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metódus, a következő üzenetet kap a várólistán. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Szeretné távolítani az üzenetet az üzenetsorból, hívja az [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metódust. 

A következő példában a olvassa végig a három üzenetsor-üzeneteket, majd várjon 10 másodpercet, amíg (a láthatatlansági időkorlátot). Olvassa el a három üzenetek újra, az üzenetek törlése után olvasásra meghívásával, majd **DeleteMessage**. Ha megpróbálja után az üzenetek törlődnek, olvassa el a várólista, $queueMessage változatlanul adódik vissza NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a Remove-AzureStorageQueue parancsmagot. A következő példa bemutatja, hogyan ebben a gyakorlatban a Remove-AzureStorageQueue parancsmaggal a megadott várólista törlése.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az eszközök, ebben a gyakorlatban létrehozott összes eltávolításához távolítsa el az erőforráscsoportot. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókban és az erőforráscsoport magát.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Útmutató cikkben megtanulta, kapcsolatos alapvető várólista tárolók kezelése a PowerShell-lel, beleértve a hogyan:

> [!div class="checklist"]
> * Üzenetsor létrehozása
> * A várólista beolvasása
> * Egy üzenet hozzáadása
> * Olvassa el a következő üzenetet
> * Üzenetek törlése 
> * Üzenetsor törlése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>A Microsoft Azure PowerShell tárolási parancsmagok
* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
