---
title: Ideiglenes törlés az Azure Storage-blobokhoz | Microsoft dokumentumok
description: Az Azure Storage mostantól elérhető entszifájloló törlést biztosít a blobobjektumokhoz, így könnyebben helyreállíthatja az adatokat, ha azokat egy alkalmazás vagy más tárfiók-felhasználó hibásan módosítja vagy törli.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234297"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Az Azure Storage-blobok helyreállítható törlése

Az Azure Storage mostantól elérhető entszifájloló törlést biztosít a blobobjektumokhoz, így könnyebben helyreállíthatja az adatokat, ha azokat egy alkalmazás vagy más tárfiók-felhasználó hibásan módosítja vagy törli.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Hogyan működik a lágy törlés?

Ha engedélyezve van, a helyreállítható törlés lehetővé teszi az adatok mentését és helyreállítását blobok vagy blob pillanatképek törlésekor. Ez a védelem kiterjed a blob adatok, amelyek felülírás eredményeként törlődik.

Az adatok törlésekor végleges törlés helyett helyreállíthatóan törölt állapotba vált. Ha a helyreállítható törlés be van kapcsolva, és felülírja az adatokat, a rendszer egy helyreállíthatóan törölt pillanatképet hoz létre a felülírt adatok állapotának mentéséhez. A helyreállíthatóan törölt objektumok csak akkor lesznek láthatatlanok, ha kifejezetten szerepelnek a listán. Megadhatja azt az időtartamot, ameddig visszaállíthatók a helyreállítható módon törölt adatok, és amely után véglegesen törlődnek.

