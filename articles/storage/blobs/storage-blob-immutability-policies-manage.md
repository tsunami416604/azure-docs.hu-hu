---
title: A Blob Storage megváltoztathatatlansági szabályzatának beállítása és kezelése - Azure Storage
description: Ismerje meg, hogyan használhatja worm (írás egyszer, sok olvasása) támogatja a Blob (objektum) tároló adatok tárolására nem elévülhető, nem módosítható állapotban egy adott intervallumban.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970102"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>A Blob storage-hoz való megváltoztathatatlansági házirendek beállítása és kezelése

Az Azure Blob storage nem módosítható tárhelye lehetővé teszi a felhasználók számára, hogy az üzleti szempontból kritikus fontosságú adatobjektumokat WORM (Egyszer írás, Sok olvasás) állapotban tárolják. Ez az állapot az adatokat nem lehet eltéve, és nem módosítható a felhasználó által megadott időközönként. A megőrzési időköz időtartama alatt blobok hozhatók létre és olvashatók, de nem módosíthatók vagy törölhetők. A nem módosítható tár minden Azure-régióban elérhető az általános célú v2- és Blob-tárfiókokhoz.

Ez a cikk bemutatja, hogyan állíthatja be és kezelheti a blobstorage-beli adatok szolgáltatása a szolgáltatás ban az Azure Portalon, a PowerShellben vagy az Azure CLI-ben tárolt adatok at. A nem módosítható tárolásról további információt az [Üzleti legkritikusabb blobadatok tárolása nem módosítható tárhellyel című témakörben talál.](storage-blob-immutable-storage.md)

## <a name="set-retention-policies-and-legal-holds"></a>Adatmegőrzési szabályok és jogi visszatartások beállítása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Hozzon létre egy új tárolót, vagy válasszon ki egy már meglévőt a nem módosítható állapotban tartandó blobok tárolására. A tárolónak egy általános célú v2- vagy Blob-tárfiókban kell lennie.

2. Válassza **az Access házirend lehetőséget** a tárolóbeállításaiközött. Ezután válassza **a Házirend hozzáadása** lehetőséget az **Átnem módosítható blobtároló csoportban.**

    ![Tárolóbeállítások a portálon](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Az időalapú adatmegőrzés engedélyezéséhez válassza az **Időalapú adatmegőrzés** lehetőséget a legördülő menüből.

    !["Házirend típusa" alatt kiválasztott "Időalapú adatmegőrzés"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Adja meg a megőrzési időközt napokban (az elfogadható értékek 1 és 146000 nap között vannak).

    !["Megőrzési időszak frissítése" mező](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    A házirend kezdeti állapota fel van oldva, így tesztelheti a funkciót, és módosíthatja a házirendet a zárolás előtt. A házirend zárolása alapvető fontosságú az olyan előírások nak való megfeleléshez, mint a SEC 17a-4.

5. Zárd le a házirendet. Kattintson a jobb gombbal a három pontra (**...**), és a következő menü további műveletekkel jelenik meg:

    !["Zárolási házirend" a menüben](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Válassza **a Házirend zárolása lehetőséget,** és erősítse meg a zárolást. A házirend zárolva van, és nem törölhető, csak a megőrzési időköz kiterjesztései engedélyezettek. Blob törlések és felülbírálások nem engedélyezettek. 

    ![A "Házirend zárolása" megerősítése a menüben](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. A jogi visszatartjanak engedélyezéséhez válassza **a Házirend hozzáadása**lehetőséget. Válassza a Legördülő menü **Jogi visszatartás** parancsát.

    !["Jogi visszatartás" a "Házirend típusa" menüpont alatt](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Hozzon létre jogi visszatartást egy vagy több címkével.

    !["Címkenév" mező a házirend-típus alatt](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. A jogi visszatartás törléséhez távolítsa el az alkalmazott jogi visszatartásazonosító címkét.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A szolgáltatás a következő parancscsoportokban található: `az storage container immutability-policy` és `az storage container legal-hold`. Fuss `-h` rajtuk, hogy a parancsokat.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Az.Storage modul támogatja a nem módosítható tárolást.  A szolgáltatás engedélyezéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a `Install-Module PowerShellGet –Repository PSGallery –Force`PowerShell legújabb verziója telepítve van: .
2. Távolítsa el az Azure PowerShell korábbi telepítését.
3. Telepítse az Azure `Install-Module Az –Repository PSGallery –AllowClobber`PowerShellt: .

A következő powershell-parancsfájlminta referenciaként szolgál. Ez a parancsfájl létrehoz egy új tárfiókot és tárolót. Ezután bemutatja, hogyan állíthat be és törölheti a jogi visszatartásokat, hozhat létre és zárolhat egy időalapú adatmegőrzési házirendet (más néven megváltoztathatatlansági házirendet), és hogyan hosszabbíthatja meg a megőrzési időközt.

Először hozzon létre egy Azure Storage-fiókot:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Meghatározott és egyértelmű jogi tart:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Időalapú megváltoztathatósági házirendek létrehozása vagy frissítése:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

A megváltoztathatatlansági házirendek beolvasása:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

A megváltoztathatatlansági házirendek zárolása (a `-Force` kérdés elvetéséhez hozzáadható):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

A megváltoztathatatlansági házirendek kiterjesztése:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Feloldott megváltoztathatatlansági házirend eltávolítása `-Force` (a kérdés elvetéséhez adja hozzá):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Védett hozzáfűző blobok írási műveletek engedélyezésének engedélyezése

### <a name="portal"></a>[Portál](#tab/azure-portal)

![További hozzáfűző írások engedélyezése](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A szolgáltatás a következő parancscsoportokban található: `az storage container immutability-policy` és `az storage container legal-hold`. Fuss `-h` rajtuk, hogy a parancsokat.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>További lépések

[Üzleti legkritikusabb blobadatok tárolása állandó tárolóval](storage-blob-immutable-storage.md)
