---
title: Az Azure Storage blobs (előzetes verzió) világos törlése |} Microsoft Docs
description: Az Azure Storage blob objektumokhoz a helyreállítható Törlés (előzetes verzió) most kínál, így könnyebben helyreállíthatja az adatokat a tévesen módosítani vagy törölni az alkalmazás vagy többi tárolási fiók felhasználói.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 0e728f9f9754d76d893b12309bb52201d772efbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057859"
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Helyreállítható törlésre az Azure Storage blobs (előzetes verzió)

## <a name="overview"></a>Áttekintés

Az Azure Storage blob objektumokhoz a helyreállítható Törlés (előzetes verzió) most kínál, így könnyebben helyreállíthatja az adatokat a tévesen módosítani vagy törölni az alkalmazás vagy többi tárolási fiók felhasználói.

## <a name="how-does-it-work"></a>Hogyan működik?

Ha engedélyezve van, helyreállítható törlésre lehetővé teszi mentéséhez és az adatok helyreállításához, ha blobokkal vagy a blob pillanatképek törlése. Ez a védelem kiterjed blobadatokat, amely egy felülírása miatt törlődik.

A törölt adatok átkerül a ahelyett, hogy véglegesen törölve helyreállíthatóan törölt állapotban. Ha helyreállítható törlésre és található adatok felülírja, enyhe törölt pillanatkép menteni az állapotot a felülírt adatok jön létre. Letölthető törölt objektumok nem láthatók, kivéve, ha explicit módon szerepel a listában.
Beállíthatja, hogy mennyi ideig letölthető a törölt adatokat, helyreállítható, mielőtt végleges lejárt.

