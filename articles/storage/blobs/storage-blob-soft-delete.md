---
title: Soft DELETE az Azure Storage-Blobok számára | Microsoft Docs
description: Az Azure Storage mostantól lágy törlést biztosít a blob-objektumokhoz, így könnyebben lehet helyreállítani az adatokat, amikor az alkalmazás vagy más Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b0a03eee06ba114ab929c8c584f382861a006bbc
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360758"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Az Azure Storage-Blobok Soft delete
Az Azure Storage mostantól lágy törlést biztosít a blob-objektumokhoz, így könnyebben lehet helyreállítani az adatokat, amikor az alkalmazás vagy más Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.

## <a name="how-does-it-work"></a>Hogyan működik?
Ha be van kapcsolva, a Soft delete lehetővé teszi az adatok mentését és helyreállítását, amikor blobokat vagy blob-pillanatképeket törölnek. Ez a védelem a felülírás eredményeként törölt blob-adatra terjed ki.

Ha az adat törlődik, a rendszer véglegesen törli a törölt állapotot a végleges törlés helyett. Ha a Soft delete be van kapcsolva, és felülírja az adatok felülírását, akkor a rendszer egy helyreállított pillanatképet hoz létre, amely a felülírt adatok állapotát menti. A helyreállított törölt objektumok csak akkor láthatók, ha explicit módon vannak felsorolva. Beállíthatja, hogy a rendszer mennyi ideig legyen helyreállítva, amíg véglegesen lejár.

