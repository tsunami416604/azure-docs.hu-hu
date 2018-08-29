---
title: Műveletek végrehajtása az Azure Queue storage, a PowerShell-lel |} A Microsoft Docs
description: Hogyan hajthat végre műveleteket a PowerShell-lel az Azure Queue storage
services: storage
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2017
ms.author: rogarana
ms.component: queues
ms.openlocfilehash: 2f347ef0bd53a8d148a56d8431e004e9e88894d8
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128571"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Az Azure Queue storage műveleteket az Azure PowerShell használatával

Az Azure Queue storage szolgáltatás üzeneteket, amelyek elérhetők bárhol a világon, HTTP vagy HTTPS használatával nagy számú tárolásához. Részletes információkért lásd: [Azure-üzenetsorok bemutatása](storage-queues-introduction.md). A cikkben található útmutató ismerteti a közös Queue storage műveletek. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Üzenetsor létrehozása
> * Egy üzenetsor beolvasása
> * Üzenet hozzáadása
> * Üzenet olvasása
> * Üzenet törlése 
> * Üzenetsor törlése

Ebben az útmutatóban az Azure PowerShell modul 3.6-os vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Nincsenek nem PowerShell-parancsmagok az adatsík sorra. Adatok végrehajtásához síkjával végzett műveletek például adjon meg egy üzenetet, olvassa el egy üzenetet, és üzenet törlése, szükség van a storage .NET ügyféloldali kódtár, az ki van téve a PowerShellben. Üzenet objektumot hoz létre, és ezután használja a parancsok, például az AddMessage műveletek végrehajtása az üzenetet. Ez a cikk bemutatja, hogyan valósítható meg.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Helyek listájának lekérése

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ebben a gyakorlatban fogja használni **eastus**. Ez a változó Store **hely** későbbi használatra.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevének Store későbbi használat céljából egy változóban. Ebben a példában egy erőforráscsoportot nevű *howtoqueuesrg* jön létre a *eastus* régióban.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Helyileg redundáns tárolás (LRS) használó általános célú standard szintű storage-fiók létrehozása [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A tárfiók környezetét, amely meghatározza a használandó tárfiókot beolvasása. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Az alábbi példában először kapcsolatot hoz létre az Azure Storage a tárfiók környezetét, amely tartalmazza a tárfiók nevét és a hozzáférési kulcs használatával. Ezután meghívja [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) parancsmaggal hozzon létre egy "queuename" nevű üzenetsor.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Információ az elnevezési konvenciók Azure Queue szolgáltatás: [elnevezési üzenetsorok és metaadatok](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Egy üzenetsor beolvasása

Lekérdezés és a egy konkrét várólistába vagy a Storage-fiókban lévő összes üzenetsor listájának lekéréséhez. Az alábbi példák bemutatják, hogyan kérheti le a storage-fiókban lévő összes üzenetsor, és a egy konkrét várólistába; mindkét parancsot használja a [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) parancsmagot.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Adjon meg egy üzenetet egy üzenetsorba

A tényleges, az üzenetsorban lévő üzenetek befolyásoló műveletek használja a storage .NET ügyféloldali kódtár érhető el a PowerShell. Vegyen fel egy üzenetet egy üzenetsorba, hozzon létre egy új példányát a üzenetobjektum [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) osztály. Ezután hívja meg az [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) módszert. Egy CloudQueueMessage egy karakterláncból (UTF-8 formátumban) vagy Bájttömbbel hozható létre.

A következő példa bemutatja, hogyan adjon meg egy üzenetet az üzenetsorba.

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

Ha használja a [Azure Storage Explorer](http://storageexplorer.com), csatlakozhat az Azure-fiókjával, és az üzenetsorok megtekintheti a storage-fiókban, és részletesebb adatainak megtekintése az üzeneteket az üzenetsor egy üzenetsorba. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Olvassa el egy üzenetet az üzenetsorból, majd törlése

Üzenetek legjobb – próbálkozzon első-first out sorrendben olvasható. Nem garantált. Ha elolvasta az üzenetet az üzenetsorból, hogy láthatóvá váljon a minden más folyamatok megnézzük a várólista. Ez biztosítja, hogy a kód nem tudja feldolgozni az üzenetet, hardver-vagy szoftverhiba miatt, ha a kódot egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra.  

Ez **láthatatlansági időtúllépési** határozza meg, mennyi ideig az üzenet marad láthatatlan mielőtt ismét használható a feldolgozásra. Az alapértelmezett érték 30 másodperc. 

A kód egy üzenetet az üzenetsorból, két lépésben beolvasása. Meghívásakor a [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metódus, a következő üzenetet kap a várólistán. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. A befejezéshez, az üzenet eltávolítása a sorból, hívja a [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metódust. 

A következő példában, olvassa el a három üzenetsorbeli üzenetek, majd várjon 10 másodpercet (az láthatatlansági időkorlátot). Ezután ismét a három üzenetek olvasásához az üzenetek törlése meghívásával elolvasásával **DeleteMessage**. Olvassa el az üzenetsor, az üzenetek törlését követően meg, ha $queueMessage küldi vissza a rendszer null értékű.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
$queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a Remove-AzureStorageQueue parancsmagot. Az alábbi példa bemutatja, hogyan használja a Remove-AzureStorageQueue parancsmag gyakorlathoz használt az adott üzenetsor törlése.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes létrehozott ebben a gyakorlatban az eszközök, távolítsa el az erőforráscsoportot. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókot és magát az erőforráscsoportot.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Az útmutató a cikkben megismerkedett a alapvető üzenetsor tárolók kezelése a PowerShell-lel, beleértve a:

> [!div class="checklist"]
> * Üzenetsor létrehozása
> * Egy üzenetsor beolvasása
> * Üzenet hozzáadása
> * Olvassa el a következő üzenetet
> * Üzenet törlése 
> * Üzenetsor törlése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>A Microsoft Azure PowerShell tárolási parancsmagjainak
* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
