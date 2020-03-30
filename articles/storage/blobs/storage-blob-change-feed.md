---
title: Csatorna módosítása az Azure Blob Storage-ban (előzetes verzió) | Microsoft dokumentumok
description: Ismerje meg az Azure Blob Storage módosítási hírcsatorna-naplóit, és azok használatát.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536887"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Hírcsatorna-támogatás módosítása az Azure Blob Storage-ban (előzetes verzió)

A változáscsatorna célja, hogy a blobok és a blob metaadatok a tárfiókban előforduló összes változás tranzakciós naplóit biztosítsa. A módosítási hírcsatorna **rendezett,** **garantált,** **tartós,** **nem módosítható,** **csak olvasható** naplót biztosít ezekről a változásokról. Az ügyfélalkalmazások bármikor olvashatják ezeket a naplókat, streamelési vagy kötegelt módban. A változáscsatorna lehetővé teszi, hogy hatékony és méretezhető megoldásokat hozzon létre, amelyek alacsony költséggel dolgozzák fel a Blob Storage-fiókban előforduló változási eseményeket.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

A változáscsatorna [blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) ként tárolódik egy speciális tárolóban a tárfiókban a szokásos [blob árképzési](https://azure.microsoft.com/pricing/details/storage/blobs/) költség. Ezeknek a fájloknak a megőrzési idejét a követelmények alapján szabályozhatja (lásd az aktuális kiadás [feltételeit).](#conditions) A változási eseményeket a rendszer az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) formátum specifikációjában rekordként fűzi hozzá a változáscsatornához: egy kompakt, gyors, bináris formátum, amely gazdag adatstruktúrákat biztosít inline sémával. Ezt a formátumot széles körben használják a Hadoop-ökoszisztémában, a Stream Analytics-ben és az Azure Data Factoryban.

Ezeket a naplókat aszinkron módon, növekményesen vagy teljes egészében is feldolgozhatja. Tetszőleges számú ügyfélalkalmazás önállóan, párhuzamosan és saját ütemben olvashatja le a módosítási hírcsatornát. Az olyan elemzési alkalmazások, mint [az Apache Drill](https://drill.apache.org/docs/querying-avro-files/) vagy az Apache [Spark,](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) közvetlenül Avro-fájlként tudják felhasználni a naplókat, amelyek lehetővé teszik, hogy alacsony költséggel, nagy sávszélességgel és egyéni alkalmazás írása nélkül dolgozza fel őket.

A módosítási hírcsatorna támogatása kiválóan alkalmas olyan esetekben, amelyek a megváltozott objektumok alapján dolgozzák fel az adatokat. Például az alkalmazások:

  - Másodlagos index frissítése, szinkronizálás gyorsítótárral, keresőmotorral vagy bármely más tartalomkezelési forgatókönyvvel.
  
  - Üzleti elemzési elemzési elemzéseket és mutatókat nyerhet ki az objektumokon végrehajtott módosítások alapján, akár streamelési módban, akár kötegelt módban.
  
  - Az objektumok változásait bármikor tárolhatja, naplózhatja és elemezheti a vállalati adatkezelés biztonsága, megfelelősége vagy intelligenciája érdekében.

  - Megoldásokat hozhat létre a fiókban a katasztrófavédelem vagy a megfelelőség érdekében az objektumállapot biztonsági mentéséhez, tükrözéséhez vagy replikálásához.

  - Olyan csatlakoztatott alkalmazásfolyamatokat hozhat létre, amelyek reagálnak az események módosítására vagy a létrehozott vagy módosított objektum alapján történő végrehajtásütemezésre.

> [!NOTE]
> A módosítási hírcsatorna egy tartós, rendezett naplómodellt biztosít a blobban előforduló módosításokról. A módosításokat a módosítási hírcsatorna naplójában a módosítási naplóban írjuk és te tetszetőssé teszna, a módosítástól számított néhány percen belül. Ha az alkalmazásnak ennél sokkal gyorsabban kell reagálnia az eseményekre, fontolja meg a [Blob Storage-események](storage-blob-event-overview.md) használatát. [A Blob Storage-események](storage-blob-event-overview.md) valós idejű egyszeri eseményeket biztosít, amelyek lehetővé teszik az Azure Functions vagy az alkalmazások számára, hogy gyorsan reagáljanak a blobok ban bekövetkező változásokra. 

## <a name="enable-and-disable-the-change-feed"></a>A módosítási hírcsatorna engedélyezése és letiltása

A módosítások rögzítésének és rögzítésének megkezdéséhez engedélyeznie kell a változáscsatornát a tárfiókban. Tiltsa le a módosítási hírcsatornát a módosítások rögzítésének leállításához. A módosításokat engedélyezheti és letilthatja az Azure Resource Manager-sablonok használatával a Portálon vagy a Powershellben.

Íme néhány dolog, amit szem előtt kell tartania, amikor engedélyezi a változáshírcsatornát.

- Minden tárfiókban csak egy változáshírcsatorna van a blobszolgáltatáshoz, és a **$blobchangefeed** tárolóban van tárolva.

- A létrehozási, frissítési és törlési módosítások rögzítése csak a blob szolgáltatás szintjén történik.

- A módosítási hírcsatorna rögzíti a fiókban elérhető összes esemény *összes* módosítását. Az ügyfélalkalmazások szükség szerint kiszűrhetik az eseménytípusokat. (Lásd az aktuális kiadás [feltételeit).](#conditions)

- Csak a GPv2- és blobtárfiókok engedélyezhetik a hírcsatorna módosítását. Prémium szintű BlockBlobStorage-fiókok és hierarchikus névtér-kompatibilis fiókok jelenleg nem támogatottak. A GPv1-tárfiókok nem támogatottak, de hibaidő nélkül frissíthetők GPv2-re, további információért olvassa el a [Frissítés GPv2-tárfiókra című témakört.](../common/storage-account-upgrade.md)

> [!IMPORTANT]
> A módosítási hírcsatorna nyilvános előzetes verzióban érhető el, és a **westcentralus** és a **westus2** régiókban érhető el. Lásd a [cikk feltételekre](#conditions) vonatkozó részét. Az előzetes verzióban való regisztrációhoz tekintse meg a cikk [előfizetésregisztrálása](#register) című szakaszát. Az előfizetés regisztrálásához engedélyeznie kell a hálózati hírcsatornákat a tárfiókokon.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Módosítsa a rendszercsatorna módosításait a tárfiókon az Azure Portal használatával:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a tárfiókot. 

2. Keresse meg az **Adatvédelmi** lehetőséget a **Blob Service csoportban.**

3. Kattintson **az Engedélyezve** lehetőségre a **Blob-módosítási hírcsatorna** csoportban.

4. Az adatvédelmi beállítások megerősítéséhez kattintson a **Mentés** gombra

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Módosítsa a hírcsatornát a PowerShell használatával:

1. Telepítse a legújabb PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zárja be, majd nyissa meg újra a Powershell konzolt.

3. Telepítse az **Az.Storage** előnézeti modult.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Jelentkezzen be az Azure-előfizetésbe a paranccsal, `Connect-AzAccount` és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

   ```powershell
   Connect-AzAccount
   ```

5. Engedélyezze a változáscsatornát a tárfiókhoz.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sablon](#tab/template)
Azure Resource Manager-sablon használatával engedélyezheti a hírcsatorna módosítását a meglévő tárfiókon az Azure Portalon keresztül:

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.**

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza **[az Egyéni sablon telepítése](https://portal.azure.com/#create/Microsoft.Template)** lehetőséget, majd válassza a Saját sablon létrehozása lehetőséget a **szerkesztőben.**

4. A sablonszerkesztőben illessze be a következő jsont. Cserélje `<accountName>` le a helyőrzőt a tárfiók nevére.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Válassza a **Mentés** gombot, adja meg a fiók erőforráscsoportját, majd a **Vásárlás** gombra kattintva telepítse a sablont, és engedélyezze a módosítási hírcsatornát.

---

## <a name="consume-the-change-feed"></a>A módosítási hírcsatorna felhasználása

A módosítási hírcsatorna több metaadatot és naplófájlt hoz létre. Ezek a fájlok a tárfiók **$blobchangefeed** tárolójában találhatók. 

> [!NOTE]
> Az aktuális kiadásban a **$blobchangefeed** tároló nem látható az Azure Storage Explorerben vagy az Azure Portalon. Jelenleg nem látja a $blobchangefeed tárolót, amikor meghívja a ListContainers API-t, de a Blobok API-t közvetlenül a tárolón hívhatja meg a blobok megtekintéséhez.

Az ügyfélalkalmazások a blob változáscsatorna-processzor kódtár, amely a Change feed processzor SDK-hoz biztosított blob change feed processzor használatával a változás-hírcsatorna-csatorna használatával is igénybe vehetik a módosítási hírcsatornát. 

Lásd: [Változáscsatorna-naplók feldolgozása az Azure Blob Storage-ban.](storage-blob-change-feed-how-to.md)

## <a name="understand-change-feed-organization"></a>A módosítási hírcsatorna-szervezet ismertetése

<a id="segment-index"></a>

### <a name="segments"></a>Szegmensek

A módosítási hírcsatorna az **óránkénti** *szegmensekbe* rendezett, de néhány percenként hozzáfűzött és frissített módosítások naplója. Ezek a szegmensek csak akkor jönnek létre, ha az adott órában blobmódosítási események lépnek fel. Ez lehetővé teszi, hogy az ügyfélalkalmazás az adott időtartományon belül bekövetkező módosításokat használja fel anélkül, hogy a teljes naplóban keresnie kellene. További információ: [Specifications](#specifications).

A módosítási hírcsatorna egy rendelkezésre álló óránkénti szegmensét egy jegyzékfájl írja le, amely meghatározza az adott szegmens módosítási hírcsatorna-fájljainak elérési útjait. A virtuális `$blobchangefeed/idx/segments/` könyvtár felsorolása ezeket a szegmenseket mutatja az idő szerint rendezetten. A szegmens elérési útja a szegmens által képviselt óránkénti időtartomány kezdetét írja le. Ezzel a listával kiszűrheti az Ön számára érdekes naplók szegmenseit.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> A `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` automatikusan létrejön, amikor engedélyezi a módosítási hírcsatornát. Nyugodtan figyelmen kívül hagyhatja ezt a fájlt. Ez egy mindig üres inicializálási fájl. 

A szegmens jegyzékfájl (`meta.json`) a `chunkFilePaths` tulajdonság adott szegmensében lévő módosítási hírcsatorna-fájlok elérési útját mutatja. Íme egy példa egy szegmens jegyzékfájlra.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> A `$blobchangefeed` tároló csak akkor jelenik meg, ha engedélyezte a módosítási hírcsatorna funkciót a fiókjában. A blobok felsorolása előtt néhány percet kell várnia a módosítási hírcsatorna engedélyezésére. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Eseményrekordok módosítása

A módosítási hírcsatorna-fájlok változásesemény-rekordok sorozatát tartalmazzák. Minden változási eseményrekord egy adott blob egy módosításának felel meg. A rekordok szerializáltak és a fájlba íródtak az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) formátum specifikációhasználatával. A rekordok az Avro fájlformátum-specifikáció használatával olvashatók. Az ilyen formátumú fájlok feldolgozásához számos tár áll rendelkezésre.

A módosítási hírcsatorna-fájlok a `$blobchangefeed/log/` virtuális könyvtárban [hozzáfűző blobként tárolódnak.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Az egyes elérési utak alatti `00000` első módosítási hírcsatornafájl a fájlnévben (például `00000.avro`) lesz. Az elérési úthoz hozzáadott további naplófájlok neve 1-gyel `00001.avro`növekszik (például: ).

Íme egy példa a változás eseményrekord változás feed fájl ból Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Az egyes tulajdonok leírását lásd: [Azure Event Grid eseményséma a Blob Storage.](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)

> [!NOTE]
> Egy szegmens módosítási hírcsatorna-fájljai nem jelennek meg azonnal egy szegmens létrehozása után. A késleltetés időtartama a változáscsatorna késésének szokásos időközén belül van, amely a módosítástól számított néhány percen belül van.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikációk

- A változási események rekordjai csak a módosítási hírcsatornához lesznek hozzáfűzve. A rekordok hozzáfűzése után nem módosíthatók, és a rekordpozíció stabil. Az ügyfélalkalmazások saját ellenőrzőpontjukat is megtarthatják a módosítási hírcsatorna olvasási pozíciójában.

- A módosítási eseményrekordok at a módosítástól számított néhány percen belül hozzáfűzi a hozzá. Az ügyfélalkalmazások bármikor választhatnak úgy, hogy a rekordokat a streamelési hozzáféréshez hozzáfűzve vagy ömlesztve használják fel.

- A módosítási eseményrekordok **blobonkénti**módosítási sorrend szerint vannak rendezve. A blobok közötti módosítások sorrendje nincs definiálva az Azure Blob Storage-ban. Egy korábbi szegmensben minden változás a következő szegmensek bármely változása előtt van.

- A változási eseményrekordok szerializálódnak a naplófájlba az [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) formátum specifikációhasználatával.

- Módosíthatja azokat `eventType` az eseményrekordokat, amelyek értéke belső `Control` rendszerrekordok, és nem tükrözik a fiók objektumainak módosítását. Nyugodtan figyelmen kívül hagyhatja ezeket a rekordokat.

- A tulajdonságtáskában lévő `storageDiagnonstics` értékek csak belső használatra szolgálnak, és nem az alkalmazás általi használatra készültek. Az alkalmazások nem rendelkeznek szerződéses függéssel az adatoktól. Ezeket a tulajdonságokat nyugodtan figyelmen kívül hagyhatja.

- A szegmens által képviselt idő **hozzávetőleges** 15 perces határral. Így az összes rekord meghatározott időn belüli felhasználásának biztosítása érdekében használja fel az egymást követő előző és következő óra szegmenst.

- Minden szegmens különböző számú `chunkFilePaths` a naplófolyam belső particionálása miatt a közzétételi átviteli szint kezeléséhez. A naplófájlok `chunkFilePath` minden garantáltan tartalmaznak kölcsönösen kizáró blobok, és lehet fogyasztani és párhuzamosan feldolgozni anélkül, hogy megsértené a módosítások rendezése blobonként az iteráció során.

- A szegmensek állapotban indulnak. `Publishing` Miután a rekordok hozzáfűzése befejeződött, `Finalized`a lesz . A `LastConsumable` `$blobchangefeed/meta/Segments.json` naplófájlokat a fájlban lévő tulajdonság dátuma után keltezett szegmensekben az alkalmazás nem használhatja fel. Íme egy példa a `LastConsumable` `$blobchangefeed/meta/Segments.json` fájlban lévő tulajdonságra:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Az előfizetés regisztrálása (előzetes verzió)

Mivel a módosítási hírcsatorna csak nyilvános előzetes verzióban érhető el, regisztrálnia kell az előfizetést a funkció használatához.

### <a name="register-by-using-powershell"></a>Regisztráció a PowerShell használatával

Egy PowerShell-konzolon futtassa a következő parancsokat:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Regisztráció az Azure CLI használatával

Az Azure Cloud Shellben futtassa a következő parancsokat:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Feltételek és ismert problémák (előzetes verzió)

Ez a szakasz a módosítási hírcsatorna aktuális nyilvános előzetes verziójában ismert problémákat és feltételeket ismerteti. 
- Előzetes verzió, először regisztrálnia kell [az előfizetést,](#register) mielőtt engedélyezheti a tárolási csatorna a westcentralus vagy westus2 régiókban a tárfiók módosítása. 
- A módosítási hírcsatorna csak a létrehozási, frissítési, törlési és másolási műveleteket rögzíti. A metaadat-frissítések et jelenleg nem rögzíti előzetes verzióban.
- Az egyes módosítások eseményrekordjainak módosítása többször is megjelenhet a módosítási hírcsatornában.
- Még nem kezelheti a módosítási naplónapló-fájlok élettartamát úgy, hogy időalapú adatmegőrzési házirendet állít be rájuk, és nem törölheti a blobokat 
- A `url` naplófájl tulajdonsága jelenleg mindig üres.
- A `LastConsumable` szegmensek.json fájl tulajdonsága nem sorolja fel a módosítási hírcsatorna által véglegesített első szegmenst. Ez a probléma csak az első szegmens véglegesítése után jelentkezik. Az első óra után minden további szegmens pontosan rögzítésre került a `LastConsumable` szálláshelyen.
- A **$blobchangefeed** tároló jatt s-t nem látja, amikor listtárolóAPI-t hív meg, és a tároló nem jelenik meg az Azure Portalon vagy a Storage Explorerben
- A [korábban fiókfeladat-átvételt](../common/storage-disaster-recovery-guidance.md) kezdeményező tárfiókok problémákat okozhatnak a naplófájl megjelenésével kapcsolatban. A jövőbeli fiókfeladat-átvételek hatással lehetnek a naplófájlelőzetes verzióra is.

## <a name="faq"></a>GYIK

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Mi a különbség a változási hírcsatorna és a Storage Analytics naplózása között?
Az Analytics-naplók az összes olvasási, írási, listás és törlési művelet rekordjait tartalmazza, amelyek sikeres és sikertelen kéréseket kapnak az összes műveletben. Az Analytics-naplók a legjobb, és nem garantált a rendelés.

A változáscsatorna olyan megoldás, amely tranzakciós naplót biztosít a sikeres mutációkról vagy a fiók módosításairól, például a blob létrehozása, módosítása és törlése. Módosítsa a hírcsatorna garantálja az összes eseményt rögzíteni kell, és megjelenik a blobonkénti sikeres módosítások sorrendjében, így nem kell kiszűrnie a nagy mennyiségű olvasási művelet vagy sikertelen kérelmek zaját. A változáshírcsatorna alapvetően olyan alkalmazásfejlesztésre lett tervezve és optimalizálva, amely bizonyos garanciákat igényel.

### <a name="should-i-use-change-feed-or-storage-events"></a>Használjam a Csatorna- vagy tárolási események módosítása vagy tárolása eseményeit?
Használhatja mindkét funkciót, mint a változás a csatorna és a [Blob tárolási események](storage-blob-event-overview.md) ugyanazt az információt ugyanazt a kézbesítési megbízhatósági garanciát, a fő különbség a késés, a rendezés és az eseményrekordok tárolása. Módosítsa a csatorna közzéteszi a rekordokat a naplóban a módosítás után néhány percen belül, és garantálja a változásműveletek blobonkénti sorrendjét. A tárolási események leküldése valós időben, és előfordulhat, hogy nem rendelhető. A változáscsatorna-események tartósan tárolódnak a tárfiókban, mint csak olvasható stabil naplók a saját meghatározott megőrzési, míg a tárolási események átmeneti kell használni az eseménykezelő, kivéve, ha explicit módon tárolja őket. A változás csatorna, az alkalmazások száma is igénybe a naplókat saját kényelmét blob API-k vagy SDK-k használatával. 

## <a name="next-steps"></a>További lépések

- Tekintse meg a módosítási hírcsatorna .NET ügyfélalkalmazás sal történő olvasásának példáját. Lásd: [Változáscsatorna-naplók feldolgozása az Azure Blob Storage-ban.](storage-blob-change-feed-how-to.md)
- További információ arról, hogyan reagálhat az eseményekre valós időben. Lásd: [Reagálás a Blob Storage-eseményekre](storage-blob-event-overview.md)
- További információ az összes kérelem sikeres és sikertelen műveleteinek részletes naplózási adatairól. Lásd: [Azure Storage-elemzési naplózás](../common/storage-analytics-logging.md)