A Soft delete visszafelé kompatibilis; nem kell módosítania az alkalmazásokat, hogy kihasználhassa a szolgáltatás által nyújtott védelem előnyeit. Az [Adathelyreállítás](#recovery) azonban bevezeti a blob API új **törlését** .

### <a name="configuration-settings"></a>Konfigurációs beállítások
Új fiók létrehozásakor a Soft delete alapértelmezés szerint ki van kapcsolva. A meglévő Storage-fiókok esetében a Soft delete is alapértelmezés szerint ki van kapcsolva. A funkciót be-és kikapcsolhatja a Storage-fiók élettartama során.

Ha a szolgáltatás ki van kapcsolva, továbbra is elérheti és helyreállíthatja a puha törölt adatokat, feltéve, hogy a szolgáltatás korábban be lett mentve. Ha bekapcsolja a helyreállítható törlést, a megőrzési időtartamot is konfigurálnia kell.

A megőrzési időtartam azt jelzi, hogy a rendszer mennyi időt tárol és biztosít a helyreállítható adatok tárolásához. A explicit módon törölt blobok és blob-Pillanatképek esetén a megőrzési időszak órája az adatok törlésekor kezdődik. Az adatok felülírása esetén a Soft delete funkció által generált törölt Pillanatképek esetében az óra a pillanatkép létrehozásakor elindul. Jelenleg az 1 és 365 nap közötti, lágyan törölt adat is megtartható.

Bármikor módosíthatja a helyreállítható törlés megőrzési időtartamát. A frissített megőrzési idő csak az újonnan törölt adatokra lesz érvényes. A korábban törölt adatok az adatok törlésekor megadott megőrzési időtartam alapján lejárnak. A helyreállított törölt objektumok törlésére tett kísérlet nem befolyásolja a lejárati idejét.

### <a name="saving-deleted-data"></a>Törölt adattárolók mentése
A helyreállítható törléssel a rendszer sok esetben megőrzi az adatait, ahol a blobokat vagy a blob-pillanatképeket törli vagy felülírja.

Ha a blob a **put blob**használatával van felülírva, a **Letiltás**, a **Letiltás** vagy a **blob másolása** a blob állapotát az írási művelet előtt, automatikusan létrejön egy pillanatkép. Ez a pillanatkép egy Soft Deleted pillanatkép; csak akkor látható, ha a Soft Deleted Objects explicit módon szerepel. A helyreállított objektumok listázásával kapcsolatos információkért tekintse meg a [helyreállítás](#recovery) szakaszt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Ha a B0 felülírja a B1-sel, a rendszer létrehoz egy, a B0 készült, törölt pillanatképet. Ha a B1 felülíródik a B2-mel, a rendszer létrehoz egy, a B1-ről készült, törölt pillanatképet.*

> [!NOTE]  
> A helyreállítható törlés csak a másolási műveletek felülírását teszi elérhetővé, ha a cél blob fiókjához be van kapcsolva.

> [!NOTE]  
> A helyreállítható törlés nem teszi elérhetővé a Blobok felülírásának védelmét az archív szinten. Ha az archívumban található blobot egy új blobtal felülírják bármely szinten, a felülírt blob véglegesen lejár.

Ha  a törlési blobot pillanatképként hívja meg, akkor a pillanatképet a rendszer nem töröltként jelöli meg. Nem jön létre új pillanatkép.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Ha a **Pillanatkép-blob** hívása megtörténik, a B0 a blob aktív állapota lesz. A B0-pillanatkép törlését követően a rendszer törli a jelölést.*

Ha  a törlési blobot egy alapblobra hívja (bármely olyan blob, amely nem pillanatkép), akkor a blobot a rendszer a töröltként jelöli meg. A korábbi viselkedéssel összhangban  az aktív pillanatképekkel rendelkező Blobok törlésének meghívása hibát jelez. A **blob törlésének** meghívása egy blobon, ha a törölt Pillanatképek nem adnak vissza hibát. A blobokat és az összes pillanatképét egyetlen művelettel törölheti, ha a Soft delete be van kapcsolva. Így az alap blob és a pillanatképek nem törlődnek.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Itt törölheti  a törlési blobot, hogy törölje a B2-et és az összes kapcsolódó pillanatképet. A rendszer az aktív blobot, a B2-et és az összes kapcsolódó pillanatképet törölve jelöli.*

> [!NOTE]  
> Ha a rendszer felülírja a törölt blobokat, a rendszer automatikusan létrehoz egy, a blob állapotára vonatkozó, az írási művelet előtt törölt pillanatképet. Az új blob örökli a felülírt blob szintjét.

A helyreállítható törlés nem menti az adatokat tároló vagy fiók törlése esetén, sem a blob metaadatainak és a blob tulajdonságainak felülírásával. Ha a Storage-fiókot helytelen törléssel kívánja biztosítani, a Azure Resource Manager használatával is beállíthat zárolást. A [váratlan változtatások elkerülése](../../azure-resource-manager/resource-group-lock-resources.md) érdekében tekintse meg az erőforrások zárolásának Azure Resource managerét ismertető cikket.

A következő táblázat részletesen ismerteti a Soft delete bekapcsolásakor elvárt viselkedést:

| REST API művelet | Erőforrás típusa | Leírás | Változás a viselkedésben |
|--------------------|---------------|-------------|--------------------|
| [Törlés](/rest/api/storagerp/StorageAccounts/Delete) | Fiók | Törli a Storage-fiókot, beleértve a benne található összes tárolót és blobot.                           | Nincs változás. A törölt fiókban lévő tárolók és Blobok nem lesznek helyreállítva. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Tároló | Törli a tárolót, beleértve a benne található összes blobot is. | Nincs változás. A törölt tárolóban lévő Blobok nem lesznek helyreállítva. |
| [Put Blob](/rest/api/storageservices/put-blob) | Blobok letiltása, hozzáfűzése és lapja | Új blob létrehozása vagy egy meglévő blob cseréje egy tárolón belül | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az és csak akkor, ha az azonos típusú blobtal (blokk, Hozzáfűzés vagy oldal) van lecserélve. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Blobok letiltása, hozzáfűzése és lapja | A törléshez a blob vagy a blob pillanatképét jelöli. A blob vagy pillanatkép később törlődik a Garbage gyűjtemény során | Ha egy blob-pillanatkép törlésére használatos, a pillanatképet a rendszer a törlésre kijelöltként jelöli meg. Ha egy blob törlésére használatos, a blob törlésre kerül. |
| [BLOB másolása](/rest/api/storageservices/copy-blob) | Blobok letiltása, hozzáfűzése és lapja | Egy forrás blobot másol egy célként megadott blobba ugyanabban a Storage-fiókban vagy egy másik Storage-fiókban. | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az és csak akkor, ha az azonos típusú blobtal (blokk, Hozzáfűzés vagy oldal) van lecserélve. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Put blokk](/rest/api/storageservices/put-block) | Blokkblobok | Létrehoz egy új blokkot, amelyet egy blokk blob részeként kíván véglegesíteni. | Ha egy blokknak az aktív blobhoz való elvégzésére van szükség, nincs változás. Ha egy blokkot töröl egy olyan blobra, amely nem törlődik, egy új blob jön létre, és a rendszer automatikusan létrehoz egy pillanatképet a helyreállított blob állapotának rögzítéséhez. |
| [Tiltási lista](/rest/api/storageservices/put-block-list) | Blokkblobok | Egy blobot véglegesít a blokk-blobot alkotó blokk-azonosítók megadásával. | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az egy blokkos blob. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Oldal elhelyezése](/rest/api/storageservices/put-page) | Lapblobok | Különböző lapokat ír egy oldal Blobba. | Nincs változás. A rendszer nem menti az oldal blobjának a művelettel felülírt vagy törölt lapjait, és nem helyreállítható. |
| [Blokk hozzáfűzése](/rest/api/storageservices/append-block) | Blobok hozzáfűzése | Adatblokkot ír egy hozzáfűző blob végére | Nincs változás. |
| [BLOB tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties) | Blobok letiltása, hozzáfűzése és lapja | Beállítja a blobhoz definiált Rendszertulajdonságok értékeit. | Nincs változás. A felülírt blob-tulajdonságok nem helyreállítható. |
| [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata) | Blobok letiltása, hozzáfűzése és lapja | A megadott blobhoz tartozó felhasználó által definiált metaadatokat állítja be egy vagy több név-érték párokként. | Nincs változás. A felülírt blob-metaadatok nem helyreállítható. |

