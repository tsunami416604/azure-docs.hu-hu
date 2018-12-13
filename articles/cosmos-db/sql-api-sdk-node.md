---
title: 'Az Azure Cosmos DB: SQL Node.js API, SDK és -erőforrások'
description: Mindent megtudhat a SQL Node.js API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB Node.js SDK minden verziója közötti végzett módosításokat.
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5616ee6f6b855cb366f24c79c73d12a03b69b2ac
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865658"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB Node.js SDK-t az SQL API-hoz: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

|Erőforrás  |Hivatkozás  |
|---------|---------|
|SDK letöltése  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Az API dokumentációja  |  [A JavaScript SDK-forrásdokumentáció](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK telepítési utasításokat  |  [Telepítési utasítások](https://github.com/Azure/azure-cosmos-js#installation)
|Hozzájárul az SDK-t | [GitHubon](https://github.com/Azure/azure-cosmos-js/tree/master)
| Példák | [NODE.js-Kódminták](sql-api-nodejs-samples.md)
| Kezdeti lépéseket ismertető oktatóanyag | [A JavaScript SDK használatának első lépései](sql-api-nodejs-get-started.md)
| Alapú webappokról szóló oktatóanyagunkat | [Az Azure Cosmos DB Node.js webalkalmazás létrehozása](sql-api-nodejs-application.md)
| Aktuális támogatott platform | [NODE.js 6.x-es](https://nodejs.org/en/blog/release/v6.10.3/) – SDK 2.0.0-s verzió vagy újabb szükséges.<br/>[NODE.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [NODE.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [NODE.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2.0.5"/>2.0.5</a>
* Hozzáadja a csomópont ügynöktípus felületet. Typescript felhasználók többé nem kell telepíteni @types/node függőségként
* Előnyben részesített helyek most már megfelelően figyelembe véve
* Hozzájárulás fejlesztői dokumentáció fejlesztései
* Különböző elgépelte javításai

### <a name="2.0.4"/>2.0.4-es</a>
* Javítások írja be a definíció probléma 2.0.3 rendszerben bevezetett

### <a name="2.0.3"/>2.0.3</a>
* Távolítsa el `big-integer` függőség
* Hivatkozási irányelvek AsyncIterable típusra váltani. Typescript felhasználóknak már nem kell testre szabhatja a saját "lib" beállítást.
* Elírta javításai

### <a name="2.0.2"/>2.0.2-es</a>
* Fontos hivatkozások javítása

### <a name="2.0.1"/>2.0.1</a>
* Javítsa ki a felhasználói felület megvalósításához újrapróbálkozási

### <a name="2.0.0"/>2.0.0</a>
* A JavaScript SDK 2.0.0-s verziójának általánosan elérhető
* Többrégiós írási műveletek támogatása.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1-es verziójának 2.0.0-s a JavaScript SDK-t a nyilvános előzetes verziójához.
* Új hálózatiobjektum-modellt, a legfelső szintű CosmosClient és módszerek elosztja a megfelelő adatbázist, a tároló és a cikk osztályokat. 
* Támogatja a [biztosítja a](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK-t TypeScript dátumformátumra alakítja át.

### <a name="1.14.4"/>1.14.4</a>
* az npm dokumentációja kijavítva.

### <a name="1.14.3"/>1.14.3</a>
* Támogatás hozzáadva a alapértelmezett újrapróbálkozások kapcsolati problémák.
* Hírcsatorna támogatása, olvassa el a gyűjtemény megváltoztatása.
* Rögzített munkamenet konzisztencia hibát okozó időnként "olvasási munkamenet nem érhető el".
* Támogatás hozzáadva a lekérdezés metrikákat.
* Kapcsolatok maximális száma a http-ügynök módosítani.

### <a name="1.14.2"/>1.14.2</a>
* Frissített dokumentáció az Azure Cosmos DB helyett az Azure DocumentDB-referenciáját.
* Támogatás hozzáadva a ConnectionPolicy proxyUrl beállítását.

### <a name="1.14.1"/>1.14.1</a>
* Kis-és nagybetűket fájlrendszerek kisebb javítása.

### <a name="1.14.0"/>1.14.0</a>
* Munkamenet-konzisztencia támogat.
* Az SDK-verziója letölthető Azure Cosmos DB Emulatort elérhető legújabb verziója szükséges https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Partíciós lekérdezések közötti proofed felosztása.
* Támogatja a kezdő és záró perjeleket (és a megfelelő tesztek) erőforrás-hivatkozás hozzáadása.

### <a name="1.12.2"/>1.12.2</a>
*   az npm dokumentációja kijavítva.

### <a name="1.12.1"/>1.12.1</a>
* Kijavítva a hiba a executeStoredProcedure, ahol a dokumentumok részt vevő kellett speciális Unicode-karaktert (LS-, PS-).
* Kijavítva a hiba, a partíciókulcs a Unicode-karaktert tartalmazó dokumentumok kezelése.
* Gyűjtemények létrehozása a neve-media rögzített támogatása. Github-problémát #114.
* Engedély engedélyezési jogkivonat rögzített támogatása. Github-problémát #178.

### <a name="1.12.0"/>1.12.0</a>
* Támogatás hozzáadva az új [konzisztenciaszint](consistency-levels.md) nevű új ConsistentPrefix.
* Támogatás hozzáadva a UriFactory.
* Unicode támogatás hibája kijavítva. GitHub-problémát #171.

### <a name="1.11.0"/>1.11.0</a>
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása.
* A partíciós lekérdezések közötti párhuzamosság való hozzáadása.
* A beállítás letiltja az SSL-ellenőrzést, amikor futtat az Azure Cosmos DB Emulatort hozzá.
* Süllyesztett minimálisan 2500 RU/s 10,100 RU/s a particionált gyűjtemények átviteli sebességet.
* Egypartíciós gyűjtemény a folytatási token hiba kijavítva. Github-problémát #107.
* Rögzített 0 kezelésében, egyetlen param executeStoredProcedure hibát. Github-problémát #155.

### <a name="1.10.2"/>1.10.2</a>
* Rögzített felhasználói ügynök fejléc tartalmazza az SDK-verzió.
* Kis kód tisztítását a kiszolgálók.

### <a name="1.10.1"/>1.10.1</a>
* SSL-ellenőrzés letiltása, az SDK-t, amelyekre a emulator(hostname=localhost) használatakor.
* Támogatás hozzáadva a tárolt eljárás végrehajtása során parancsfájl naplózás engedélyezése.

### <a name="1.10.0"/>1.10.0</a>
* Támogatás hozzáadva a párhuzamos lekérdezések partíció közötti.
* Támogatás hozzáadva a particionált gyűjtemények felső/ORDER BY lekérdezések.

### <a name="1.9.0"/>1.9.0</a>
* A hozzáadott újrapróbálkozási házirend támogatása szabályozott kérelmeinek száma. (Szabályozott kérelmeinek kap egy kérelem aránya túl nagy kivétel, 429-es hibakód). Alapértelmezés szerint az Azure Cosmos DB újrapróbálkozik kilenc alkalommal az egyes kérések 429-es hibakód észlelt, amikor a válaszfejlécben retryAfter idő teljesítésére. Egy rögzített újrapróbálkozási időközt is most már beállíthat a RetryOptions tulajdonság részeként a ConnectionPolicy objektum Ha azt szeretné, figyelmen kívül hagyja a próbálkozások közötti kiszolgáló által visszaadott retryAfter idő. Az Azure Cosmos DB mostantól megvárja, legfeljebb 30 másodpercig az egyes kérelmek szabályozva lett (függetlenül az újrapróbálkozások számát), és visszaadja a választ, a 429-es hibakód. Ezúttal is felülbírálható ConnectionPolicy objektumon a RetryOptions tulajdonságban.
* Cosmos DB mostantól adja vissza x-ms-szabályozás--újrapróbálkozások és x-ms-throttle-retry-wait-time-ms, ismételje meg a válaszfejlécek, a késleltetési jelölésére minden kérésben, száma és a kérelem összesített, az újrapróbálkozások közötti idő.
* A RetryOptions osztály lett hozzáadva, közzéteheti a ConnectionPolicy osztály felülbírálhatja az alapértelmezett újrapróbálkozási opciók egy része felhasználható RetryOptions tulajdonsága.

### <a name="1.8.0"/>1.8.0</a>
* Többrégiós adatbázisfiókhoz támogatása.

### <a name="1.7.0"/>1.7.0</a>
* Dokumentumok idő a Live(TTL) funkció támogatása.

### <a name="1.6.0"/>1.6.0</a>
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Kijavított RangePartitionResolver.resolveForRead hiba, ahol ez volt nem visszaadó hivatkozások a hibás concat eredmények miatt.

### <a name="1.5.5"/>1.5.5</a>
* Rögzített hashPartitionResolver resolveForRead(): Ha nincs megadva partíciós kulcs lett kivétel a kivétel, minden regisztrált hivatkozások listájának visszaadása helyett.

### <a name="1.5.4"/>1.5.4</a>
* Javítva [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – HTTPS ügynök dedikált: az Azure Cosmos DB célokra globális ügynök módosítása. Használja a dedikált ügynököt az összes a lib kérelmek.

### <a name="1.5.3"/>1.5.3</a>
* Javítva [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – megfelelően kötőjelek szerepelhetnek, az adathordozó-azonosítók kezelésére.

### <a name="1.5.2"/>1.5.2</a>
* Javítva [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter figyelő memóriavesztés figyelmeztetés.

### <a name="1.5.1"/>1.5.1</a>
* Javítva [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -kivonat mappa átnevezése a kis-és nagybetűket rendszerek kivonatát.

### <a name="1.5.0"/>1.5.0</a>
* Tartomány & kivonat partíció feloldók hozzáadásával megvalósítása a horizontális skálázás támogatása.

### <a name="1.4.0"/>1.4.0</a>
* Upsert megvalósításához. A documentClient új upsertXXX módszerek.

### <a name="1.3.0"/>1.3.0</a>
* Ahhoz, hogy a verziószámok más SDK-k ciklustól kihagyva.

### <a name="1.2.2"/>1.2.2</a>
* Split Q új tárházba burkoló biztosítja.
* Alkalmazáscsomag-fájl az npm-beállításjegyzék frissítése.

### <a name="1.2.1"/>1.2.1</a>
* Valósítja meg az azonosító alapján útválasztást.
* Javítva [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuální vlastnost metódus current() ütközik.

### <a name="1.2.0"/>1.2.0</a>
* Támogatás hozzáadva a földrajzi index.
* Ellenőrzi az összes erőforrás id tulajdonsága. Az erőforrások azonosítóit nem tartalmazhat?, /, #, &#47; &#47;, karaktereket vagy záró szóközzel.
* ResourceResponse ad hozzá új fejléc "index átalakítási folyamat".

### <a name="1.1.0"/>1.1.0</a>
* Indexelési házirend V2 valósítja meg.

### <a name="1.0.3"/>1.0.3</a>
* A probléma [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – eslint megvalósított és grunt a core-konfigurációja és garantálniuk SDK-t.

### <a name="1.0.2"/>1.0.2</a>
* A probléma [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -Ígéreteinket burkoló nem tartalmazza a hiba a fejléc.

### <a name="1.0.1"/>1.0.1</a>
* Megvalósított épülő lekérdezés képessége az ütközések readConflicts readConflictAsync és queryConflicts hozzáadásával.
* Frissített API-dokumentációt.
* A probléma [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync hiba.

### <a name="1.0.0"/>1.0.0</a>
* ÁLTALÁNOSAN ELÉRHETŐ SDK-T.

## <a name="release--retirement-dates"></a>Állapot tárolá & kivezetési dátum
A Microsoft biztosít értesítési legalább **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz, ezért javasoljuk, hogy mindig a legújabb SDK verzióra frissít leghamarabb lehető.

Egy kivont SDK Cosmos DB használatával bármely kérelem elutasítja a szolgáltatás által.

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018. augusztus 2. |--- |
| [1.14.4](#1.14.4) |2018. május 03. |--- |
| [1.14.3](#1.14.3) |2018. május 03. |--- |
| [1.14.2](#1.14.2) |2017. december 21. |--- |
| [1.14.1](#1.14.1) |2017. november 10. |--- |
| [1.14.0](#1.14.0) |2017. november 9. |--- |
| [1.13.0](#1.13.0) |2017. október 11. |--- |
| [1.12.2](#1.12.2) |2017. augusztus 10. |--- |
| [1.12.1](#1.12.1) |2017. augusztus 10. |--- |
| [1.12.0](#1.12.0) |2017. május 10. |--- |
| [1.11.0](#1.11.0) |2017. március 16. |--- |
| [1.10.2](#1.10.2) |2017. január 27. |--- |
| [1.10.1](#1.10.1) |2016. december 22. |--- |
| [1.10.0](#1.10.0) |2016. október 03. |--- |
| [1.9.0](#1.9.0) |2016. július 07. |--- |
| [1.8.0](#1.8.0) |2016. június 14-én |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.0](#1.6.0) |2016. március 29-én |--- |
| [1.5.6](#1.5.6) |2016. március 08 |--- |
| [1.5.5](#1.5.5) |2016. február 02 |--- |
| [1.5.4](#1.5.4) |2016. február 01. |--- |
| [1.5.2](#1.5.2) |2016. január 26. |--- |
| [1.5.2](#1.5.2) |2016. január 22. |--- |
| [1.5.1](#1.5.1) |2016. január 4-én |--- |
| [1.5.0](#1.5.0) |2015. december 31-ig. |--- |
| [1.4.0](#1.4.0) |2015. október 06. |--- |
| [1.3.0](#1.3.0) |2015. október 06. |--- |
| [1.2.2](#1.2.2) |2015. szeptember 10. |--- |
| [1.2.1](#1.2.1) |2015. augusztus 15. |--- |
| [1.2.0](#1.2.0) |2015. augusztus 05 |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.3](#1.0.3) |2015. június 04. |--- |
| [1.0.2](#1.0.2) |2015. május 23. |--- |
| [1.0.1](#1.0.1) |2015. május 15. |--- |
| [1.0.0](#1.0.0) |2015. április 08. |--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

