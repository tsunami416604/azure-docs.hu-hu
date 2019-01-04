---
title: Helyreállítható Törlés az Azure Storage-blobok |} A Microsoft Docs
description: Azure Storage blob-objektumok a helyreállítható Törlés most kínál, így könnyebben helyreállíthatja az adatokat próbál módosított vagy törölt egy alkalmazás vagy más storage-fiók felhasználó által.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 07/15/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 7f7071c9f87528eddbfe3d541cd85624e308948f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633385"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Helyreállítható Törlés az Azure Storage-BLOB
Azure Storage blob-objektumok a helyreállítható Törlés most kínál, így könnyebben helyreállíthatja az adatokat próbál módosított vagy törölt egy alkalmazás vagy más storage-fiók felhasználó által.

## <a name="how-does-it-work"></a>Hogyan működik?
Bekapcsolása esetén helyreállítható törlés lehetővé teszi a mentheti és blobokat vagy pillanatképeket törlése esetén az adatok helyreállításához. Blobadatok, amely felülírásának eredményeként törlődik kiterjeszti a védelmet.

A törölt adatok átirányítja a helyreállíthatóan törölt állapotból ahelyett, hogy véglegesen törölve lesz. Helyreállítható törlés és adatok felülírása, ha egy helyreállíthatóan törölt pillanatkép menteni az állapotot a felülírt adatok jön létre. Helyreállíthatóan törölt objektumok nem láthatók, kivéve, ha explicit módon szerepel. Beállíthatja, hogy mennyi ideig helyreállíthatóan törölt adatok helyreállítható, mielőtt véglegesen lejár.