Fontos megjegyezni, hogy a "Put Page" meghívásával felülírhatja vagy törölheti az oldal blobjának tartományait, nem fog automatikusan pillanatképeket létrehozni. A virtuálisgép-lemezeket a Blobok végzik, és a **put Page** használatával írhatnak be adatbevitelt.

### <a name="recovery"></a>Helyreállítás
A törölt adatok helyreállításának egyszerűbbé tétele érdekében bevezetjük a "blob törlésének visszavonása" API-t. Ha a törlési API-t egy puha törölt alapblobon hívja vissza, a rendszer visszaállítja azt, és az összes kapcsolódó, helyreállított törölt pillanatkép aktívként működik. Ha a törlési API-t egy aktív alapszintű blobon hívja meg, az összes társított, törölt pillanatképet aktívként állítja vissza. Ha a pillanatképek aktívként lettek visszaállítva, a felhasználó által létrehozott pillanatképeket hasonlítják, nem írják felül az alap blobot.

Ha egy blobot egy adott helyreállítható törölt pillanatképre szeretne visszaállítani  , akkor a blob törlését az alap blobon is meghívhatja. Ezután átmásolhatja a pillanatképet a most aktív blobon keresztül. A pillanatképet egy új blobba is másolhatja.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Itt a **blob törlésének** visszavonása a B blobon történik, így az alap blobot, a B1-t és az összes kapcsolódó pillanatképet – itt csak B0 – aktívként kell visszaállítani. A második lépésben a B0 a rendszer az alap blobon másolja át. Ez a másolási művelet létrehoz egy, a B1-ről készült, törölt pillanatképet.*

A törölt blobok és blob-Pillanatképek megtekintéséhez dönthet úgy, hogy a törölt fájlokat is belefoglalja a **List blobba**. Dönthet úgy, hogy csak a nem megfelelően törölt alapblobokat vagy a törölt Blobok pillanatképeit is megtekinti. Az összes nem törölt adattal kapcsolatban megtekintheti az adattörlés időpontját, valamint azt, hogy hány nap elteltével kell véglegesen lejár az adatmennyiség.

### <a name="example"></a>Példa
A következő egy olyan .NET-parancsfájl konzoljának kimenete, amely feltölti, felülírja, pillanatképeket, töröl és visszaállítja a "HelloWorld" nevű blobot, ha a Soft delete be van kapcsolva:

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

