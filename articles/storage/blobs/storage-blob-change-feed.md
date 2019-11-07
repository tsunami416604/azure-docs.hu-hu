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
ms.openlocfilehash: 07123fd5701e9041ff377ea5309cf1291e737ca6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693617"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>A hírcsatorna-támogatás módosítása az Azure Blob Storage (előzetes verzió)

A változási csatorna célja, hogy tranzakciós naplókat szolgáltasson a blobok és a blob metaadatainak a Storage-fiókban történt változásairól. A változási hírcsatorna **rendezett**, **garantált**, **tartós**, **nem módosítható,** **csak olvasható** naplót biztosít ezekről a változásokról. Az ügyfélalkalmazások bármikor elolvashatják ezeket a naplókat, akár streaming, akár batch módban. A módosítási hírcsatorna lehetővé teszi olyan hatékony és méretezhető megoldások kiépítését, amelyek alacsony áron dolgozzák fel az Blob Storage-fiókban bekövetkező változásokat.

> [!NOTE]
> A módosítási hírcsatorna nyilvános előzetes verzióban érhető el, és a **westcentralus** és **westus2** régiókban is elérhető. Tekintse meg a jelen cikk [feltételek](#conditions) című szakaszát. Az előzetes verzióra való regisztráláshoz tekintse meg a jelen cikk [előfizetés regisztrálása](#register) című szakaszát.

A módosítási csatornát [blobként](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) tárolja a rendszer a Storage-fiókban lévő speciális tárolóban, standard [blob díjszabási](https://azure.microsoft.com/pricing/details/storage/blobs/) költséggel. A fájlok megőrzési időtartamát a követelmények alapján szabályozhatja (lásd a jelenlegi kiadás [feltételeit](#conditions) ). Az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) formátumának specifikációja: egy kompakt, gyors, bináris formátum, amely beágyazott sémával rendelkező, gazdag adatstruktúrákat biztosít a változási hírcsatornához. Ezt a formátumot széles körben használják a Hadoop ökoszisztémájában, Stream Analytics és Azure Data Factory.

Ezeket a naplókat aszinkron módon, Növekményesen vagy teljes mértékben feldolgozhatja. Tetszőleges számú ügyfélalkalmazás önállóan, párhuzamosan és saját tempójában is elolvashatja a változási csatornát. Az olyan elemzési alkalmazások, mint az [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) vagy a [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) közvetlenül Avro-fájlként használhatják a naplókat, így alacsony költséghatékonyságú, nagy sávszélességű és egyéni alkalmazások írása nélkül is feldolgozhatják azokat.

A hírcsatorna-támogatás módosítása olyan forgatókönyvek esetén megfelelő, amelyek a módosított objektumok alapján dolgozzák fel az adatfeldolgozást. Az alkalmazások például a következőket tehetik:

  - Másodlagos index frissítése, szinkronizálás gyorsítótárral, keresőmotor-kezelővel vagy más tartalomkezelési forgatókönyvekkel.
  
  - Az objektumokon végrehajtott módosítások alapján kinyerheti az üzleti elemzési elemzéseket és mérőszámokat, akár streaming módon, akár kötegelt módban.
  
  - A nagyvállalati adatkezelés érdekében a biztonsági, megfelelőségi vagy hírszerzési adatokhoz bármilyen időszakon belül tárolhatja, naplózhatja és elemezheti az objektum módosításait.

  - Létrehozhat olyan megoldásokat, amelyekkel biztonsági mentést készíthet, tükrözheti vagy replikálhatja az objektumok állapotát a fiókban a katasztrófák kezelése vagy a megfelelőség érdekében.

  - Olyan csatlakoztatott alkalmazás-folyamatokat hozhat létre, amelyek reagálnak az események változására vagy a létrehozott vagy módosított objektum alapján történő végrehajtásra.

> [!NOTE]
> [Blob Storage események](storage-blob-event-overview.md) valós idejű eseményeket biztosítanak, amelyek lehetővé teszik, hogy a Azure functions vagy az alkalmazások reagálni tudjanak a blobba történt változásokra. A módosítási hírcsatorna tartós, rendezett naplózási modellt biztosít a változásokhoz. A változási hírcsatorna változásai a változási csatornán belül, néhány percen belül elérhetővé válnak. Ha az alkalmazása sokkal gyorsabban reagál az eseményekre, érdemes inkább [blob Storage eseményeket](storage-blob-event-overview.md) használni. Blob Storage események lehetővé teszik, hogy a Azure Functions vagy az alkalmazások valós időben reagáljanak az egyes eseményekre.

## <a name="enabling-and-disabling-the-change-feed"></a>A módosítási csatorna engedélyezése és letiltása

A változtatások rögzítésének megkezdéséhez engedélyeznie kell a változási csatornát. Tiltsa le a változási csatornát a módosítások rögzítésének leállításához. A módosításokat engedélyezheti és letilthatja Azure Resource Manager sablonok használatával a portálon vagy a Powershellen.

### <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A sablon üzembe helyezése Azure Portal használatával:

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **template Deployment**lehetőséget, válassza a **Létrehozás**lehetőséget, majd **a szerkesztőben válassza a saját sablon**létrehozása lehetőséget.

5. A sablon szerkesztőjében illessze be a következő JSON-t. Cserélje le a `<accountName>` helyőrzőt a Storage-fiók nevére.

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
4. Kattintson a **Save (Mentés** ) gombra, adja meg a fiókhoz tartozó erőforráscsoportot, majd válassza a **vásárlás** gombot a változási csatorna engedélyezéséhez.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon üzembe helyezése a PowerShell használatával:

1. Telepítse a legújabb PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force
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
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

Íme néhány dolog, amelyet figyelembe kell venni, amikor engedélyezi a változási csatornát.

- Az egyes Storage-fiókokban csak egyetlen változási csatorna van a blob szolgáltatáshoz. 

- A módosítások csak a blob szolgáltatási szintjén lesznek rögzítve.

- A módosítási hírcsatorna rögzíti *az összes,* a fiókon elérhető esemény változását. Az ügyfélalkalmazások igény szerint szűrhetik az események típusait. (Lásd a jelenlegi kiadás [feltételeit](#conditions) ).

- A hierarchikus névtérrel rendelkező fiókok nem támogatottak.

## <a name="consuming-the-change-feed"></a>A módosítási csatorna fogyasztása

A módosítási hírcsatorna számos metaadat-és naplófájlt hoz létre. Ezek a fájlok a Storage-fiók **$blobchangefeed** tárolójában találhatók. 

>[!NOTE]
> A jelenlegi kiadásban a **$blobchangefeed** tároló nem látható Storage Explorer vagy a Azure Portal. 

Az ügyfélalkalmazások a változási csatornát az SDK-val biztosított blob Change feed Processor Library használatával használhatják fel. 

Lásd: [Az Azure Blob Storageban található adatcsatorna-naplók feldolgozása](storage-blob-change-feed-how-to.md).

## <a name="understanding-change-feed-organization"></a>A változási hírcsatorna szervezetének ismertetése

<a id="segment-index"></a>

### <a name="segments"></a>Szegmensek

A változási hírcsatorna egy **óránkénti** *szegmensbe* rendezett változások naplója (lásd a [specifikációkat](#specifications)). Ez lehetővé teszi, hogy az ügyfélalkalmazás a teljes naplón keresztüli keresés nélkül használja fel az adott időtartományon belül végrehajtott módosításokat.

A változási hírcsatorna rendelkezésre álló óránkénti szegmensét egy olyan jegyzékfájl írja le, amely megadja az adott szegmenshez tartozó adatváltozási fájlok elérési útját. A `$blobchangefeed/idx/segments/` virtuális könyvtár felsorolása megjeleníti ezeket a szegmenseket idő szerint rendezve. A szegmens elérési útja a szegmens által reprezentált óránkénti időtartomány kezdetét írja le. (Lásd a [specifikációkat](#specifications)). Ezzel a listával kiszűrheti az Önt érdeklő naplók szegmenseit.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> A `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` automatikusan létrejön, amikor engedélyezi a változási csatornát. Nyugodtan figyelmen kívül hagyhatja ezt a fájlt. Mindig üres. 

A szegmens jegyzékfájlja (`meta.json`) megjeleníti az adott szegmenshez tartozó adatváltozási fájlok elérési útját a `chunkFilePaths` tulajdonságban. Íme egy példa egy szegmens jegyzékfájl fájlra.

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
> Ha a Storage-fiókban listázza a tárolókat, akkor a `$blobchangefeed` tároló csak azután jelenik meg, hogy engedélyezte a fiók módosítása funkciót. Néhány percet várnia kell, miután engedélyezte a változási csatornát, mielőtt megtekinti a tárolót.

<a id="log-files"></a>

### <a name="change-event-records"></a>Események rekordjainak módosítása

A módosítási hírcsatorna-fájlok sorozata változási esemény rekordokat tartalmaz. Minden változási esemény rekordja egy adott blob módosításának felel meg. A rendszer szerializálja a rekordokat, és az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format specifikáció használatával írja a fájlba. A rekordok a Avro fájlformátum specifikációjának használatával olvashatók. Az adott formátumú fájlok feldolgozásához több könyvtár is rendelkezésre áll.

A médiafájlok módosítását a `$blobchangefeed/log/` virtuális könyvtárban, [hozzáfűzési blobként](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)tárolja a rendszer. Az egyes elérési utakon az első módosítási adatcsatornán a fájl neve `00000` lesz (például `00000.avro`). Az elérési úthoz hozzáadott minden további naplófájl neve 1 lesz (például: `00001.avro`).

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

- Az események azon rekordjainak módosítása, amelyekben a `eventType` `Control` belső rendszerrekordok, és nem tükrözik a fiókban lévő objektumok módosításait. Figyelmen kívül hagyhatja őket.

- A `storageDiagnonstics` tulajdonság táskájában lévő értékek csak belső használatra készültek, és nem az alkalmazás általi használatra lettek tervezve. Az alkalmazásai nem rendelkezhetnek az adott adattal kapcsolatos szerződéses függőséggel.

- A szegmens által jelzett idő 15 perces határokkal van **megközelítve** . Annak érdekében, hogy a megadott időn belül az összes rekord felhasználását meg lehessen adni, az előző és a következő óránkénti szegmenst kell használni.

- Minden szegmens különböző számú `chunkFilePaths`rendelkezhet. Ennek oka a naplók belső particionálása a közzétételi teljesítmény kezeléséhez. Az egyes `chunkFilePath`okban lévő naplófájlok garantáltan kölcsönösen kizáró blobokat tartalmaznak, és párhuzamosan használhatók és feldolgozhatók anélkül, hogy az iteráció során megsértsék a Blobok módosításának sorrendjét.

- A szegmensek `Publishing` állapotban kezdődnek. Miután befejeződött a rekordok hozzáfűzése a szegmenshez, `Finalized`lesz. Az alkalmazás nem használja fel azokat a naplófájlokat, amelyek a `$blobchangefeed/meta/Segments.json` fájlban a `LastConsumable` tulajdonságot követő dátum után lesznek felhasználva. Íme egy példa a `$blobchangefeed/meta/Segments.json` fájl `LastConsumable`tulajdonságára:

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

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Feltételek és ismert problémák (előzetes verzió)

Ez a szakasz a változási hírcsatorna aktuális nyilvános előzetes verziójának ismert problémáit és feltételeit ismerteti.

- A módosítási hírcsatorna csak a létrehozási, frissítési, törlési és másolási műveleteket rögzíti.
- Ha módosítja az események rekordjait, előfordulhat, hogy a módosítási hírcsatorna többször is megjelenhet.
- Az időalapú adatmegőrzési szabályzat beállításával még nem kezelheti a hírcsatorna-naplófájlok módosításának élettartamát.
- A naplófájl `url` tulajdonsága mindig üres.
- A szegmensek. JSON fájl `LastConsumable` tulajdonsága nem sorolja fel azt a legelső szegmenst, amelyet a módosítási hírcsatorna véglegesít. Ez a probléma csak az első szegmens véglegesítése után fordul elő. Az első óra után az összes további szegmens pontosan rögzítve lesz a `LastConsumable` tulajdonságban.

## <a name="next-steps"></a>További lépések

- Tekintse át a változási hírcsatorna .NET-ügyfélalkalmazás használatával történő beolvasásának példáját. Lásd: [Az Azure Blob Storageban található adatcsatorna-naplók feldolgozása](storage-blob-change-feed-how-to.md).
- Ismerje meg, hogyan reagálhat az eseményekre valós időben. További tudnivalók [a blob Storage eseményekre való reagálásról](storage-blob-event-overview.md)