Helyreállítható törlés visszafelé kompatibilis; nem kell módosításokat az alkalmazások előnyeit Ez a szolgáltatás számára biztosítja a védelmet. Azonban [adat-helyreállítás](#recovery) újdonsága **Blob törlésének visszavonása** API-t.

### <a name="configuration-settings"></a>Konfigurációs beállítások
Amikor létrehoz egy új fiókot, a helyreállítható törlés alapértelmezés szerint le van. Helyreállítható törlés is alapértelmezés szerint le van meglévő storage-fiókok. A szolgáltatás be- és kikapcsolhatja a storage-fiók élettartama során bármikor válthat.

Is elérheti, és a helyreállítás helyreállíthatóan törölt adatok, ha a funkció be van kapcsolva, feltételezve, hogy a helyreállíthatóan törölt adatok mentése során a szolgáltatás korábban be lett kapcsolva. A helyreállítható törlés bekapcsolásakor, emellett a megőrzési időszak beállítása.

A megőrzési időtartam azt jelzi, hogy mennyi ideig, helyreállíthatóan törölt adatok tárolt és a helyreállításhoz elérhető. A blobok és explicit módon törölt blobpillanatképeket a megőrzési időszak órája kezdődik meg az adatok törlődnek. Helyreállíthatóan törölt pillanatképek a helyreállítható törlési funkció szerint jönnek létre, ha a rendszer felülírja azok adatait az óra a pillanatkép létrehozása után kezdődik. Jelenleg őrizheti meg 1 és 365 nap közötti helyreállíthatóan törölt adatok.

A helyreállítható törlés megőrzési időszak bármikor módosíthatja. Egy frissített megőrzési idő újonnan törölt adatok csak érvényes. Korábban törölt adatok alapján-én jár le a megőrzési időszak, amelyet az adatok törlésekor. Helyreállíthatóan törölt objektum törlése nem érinti a lejárati idő.

### <a name="saving-deleted-data"></a>Törölt adatok mentése
Helyreállítható törlés megőrzi az adatok sok esetben, ha blobokat vagy pillanatképeket törölni vagy felül.

Amikor egy blob használatával felülírja **Put Blob**, **Blokkelraktározási**, **tiltólista Put** vagy **a Blob másolásához** előtt, a blob állapot pillanatképét a az írási művelet automatikusan jön létre. A pillanatkép a helyreállíthatóan törölt pillanatkép; nem látható, kivéve, ha kifejezetten felsorolt helyreállíthatóan törölt objektumokat. Tekintse meg a [helyreállítási](#recovery) részből megtudhatja, hogyan kell felsorolni a helyreállíthatóan törölt objektumokat.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Helyreállíthatóan törölt adatok nem szürke, kék nem aktív adatok esetében. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. B0 B1 felülírja, ha B0 helyreállíthatóan törölt pillanatkép jön létre. B1 B2 felülírja, ha B1 helyreállíthatóan törölt pillanatkép jön létre.*

> [!NOTE]  
> Helyreállítható törlés csak végrehajtható írja felül a másolási műveletek védelem bekapcsolásakor, a cél blob-fiókhoz.

> [!NOTE]  
> Helyreállítható törlés nem biztosít írja felül az archív szinten lévő blobok védelmét. Minden csomagban egy új blob egy blobot, felülírja, ha a felülírt blob véglegesen lejárt.

Amikor **Blob törlése** nevezzük a pillanatképet, a pillanatkép van megjelölve, enyhe törölve. Új pillanatkép nem jön létre.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Helyreállíthatóan törölt adatok nem szürke, kék nem aktív adatok esetében. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Amikor **Blob pillanatkép** B0 válik nevű, pillanatképek pedig az B1 aktív állapota a blobot. A B0 pillanatkép törlése esetén van megjelölve a helyreállíthatóan törölt.*

Amikor **Blob törlése** (minden blob, amely nem magát egy pillanatkép) egy base BLOB neve, hogy a blob van megjelölve a helyreállíthatóan törölt. Ez előző viselkedés, konzisztens hívása **Blob törlése** az aktív pillanatképekkel rendelkező blobok hibát ad vissza. Hívó **Blob törlése** a helyreállíthatóan törölt pillanatképekkel rendelkező blobok nem ad vissza hibát. Amikor a helyreállítható törlés be van kapcsolva egy blobot, és annak egyetlen műveletben lévő összes pillanatképet is törölheti. Így jelöli meg az alap blob és a helyreállítható, pillanatképet törölni.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Helyreállíthatóan törölt adatok nem szürke, kék nem aktív adatok esetében. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Itt egy **Blob törlése** kezdeményezték a B2, és minden társított pillanatképet törölni. Az aktív blob, B2 és minden társított pillanatképet lesznek megjelölve a helyreállíthatóan törölt.*

> [!NOTE]  
> Amikor a rendszer felülírja a helyreállíthatóan törölt blobok, az írási művelet előtt a blob állapot helyreállíthatóan törölt pillanatkép automatikusan jön létre. Az új blob örökli a felülírt blob szintjét.

Helyreállítható törlés nem mentse az adatait tároló vagy a fiók törlése, illetve ha blob metaadatait, és felülírja a blob tulajdonságai. Storage-fiók hibás törlés elleni védelem, a zárolás, az Azure Resource Manager használatával is konfigurálhat. Tekintse át az Azure Resource Manager-cikk [erőforrások zárolása a váratlan módosítások megelőzése](../../azure-resource-manager/resource-group-lock-resources.md) további.

Az alábbi táblázat ismerteti a várt viselkedés, amikor egy helyreállítható törlés:

| REST API-művelet | Erőforrás típusa | Leírás | A viselkedés módosítása |
|--------------------|---------------|-------------|--------------------|
| [Törlés](/rest/api/storagerp/StorageAccounts/Delete) | Fiók | Törli a tárfiókot, beleértve a tárolók és a benne található blobok.                           | Nincs változás. Tárolók és blobok a törölt fiók amelyek nem állítható helyre. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Tároló | Törli a tárolót, beleértve az összes benne található blobokat. | Nincs változás. Törölt tárolóban található blobokat, amelyek nem állítható helyre. |
| [Put Blob](/rest/api/storageservices/put-blob) | Blokkolás, Hozzáfűzés, és Lapblobok | Létrehoz egy új blob vagy lecseréli a tárolóban lévő meglévő blobokhoz | Ha lecseréli a meglévő blobokhoz, automatikusan létrejött egy pillanatkép a blob állapot hívása előtt. Ez is vonatkozik egy korábban helyreállíthatóan törölt blob csak, ha egy blob (blokk, Hozzáfűzés vagy lap) azonos típusú váltotta fel. Ha egy blob egy másik típusú váltotta fel, az összes meglévő helyreállíthatóan törölt adatok véglegesen lejár. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Blokkolás, Hozzáfűzés, és Lapblobok | Egy blob vagy a blob-pillanatkép törlésre jelöli meg. A blob vagy pillanatkép később törlése során szemétgyűjtés | Ha a blob-pillanatkép, a pillanatkép van megjelölve, a helyreállítható törlés törlése. Blobok törléséhez használatakor, hogy a blob van megjelölve, enyhe törölve. |
| [Blob másolása](/rest/api/storageservices/copy-blob) | Blokkolás, Hozzáfűzés, és Lapblobok | Másolja át a forrás blob egy forrásblobot ugyanazt a tárfiókot vagy egy másik tárfiókba. | Ha lecseréli a meglévő blobokhoz, automatikusan létrejött egy pillanatkép a blob állapot hívása előtt. Ez is vonatkozik egy korábban helyreállíthatóan törölt blob csak, ha egy blob (blokk, Hozzáfűzés vagy lap) azonos típusú váltotta fel. Ha egy blob egy másik típusú váltotta fel, az összes meglévő helyreállíthatóan törölt adatok véglegesen lejár. |
| [PUT letiltása](/rest/api/storageservices/put-block) | Blokkblobok | Egy blokkblob véglegesítése új blokkot hoz létre. | Ha egy blob, amely aktív véglegesítse nem történik változás. Használja, hogy egy blobot, amely helyreállíthatóan törölt egy letiltása, ha egy új blob jön létre, és a helyreállíthatóan törölt blob állapotát, automatikusan létrejött egy pillanatkép. |
| [PUT tiltólista](/rest/api/storageservices/put-block-list) | Blokkblobok | Egy blob véglegesíti a letiltása a blokkblob típusú alkotó azonosítók készletét megadásával. | Ha lecseréli a meglévő blobokhoz, automatikusan létrejött egy pillanatkép a blob állapot hívása előtt. Ez is vonatkozik egy korábban helyreállíthatóan törölt blob csak, ha egy Blokkblob típusú. Ha egy blob egy másik típusú váltotta fel, az összes meglévő helyreállíthatóan törölt adatok véglegesen lejár. |
| [PUT lap](/rest/api/storageservices/put-page) | Lapblobok | Oldalak számos ír egy Lapblob. | Nincs változás. A rendszer felülírja, vagy nincs bejelölve, a művelet használatával Lapblob adatokat a rendszer nem menti és nem állítható helyre. |
| [Hozzáfűzés letiltása](/rest/api/storageservices/append-block) | Hozzáfűző Blobok | Egy adatblokk ír egy hozzáfűző Blob végéhez | Nincs változás. |
| [A Blob tulajdonságainak megadása](/rest/api/storageservices/set-blob-properties) | Blokkolás, Hozzáfűzés, és Lapblobok | Adja meg az értékeket a megadott blob tulajdonságai. | Nincs változás. Nem állítható helyre, felülírt blob tulajdonságai. |
| [Állítsa be a Blob metaadatai](/rest/api/storageservices/set-blob-metadata) | Blokkolás, Hozzáfűzés, és Lapblobok | Csoportok felhasználó által definiált metaadatok a megadott BLOB egy vagy több név-érték-párokként. | Nincs változás. Felülírt blob metaadatai nem állítható helyre. |

Fontos, és figyelje meg, hogy "Oldal Put" felülírása, vagy törölje a jelet, egy Lapblob címtartományok hívása nem hoz automatikusan létre pillanatképeket. A virtuális gép lemezeinek Lapblobok élvezik, és használja **Put lap** szeretne adatokat írni.

### <a name="recovery"></a>Helyreállítás
Hogy egyszerűbb legyen a törölt adatok helyreállítása, bevezettük az új "Blob törlésének visszavonása" API-t. A Törlés visszavonása API meghívása egy helyreállíthatóan törölt alap BLOB visszaállítja azt, és minden ahhoz kapcsolódó helyreállíthatóan törölt pillanatképek aktívként. Helyreállíthatóan törölt pillanatképek aktívként a törlésének visszavonása API meghívása egy aktív alap BLOB visszaállítások minden társított. Pillanatképek aktív visszaállítását végzi, ha felhasználó által létrehozott pillanatfelvételeket néznek Ezek nem írja felül az alap blob.

Egy blob meghívása adott helyreállíthatóan törölt pillanatkép visszaállítása **Blob törlésének visszavonása** az alap BLOB. Ezt követően másolhatja a pillanatkép a jelenleg aktív BLOB. A pillanatkép egy új blob is másolhatja.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Helyreállíthatóan törölt adatok nem szürke, kék nem aktív adatok esetében. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Itt **Blob törlésének visszavonása** B, ezáltal visszaállítása az alap blob, B1 és minden társított pillanatképet, itt BLOB neve csak B0 aktívként. A második lépésben B0 átmásolt az alap blob. A másolási művelet B1 helyreállíthatóan törölt pillanatképet hoz létre.*

Helyreállíthatóan törölt blobok megjelenítése és a blob-pillanatképekkel, dönthet úgy, hogy a törölt adatokat **Blobok listázása**. Kiválaszthatja, hogy csak helyreállíthatóan törölt alap blobok megtekintéséhez, vagy a helyreállíthatóan törölt tárolóblob-pillanatképek is tartalmazza. Minden helyreállíthatóan törölt adatok az idő, amikor törölve lett az adatok és a hány nap elteltével az adatok véglegesen lejár is megtekintheti.

### <a name="example"></a>Példa
Az alábbiakban látható a konzol kimenete egy .NET-parancsprogram, amely feltölti, felülírja, a pillanatképek, a törlések, és a egy blobot "a HelloWorld" nevű, amikor a helyreállítható törlés visszaállítások be van kapcsolva:

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

Tekintse meg a [további lépések](#next-steps) az alkalmazást, amelyet ez a kimenet előállítása mutató a következő szakaszban.

## <a name="pricing-and-billing"></a>Árak és számlázás
Minden helyreállíthatóan törölt adatok költsége megegyezik a aktív számoljuk fel. Nem kell fizetni adatokat, amelyek a konfigurált megőrzési időszakot követően véglegesen törölve lesz. A pillanatképek és azok előfizetéstípusok hogyan részletesebben megismerni, tekintse meg [ismertetése, hogyan pillanatképek előfizetéstípusok](storage-blob-snapshots.md).

Akkor nem kell fizetnie a pillanatképek periodicitásmutató automatikus előállításához kapcsolatos tranzakciók. Akkor kell fizetnie **Blob törlésének visszavonása** tranzakciók "Írási műveletnek" mellett.

Az Azure Blob Storage díjai az általános további részletekért tekintse meg a [Azure Blob Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/).

Ha kezdetben kapcsolni a helyreállítható törlés, segít jobban megérteni, hogy a szolgáltatás hogyan érinti a számla egy kis megőrzési időszak használatát javasoljuk.

## <a name="quickstart"></a>Első lépések
### <a name="azure-portal"></a>Azure Portal
Helyreállítható törlés engedélyezéséhez lépjen a **helyreállítható törlési** lehetőség **Blob Service**. Kattintson a **engedélyezve** , és meg szeretné őrizni a helyreállíthatóan törölt adatok napok számát adja meg.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Helyreállíthatóan törölt blobok megtekintéséhez jelölje ki a **Show törölt blobok** jelölőnégyzetet.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Egy adott BLOB helyreállíthatóan törölt pillanatképek megtekintéséhez válassza ki a blob, majd kattintson **pillanatképek megtekintése**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **Show törölt pillanatképek** jelölőnégyzet be van jelölve.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Amikor egy helyreállíthatóan törölt blob vagy egy pillanatképet kattint, figyelje meg, hogy az új blob tulajdonságai. Azt jelzik, hogy amikor az objektum törölve lett, és hogy hány nap van hátra, amíg a blob- vagy blob-pillanatkép véglegesen lejárt. Ha a helyreállíthatóan törölt objektum nem egy pillanatképet, akkor törlésének visszavonása azt a lehetőséget is.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy egy blob visszavonás fog is az összes társított visszavonni a pillanatképek törlését. Helyreállíthatóan törölt pillanatképek egy aktív BLOB törlésének visszavonása, kattintson a blobra, majd válassza a **összes pillanatkép törlésének visszavonása**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután visszavonni a pillanatképek egy blobhoz törlését, rákattinthat **előléptetés** pillanatkép másolása a legfelső szintű BLOB, ezáltal a pillanatkép visszaállítása a blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Helyreállítható törlés engedélyezéséhez a blob-ügyfelet szolgáltatás tulajdonságainak frissítése. Az alábbi példa egy alegységénél fiókokat egy előfizetésben a helyreállítható törlés lehetővé teszi:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
A helyreállítható törlés kapcsolta be a következő paranccsal ellenőrizheti:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Véletlenül törölt blobok helyreállítása, hívható meg törölt azokat a blobokat. Ne feledje, hogy a hívó **Blob törlésének visszavonása**, mind az aktív és a helyreállíthatóan törölt blobok visszaállítja az összes társított helyreállíthatóan törölt pillanatképek aktívként. Az alábbi példa a törlés visszavonásának meghívja a minden helyreállíthatóan törölt és aktív tárolóban lévő blobokat:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
A helyreállítható törlés currrent adatmegőrzési házirend megkereséséhez használja a következő parancsot:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>Azure CLI 
Helyreállítható törlés engedélyezéséhez frissítse az ügyfél blob szolgáltatás tulajdonságai:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Ellenőrizze a helyreállítható törlési be van kapcsolva, a következő paranccsal: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Python ügyféloldali kódtár
Helyreállítható törlés engedélyezéséhez frissítse az ügyfél blob szolgáltatás tulajdonságai:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>.NET ügyféloldali kódtár
Helyreállítható törlés engedélyezéséhez frissítse az ügyfél blob szolgáltatás tulajdonságai:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Véletlenül törölt blobok helyreállítása, hívható meg törölt azokat a blobokat. Ne feledje, hogy a hívó **Blob törlésének visszavonása**, mind az aktív és a helyreállíthatóan törölt blobok visszaállítja az összes társított helyreállíthatóan törölt pillanatképek aktívként. Az alábbi példa a törlés visszavonásának meghívja a minden helyreállíthatóan törölt és aktív tárolóban lévő blobokat:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Helyreállít egy adott blob verzióra, először hívható meg törölt egy blobot, majd a kívánt pillanatkép másolja át a blob. Az alábbi példa állítja helyre a legutoljára létrehozott pillanatfelvétel a blokkblobok:

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

## <a name="should-i-use-soft-delete"></a>Helyreállítható törlés érdemes használni?
Ha esély van arra, hogy az adatok akkor véletlenül által módosított vagy törölt egy alkalmazás vagy a storage-fiók egy másik felhasználó, ajánlott bekapcsolni a helyreállítható törlés. Helyreállítható törlés egy adatvédelmi stratégiát egy része, és segít megelőzni a véletlen adatvesztést okoz.

## <a name="faq"></a>GYIK
**Milyen tárolási típus esetében használható a helyreállítható törlés?**  
Jelenleg a helyreállítható törlés csak akkor használható (objektum) a blob Storage.

**Minden storage fióktípus esetében elérhető-e a helyreállítható törlés?**  
Igen, a helyreállítható törlés érhető el a blob storage-fiókok is mint a blobok az általános célú tárfiókok (GPv1 és a GPv2). Ez vonatkozik a standard és prémium szintű fiókok. Helyreállítható törlés felügyelt lemezek nem érhető el.

**Az összes tárolási szinten érhető el a helyreállítható törlés?**  
Igen, a helyreállítható törlés érhető el az összes tárolási rétegek, beleértve a gyakori és ritka elérésű és archív. Azonban nem biztosít a helyreállítható törlés írja felül az archív szinten lévő blobok védelmét.

**Blob szint beállítása API használatával helyreállíthatóan törölt pillanatképekkel rendelkező blobok díjcsomag?**  
Igen. A helyreállíthatóan törölt pillanatképek maradnak az eredeti szinten, de az új csomag az alapszintű blob kerül át. 

**Prémium szintű tárfiókkal rendelkezik egy blob-pillanatkép korlát 100. Tegye helyreállíthatóan törölt pillanatképek beleszámítanak ezt a határt?**  
Nem, törölték a pillanatképek nem beleszámítanak ezt a korlátot.

**Lehet kapcsolni a meglévő tárfiókok a helyreállítható törlés?**  
Igen, a helyreállítható törlés nem konfigurálható meglévő és az új tárfiókok esetében.

**Ha egy teljes fiók vagy a tároló a helyreállítható törlés van kapcsolva, az összes társított blobok menti a rendszer?**  
Nem, ha törli a teljes fiókot vagy a tároló, az összes társított blobok véglegesen törlődik. Megtudhatja, hogyan védheti meg a tárfiók a véletlen törlések, tekintse át az Azure Resource Manager-cikk [erőforrások zárolása a váratlan módosítások megelőzése](../../azure-resource-manager/resource-group-lock-resources.md).

**Megtekintheti a törölt adatok teljesítmény-mérőszámait?**  
Helyreállíthatóan törölt adatok a teljes tárfiók-kapacitás részét képezi. Nyomon követése és figyelése a tárolási kapacitás további információkért tekintse meg a [Storage Analytics](../common/storage-analytics.md) cikk.

**Helyreállítható törlés a kikapcsolása után, ha szeretnék továbbra is képesek lesznek helyreállíthatóan törölt adatok eléréséhez?**  
Igen, akkor is hozzáférhet, és a fennmaradó helyreállíthatóan törölt adatok helyreállítása, ha helyreállítható törlési ki van kapcsolva.

**Olvassa el és másolja ki a BLOB helyreállíthatóan törölt pillanatképek?**  
Igen, de meg kell hívni a törlés visszavonásának a BLOB először.

**Helyreállítható törlés érhető el az összes blob-típusok?**  
Igen, a helyreállítható törlés Blokkblobokat, hozzáfűző Blobok és Lapblobok esetében érhető el.

**Virtuális gépek lemezei számára elérhető-e a helyreállítható törlés?**  
Helyreállítható törlés mind a prémium szintű, mind a standard szintű nem felügyelt lemezek esetén érhető el. Helyreállítható törlés csak segítségével helyreállíthatja a törölt adatok **Blob törlése**, **Put Blob**, **tiltólista Put**, **Blokkelraktározási** és **Blob másolása**. Felülírja a hívás adatok **Put lap** nem állítható helyre.

**Van szükségem a helyreállítható törlés használata a meglévő alkalmazásaimat?**  
Helyreállítható törlés használata API-verziótól függetlenül előnyeinek kihasználása érdekében lehetőség. Azonban listázása és a helyreállíthatóan törölt blobok és a tárolóblob-pillanatképek helyreállítása, szüksége lesz a 2017-07-29 verzióját használja a [a Storage szolgáltatások REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) vagy nagyobb. Általában javasoljuk, hogy mindig a legújabb verzió, függetlenül attól, hogy a ezt a szolgáltatást.

## <a name="next-steps"></a>További lépések
* [.NET mintakód](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Az Azure Storage replikáció](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [RA-GRS használatával magas rendelkezésre álló alkalmazások tervezése](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Mi a teendő az Azure Storage leállása esetén?](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