A kimenetet előkészítő alkalmazásra mutató hivatkozásról a [következő lépések](#next-steps) című szakaszban olvashat.

## <a name="pricing-and-billing"></a>Árak és számlázás
A rendszer az aktív adatforgalommal megegyező sebességgel számítja fel az összes helyreállított törlési értéket. A beállított megőrzési időtartam után véglegesen törölt adatokért nem számítunk fel díjat. A pillanatképek mélyebb megismeréséhez és a díjak elsajátításához lásd: a [Pillanatképek felmerülésének ismertetése](storage-blob-snapshots.md).

A pillanatképek automatikus generálásával kapcsolatos tranzakciókért nem számítunk fel díjat. A **blob** -tranzakciók törlésének visszavonása a "Write Operations" (írási műveletek) arányban történik.

Az Azure Blob Storage díjszabásával kapcsolatos további információkért tekintse meg az [azure blob Storage díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/blobs/).

Amikor először kapcsolja be a Soft delete szolgáltatást, javasoljuk, hogy használjon egy kis megőrzési időtartamot, hogy jobban megértse, hogyan befolyásolja a szolgáltatás a számlát.

## <a name="quickstart"></a>Gyors üzembe helyezés
### <a name="azure-portal"></a>Azure Portal
Ha engedélyezni szeretné a törlést, navigáljon a **Soft delete** lehetőségre a **blob Service**-ben. Ezután kattintson az **engedélyezve** lehetőségre, és adja meg, hogy hány nap elteltével szeretné megőrizni a nem kötelezően törölt adatmennyiséget.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

A nem kötelezően törölt Blobok megtekintéséhez jelölje be a **törölt Blobok megjelenítése** jelölőnégyzetet.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Egy adott blobhoz tartozó, törölt Pillanatképek megtekintéséhez válassza ki a blobot, majd kattintson a **Pillanatképek megtekintése**elemre.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **törölt Pillanatképek megjelenítése** jelölőnégyzet be van jelölve.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ha a törölt blobra vagy pillanatképre kattint, figyelje az új blob tulajdonságait. Ezek azt jelzik, hogy mikor lett törölve az objektum, és hány nap van hátra, amíg a blob vagy a blob pillanatképe véglegesen lejár. Ha a helyreállított törölt objektum nem pillanatkép, akkor lehetősége van törölni a törlést is.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy a Blobok törlésének visszavonása törli az összes társított pillanatképet is. Ha törölni szeretné a nem törölt pillanatképeket egy aktív blobhoz, kattintson a blobra, és válassza az **összes pillanatkép törlésének**visszavonása lehetőséget.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután visszavonta egy blob pillanatképének törlését, az **előléptetés** gombra kattintva másolhatja a pillanatképet a gyökérkönyvtárba, így visszaállíthatja a blobot a pillanatképre.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait. A következő példa lehetővé teszi a Soft delete használatát egy előfizetésben lévő fiókok egy részhalmaza számára:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
A következő parancs használatával ellenőrizheti, hogy a Soft delete be van-e kapcsolva:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

A véletlenül törölt Blobok helyreállításához hívja meg a törlést a blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok törlésének visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a törlést a tároló összes lágy törölt és aktív blobján:
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

### <a name="azure-cli"></a>Azure CLI 
A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

A Soft delete bekapcsolásának ellenőrzéséhez használja a következő parancsot: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Python ügyféloldali kódtár
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

### <a name="net-client-library"></a>.NET ügyféloldali kódtár
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

A véletlenül törölt Blobok helyreállításához hívja meg a törlést a blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok törlésének visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a törlést a tároló összes lágy törölt és aktív blobján:

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

## <a name="are-there-any-special-considerations-for-using-soft-delete"></a>Vannak a Soft delete használatának speciális szempontjai?
Ha egy alkalmazás vagy egy másik Storage-fiók felhasználója véletlenül módosította vagy törölte az adatait, javasoljuk, hogy kapcsolja be a Soft delete szolgáltatást. Ha engedélyezi a gyakori felülírású adatok törlését, megnövelheti a tárolási kapacitást, és a Blobok listázása nagyobb késést eredményezhet. Ezt úgy csökkentheti, ha a gyakran felülírt, különálló Storage-fiókban tárolja a letiltott törlést. 

## <a name="faq"></a>GYIK
**Milyen tárolási típusok esetén használhatom a Soft delete szolgáltatást?**  
A Soft delete jelenleg csak blob (Object) tárolóhoz érhető el.

**Az összes Storage-fióktípus esetében elérhető-e a helyreállítható törlés?**  
Igen, a Soft delete elérhető a blob Storage-fiókokhoz, valamint az általános célú blobokhoz (mind a GPv1, mind a GPv2). Ez a standard és a prémium szintű fiókok esetében is érvényes. A Soft delete nem érhető el a felügyelt lemezeken.

**Az összes tárolási szinten elérhető-e a Soft delete?**  
Igen, a Soft delete minden tárolási réteghez elérhető, beleértve a gyakori, a ritka elérésű és az archív csomagokat is. A Soft delete azonban nem teszi elérhetővé a Blobok felülírásának védelmét az archiválási szinten.

**Használhatom a blob-rétegek API-ját a Blobok a Soft delete-pillanatképekkel való létrehozására?**  
Igen. A helyreállított törölt Pillanatképek az eredeti szinten maradnak, de az alap blob az új rétegre kerül. 

**A Premium Storage-fiókokhoz a blob pillanatkép-korlátja 100. A rugalmasan törölt Pillanatképek száma a korlát felé?**  
Nem, a nem kötelező törölt Pillanatképek nem számítanak bele a korlátba.

**Be lehet kapcsolni a meglévő Storage-fiókok helyreállítható törlését?**  
Igen, a Soft delete konfigurálható a meglévő és az új Storage-fiókok esetében is.

**Ha olyan teljes fiókot vagy tárolót törölek, amelynél a Soft delete be van kapcsolva, a rendszer minden társított blobot ment?**  
Nem, ha töröl egy teljes fiókot vagy tárolót, az összes társított blob véglegesen törölve lesz. Ha meg szeretné tudni, hogyan védhető meg a Storage-fiók a véletlen törlésekről, tekintse meg a Azure Resource Manager az [erőforrások zárolása a váratlan változtatások elkerülése érdekében](../../azure-resource-manager/resource-group-lock-resources.md)című cikket.

**Megtekinthetem a törölt adatokhoz tartozó kapacitási metrikákat?**  
A rendszer az összes Storage-fiók kapacitásának részeként tartalmazza a helyreállított törölt adatmennyiséget. A tárolási kapacitás nyomon követésével és figyelésével kapcsolatos további információkért tekintse meg a [Storage Analytics](../common/storage-analytics.md) cikket.

**Ha kikapcsolom a nem kötelező törlést, továbbra is hozzáférhetek a nem törölt adatmennyiségekhez?**  
Igen, a rendszer továbbra is elérheti és helyreállíthatja a nem lejárt törlési adatokat, ha a Soft delete ki van kapcsolva.

**Elolvashatom és kimásolhatom a Blobok helyreállított törölt pillanatképeit?**  
Igen, de először a blob törlését kell meghívnia.

**Az összes blob-típushoz elérhető-e a Soft delete?**  
Igen, a Soft delete elérhető a Blobok letiltásához, a blobok és a Blobok hozzáfűzéséhez.

**Elérhető-e a Soft Delete a virtuális gépek lemezei számára?**  
A Soft Delete a prémium és a standard szintű nem felügyelt lemezek esetében is elérhető. A Soft delete utasítás csak a **blob törlésével**, a Blobok , a letiltási **listák**és **a** másolási **Blobok**használatával törölt adatok helyreállítását segíti. A **put oldal** hívása által felülírt adathalmaz nem helyreállítható.

**Módosítani kell a meglévő alkalmazásaikat a Soft delete használatára?**  
A Soft delete előnyeit kihasználhatja a használt API-verziótól függetlenül. A Soft Deleted blobok és blob-Pillanatképek listázásához és helyreállításához azonban a [Storage Services](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 2017-07-29-es verzióját kell használnia REST API vagy annál nagyobb. Általánosságban azt javasoljuk, hogy a legújabb verziót használja, függetlenül attól, hogy használja-e ezt a funkciót.

## <a name="next-steps"></a>További lépések
* [.NET-mintakód](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage-replikáció](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Magasan elérhető alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Vész-helyreállítási és Storage-fiók feladatátvétele (előzetes verzió) az Azure Storage-ban](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