Helyreállítható törlésre található visszafelé kompatibilis; ne kelljen végezze el a módosításokat az alkalmazások kihasználni a funkció számára biztosítja a védelmet. Azonban [adat-helyreállítás](#recovery) újdonsága **törlés visszavonása Blob** API.

> [!NOTE]
> Nyilvános előzetes Blob szint beállítása egy blobot a pillanatképek hívása nem engedélyezett.
Helyreállítható törlésre állít elő, az adatok védelme érdekében, amikor a rendszer felülírja azt pillanatképeket. Folyamatosan dolgozunk a a megoldáson pillanatképekkel blobok rétegezéséhez engedélyezéséhez.

### <a name="configuration-settings"></a>Konfigurációs beállítások

Amikor létrehoz egy új fiókot, helyreállítható törlésre alapértelmezés szerint van kapcsolva. Helyreállítható törlésre is alapértelmezés szerint van kapcsolva a meglévő tárfiókok. A szolgáltatás be- és kikapcsolását a tárfiók élettartama során bármikor válthat.

Is hozzáférhetnek, és véglegesen törölt adatok helyreállítása a funkció ki van kapcsolva, feltéve, hogy véglegesen a törölt adatokat lett mentve, ha a szolgáltatás korábban be lett kapcsolva. Ha bekapcsolja a helyreállítható törlés, akkor is konfigurálnia kell a megőrzési időn.

A megőrzési időtartam azt jelzi, hogy mennyi ideig, amely letölthető a törölt adatokat tárolt és rendelkezésre a helyreállításhoz. A blobok és explicit módon törölt blob pillanatképeket a megőrzési időszak óra kezdődik, amikor az adatok törlése. Letölthető törölt pillanatkép-készítési adatokat a rendszer felülírja a helyreállítható törlés funkció által generált az óra kezdődik, amikor a pillanatkép jön létre. Jelenleg őrizheti meg 1 és 365 nap között szoftveres törölt adat.

A helyreállítható törlés megőrzési időszak bármikor módosíthatja. Újonnan törölt adatok csak egy frissített megőrzési időszak vonatkozik. Korábban törölt adatok alapján fog járni a a megőrzési időszakot adott meg, amikor az adott adat törölve lett.

### <a name="saving-deleted-data"></a>A törölt adatok mentése

A helyreállítható törlés blobokkal vagy a blob pillanatképek törölték vagy felül, ha sok esetben megőrzi.

Blob van felülírása használatával **Put Blob**, **Put blokk**, **Put tiltólista** vagy **másolási Blob** a blob-ről a pillanatképet a az írási művelet automatikusan jön létre. A pillanatkép enyhe törölt pillanatkép; nem látható, kivéve, ha letölthető törölt objektumok explicit módon vannak felsorolva. Tekintse meg a [helyreállítási](#recovery) részből megtudhatja, hogyan listázhat puha törölt objektumokat.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Letölthető a törölt adatokat szürke, addig, amíg aktív adatforrás kék. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Amikor B0 B1 felülírja, B0 törölt enyhe pillanatképe jön létre. Ha B1 K2 felülírja, B1 törölt enyhe pillanatképe jön létre.*

> [!NOTE]
> Helyreállítható törlésre csak számára biztosítja a másolási műveletek védelmi felülírja, ha van kapcsolva a cél blob-fiók.

> [!NOTE]
> Helyreállítható törlésre nem biztosít felülírja az archív rétegében a blobok védelmét. Az archívumban blob egy új blob bármely réteg felülírja, ha a felülírt blob véglegesen lejárt.

Ha **Blob törlése** nevezik pillanatkép, az adott pillanatkép van megjelölve, enyhe törölték. Új pillanatkép nem jön létre.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Letölthető a törölt adatokat szürke, addig, amíg aktív adatforrás kék. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Ha **pillanatkép Blob** nevű B0 pillanatkép válik, és B1 BLOB aktív állapotú. A B0 pillanatkép törlését, ha meg van jelölve, enyhe törölték.\*

Ha **Blob törlése** (a blob-, amely nem maga pillanatkép) Alap blob, metódust, hogy a blob van megjelölve, enyhe törölték. A korábbi viselkedése konzisztens hívása **Blob törlése** hibát ad vissza a blob, amely aktív pillanatképekkel rendelkezik. Hívása **Blob törlése** enyhe törölt pillanatképekkel blob a hibát nem adott vissza. Helyreállítható törlésre bekapcsolásakor blob és egyetlen műveletben a pillanatképek is törölheti. A kiinduló blob így jelöli meg, és véglegesen, a pillanatképeket törölni.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Letölthető a törölt adatokat szürke, addig, amíg aktív adatforrás kék. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Itt egy **Blob törlése** kezdeményezték K2 és minden társított pillanatképet törölni. Az aktív blob, K2 és minden társított pillanatképet megjelölve a helyreállítható törlése.\*

> [!NOTE]
> Letölthető törölt blob felülírja a rendszer, amikor a rendszer automatikusan előállítja a blob állapota az írási művelet előtt törölt enyhe pillanatképe. Az új blob örökli a réteg a felülírt BLOB.

Helyreállítható törlésre nem menteni az adatokat tároló vagy a fiók törlése, illetve ha blob-metaadatok, és a blob tulajdonságai a rendszer felülírja. Hibás törlés elleni védelem egy tárfiókot, a zárolás, az Azure Resource Manager használatával is konfigurálhat. Az Azure Resource Manager cikke [zárolási erőforrások váratlan változások tiltása](../../azure-resource-manager/resource-group-lock-resources.md) további.

Az alábbi táblázat részletezi az elvárt viselkedés helyreállítható törlésre bekapcsolása:

| REST API-művelet                                                                                                | Erőforrás típusa                 | Leírás                                                                                                 | A viselkedés módosítása                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Törlés](/rest/api/storagerp/StorageAccounts/Delete)              | Fiók                       | Törli a tárfiókot, beleértve a tárolók és a benne található blobokat.                           | Nincs változás. Nincsenek helyreállítható tárolók és blobok a törölt fiók.                                                                                                                                                                                                                                                                                          |
| [Törli a tárolót](/rest/api/storageservices/fileservices/delete-container)       | Tároló                     | Törli a tárolót, beleértve az összes benne található blobokat.                                                | Nincs változás. A törölt tárolóban lévő blobok nincsenek helyreállítható.                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | Blokk, hozzáfűzése, és Lapblobok | Létrehoz egy új blob vagy a felváltja egy meglévő blob a tárolóban                                          | Cserélje ki egy meglévő blob használatával, ha a rendszer automatikusan előállítja a pillanatkép készítése a blob állapota a hívás előtt. Ez is vonatkozik egy korábban véglegesen törölve blob csak, ha azonos típusú (blokk, hozzáfűző vagy lapon) blob váltotta fel. Ha más típusú blob váltotta fel, az összes meglévő szoftveres törölt adat véglegesen lejár. |
| [Delete Blob](/rest/api/storageservices/fileservices/delete-blob)                 | Blokk, hozzáfűzése, és Lapblobok | Egy blob vagy a blob pillanatkép törlésre jelöli meg. A blob vagy a pillanatképet később törlődik a szemétgyűjtés során | Ha a használt blob pillanatkép, a pillanatkép van megjelölve a helyreállítható törlés törlődnek. Blobok törléséhez használatakor, hogy a blob van megjelölve, enyhe törölték.                                                                                                                                                                                                                           |
| [A Blob másolása](/rest/api/storageservices/fileservices/copy-blob)                     | Blokk, hozzáfűzése, és Lapblobok | Másolja át a forrás blob egy forrásblobot ugyanazt a tárfiókot vagy egy másik tárfiókhoz.       | Cserélje ki egy meglévő blob használatával, ha a rendszer automatikusan előállítja a pillanatkép készítése a blob állapota a hívás előtt. Ez is vonatkozik egy korábban véglegesen törölve blob csak, ha azonos típusú (blokk, hozzáfűző vagy lapon) blob váltotta fel. Ha más típusú blob váltotta fel, az összes meglévő szoftveres törölt adat véglegesen lejár. |
| [PUT letiltása](/rest/api/storageservices/put-block)                                  | Blokkblobok                   | Létrehoz egy új blokkot a blokkblob a véglegesítése.                                                | Ha használja a blokkolás véglegesítése blob, amely aktív nincs változás. Használja a blokkolás véglegesítése egy blobot, amely véglegesen törölve, ha egy új blob jön létre, és a helyreállítható törölt blob állapotának rögzítéséhez automatikusan létrejött egy pillanatkép.                                                                                                                      |
| [PUT tiltólista](/rest/api/storageservices/fileservices/put-block-list)           | Blokkblobok                   | Egy blob érvényesítése blokk azonosító alkotó a blokkblob készletét megadásával.                             | Cserélje ki egy meglévő blob használatával, ha a rendszer automatikusan előállítja a pillanatkép készítése a blob állapota a hívás előtt. Ez is vonatkozik egy korábban véglegesen törölve blob csak, ha a Blokkblob. Ha más típusú blob váltotta fel, az összes meglévő szoftveres törölt adat véglegesen lejár.                                                |
| [Helyezze a lap](/rest/api/storageservices/fileservices/put-page)                       | Lapblobok                    | Lapok számos írja az oldalakra vonatkozó Blob.                                                                     | Nincs változás. Felülírja a rendszer, vagy ezzel a művelettel törli az oldalakra vonatkozó Blob adatokat a rendszer nem menti és nem állítható helyre.                                                                                                                                                                                                                                                   |
| [Blokk hozzáfűzése](/rest/api/storageservices/fileservices/append-block)               | Hozzáfűző blobokat                  | Egy adatblokk ír egy hozzáfűző Blob végéhez                                                         | Nincs változás.                                                                                                                                                                                                                                                                                                                                                           |
| [A Blob tulajdonságainak beállítása](/rest/api/storageservices/fileservices/set-blob-properties) | Blokk, hozzáfűzése, és Lapblobok | Beállítja a megadott blob rendszer tulajdonságok értékeit.                                                       | Nincs változás. Felülírt blob tulajdonságai nincsenek helyreállítható.                                                                                                                                                                                                                                                                                                          |
| [Állítsa be a Blob metaadatai](/rest/api/storageservices/fileservices/set-blob-metadata)     | Blokk, hozzáfűzése, és Lapblobok | Készletek felhasználó által definiált metaadatok a megadott BLOB egy vagy több név-érték párként.                          | Nincs változás. Felülírt blob metaadatai nem helyreállítható.                                                                                                                                                                                                                                                                                                             |

Fontos figyelje meg, hogy hívása "Page Put" felülírása vagy a tartomány az oldalakra vonatkozó Blob törlése nem automatikusan létrehoz pillanatképeket. Virtuális gépek lemezeit Lapblobokat üzemelnek, és használjon **Put lap** adatokat írni.

### <a name="recovery"></a>Helyreállítás

Könnyebb a törölt adatokat helyreállítani, azt vezettek be egy új "Törlésének visszavonása Blob" API-t. A Törlés visszavonása API hívása a törölt letölthető alapszintű blob visszaállítja, valamint az összes kapcsolód aktívnak enyhe törölt pillanatképeket. A Törlés visszavonása API hívása egy aktív alap blob visszaállítások minden kapcsolódó aktívnak enyhe törölt pillanatképeket. Pillanatképek visszaállításának aktívnak, ha néznek a felhasználó által létrehozott pillanatképek; a kiinduló blob nem felülírja.

Egy blob hívása adott enyhe törölt pillanatkép visszaállítása **törlés visszavonása Blob** alap blobot. Ezt követően a pillanatkép másolhatja a jelenleg aktív blob keresztül. A pillanatkép egy új blob is másolhatja.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Letölthető a törölt adatokat szürke, addig, amíg aktív adatforrás kék. Több legutóbb írt adatok régebbi adatok alatt jelenik meg. Itt **törlés visszavonása Blob** blob B, ezáltal visszaállítása az alap blob B1 és minden társított pillanatképet, itt csupán B0 aktívnak metódust. A második lépésben B0 felülírt az alap blob. A másolási művelet B1 törölt enyhe pillanatképe állít elő.\*

Letölthető törölt blobok megtekintéséhez, és a blob-pillanatképeket, ha szeretné, a törölt adatokat **lista Blobok**. Kiválaszthatja, hogy csak enyhe törölt alap blobok megjelenítéséhez, vagy letölthető törölt blob pillanatképek is tartalmazza. Az összes szoftveres törölt adat megtekintheti az idő, amikor törölve lett az adatokat továbbá a hány nap elteltével az adatok véglegesen lejár.

### <a name="example"></a>Példa

A következő .NET parancsfájl, amely feltölti, felülírja, pillanatképeket, törli a konzol kimeneti, és "a HelloWorld" nevű, ha letölthető blob törlése visszaállítások be van kapcsolva:

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

Tekintse meg a [további lépések](#Next steps) az alkalmazást, amely a kimenetet előállított mutató szakaszát.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes szoftveres törölt adat aktív adatként azonos ütemben lesz számlázva. Nem kell fizetnie az adatokat, az véglegesen törlődni fog a konfigurált megőrzési időszak letelte után. A pillanatképek és díjak ütemezésének hogyan mélyebb bemutatója, lásd: [megértése, hogyan pillanatképek keletkeznek költségek](storage-blob-snapshots.md).

Nem a számlázás történik a automatikus létrehozását a pillanatképek kapcsolatos tranzakciók. A számlázott **törlés visszavonása Blob** az "Írási műveletek" díj tranzakciók.

Általában az Azure Blob Storage árak a további részletekért tekintse meg a [Azure Blob Storage árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/storage/blobs/).

Kezdetben a bekapcsolásakor helyreállítható törlésre, a mélyebb megismeréséhez, hogy a szolgáltatás milyen hatással van a számlázási kis megőrzési időtartamot használatát javasoljuk.

## <a name="quickstart"></a>Első lépések

### <a name="azure-portal"></a>Azure Portal
Ahhoz, hogy a helyreállítható törlés, navigáljon a **helyreállítható törlésre** lehetőség alatt **Blob szolgáltatás**. Kattintson a **engedélyezve** , és meg szeretné őrizni a szoftveres törölt adat napok számát adja meg.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Válassza ki, ha véglegesen törölve blobok a **megjelenítése blobok törlése** jelölőnégyzetet.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

A megadott blob enyhe törölt pillanatképek megtekintéséhez válassza ki a blob, majd kattintson az **pillanatképek megtekintéséhez**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **megjelenítése a pillanatképeket törölni** jelölőnégyzet be van jelölve.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ha véglegesen törölve blob vagy pillanatkép gombra kattint, figyelje meg, az új blob tulajdonságai. Ha az objektum törölve lett, és hány napig marad mindaddig, amíg a blob vagy a blob pillanatkép véglegesen lejárt utal. Ha a letölthető törölt objektum nem pillanatkép, akkor törlésének visszavonása azt a lehetőséget is.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy a blob visszavonás fog is törlés visszavonása minden társított pillanatképet. Törlés visszavonása egy aktív BLOB enyhe törölt pillanatképeket, kattintson a blob, és válassza **törlés visszavonása minden pillanatképet**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután egy blob pillanatképek törlés visszavonása, rákattinthat **előléptetés** a pillanatkép másolja át a legfelső szintű blob, ezáltal a pillanatkép visszaállítása a blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Ahhoz, hogy a helyreállítható törlés, a blob-ügyfél szolgáltatás tulajdonságainak frissítése. A következő példa engedélyezi az előfizetés fiókok egy részéhez helyreállítható törlésre:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Blobok véletlenül törölt helyreállításához hívható meg törölt ezeket a blobokat. Ne feledje, hogy a hívó **törlés visszavonása Blob**, mind a törölt blobok aktív és helyreállítható, fog állítson vissza egy aktívnak minden társított enyhe törölt pillanatképet. Az alábbi példa meghívja a törölt minden enyhe törölt és aktív a tárolóban lévő blobok:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Python ügyféloldali kódtár

Ahhoz, hogy a helyreállítható törlés, a blob-ügyfél szolgáltatás tulajdonságainak frissítése:

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

Ahhoz, hogy a helyreállítható törlés, a blob-ügyfél szolgáltatás tulajdonságainak frissítése:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Blobok véletlenül törölt helyreállításához hívható meg törölt ezeket a blobokat.
Ne feledje, hogy a hívó **törlés visszavonása Blob**, mind a törölt blobok aktív és helyreállítható, fog állítson vissza egy aktívnak minden társított enyhe törölt pillanatképet. Az alábbi példa meghívja a törölt minden enyhe törölt és aktív a tárolóban lévő blobok:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Helyreállítás egy adott blob verzióra, először hívható meg törölt blob, majd a kívánt pillanatkép másolja át a blob. A következő példa egy blokkblob a legutóbb létrehozott pillanatfelvétel állítja helyre:

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

## <a name="should-i-use-soft-delete"></a>Helyreállítható törlésre érdemes használni?

Ha az adatok véletlenül módosított vagy törölt egy alkalmazás vagy egy másik tárolási fiókot felhasználó esély van, ajánlott bekapcsolja a helyreállítható törlés.
Helyreállítható törlésre data protection stratégia egyik összetevője, és segít megelőzni a véletlen adatvesztést okoz.

## <a name="faq"></a>GYIK

**Milyen tárolási típusok használhatók helyreállítható törlésre?**

Helyreállítható törlésre jelenleg csak blobtárolóba (objektum) érhető el.

**Helyreállítható törlésre érhető el tárhely minden fióktípus esetében?**

Igen, helyreállítható törlésre érhető el blob storage-fiókok is blobot, amely az általános célú tárfiókok esetében. Ez a standard és a prémium szintű fiókok vonatkozik. Helyreállítható törlésre felügyelt lemezek nem érhető el.

**Helyreállítható törlésre érhető el az összes tárolási rétegek?**

Igen, a helyreállítható törlésre esetén minden beleértve a gyakran használt adatok, a ritkán használt adatok tárolási rétegek és archiválási érhető el. Azonban nem biztosít a helyreállítható törlés felülírja az archív rétegében a blobok védelmét.

**Prémium szintű storage-fiókok rendelkezik egy blob pillanatkép korlát 100. Letölthető törölt pillanatképek számolják felé ezt a határt?**

Nem törölték a pillanatképek nem számítanak felé ezt a határt.

**Be lehet kapcsolni a meglévő tárfiókok helyreállítható törlésre?**

Igen, a helyreállítható törlés lehet konfigurálni a meglévő és új storage-fiókok.

**Ha egy teljes fiók vagy a tároló az engedélyezve van a helyreállítható törlés, a rendszer összes társított BLOB menti?**

Nem, ha törli egy teljes fiók vagy a tároló, az összes társított BLOB véglegesen törlődik. Megtudhatja, hogyan védi meg a tárfiók a véletlen törlések, tanulmányozza az Azure Resource Manager [zárolási erőforrások váratlan változások tiltása](../../azure-resource-manager/resource-group-lock-resources.md).

**Megtekintheti a teljesítmény-mérőszámait a törölt adatokat?**

Szoftveres törölt adat szerepel a teljes tárfiókok kapacitásával részeként. Nyomon követése, és a tárolási kapacitás ellenőrzésére vonatkozó további információkért lásd: a [tárolási analitika](../common/storage-analytics.md) cikk.

**Helyreállítható törlésre kikapcsolni, ha szeretnék is letölthető törölt adatok eléréséhez?**

Igen, akkor is hozzáférhetnek, és a fennmaradó enyhe törölt adatok helyreállítása helyreállítható törlésre ki van kapcsolva.

**Olvassa el és másolja ki a BLOB enyhe törölt pillanatképeket?**

Igen, de meg kell hívnia törölt blobot először.

**Helyreállítható törlésre érhető el az összes blob-típusok?**

Igen, a helyreállítható törlés áll rendelkezésre a Blokkblobokat, hozzáfűző blobokat és Lapblobokat.

**Helyreállítható törlésre érhető el a virtuálisgép-lemezeket?**

Prémium szintű és a szabványos nem felügyelt lemezek helyreállítható törlésre érhető el. Helyreállítható törlésre csak segít törölte adatok helyreállítása **Blob törlése**, **Put Blob**, **Put tiltólista**, **Put blokk** és **Blob másolása**. Adatok hívásával felül **Put lap** nincs helyreállítható.

**Kell a meglévő alkalmazásaimat a helyreállítható törlés használni?**

Úgy is használ az API-verziótól függetlenül helyreállítható törlésre előnyeit. Azonban listában, és véglegesen törölve blobok és blob pillanatképek helyreállítása, szüksége lesz 2017-07-29 verzióját használja a [Storage szolgáltatások REST API felülete](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) vagy nagyobb. Általánosságban elmondható mindig ajánlott függetlenül attól, hogy ez a funkció a legújabb verzióját használja.

## <a name="next-steps"></a>További lépések

* [.NET mintakód](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Blob Service REST API](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Az Azure Storage replikáció](../common/storage-redundancy.md)

* [RA-GRS használatával magas rendelkezésre álló alkalmazások megtervezése](../common/storage-designing-ha-apps-with-ragrs.md)

* [Mi a teendő, ha egy Azure Storage kimaradás során](../common/storage-disaster-recovery-guidance.md)
