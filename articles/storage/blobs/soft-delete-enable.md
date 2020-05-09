---
title: A Blobok helyreállítható törlésének engedélyezése
titleSuffix: Azure Storage
description: A blob-objektumok helyreállítható törlésének engedélyezésével könnyebben állíthatja helyre az adatokat, ha azok hibásan lettek módosítva vagy törölve lettek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 26a16d0eeb81c12faede1c00bdf5a0d724f7a6c6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884682"
---
# <a name="enable-soft-delete-for-blobs"></a>A Blobok helyreállítható törlésének engedélyezése

A következő lépések bemutatják, hogyan kezdheti el a Soft deletet.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Blobok törlésének engedélyezése a Storage-fiókban Azure Portal használatával:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a **blob Service**területen a **Adatvédelem** lehetőségre.

3. Kattintson az **engedélyezve** lehetőségre a blob-helyreállító **Törlés** alatt

4. Adja meg, hogy hány napon belül szeretné *megőrizni az* **adatmegőrzési szabályzatokat**

5. Kattintson a **Save (Mentés** ) gombra az adatvédelmi beállítások megerősítéséhez

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

A nem kötelezően törölt Blobok megtekintéséhez jelölje be a **törölt Blobok megjelenítése** jelölőnégyzetet.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Egy adott blobhoz tartozó, törölt Pillanatképek megtekintéséhez válassza ki a blobot, majd kattintson a **Pillanatképek megtekintése**elemre.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **törölt Pillanatképek megjelenítése** jelölőnégyzet be van jelölve.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ha a törölt blobra vagy pillanatképre kattint, figyelje az új blob tulajdonságait. Ezek azt jelzik, hogy mikor lett törölve az objektum, és hány nap van hátra, amíg a blob vagy a blob pillanatképe véglegesen lejár. Ha a helyreállított törölt objektum nem pillanatkép, akkor lehetősége van törölni a törlést is.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy a Blobok törlésének visszavonása törli az összes társított pillanatképet is. Ha törölni szeretné a nem törölt pillanatképeket egy aktív blobhoz, kattintson a blobra, és válassza az **összes pillanatkép törlésének**visszavonása lehetőséget.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután visszavonta egy blob pillanatképének törlését, az **előléptetés** gombra kattintva másolhatja a pillanatképet a gyökérkönyvtárba, így visszaállíthatja a blobot a pillanatképre.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait. A következő példa lehetővé teszi a Soft delete használatát egy előfizetésben lévő fiókok egy részhalmaza számára:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
A következő parancs használatával ellenőrizheti, hogy a Soft delete be van-e kapcsolva:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

A véletlenül törölt Blobok helyreállításához hívja meg a törlést a blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok **törlésének**visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a törlést a tároló összes lágy törölt és aktív blobján:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
A következő parancs használatával megkeresheti az aktuális helyreállítható törlési szabályzatot:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[parancssori felület](#tab/azure-CLI)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

A Soft delete bekapcsolásának ellenőrzéséhez használja a következő parancsot: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

A véletlenül törölt Blobok helyreállításához hívja meg a törlést a blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok **törlésének**visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a törlést a tároló összes lágy törölt és aktív blobján:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Egy adott blob-verzióra történő helyreállításhoz először hívja meg a törlést egy blobon, majd másolja a kívánt pillanatképet a blobon. A következő példa egy blokk blobot állít be a legutóbb generált pillanatképre:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