A helyreállítható törlés visszafelé kompatibilis, így nem kell módosítania az alkalmazásokat, hogy kihasználhassa a szolgáltatás által biztosított védelmet. Az [adat-helyreállítás](#recovery) azonban egy új **Blob-API-t** vezet be.

### <a name="configuration-settings"></a>Konfigurációs beállítások

Amikor új fiókot hoz létre, a törlés alapértelmezés szerint ki van kapcsolva. A helyreállítható törlés alapértelmezés szerint a meglévő tárfiókok esetében is ki van kapcsolva. A funkció be- és kikapcsolhatja bármikor élettartama alatt egy tárfiók.

A szolgáltatás kikapcsolásakor továbbra is hozzáférhet a helyreállíthatóan törölt adatokhoz, feltéve, hogy a helyreállíthatóan törölt adatok at mentette, amikor a funkció korábban be volt kapcsolva. Ha bekapcsolja a helyreállítható törlést, konfigurálnia kell a megőrzési időszakot is.

A megőrzési időszak azt jelzi, hogy mennyi helyreállíthatóan törölt adatok tárolása és helyreállítása érdekében rendelkezésre álló. A blobok és blob pillanatképek, amelyek explicit módon törlődnek, a megőrzési időszak órája az adatok törlésekor kezdődik. A helyreállíthatóan törölt pillanatképek által létrehozott, a soft delete funkció, amikor az adatok felülírása, az óra elindul, amikor a pillanatkép jön létre. Jelenleg 1 és 365 nap között őrizheti meg a helyreállíthatóan törölt adatokat.

A gyenge törlés megőrzési időszaka bármikor módosítható. A frissített megőrzési időszak csak az újonnan törölt adatokra vonatkozik. A korábban törölt adatok az adatok törlésekor konfigurált megőrzési időszak alapján lejárnak. A helyreállíthatóan törölt objektum törlésének megkísérlése nem befolyásolja annak lejárati idejét.

### <a name="saving-deleted-data"></a>Törölt adatok mentése

A helyreállítható törlés sok esetben megőrzi az adatokat, amikor a blobok vagy a blobpillanatképek törlődnek vagy felülíródnak.

Ha egy blob felülírása a **Put Blob**, **Put Block**, Put Block **List**, vagy **Copy Blob** egy pillanatképet a blob állapota, mielőtt az írási művelet automatikusan jön létre. Ez a pillanatkép egy helyreállíthatóan törölt pillanatkép; ez láthatatlan, kivéve, ha a helyreállíthatóan törölt objektumok explicit módon szerepelnek. A [helyreállíthatóan](#recovery) törölt objektumok felsorolásához tekintse meg a Helyreállítás című szakaszt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*A helyreállíthatóan törölt adatok szürkén jelennek meg, míg az aktív adatok kékszínűek. A legutóbb írt adatok a régebbi adatok alatt jelennek meg. Ha a B0 felülírja a B1, a B0 helyreállíthatóan törölt pillanatképe jön létre. Ha a B1 felülírja a B2, a B1 helyreállíthatóan törölt pillanatképe jön létre.*

> [!NOTE]  
> A helyreállítható törlés csak felülírási védelmet biztosít a másolási műveletekhez, ha be van kapcsolva a célblob fiókjához.

> [!NOTE]  
> A helyreállítható törlés nem biztosít felülírási védelmet az archív rétegben lévő blobok számára. Ha egy blob az archívumban felülírja egy új blob bármely rétegben, a felülírt blob véglegesen lejárt.

Blob **törlése** esetén egy pillanatkép, hogy a pillanatkép van megjelölve helyreállíthatóan törölt. Nem jön létre új pillanatkép.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*A helyreállíthatóan törölt adatok szürkén jelennek meg, míg az aktív adatok kékszínűek. A legutóbb írt adatok a régebbi adatok alatt jelennek meg. Pillanatkép **blob** neve, B0 lesz egy pillanatkép, és B1 a blob aktív állapota. A B0 pillanatkép törlésekor a helyreállíthatóan töröltként lesz megjelölve.*

Blob **törlése** esetén egy alapblob (bármely blob, amely maga nem egy pillanatkép), hogy a blob van megjelölve helyreállíthatóan törölt. A korábbi viselkedéssel összhangban a **Blob törlése** egy aktív pillanatképekkel rendelkező blobon való hívása hibát ad vissza. A **Blob törlése** egy helyreállíthatóan törölt pillanatképekkel rendelkező blobon való hívása nem ad vissza hibát. Továbbra is törölheti a blobot és annak összes pillanatképét egyetlen műveletben, ha a helyreállítható törlés be van kapcsolva. Ezzel az alapblobot és a pillanatképeket helyreállíthatóan töröltként jelöli meg.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*A helyreállíthatóan törölt adatok szürkén jelennek meg, míg az aktív adatok kékszínűek. A legutóbb írt adatok a régebbi adatok alatt jelennek meg. Itt egy **Blob törlése** hívás történik a B2 és az összes kapcsolódó pillanatképek törléséhez. Az aktív blob, B2 és az összes társított pillanatképek vannak megjelölve helyreállíthatóan törölt.*

> [!NOTE]  
> Ha egy helyreállíthatóan törölt blob felülírja, a blob állapotának az írási művelet előtt egy helyreállíthatóan törölt pillanatkép automatikusan létrejön. Az új blob örökli a felülírt blob rétegét.

A helyreállítható törlés nem menti az adatokat tároló vagy fiók törlése esetén, sem blob metaadatok és blob-tulajdonságok felülírása esetén. A tárfiók a hibás törlés elleni védelem érdekében konfigurálhatja a zárolást az Azure Resource Manager használatával. További információkért tekintse meg az Azure Resource Manager cikk [erőforrások zárolása a váratlan változások megelőzése című](../../azure-resource-manager/management/lock-resources.md) cikket.

A következő táblázat részletezi a várt viselkedést, amikor a helyreállítható törlés be van kapcsolva:

| REST API-művelet | Erőforrás típusa | Leírás | A viselkedés megváltozása |
|--------------------|---------------|-------------|--------------------|
| [Szabályzat](/rest/api/storagerp/StorageAccounts/Delete) | Fiók | Törli a tárfiókot, beleértve az összes tárolók és blobok, amely tartalmazza.                           | Nincs változás. A törölt fiókban lévő tárolók és blobok nem állíthatók helyre. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Tároló | Törli a tárolót, beleértve az összes blobot, amely tartalmazza. | Nincs változás. A törölt tárolóban lévő blobok nem állíthatók helyre. |
| [Put Blob](/rest/api/storageservices/put-blob) | Letiltás, hozzáfűzés és lapblobok | Új blobot hoz létre, vagy egy meglévő blobot cserél le egy tárolón belül | Ha egy meglévő blob lecserélésére használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez akkor és csak akkor érvényes egy korábban helyreállíthatóan törölt blobra is, ha azt egy azonos típusú blob (Blokk, Hozzáfűzés vagy Lap) váltja fel. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállíthatóan törölt adat véglegesen lejár. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Letiltás, hozzáfűzés és lapblobok | Blob ot vagy blob-pillanatképet jelöl törlésre. A blob vagy pillanatkép később törlődik a szemétgyűjtés során | Blob-pillanatkép törléséhez, a pillanatkép helyreállíthatóan töröltként lesz megjelölve. Blob törléséhez, a blob helyreállíthatóan töröltként van megjelölve. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Letiltás, hozzáfűzés és lapblobok | Átmásolja a forrásblobot egy célblobba ugyanabban a tárfiókban vagy egy másik tárfiókban. | Ha egy meglévő blob lecserélésére használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez akkor és csak akkor érvényes egy korábban helyreállíthatóan törölt blobra is, ha azt egy azonos típusú blob (Blokk, Hozzáfűzés vagy Lap) váltja fel. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállíthatóan törölt adat véglegesen lejár. |
| [Blokk berakása](/rest/api/storageservices/put-block) | Blokkblobok | Létrehoz egy új blokkot, amelyet egy blokkblob részeként kell lekötni. | Ha egy blokk aktív blobhoz való véglegesítésére szolgál, nincs változás. Ha egy letiltás véglegesítésére szolgál, amely helyreállíthatóan törölt, egy új blob jön létre, és a rendszer automatikusan létrehoz egy pillanatképet a helyreállíthatóan törölt blob állapotának rögzítéséhez. |
| [Blokklista felrakása](/rest/api/storageservices/put-block-list) | Blokkblobok | Véglegesítegy blobot a blokkblobot alkotó blokkazonosítók készletének megadásával. | Ha egy meglévő blob lecserélésére használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban helyreállíthatóan törölt blobra is vonatkozik, ha és csak akkor, ha blokkblobról van szó. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállíthatóan törölt adat véglegesen lejár. |
| [Oldal berakása](/rest/api/storageservices/put-page) | Lapblobok | Lapblobok tartományának írása. | Nincs változás. A művelettel felülírt vagy törölt lapblob-adatok nem kerülnek mentésre, és nem állíthatók helyre. |
| [Hozzáfűző blokk](/rest/api/storageservices/append-block) | Hozzáfűző blobok | Adatblokk írása egy hozzáfűző blob végére | Nincs változás. |
| [Blob tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties) | Letiltás, hozzáfűzés és lapblobok | A blobhoz definiált rendszertulajdonságok értékeit állítja be. | Nincs változás. Felülírt blob tulajdonságai nem állíthatók helyre. |
| [Blob metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata) | Letiltás, hozzáfűzés és lapblobok | A megadott blob felhasználó által definiált metaadatait egy vagy több név-érték párként állítja be. | Nincs változás. A felülírt blob metaadatok nem állíthatók helyre. |

Fontos megjegyezni, hogy a "Put Page" hívása a lapblobok tartományainak felülírásához vagy törléséhez nem hoz létre automatikusan pillanatképeket. A virtuálisgép-lemezek et lapblobok támasztják alá, és az Adatok írásához használja a **Lap beírását.**

### <a name="recovery"></a>Helyreállítás

A [Blob törlése törlése](/rest/api/storageservices/undelete-blob) művelet hívása egy helyreállíthatóan törölt alapblobon visszaállítja azt, és az összes társított helyreállíthatóan törölt pillanatképet aktívként. Az `Undelete Blob` aktív alapblobon a művelet hívása visszaállítja az összes társított helyreállíthatóan törölt pillanatképet aktívként. Amikor a pillanatképek aktívként vannak visszaállítva, úgy néznek ki, mint a felhasználó által létrehozott pillanatképek; nem írják felül az alapblobot.

Blob visszaállítása egy adott helyreállíthatóan törölt `Undelete Blob` pillanatkép, hívhatja az alap blob. Ezután átmásolhatja a pillanatképet a most aktív blobon keresztül. A pillanatképet egy új blobba is átmásolhatja.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*A helyreállíthatóan törölt adatok szürkén jelennek meg, míg az aktív adatok kékszínűek. A legutóbb írt adatok a régebbi adatok alatt jelennek meg. Itt a **Blob törlésének törlését** a B blobneve, ezáltal az alapblob, a B1 és az összes kapcsolódó pillanatkép aktívként állítja vissza. A második lépésben a B0 az alapblobon keresztül kerül másolásra. Ez a másolási művelet a B1 helyreállíthatóan törölt pillanatképét hozza létre.*

A helyreállíthatóan törölt blobok és blobpillanatképek megtekintéséhez választhatja ki, hogy a törölt adatokat is felszeretné-e foglalni a **List Blobs listában.** Választhat, hogy csak a helyreállíthatóan törölt alapblobok megtekintéséhez, vagy a helyreállíthatóan törölt blob pillanatképek is. Az összes helyreállíthatóan törölt adat esetében megtekintheti az adatok törlésének idejét, valamint az adatok végleges lejárta előtti napok számát.

### <a name="example"></a>Példa

A következő a .NET parancsfájl konzolkimenete, amely feltölti, felülírja, pillanatképeket, törléseket és visszaállítást hajt fel egy *HelloWorld* nevű blobban, ha a helyreállítható törlés be van kapcsolva:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

A [következő lépések](#next-steps) szakaszban található a kimenetet hozó alkalmazásra mutató mutató.

## <a name="pricing-and-billing"></a>Árak és számlázás

Minden helyreállíthatóan törölt adat számlázása az aktív adatokkal megegyező sebességgel történik. A beállított megőrzési időszak után véglegesen törölt adatokért nem kell fizetnie. A pillanatképek mélyebb megismerése és a díjak elhalmozásának módjáért olvassa [el A pillanatképek halmozódásának ismertetése](storage-blob-snapshots.md)című témakört.

A pillanatképek automatikus létrehozásához kapcsolódó tranzakciókért nem kell fizetnie. Az írási műveletek díjának mértéke a **Blob-tranzakciók törlésének kiírásáért** kell fizetnie.

Az Azure Blob Storage árairól az [Azure Blob Storage díjszabási lapján](https://azure.microsoft.com/pricing/details/storage/blobs/)további részleteket talál.

Amikor először kapcsolja be a helyreállítható törlést, javasoljuk, hogy használjon egy kis megőrzési időszakot, hogy jobban megértse, hogyan befolyásolja a funkció a számlát.

## <a name="get-started"></a>Bevezetés

A következő lépések bemutatják, hogyan kezdheti el a helyreállítható törlést.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Engedélyezze a blobok ideiglenes törlését a tárfiókban az Azure Portal használatával:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a tárfiókot. 

2. Keresse meg az **Adatvédelmi** lehetőséget a **Blob Service csoportban.**

3. Kattintson **az Engedélyezve** gombra a **Blob helyreállítható törlése csoportban.**

4. Adja meg, hogy hány napig szeretne *megőrizni a* **megőrzési házirendek alatt.**

5. Az adatvédelmi beállítások megerősítéséhez kattintson a **Mentés** gombra

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

A helyreállíthatóan törölt blobok megtekintéséhez jelölje be a **Törölt blobok megjelenítése jelölőnégyzetet.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Egy adott blob helyreállíthatóan törölt pillanatképeinek megtekintéséhez jelölje ki a blobot, majd kattintson a **Pillanatképek megtekintése gombra.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **Törölt pillanatképek megjelenítése** jelölőnégyzet be van jelölve.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ha egy helyreállíthatóan törölt blobra vagy pillanatképre kattint, figyelje meg az új blobtulajdonságokat. Azt jelzik, hogy mikor törölték az objektumot, és hány nap van hátra, amíg a blob vagy blob pillanatkép véglegesen lejár. Ha a helyreállíthatóan törölt objektum nem pillanatkép, akkor lehetősége van a törlés törlésére is.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy a blob törlése is törli az összes társított pillanatképek törlését. Ha egy aktív blob helyreállíthatóan törölt pillanatképeit szeretné törölni, kattintson a blobra, és válassza **az Összes pillanatkép törlése törlése lehetőséget.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután törölte egy blob pillanatképeit, a **Előléptetés** gombra kattintva átmásolhat egy pillanatképet a gyökérblobon, ezáltal visszaállítva a blobot a pillanatképbe.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A helyreállítható törlés engedélyezéséhez frissítse a blobügyfél szolgáltatástulajdonságait. A következő példa lehetővé teszi a helyreállítható törlést az előfizetésfiókok egy részhalmazához:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
A következő paranccsal ellenőrizheti, hogy a helyreállítható törlés be volt-e kapcsolva:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

A véletlenül törölt blobok helyreállításához hívja meg a Törlés törlése parancsot ezeken a blobokon. Ne feledje, hogy a **Blob törlése törlése hívása**– mind az aktív, mind a helyreállíthatóan törölt blobok esetén – az összes társított helyreállíthatóan törölt pillanatképet aktívként állítja vissza. A következő példa meghívja a Törlés törlését a tárolóban lévő összes helyreállíthatóan törölt és aktív blobon:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Az aktuális helyreállítható törlési adatmegőrzési házirend megkereséséhez használja a következő parancsot:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[parancssori felület](#tab/azure-CLI)

A helyreállítható törlés engedélyezéséhez frissítse a blobügyfél szolgáltatástulajdonságait:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

A helyreállítható törlés bekapcsoltégének ellenőrzéséhez használja a következő parancsot: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

A helyreállítható törlés engedélyezéséhez frissítse a blobügyfél szolgáltatástulajdonságait:

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

A helyreállítható törlés engedélyezéséhez frissítse a blobügyfél szolgáltatástulajdonságait:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

A véletlenül törölt blobok helyreállításához hívja meg a Törlés törlése parancsot ezeken a blobokon. Ne feledje, hogy a **Blob törlése törlése hívása**– mind az aktív, mind a helyreállíthatóan törölt blobok esetén – az összes társított helyreállíthatóan törölt pillanatképet aktívként állítja vissza. A következő példa meghívja a Törlés törlését a tárolóban lévő összes helyreállíthatóan törölt és aktív blobon:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Egy adott blobverzió helyreállítása, először hívja undelete egy blobot, majd másolja a kívánt pillanatképet a blob felett. A következő példa helyreállítja a blokkblobot a legutóbb létrehozott pillanatképhez:

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

## <a name="special-considerations"></a>Különleges szempontok

Ha fennáll annak az esélye, hogy az adatokat véletlenül módosítja vagy törli egy alkalmazás vagy egy másik tárfiók felhasználója, a helyreállítható törlés bekapcsolása ajánlott. A gyakran felülírt adatok helyreállítható törlésengedélyezése növelheti a tárolási kapacitás díját és a blobok listázása kor megnövekedett késést. Ezt a többletköltséget és késést mérsékelheti, ha a gyakran felülírt adatokat egy külön tárfiókban tárolja, ahol a helyreállítható törlés le van tiltva. 

## <a name="faq"></a>GYIK

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Mely tárolási szolgáltatásokhoz használhatom a helyreállítható törlést?

Jelenleg a helyreállítható törlés csak blob (objektum) tárolóhoz érhető el.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Az ideiglenes törlés minden tárfióktípushoz elérhető?

Igen, a helyreállítható törlés érhető el a Blob storage-fiókok, valamint az általános célú (GPv1 és GPv2) tárfiókok blobok esetén. Mind a standard, mind a prémium fióktípusok támogatottak. A helyreállítható törlés nem felügyelt lemezekhez érhető el, amelyek a borítók alatt lapblobok. A felügyelt lemezek nem érhetők el a helyreállítható törléssel.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Az ideiglenes törlés az összes tárolási réteghez elérhető?

Igen, a helyreállítható törlés minden tárolási szinten elérhető, beleértve a gyakori elérésű, a ritka elérésű és az archiválást. A helyreállítható törlés azonban nem biztosít felülírási védelmet az archív rétegben lévő blobok számára.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Használhatom a Blob tier API beállítása blobok helyreállíthatóan törölt pillanatképek?

Igen. A helyreállíthatóan törölt pillanatképek az eredeti rétegben maradnak, de az alapblob az új szintre kerül. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Prémium szintű tárfiókok blob pillanatkép-korlát 100. A helyreállíthatóan törölt pillanatképek beleszámítanak ebbe a korlátba?

Nem, a helyreállíthatóan törölt pillanatképek nem számítanak bele ebbe a korlátba.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Bekapcsolhatom a meglévő tárfiókok ideiglenes törlését?

Igen, a helyreállítható törlés a meglévő és az új tárfiókokhoz is konfigurálható.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Ha egy teljes fiókot vagy tárolót törlök, és be van kapcsolva a törlés, az összes társított blob mentésre kerül?

Nem, ha egy teljes fiókot vagy tárolót töröl, az összes társított blob véglegesen törlődik. A tárfiók véletlen törlésekkel szembeni védelméről az [Erőforrások zárolása a váratlan változások megelőzése érdekében](../../azure-resource-manager/management/lock-resources.md)című témakörben talál további információt.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Megtekinthetem a törölt adatok kapacitásmutatóit?

A helyreállíthatóan törölt adatok a teljes tárfiók teljes kapacitásának részét képezik. A tárolókapacitás nyomon követéséről és figyelésével kapcsolatos további információkért lásd: [Storage Analytics](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Ha kikapcsolom a helyreállítható törlést, továbbra is hozzáférhetek a helyreállíthatóan törölt adatokhoz?

Igen, továbbra is hozzáférhet a lejárt, helyreállíthatóan törölt adatokhoz, ha a helyreállítható törlés ki van kapcsolva.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Elolvashatom és kimásolhatom a blobról törölt, helyreállíthatóan törölt pillanatképeket?  

Igen, de először meg kell hívnia a törlés törlését a blobon.

### <a name="is-soft-delete-available-for-all-blob-types"></a>A helyreállítható törlés minden blobtípushoz elérhető?

Igen, a helyreállítható törlés blokkblobok, hozzáfűző blobok és lapblobok esetén érhető el.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>A rendszer elérhető a helyreállítható törlés a virtuálisgép-lemezekhez?  

A helyreállítható törlés prémium és nem felügyelt lemezekhez is elérhető, amelyek a borítók alatt lapblobok. A helyreállítható törlés csak a **Blob törlése,** **a Blob felállítása,** a **Blokklista felállítása,** **a Blokk elrakásával** és **a Blob másolásával** végzett műveletek által törölt adatok helyreállításában segít. Az **Oldal beírása** által felülírt adatok nem állíthatók helyre.

Egy Azure virtuális gép írja egy nem felügyelt lemez hívásokkal **put page,** így a soft delete visszavonása írások egy Azure virtuális gép nem támogatott forgatókönyv.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Módosítanom kell a meglévő alkalmazásaimat a helyreállítható törlés használatához?

Lehetőség van a soft delete előnyeinek kihasználására, függetlenül attól, hogy milyen API-verziót használ. Azonban a felpróbálatlan törölt blobok és blob pillanatképek listázásához és helyreállításához a Storage [Services REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 2017-07-29-es verzióját vagy azt kell használnia. A Microsoft mindig az Azure Storage API legújabb verzióját javasolja.

## <a name="next-steps"></a>További lépések

* [.NET mintakód](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [A Blob szolgáltatás REST API-ja](/rest/api/storageservices/blob-service-rest-api)
* [Azure storage replikációja](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Magas rendelkezésre állású alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Vész-helyreállítási és tárfiók feladatátvétel (előzetes verzió) az Azure Storage-ban](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
