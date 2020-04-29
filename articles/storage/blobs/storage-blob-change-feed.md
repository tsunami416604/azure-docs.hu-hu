---
title: Adatcsatorna módosítása az Azure Blob Storageban (előzetes verzió) | Microsoft Docs
description: Ismerje meg, hogyan válthat a hírcsatorna-naplók az Azure Blob Storageban és hogyan használhatók.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536887"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>A hírcsatorna-támogatás módosítása az Azure Blob Storage (előzetes verzió)

A változási csatorna célja, hogy tranzakciós naplókat szolgáltasson a blobok és a blob metaadatainak a Storage-fiókban történt változásairól. A változási hírcsatorna **rendezett**, **garantált**, **tartós**, **nem módosítható,** **csak olvasható** naplót biztosít ezekről a változásokról. Az ügyfélalkalmazások bármikor elolvashatják ezeket a naplókat, akár streaming, akár batch módban. A módosítási hírcsatorna lehetővé teszi olyan hatékony és méretezhető megoldások kiépítését, amelyek alacsony áron dolgozzák fel az Blob Storage-fiókban bekövetkező változásokat.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

A módosítási csatornát [blobként](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) tárolja a rendszer a Storage-fiókban lévő speciális tárolóban, standard [blob díjszabási](https://azure.microsoft.com/pricing/details/storage/blobs/) költséggel. A fájlok megőrzési időtartamát a követelmények alapján szabályozhatja (lásd a jelenlegi kiadás [feltételeit](#conditions) ). Az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) formátumának specifikációja: egy kompakt, gyors, bináris formátum, amely beágyazott sémával rendelkező, gazdag adatstruktúrákat biztosít a változási hírcsatornához. Ezt a formátumot széles körben használják a Hadoop ökoszisztémájában, Stream Analytics és Azure Data Factory.

Ezeket a naplókat aszinkron módon, Növekményesen vagy teljes mértékben feldolgozhatja. Tetszőleges számú ügyfélalkalmazás önállóan, párhuzamosan és saját tempójában is elolvashatja a változási csatornát. Az olyan elemzési alkalmazások, mint az [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) vagy a [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) közvetlenül Avro-fájlként használhatják a naplókat, így alacsony költséghatékonyságú, nagy sávszélességű és egyéni alkalmazások írása nélkül is feldolgozhatók.

A hírcsatorna-támogatás módosítása olyan forgatókönyvek esetén megfelelő, amelyek a módosított objektumok alapján dolgozzák fel az adatfeldolgozást. Az alkalmazások például a következőket tehetik:

  - Másodlagos index frissítése, szinkronizálás gyorsítótárral, keresőmotorral vagy más tartalomkezelési forgatókönyvekkel.
  
  - Az objektumokon végrehajtott módosítások alapján kinyerheti az üzleti elemzési elemzéseket és mérőszámokat, akár streaming módon, akár kötegelt módban.
  
  - Az objektumok módosításainak tárolása, naplózása és elemzése, a biztonság, a megfelelőség vagy a vállalati adatkezelési intelligencia tekintetében.

  - Létrehozhat olyan megoldásokat, amelyekkel biztonsági mentést készíthet, tükrözheti vagy replikálhatja a fiókját a katasztrófák kezelése vagy megfelelősége érdekében.

  - Olyan összekapcsolt alkalmazás-folyamatokat hozhat létre, amelyek reagálnak az események módosítására, vagy a létrehozott vagy módosított objektum alapján hajtják végre a végrehajtást.

> [!NOTE]
> A módosítási hírcsatorna tartós, rendezett naplózási modellt biztosít a Blobok változásaihoz. A módosítások a változási hírcsatorna naplójában a módosítás néhány percen belül elérhetővé válnak. Ha az alkalmazása sokkal gyorsabban reagál az eseményekre, érdemes inkább [blob Storage eseményeket](storage-blob-event-overview.md) használni. [Blob Storage események](storage-blob-event-overview.md) valós idejű eseményeket biztosítanak, amelyek lehetővé teszik, hogy a Azure functions vagy az alkalmazások gyorsan reagálni tudjanak a blobon végrehajtott változásokra. 

## <a name="enable-and-disable-the-change-feed"></a>A módosítási csatorna engedélyezése és letiltása

A módosítások rögzítésének és rögzítésének megkezdéséhez engedélyeznie kell a Storage-fiók módosítási csatornáját. Tiltsa le a változási csatornát a módosítások rögzítésének leállításához. A módosításokat engedélyezheti és letilthatja Azure Resource Manager sablonok használatával a portálon vagy a Powershellen.

Íme néhány dolog, amelyet figyelembe kell venni, amikor engedélyezi a változási csatornát.

- Minden egyes Storage-fiókban csak egy változási csatorna található a blob szolgáltatáshoz, és a **$blobchangefeed** tárolóban van tárolva.

- A létrehozási, frissítési és törlési módosítások csak a blob szolgáltatási szintjén lesznek rögzítve.

- A módosítási hírcsatorna rögzíti *az összes,* a fiókon elérhető esemény változását. Az ügyfélalkalmazások igény szerint szűrhetik az események típusait. (Lásd a jelenlegi kiadás [feltételeit](#conditions) ).

- Csak a GPv2 és a blob Storage-fiókok módosíthatják a módosítási csatornát. A prémium szintű BlockBlobStorage-fiókok és a hierarchikus névtér-kompatibilis fiókok jelenleg nem támogatottak. A GPv1 Storage-fiókok nem támogatottak, de a GPv2 nem lehet állásidő nélkül frissíteni, további információért lásd: [verziófrissítés egy GPv2 Storage-fiókra](../common/storage-account-upgrade.md) .

> [!IMPORTANT]
> A módosítási hírcsatorna nyilvános előzetes verzióban érhető el, és a **westcentralus** és **westus2** régiókban is elérhető. Tekintse meg a jelen cikk [feltételek](#conditions) című szakaszát. Az előzetes verzióra való regisztráláshoz tekintse meg a jelen cikk [előfizetés regisztrálása](#register) című szakaszát. Regisztrálnia kell az előfizetését, mielőtt engedélyezi a módosítási csatornát a Storage-fiókokon.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal használatával engedélyezze a Storage-fiók módosítási csatornájának használatát:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a **blob Service**területen a **Adatvédelem** lehetőségre.

3. Kattintson az **engedélyezve** lehetőségre a **blob-módosítási hírcsatorna** területen

4. Kattintson a **Save (Mentés** ) gombra az adatvédelmi beállítások megerősítéséhez

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A módosítási hírcsatorna engedélyezése a PowerShell használatával:

1. Telepítse a legújabb PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Kattintson a Bezárás gombra, majd nyissa meg újra a PowerShell-konzolt.

3. Telepítse az az **. Storage** Preview-modult.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

   ```powershell
   Connect-AzAccount
   ```

5. A Storage-fiók módosítási csatornájának engedélyezése.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sablon](#tab/template)
Azure Resource Manager sablon használatával engedélyezheti a meglévő Storage-fiók módosítási csatornáját Azure Portalon keresztül:

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza az **[egyéni sablon üzembe helyezése](https://portal.azure.com/#create/Microsoft.Template)** lehetőséget, majd **a szerkesztőben válassza a saját sablon létrehozása**lehetőséget.

4. A sablon szerkesztőjében illessze be a következő JSON-t. Cserélje le `<accountName>` a helyőrzőt a Storage-fiók nevére.

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
    
5. Kattintson a **Save (Mentés** ) gombra, adja meg a fiókhoz tartozó erőforráscsoportot, majd válassza a **vásárlás** gombot a sablon üzembe helyezéséhez és a módosítási csatorna engedélyezéséhez.

---

## <a name="consume-the-change-feed"></a>A módosítási csatorna felhasználása

A módosítási hírcsatorna számos metaadat-és naplófájlt hoz létre. Ezek a fájlok a Storage-fiók **$blobchangefeed** tárolójában találhatók. 

> [!NOTE]
> A jelenlegi kiadásban a **$blobchangefeed** tároló nem látható Azure Storage Explorer vagy a Azure Portal. A ListContainers API meghívása során jelenleg nem látja a $blobchangefeed tárolót, de a Blobok megjelenítéséhez közvetlenül a tárolón hívhatja a ListBlobs API-t.

Az ügyfélalkalmazások a Change feed Processor SDK-val megadott blob Change feed Processor Library használatával használhatják a változási csatornát. 

Lásd: [Az Azure Blob Storageban található adatcsatorna-naplók feldolgozása](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Az adatcsatorna-szervezet változásának megismerése

<a id="segment-index"></a>

### <a name="segments"></a>Szegmensek

A módosítási hírcsatorna **óránkénti** *szegmensekben* rendezett változások naplója, de néhány percenként hozzá van fűzve, és frissül. Ezek a szegmensek csak akkor jönnek létre, ha az adott órában előforduló blob-változási események vannak. Ez lehetővé teszi, hogy az ügyfélalkalmazás a teljes naplón keresztüli keresés nélkül használja fel az adott időtartományon belül végrehajtott módosításokat. További információért lásd a [specifikációkat](#specifications).

A változási hírcsatorna rendelkezésre álló óránkénti szegmensét egy olyan jegyzékfájl írja le, amely megadja az adott szegmenshez tartozó adatváltozási fájlok elérési útját. A `$blobchangefeed/idx/segments/` virtuális könyvtár felsorolása megjeleníti ezeket a szegmenseket idő szerint rendezve. A szegmens elérési útja a szegmens által reprezentált óránkénti időtartomány kezdetét írja le. Ezzel a listával kiszűrheti az Önt érdeklő naplók szegmenseit.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> A `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` automatikusan létrejön, amikor engedélyezi a változási csatornát. Nyugodtan figyelmen kívül hagyhatja ezt a fájlt. Ez egy mindig üres inicializálási fájl. 

A szegmens jegyzékfájlja (`meta.json`) az adott szegmenshez tartozó adatváltozási fájlok elérési útját `chunkFilePaths` jeleníti meg a tulajdonságban. Íme egy példa egy szegmens jegyzékfájl fájlra.

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
> A `$blobchangefeed` tároló csak azután jelenik meg, hogy engedélyezte a fiók módosítása funkciót. A tárolóban lévő Blobok listázása előtt néhány percet várnia kell, miután engedélyezte a változási csatornát. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Események rekordjainak módosítása

A módosítási hírcsatorna-fájlok sorozata változási esemény rekordokat tartalmaz. Minden változási esemény rekordja egy adott blob módosításának felel meg. A rendszer szerializálja a rekordokat, és az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format specifikáció használatával írja a fájlba. A rekordok a Avro fájlformátum specifikációjának használatával olvashatók. Az adott formátumú fájlok feldolgozásához több könyvtár is rendelkezésre áll.

A médiafájlok módosítását a virtuális `$blobchangefeed/log/` könyvtárban, [hozzáfűzési blobként](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)tárolja a rendszer. Az egyes elérési utakon az első módosítási `00000` hírcsatorna a fájl nevében fog megjelenni (például `00000.avro`). Az elérési úthoz hozzáadott minden további naplófájl neve eggyel nő (például: `00001.avro`).

Íme egy példa arra, hogy változási esemény rekordja a JSON-ra konvertált módosítási adatcsatornán.

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

Az egyes tulajdonságok leírását lásd: [Azure Event Grid blob Storagehoz tartozó esemény-séma](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> A szegmensek változási csatornáinak módosítása nem jelenik meg azonnal a szegmens létrehozása után. A késleltetési idő a változási hírcsatornán a módosítási adatcsatornán belül a közzétételi késésnek a normál intervallumán belül van.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikációk

- Az események módosítása rekordok csak a változási csatornához vannak hozzáfűzve. A rekordok hozzáfűzése után nem változtathatók meg, és a rekord pozíciója stabil. Az ügyfélalkalmazások megtarthatják saját ellenőrzőpontját a változási hírcsatorna olvasási pozícióján.

- Az események rekordjának módosítása néhány percen belül megtörténik. Az ügyfélalkalmazások dönthetnek úgy, hogy felhasználják a rekordokat a folyamatos átviteli hozzáféréshez, vagy bármilyen más időpontban tömegesen.

- Az események rekordjainak módosítása a **Blobok**módosítási sorrendjének megfelelően történik. A Blobok közötti változások sorrendje nincs meghatározva az Azure Blob Storageban. Egy korábbi szegmens összes változása előtt a további szegmensek változásai megváltoznak.

- Az Event Records módosításait a rendszer az [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) formátumának specifikációjának használatával szerializálja a naplófájlba.

- Módosítsa az eseményeket, ahol `eventType` a belső rendszerrekordok értéke `Control` , és nem tükrözi a fiókban lévő objektumok változását. Ezeket a rekordokat nyugodtan figyelmen kívül hagyhatja.

- A `storageDiagnonstics` tulajdonság táskájában lévő értékek csak belső használatra vannak kialakítva, és nem az alkalmazás általi használatra készültek. Az alkalmazásai nem rendelkezhetnek az adott adattal kapcsolatos szerződéses függőséggel. Ezeket a tulajdonságokat nyugodtan figyelmen kívül hagyhatja.

- A szegmens által jelzett idő 15 perces határokkal van **megközelítve** . Annak érdekében, hogy a megadott időn belül az összes rekord felhasználását meg lehessen adni, az előző és a következő óránkénti szegmenst kell használni.

- Minden szegmens különböző számú lehet a naplók `chunkFilePaths` belső particionálása miatt a közzétételi teljesítmény kezeléséhez. A naplófájlok `chunkFilePath` garantáltan kölcsönösen kizárják egymást, és párhuzamosan is feldolgozhatók és feldolgozhatók, anélkül, hogy az iteráció során megsértsék a Blobok módosításának sorrendjét.

- A szegmensek `Publishing` állapota megkezdődik. Miután befejeződött a rekordok összefűzése a szegmensbe, a következő lesz: `Finalized`. Az alkalmazás nem használja fel azokat a naplófájlokat, amelyeknek a `LastConsumable` `$blobchangefeed/meta/Segments.json` fájl tulajdonságának a dátuma után a rendszer nem használja fel a fájlt. Íme egy példa a `LastConsumable` `$blobchangefeed/meta/Segments.json` fájl tulajdonságára:

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

## <a name="register-your-subscription-preview"></a>Előfizetés regisztrálása (előzetes verzió)

Mivel a változási csatorna csak nyilvános előzetes verzióban érhető el, regisztrálnia kell az előfizetését a funkció használatához.

### <a name="register-by-using-powershell"></a>Regisztrálás a PowerShell használatával

A PowerShell-konzolon futtassa a következő parancsokat:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Regisztrálás az Azure CLI használatával

A Azure Cloud Shell futtassa a következő parancsokat:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Feltételek és ismert problémák (előzetes verzió)

Ez a szakasz a változási hírcsatorna aktuális nyilvános előzetes verziójának ismert problémáit és feltételeit ismerteti. 
- Előzetes verzióként [regisztrálnia kell az előfizetését](#register) , mielőtt engedélyezi a westcentralus vagy westus2-régiókban lévő Storage-fiókhoz tartozó módosítási csatornát. 
- A módosítási hírcsatorna csak a létrehozási, frissítési, törlési és másolási műveleteket rögzíti. A metaadatok frissítése jelenleg nem történik meg az előzetes verzióban.
- Ha módosítja az események rekordjait, előfordulhat, hogy a módosítási hírcsatorna többször is megjelenhet.
- Az időalapú adatmegőrzési szabályzat beállításával még nem kezelheti a hírcsatorna-naplófájlok módosításának élettartamát, és nem törölheti a blobokat 
- A `url` naplófájl tulajdonsága jelenleg mindig üres.
- A `LastConsumable` szegmens. JSON fájl tulajdonsága nem sorolja fel azt a legelső szegmenst, amelyet a módosítási hírcsatorna véglegesít. Ez a probléma csak az első szegmens véglegesítése után fordul elő. Az első óra utáni összes további szegmens rögzítése pontosan megtörténik a `LastConsumable` tulajdonságban.
- A ListContainers API meghívásakor jelenleg nem látható a **$blobchangefeed** tároló, és a tároló nem jelenik meg Azure Portal vagy Storage Explorer
- Azok a Storage-fiókok, amelyek korábban már kezdeményezték a [fiók feladatátvételét](../common/storage-disaster-recovery-guidance.md) , a naplófájlban nem jelennek meg problémák. A jövőbeli fiók-feladatátvételek az előzetes verzió során is befolyásolhatják a naplófájlt.

## <a name="faq"></a>GYIK

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Mi a különbség a változási hírcsatorna és a Storage Analytics naplózás között?
Az elemzési naplók minden művelet során a sikeres és sikertelen kérelmekkel rendelkező összes olvasási, írási, listázási és törlési műveletet rögzítik. Az elemzési naplók a legjobb erőfeszítés, és a megrendelés nem garantált.

A Change feed olyan megoldás, amely tranzakciós naplót biztosít a sikeres mutációk vagy a fiók módosításai, például a Blobok létrehozása, módosítása és törlése során. A módosítási hírcsatorna garantálja az összes rögzítendő eseményt, és a Blobok sikeres változásainak sorrendjében jeleníti meg, így nem kell kiszűrnie a zajt egy nagy mennyiségű olvasási művelet vagy sikertelen kérelem alapján. A módosítási hírcsatorna alapvetően olyan alkalmazások fejlesztéséhez lett optimalizálva, amelyek bizonyos garanciákat igényelnek.

### <a name="should-i-use-change-feed-or-storage-events"></a>Használhatom a Change feed vagy a Storage eseményt?
Mindkét funkciót kihasználhatja, mivel a memória-és [blob-tárolási események](storage-blob-event-overview.md) is ugyanazokat az információkat nyújtják, mint a kézbesítés megbízhatósági garanciája, és a fő különbség az, hogy az események rekordjainak késése, rendezése és tárolása is megmarad. A módosítási hírcsatorna a változást követően néhány percen belül közzéteszi a rekordokat a naplóba, és a módosítási műveletek sorrendjét is megtartja blobban. A tárolási események valós időben lesznek leküldve, és előfordulhat, hogy nem rendelhető meg. A tartósan a Storage-fiókban tárolt adatok módosítása csak olvasható stabil naplókat tartalmaz a saját meghatározott adatmegőrzéssel, míg a tárolási események átmenetiek, ha kifejezetten tárolja őket. A változási hírcsatornával tetszőleges számú alkalmazás használhatja a naplókat a saját kényelmében a blob API-k vagy SDK-k használatával. 

## <a name="next-steps"></a>További lépések

- Tekintse át a változási hírcsatorna .NET-ügyfélalkalmazás használatával történő beolvasásának példáját. Lásd: [Az Azure Blob Storageban található adatcsatorna-naplók feldolgozása](storage-blob-change-feed-how-to.md).
- Ismerje meg, hogyan reagálhat az eseményekre valós időben. További tudnivalók [a blob Storage eseményekre való reagálásról](storage-blob-event-overview.md)
- További információ az összes kérelem sikeres és sikertelen műveleteinek részletes naplózási adatairól. Lásd: az [Azure Storage Analytics naplózása](../common/storage-analytics-logging.md)
