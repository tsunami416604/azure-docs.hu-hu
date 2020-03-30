---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & erőforrások'
description: Tudjon meg mindent az SQL Node.js API-ról és az SDK-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az Azure Cosmos DB Node.js SDK egyes verziói között végrehajtott módosításokat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: de1c1b93b813f71b321da0625bc60e0762a859c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70207956"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Többi](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

|Erőforrás  |Hivatkozás  |
|---------|---------|
|SDK letöltése  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API dokumentáció  |  [JavaScript SDK referenciadokumentáció](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK telepítési utasítások  |  [Telepítési utasítások](https://github.com/Azure/azure-cosmos-js#installation)
|Hozzájárulás az SDK-hoz | [Github](https://github.com/Azure/azure-cosmos-js/tree/master)
| Példák | [Node.js kódminták](sql-api-nodejs-samples.md)
| Első lépések oktatóanyag | [A JavaScript SDK – első lépések](sql-api-nodejs-get-started.md)
| Webalkalmazás oktatóanyaga | [Node.js webalkalmazás létrehozása az Azure Cosmos DB használatával](sql-api-nodejs-application.md)
| Jelenlegi támogatott platform | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK verzió 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK verzió 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK verzió 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK verzió 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK 1.x.x verzió<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK verzió 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK verzió 1.x.x

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Állítsa az alapértelmezett ResponseContinuationTokenLimitInKB értéket 1kb-ra. Alapértelmezés szerint ezt az 1 kb-ra korlátozzuk, hogy elkerüljük a hosszú fejléceket (a Node.js globális fejlécméret-korláttal rendelkezik). A felhasználó beállíthatja ezt a mezőt, hogy hosszabb fejléceket engedélyezze, ami segíthet a háttérrendszernek a lekérdezés végrehajtásának optimalizálásában.
* Távolítsa el a disableSSLVerification parancsot. Ez a beállítás új alternatívákat ír le [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* A initialHeaders metódusok explicit módon beállíthatják a partíciókulcs fejlécét
* A package.json#files használata a felesleges fájlok közzétételének megakadályozására
* Útválasztási térkép rendezési hibájának kijavítása a csomópont+v8 régebbi verzióján
* Javítja a hibát, ha a felhasználó részleges újrapróbálkozási lehetőségeket biztosít

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* A szükséges modulokkal megnevezett webpack feloldásának megakadályozása

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Kijavít egy régóta fennálló hibát, amelyben a felelős részeket mindig 0-ként jelentették az összesített lekérdezésekhez

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 kiadás! 🎉 Számos új funkciót, hibajavítást és néhány apró változtatást. A jelen kiadás elsődleges céljai:

* A főbb új funkciók megvalósítása
  * ELTÉRŐ lekérdezések
  * LIMIT/OFFSET lekérdezések
  * Felhasználó visszavonható kérései
* Frissítés a legújabb Cosmos REST API-verzióra, ahol az összes tároló korlátlan méretű
* A Cosmos használatának megkönnyítése a böngészőből
* Jobban igazodjon az Azure JS SDK új irányelveihez

#### <a name="migration-guide-for-breaking-changes"></a>Áttelepítési útmutató a módosítások megszakadásához
##### <a name="improved-client-constructor-options"></a>Továbbfejlesztett ügyfélkonstruktor-beállítások

A kivitelezői beállítások egyszerűsödtek:

* a masterKey névre keresztelték a kulcsot, és a legfelső szintre került
* Az options.auth alatt korábban a legmagasabb szintre kerültek a tulajdonságok

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Egyszerűsített QueryIterator API
A v2-ben számos különböző módon lehet iteitálni vagy lekérni egy lekérdezés eredményeit. Megpróbáltuk egyszerűsíteni a v3 API-t, és eltávolítani a hasonló vagy ismétlődő API-kat:

* Távolítsa el az iterátor.next() és iterator.current() A fetchNext() segítségével kapja meg az eredmények oldalait.
* Távolítsa el az iterátort.forEach(). Használja inkább az aszinkron iterátorokat.
* iterator.executeNext() átnevezve iterátor.fetchNext()
* iterator.toArray() átnevezve iterátor.fetchAll()
* Az oldalak mostantól megfelelő válaszobjektumok az egyszerű JS-objektumok helyett
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>A rögzített tárolók particionálva vannak
A Cosmos szolgáltatás mostantól támogatja a partíciós kulcsokat az összes tárolón, beleértve azokat is, amelyeket korábban rögzített tárolókként hoztak létre. A v3 SDK frissíti a legújabb API-verzió, amely végrehajtja ezt a módosítást, de nem törik. Ha nem adja meg a műveletekhez a partíciókulcsot, alapértelmezés szerint olyan rendszerkulcsot fogunk megadni, amely az összes meglévő tárolóval és dokumentummal működik.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert eltávolítva a tárolt eljárások
Korábban upsert engedélyezték a nem particionált gyűjtemények, de az API-verzió frissítése, az összes gyűjtemény particionált, így teljesen eltávolítottuk.

##### <a name="item-reads-will-not-throw-on-404"></a>Az elem olvasása nem dobja a 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Alapértelmezett többrégiós írás
Az SDK alapértelmezés szerint több régióba is ír, ha a Cosmos-konfiguráció támogatja azt. Ez korábban opt-in viselkedés.

##### <a name="proper-error-objects"></a>Megfelelő hibaobjektumok
A sikertelen kérelmek mostantól megfelelő hibát vagy hibaalosztályokat eredményeznek. Korábban dobtak sima JS tárgyakat.

#### <a name="new-features"></a>Új funkciók
##### <a name="user-cancelable-requests"></a>Felhasználó által visszavonható kérelmek
Az áthelyezés lekérése belsőlehetővé teszi számunkra, hogy használja a böngésző AbortController API-t, hogy támogassa a felhasználó által megszakítható műveleteket. Olyan műveletek esetén, ahol több kérelem is folyamatban van (például a partíciók közötti lekérdezések), a műveletre vonatkozó összes kérelem megszakad. A modern böngésző felhasználók már rendelkeznek AbortController. A Node.js felhasználóknak polikitöltés-könyvtárat kell használniuk

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Átviteli művelet beállítása a db/container create művelet részeként
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A fejléctoken-létrehozás egy új @azure/cosmos-signkönyvtárra lett felosztva, . Bárki, aki hívja a Cosmos REST API-t közvetlenül használhatja ezt a fejlécek aláírására ugyanazt a kódot hívunk belül @azure/cosmos.

##### <a name="uuid-for-generated-ids"></a>UUID a generált azonosítókhoz
V2 volt egyéni kódot generálni elem azonosítók. Van kapcsolva, hogy a jól ismert és karbantartott közösségi könyvtár uuid.

##### <a name="connection-strings"></a>Kapcsolati sztringek
Most már át lehet adni egy kapcsolati karakterláncot az Azure Portalról másolt:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Továbbfejlesztett böngészőélmény
Bár a v2 SDK-t a böngészőben lehetett használni, nem volt ideális élmény. Több node.jbeépített könyvtárat kellett kitöltenie, és olyan kötegelőt kellett használnia, mint a Webpack vagy a Parcel. A v3 SDK teszi a ki a dobozból tapasztalat sokkal jobb a böngésző felhasználók számára.

* A kérés belső immára (#245)
* A puffer használatának eltávolítása (#330)
* Távolítsa el a csomópont beépített használatát az univerzális csomagok/API-k javára (#328)
* Váltás csomópont-megszakítás-vezérlőre (#294)

#### <a name="bug-fixes"></a>Hibajavítások
* Fix ajánlat olvasása és hozza vissza ajánlat tesztek (#224)
* EnableEndpointDiscovery javítása (#207)
* A hiányzó válaszországok javítása a kétoldalú eredményeken (#360)
* SQL-lekérdezésparamétertípus kibontása (#346)
* Ttl hozzáadása az ItemDefinition (#341)
* Cp-lekérdezési mutatók javítása (#311)
* ActivityId hozzáadása a FeedResponse-hoz (#293)
* Váltás _ts karakterláncról számra (#252)(#295)
* Kérelemdíj összesítésének javítása (#289)
* Üres karakterláncpartíció-kulcsok engedélyezése (#277)
* Karakterlánc hozzáadása az ütközési lekérdezéstípushoz (#237)
* UniqueKeyPolicy hozzáadása a tárolóhoz (#234)

#### <a name="engineering-systems"></a>Mérnöki rendszerek
Nem mindig a leglátványosabb változások, de segítenek a csapatunk nak jobb kódot szállítani, gyorsabban.

* Összesítés használata termelési buildekhez (#104)
* Frissítés typescript 3.5(#327)
* Konvertálás TS-projektreferenciákká. Tesztmappa kibontása (#270)
* NoUnusedLocals és noUnusedParameters engedélyezése (#275)
* Azure Pipelines YAML a CI buildek (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Nincs kódváltozás. Kijavít egy problémát, amely miatt néhány további fájl a 2.1.4-es csomagban található.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Regionális feladatátvétel javítása az újrapróbálkozási házirenden belül
* Fix ChangeFeed hasMoreResults tulajdonság
* Fejlesztői függőségi frissítések
* PolicheckExclusions.txt hozzáadása

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Váltás _ts karakterláncról számra
* Alapértelmezett indexelési tesztek javítása
* Backport uniqueKeyPolicy a v2
* A demó és a demó hibakeresési javításai

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Backport ajánlat javítások v3 ág
* Hiba kijavítása a executeNext() típusú aláírással
* Elírás javítások

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Szerkezetátalakítás kiépítése. Lehetővé teszi az SDK-verzió leépítését a létrehozás kor.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Új funkciók
* Hozzáadott ChangeFeed támogatás (#196)
* MultiPolygon adattípus hozzáadása indexeléshez (#191)
* Adja hozzá a "key" tulajdonságot a konstruktorhoz a masterkey (#202) aliasaként

#### <a name="fixes"></a>Javítások
* Fix hiba, ahol next() vissza helytelen értéket iterátor

#### <a name="engineering-improvements"></a>Mérnöki fejlesztések
* A gépírási felhasználás integrációs tesztjének hozzáadása (#199)
* Telepítés engedélyezése közvetlenül a GitHubról (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Kapcsolat hozzáadása a csomópontügynök típushoz. A gépírásban a @types/node felhasználóknak már nem kell függőségként telepíteniük
* Az előnyben részesített helyeket megfelelően tiszteletben tartják
* Fejlesztések a közreműködő fejlesztői dokumentációhoz
* Különböző elírásjavítások

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* A 2.0.3-ban bevezetett típusdefiníciós probléma javítása

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Függőség `big-integer` eltávolítása
* Váltson referenciadirektívákra az AsyncIterable típushoz. A gépírásban a felhasználóknak már nem kell testreszabniuk a "lib" beállítást.
* Elírás javítások

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Readme hivatkozások javítása

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Az újrapróbálkozási felület megvalósításának javítása

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* A JavaScript SDK 2.0.0-s verziójának GA-ja
* Hozzáadott támogatás többrégiós írások.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* A JavaScript SDK 2.0.0-s verziójának RC1-es verziója nyilvános előzetes verzióra.
* Új objektummodell, legfelső szintű CosmosClient és metódusok a megfelelő adatbázis-, tároló- és elemosztályok között. 
* Az [ígéretek](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)támogatása . 
* Az SDK TypeScript-re konvertálva.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm dokumentáció javítva.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* A csatlakozási problémák alapértelmezett újrapróbálkozásai támogatással bővültek.
* Támogatás hozzáadva a gyűjteménymódosítási hírcsatorna olvasásához.
* Javítottuk a munkamenet konzisztenciahibáját, amely időnként "olvasási munkamenet nem érhető el" hibát okozott.
* A lekérdezési mutatók támogatása hozzáadva.
* Módosított http ügynök maximális kapcsolatok száma.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Frissített dokumentáció az Azure Cosmos DB-re való hivatkozáshoz az Azure DocumentDB helyett.
* Hozzáadott támogatás proxyUrl beállítás ConnectionPolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Kisebb javítás a kis- és nagybetűket megkülönböztető fájlrendszerekhez.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Támogatja a munkamenet-konzisztenciát.
* Ez az SDK-verzió az Azure Cosmos DB Emulator legújabb verzióját igényli, amely letölthető a alkalmazásból. https://aka.ms/cosmosdb-emulator

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Megosztott egyezésű partíciós lekérdezések.
* Támogatja az erőforrás-kapcsolatot a kezdő és a záró perjelekkel (és a megfelelő tesztekkel).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   npm dokumentáció javítva.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Javítva egy hiba az executeStoredProcedure-ban, ahol az érintett dokumentumok speciális Unicode karakterekkel (LS, PS) voltak.
* Javítva egy hiba a dokumentumok unicode karakterekkel való kezelésében a partíciós kulcsban.
* Rögzített támogatás létrehozására gyűjtemények a neve adathordozó. A GitHub #114.
* Az engedélyengedélyezési jogkivonat támogatott. GitHub probléma #178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* A [Konzisztens előtag nevű új konzisztenciaszint](consistency-levels.md) támogatása hozzáadva.
* Hozzáadott támogatás UriFactory.
* Javítva egy Unicode támogatási hiba. GitHub-probléma #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Hozzáadva az összesítési lekérdezések támogatása (DARAB, MIN, MAX, SZUM és AVG).
* Hozzáadva a párhuzamosság mértékének szabályozásának lehetősége a partíciók közötti lekérdezésekhez.
* Az SSL-ellenőrzés letiltásának lehetősége az Azure Cosmos DB-emulátor on-t adta hozzá.
* A particionált gyűjtemények minimális átviteli hatása 10 100 RU/s-ról 2500 RU/s-ra csökkent.
* Javítva az egypartíciós gyűjtemény folytatási tokenhibája. A GitHub-probléma #107.
* Javítva a executeStoredProcedure hiba a 0 egyetlen param-ként való kezelésében. A GitHub #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Rögzített user-agent fejléc tartalmazza az SDK-verzió.
* Kisebb kódtisztítás.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* SSL-ellenőrzés letiltása az SDK-val az emulátor(hostname=localhost) célzásához.
* A parancsfájlnaplózás engedélyezésének támogatása a tárolt eljárás végrehajtása során.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* A keresztpartíciós párhuzamos lekérdezések támogatása hozzáadva.
* A TOP/ORDER BY lekérdezések támogatása hozzáadva a particionált gyűjtemények számára.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Újrapróbálkozási szabályzat támogatása a szabályozott kérelmekhez hozzáadva. (A szabályozott kérelmek túl nagy kivételt kapnak, hibakód: 429.) Alapértelmezés szerint az Azure Cosmos DB újrapróbálkozik kilenc alkalommal minden kérelmet, ha hibakód 429-es hibakód merül fel, tiszteletben tartva az újrapróbálkozási idő után a válasz fejlécében. A rögzített újrapróbálkozási időmost már beállítható a ConnectionPolicy objektum Újrapróbálkozási tulajdonságának részeként, ha figyelmen kívül szeretné hagyni a kiszolgáló által az újrapróbálkozások között visszaadott újrapróbálkozási időt. Az Azure Cosmos DB most antól legfeljebb 30 másodpercet vár minden egyes szabályozás alatt álló kérelemre (az újrapróbálkozások számától függetlenül), és a 429-es hibakóddal adja vissza a választ. Ez az idő a ConnectionPolicy objektum ÚjrapróbálkozásI beállítások tulajdonságában is felülbírálható.
* A Cosmos DB most antól x-ms-throttle-retry-count és x-ms-throttle-retry-wait-time-ms a válaszfejlécek minden kérelemben a szabályozás újrapróbálkozások számát és a kérelem által az újrapróbálkozások között várakozó összesített időt adja vissza.
* A RetryOptions osztály hozzá lett adva, és a ConnectionPolicy osztály RetryOptions tulajdonságát tette ki, amely az alapértelmezett újrapróbálkozási beállítások felülbírálására használható.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Hozzáadva a többrégiós adatbázisfiókok támogatása.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Hozzáadva a Time To Live(TTL) funkció támogatása a dokumentumokhoz.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* [Particionált gyűjtemények](partition-data.md) és [a felhasználó által definiált teljesítményszintek.](performance-levels.md)

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Javítva RangePartitionResolver.resolveForRead hiba, ahol nem adott vissza hivatkozásokat az eredmények rossz összeegyeztetése miatt.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Javítottunk hashPartitionResolver resolveForRead(): Ha nem adott meg partíciókulcs, kivételt szór, ahelyett, hogy az összes regisztrált hivatkozás listáját visszaadná.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Javítások probléma [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - dedikált HTTPS-ügynök: Ne módosítsa a globális ügynök az Azure Cosmos DB célokra. Használjon dedikált ügynököt a lib összes kéréshez.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Javítások [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Megfelelően kezelje a kötőjeleket a médiaazonosítókban.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Javítások probléma [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter figyelő szivárgás figyelmeztetés.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Javítja [a problémát #92](https://github.com/Azure/azure-documentdb-node/issues/90) - átnevezi a mappa kivonatát kivonatra a kis- és nagybetűket megkülönböztető rendszerekesetében.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* A skálázás támogatása a tartománypartíció-feloldók & hozzáadásával valósítsa meg.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Valósítsa meg upsert. Új upsertXXX metódusok a documentClient.New upsertXXX methods on documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Kihagyva, hogy a verziószámok a többi SDK-val egy vonalban kerüljenek.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* A Split Q burkolást ígér az új tárháznak.
* Frissítés az npm rendszerleíró adatbázis csomagfájljára.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Azonosítóalapú útválasztást valósít meg.
* Javítja a probléma [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - az aktuális tulajdonság ütközik a metódus current().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* A térinformatikai index támogatása hozzáadva.
* Az összes erőforrás azonosítótulajdonságának ellenőrzése. Az erőforrások azonosítói nem tartalmazhatnak ?, /, #, &#47;&#47; karaktereket, és szóközökkel végződhetnek.
* Új fejléc "index átalakítási folyamat" a ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* V2 indexelési házirendet valósít meg.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* #40 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) probléma - A magban megvalósított eslint és morgás konfigurációk, és ígéret sdk.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* A [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promises wrapper nem tartalmazza a hibás fejlécet.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Az ütközések lekérdezése a readConflicts, readConflictAsync és queryConflicts hozzáadásával valósítottmeg.
* Frissített API-dokumentáció.
* A [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - ügyfél.createDocumentAsync hiba.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

Az új funkciók és funkciók és optimalizálások csak az aktuális SDK-hoz kerülnek hozzáadásra, ezért ajánlott mindig a lehető leghamarabb frissíteni a legújabb SDK-verzióra.

A Cosmos DB-nek a rendszer egy kivisszavonult SDK-t használó kérését a szolgáltatás elutasítja.

> [!WARNING]
> **2020. augusztus 30-án**a Node client SDK for SQL API **1.x** verziója megszűnik. Ez csak az ügyféloldali node SDK-t érinti, és nem érinti a kiszolgálóoldali parancsfájlokat (tárolt eljárásokat, eseményindítókat és UDF-eket).
> 
>
<br/>

| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019. július 26.July 26, 2019 |--- |
| [3.0.4](#3.0.4) |2019. július 22.July 22, 2019 |--- |
| [3.0.3](#3.0.3) |2019. július 17.July 17, 2019 |--- |
| [3.0.2](#3.0.2) |2019. július 9.July 9, 2019 |--- |
| [3.0.0](#3.0.0) |2019. június 28.June 28, 2019 |--- |
| [2.1.5](#2.1.5) |2019. március 20.March 20, 2019 |--- |
| [2.1.4](#2.1.4) |2019. március 15.March 15, 2019 |--- |
| [2.1.3](#2.1.3) |2019. március 8., 2019. |--- |
| [2.1.2](#2.1.2) |2019. január 28., 2019. január 28. |--- |
| [2.1.1](#2.1.1) |2018. december 5., |--- |
| [2.1.0](#2.1.0) |2018. december 4., |--- |
| [2.0.5](#2.0.5) |2018. november 7., |--- |
| [2.0.4](#2.0.4) |2018. október 30.October 30, 2018 |--- |
| [2.0.3](#2.0.3) |2018. október 30.October 30, 2018 |--- |
| [2.0.2](#2.0.2) |2018. október 10.October 10, 2018 |--- |
| [2.0.1](#2.0.1) |2018. szeptember 25. |--- |
| [2.0.0](#2.0.0) |24, 2018. szeptember |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018. augusztus 2.August 2, 2018 |--- |
| [1.14.4](#1.14.4) |2018. május 03.May 03, 2018 |2020. augusztus 30. |
| [1.14.3](#1.14.3) |2018. május 03.May 03, 2018 |2020. augusztus 30. |
| [1.14.2](#1.14.2) |2017. december 21., 2017 |2020. augusztus 30. |
| [1.14.1](#1.14.1) |2017. november 10., 2017. |2020. augusztus 30. |
| [1.14.0](#1.14.0) |2017. november 9., |2020. augusztus 30. |
| [1.13.0](#1.13.0) |2017. október 11.October 11, 2017 |2020. augusztus 30. |
| [1.12.2](#1.12.2) |2017. augusztus 10.August 10, 2017 |2020. augusztus 30. |
| [1.12.1](#1.12.1) |2017. augusztus 10.August 10, 2017 |2020. augusztus 30. |
| [1.12.0](#1.12.0) |2017. május 10. |2020. augusztus 30. |
| [1.11.0](#1.11.0) |2017. március 16.March 16, 2017 |2020. augusztus 30. |
| [1.10.2](#1.10.2) |2017. január 27., az amerikai |2020. augusztus 30. |
| [1.10.1](#1.10.1) |2016. január 25. |2020. augusztus 30. |
| [1.10.0](#1.10.0) |2016. október 03. |2020. augusztus 30. |
| [1.9.0](#1.9.0) |2016. július 07. |2020. augusztus 30. |
| [1.8.0](#1.8.0) |2016. június 14. |2020. augusztus 30. |
| [1.7.0](#1.7.0) |2016. április 26. |2020. augusztus 30. |
| [1.6.0](#1.6.0) |2016. március 29. |2020. augusztus 30. |
| [1.5.6](#1.5.6) |2016. március 08. |2020. augusztus 30. |
| [1.5.5](#1.5.5) |2016. február 02. |2020. augusztus 30. |
| [1.5.4](#1.5.4) |2016. február 01. |2020. augusztus 30. |
| [1.5.2](#1.5.2) |2016. január 26. |2020. augusztus 30. |
| [1.5.2](#1.5.2) |2016. január 22. |2020. augusztus 30. |
| [1.5.1](#1.5.1) |2016. január 15. |2020. augusztus 30. |
| [1.5.0](#1.5.0) |2015. augusztus 25. |2020. augusztus 30. |
| [1.4.0](#1.4.0) |2015. augusztus 26. |2020. augusztus 30. |
| [1.3.0](#1.3.0) |2015. augusztus 26. |2020. augusztus 30. |
| [1.2.2](#1.2.2) |2015. szeptember 10. |2020. augusztus 30. |
| [1.2.1](#1.2.1) |2015. augusztus 15. |2020. augusztus 30. |
| [1.2.0](#1.2.0) |2015. augusztus 05. |2020. augusztus 30. |
| [1.1.0](#1.1.0) |2015. július 09. |2020. augusztus 30. |
| [1.0.3](#1.0.3) |2015. június 04. |2020. augusztus 30. |
| [1.0.2](#1.0.2) |2015. május 23. |2020. augusztus 30. |
| [1.0.1](#1.0.1) |2015. május 15. |2020. augusztus 30. |
| [1.0.0](#1.0.0) |2015. augusztus 25. |2020. augusztus 30. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB szolgáltatásról a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatáslapján olvashat bővebben.

