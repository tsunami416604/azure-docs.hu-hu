---
title: 'Azure Cosmos DB: SQL Node.js API-t, az SDK és erőforrások |} Microsoft Docs'
description: Tudnivalók az SQL Node.js API-t és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB Node.js SDK verziói között végrehajtott módosításokat.
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 5/3/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 271b090468dee306dc0bf2567aeff08eb0526642
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB Node.js SDK API-SQL: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**SDK letöltése**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[NODE.js API referenciadokumentációt](https://docs.microsoft.com/javascript/api/documentdb/?view=azure-node-latest)</td></tr>

<tr><td>**SDK telepítési utasításokat**</td><td>[Telepítési utasításokat](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Hozzájárul az SDK**</td><td>[GitHubon](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Minták**</td><td>[NODE.js-Kódminták](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**Első lépéseket ismertető oktatóanyag**</td><td>[Ismerkedés a Node.js SDK-val](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Webes alkalmazás oktatóanyag**</td><td>[Azure Cosmos DB használata Node.js-webalkalmazás létrehozása](sql-api-nodejs-application.md)</td></tr>

<tr><td>**Aktuális támogatott platform**</td><td> 
[NODE.js 6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[NODE.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[NODE.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[NODE.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="1.14.4"/>1.14.4</a>
* rögzített npm dokumentációját.

### <a name="1.14.3"/>1.14.3</a>
* A kapcsolódási problémák alapértelmezett újrapróbálkozások támogatása.
* Adatcsatorna-gyűjtemény módosítása a következőre támogatása.
* Rögzített munkamenet konzisztencia hiba, ami időnként a "olvasási munkamenet nem érhető el" miatt.
* Lekérdezés metrikák támogatása.
* Módosított http ügynök kapcsolatok maximális számát.

### <a name="1.14.2"/>1.14.2</a>
* Frissített hivatkozás Azure Cosmos DB helyett az Azure DocumentDB dokumentációját.
* ConnectionPolicy proxyUrl beállítás támogatása.

### <a name="1.14.1"/>1.14.1</a>
* Kis-és nagybetűket fájlrendszerek kisebb javítása.

### <a name="1.14.0"/>1.14.0</a>
* Támogatást nyújt a munkamenet-konzisztencia.
* Az SDK-verzió Azure Cosmos DB emulátorának elérhetősége a legújabb verziója szükséges a letölthető https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* A felosztott proofed közötti partíció lekérdezések.
* Támogatja a kezdő és záró perjelet (és a megfelelő tesztek) erőforrás-hivatkozás hozzáadása.

### <a name="1.12.2"/>1.12.2</a>
*   rögzített npm dokumentációját.

### <a name="1.12.1"/>1.12.1</a>
* Rögzített programhiba executeStoredProcedure ahol érintett dokumentumok kellett speciális Unicode-karaktereket (LS, PS).
* A partíciókulcs az Unicode karaktereket tartalmazó dokumentumok kezelése hiba kijavítva.
* Gyűjtemények létrehozása a név adathordozó rögzített támogatása. Github probléma #114.
* Engedély engedélyezési jogkivonat rögzített támogatása. Github probléma #178.

### <a name="1.12.0"/>1.12.0</a>
* Támogatása egy új [konzisztenciaszint](consistency-levels.md) ConsistentPrefix nevezik.
* UriFactory támogatása.
* Egy Unicode támogatási hiba kijavítva. GitHub probléma #171.

### <a name="1.11.0"/>1.11.0</a>
* Összesítési lekérdezéseket (COUNT, MIN, MAX, SUM és átlagos) támogatása.
* A beállítás párhuzamossági a partíció lekérdezések közötti mértékű vezérlése hozzá.
* A kívánt beállítást SSL ellenőrzési letiltása Azure Cosmos DB emulatorban futtatásakor hozzá.
* Minimális átviteli sebességet 2500 RU/mp 10,100 RU/mp a particionált gyűjtemények szintűre csökkent.
* A folytatási egypartíciós gyűjtemény token hiba kijavítva. Github probléma #107.
* Rögzített 0 kezelése, egyetlen param executeStoredProcedure hibát. Github probléma #155.

### <a name="1.10.2"/>1.10.2</a>
* Rögzített felhasználói ügynök fejléc az SDK-verzió.
* Kisebb kód karbantartása.

### <a name="1.10.1"/>1.10.1</a>
* SSL-ellenőrzés letiltása, ha az SDK használatával a emulator(hostname=localhost) céloz.
* Támogatja a tárolt eljárás végrehajtása során parancsfájl naplózásának engedélyezéséről.

### <a name="1.10.0"/>1.10.0</a>
* Támogatja a párhuzamos lekérdezések partíció közötti.
* FELSŐ/ORDER BY lekérdezések a particionált gyűjtemények támogatása.

### <a name="1.9.0"/>1.9.0</a>
* Szabályozottan halmozott kérelmek hozzáadott újrapróbálkozási házirend támogatása. (A szabályozottan halmozott kérelmek egy kérelem aránya túl nagy kivétel, hibakód 429 kapnak.) Alapértelmezés szerint Azure Cosmos DB újrapróbálja kilenc alkalommal az egyes kérelmek 429. Hibakód: a rendszer észlelt, amikor az retryAfter idő a válaszfejlécet érvényesítenie. A rögzített újrapróbálkozási időköz most már beállítható a RetryOptions tulajdonság részeként a ConnectionPolicy objektum Ha azt szeretné, figyelmen kívül hagyja a próbálkozások közötti kiszolgáló által visszaadott retryAfter idő. Azure Cosmos-adatbázis most megvárja, legfeljebb 30 másodpercig (függetlenül újrapróbálkozások száma) leszabályozta, és visszaadja a választ 429. Hibakód: minden egyes kérelemhez. Most is felülbírálható lesz a RetryOptions tulajdonságban ConnectionPolicy objektumon.
* Cosmos DB most adja vissza x-ms-szabályozási--újrapróbálkozások és x-ms-throttle-retry-wait-time-ms a válaszfejlécek minden kérelemben a késleltetési jelöléséhez újra számát és a próbálkozások közötti várta a kérelem összesített idő szerint.
* A RetryOptions osztály adott, az ilyen a ConnectionPolicy osztály, amely felülbírálhatja az alapértelmezett beállítások némelyike használható a RetryOptions tulajdonsága.

### <a name="1.8.0"/>1.8.0</a>
* Több területi adatbázis fiókok támogatása.

### <a name="1.7.0"/>1.7.0</a>
* Idő a Live(TTL) szolgáltatás dokumentumok támogatása.

### <a name="1.6.0"/>1.6.0</a>
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználói teljesítményszintet](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Javított RangePartitionResolver.resolveForRead hibát, ahol azt lett nem ad vissza hivatkozások eredmények rossz concat miatt.

### <a name="1.5.5"/>1.5.5</a>
* Rögzített hashParitionResolver resolveForRead(): Ha nincs megadva partíciókulcs kivétel, helyett sorolja fel az összes regisztrált hivatkozások kiváltása volt.

### <a name="1.5.4"/>1.5.4</a>
* Kijavítja a hibát [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -HTTPS-ügynök dedikált: elkerülése érdekében a globális ügynök Azure Cosmos DB célokra módosítása. Használja a dedikált ügynököt az összes a lib kérelmek.

### <a name="1.5.3"/>1.5.3</a>
* Kijavítja a hibát [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - megfelelően kötőjelek az adathordozó-azonosítók kezelésére.

### <a name="1.5.2"/>1.5.2</a>
* Kijavítja a hibát [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter figyelő szivárgás lépett fel, figyelmeztetés.

### <a name="1.5.1"/>1.5.1</a>
* Kijavítja a hibát [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -nevezze át a mappát kivonat kivonatoló rendszerek kis-és nagybetűket.

### <a name="1.5.0"/>1.5.0</a>
* Horizontális segítséget nyújt kivonatoló & tartomány partíció feloldókat hozzáadásával.

### <a name="1.4.0"/>1.4.0</a>
* Upsert megvalósításához. Új upsertXXX metódusai documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Kihagyott verziószámok átveendő Csomagjától összehangolás.

### <a name="1.2.2"/>1.2.2</a>
* Vegyes Q kihasználásának köszönhetően akár még a burkoló új tárházba.
* Frissítse az npm beállításjegyzék csomagfájlt.

### <a name="1.2.1"/>1.2.1</a>
* Megvalósítja-alapú útválasztási azonosítója.
* Kijavítja a hibát [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuális tulajdonság metódus current() ütközik.

### <a name="1.2.0"/>1.2.0</a>
* A földrajzi index támogatása.
* Ellenőrzi az összes erőforrás id tulajdonság. Erőforrások azonosító nem tartalmazhat?, /, #, &#47; &#47;, karaktereket vagy záró szóközt.
* Új fejléc "index átalakítása folyamatban" hozzáadása ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* V2 indexelési házirendet alkalmazza.

### <a name="1.0.3"/>1.0.3</a>
* A probléma [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - eslint megvalósítása és a core-konfigurációja grunt és SDK készletéről.

### <a name="1.0.2"/>1.0.2</a>
* A probléma [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -tett burkoló nem tartalmazza a hiba: a fejlécben.

### <a name="1.0.1"/>1.0.1</a>
* Az ütközések readConflicts, readConflictAsync és queryConflicts hozzáadásával lekérdezés megvalósított képessége.
* Frissített API dokumentációját.
* A probléma [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync hiba.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
A Microsoft biztosít értesítési legalább **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető.

A Cosmos DB használatával kivont SDK kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.14.4](#1.14.4) |2018. Előfordulhat, hogy 03. |--- |
| [1.14.3](#1.14.3) |2018. Előfordulhat, hogy 03. |--- |
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
| [1.8.0](#1.8.0) |2016. június 14. |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.0](#1.6.0) |2016. március 29. |--- |
| [1.5.6](#1.5.6) |2016. március 08. |--- |
| [1.5.5](#1.5.5) |2016. február 02. |--- |
| [1.5.4](#1.5.4) |2016. február 01. |--- |
| [1.5.2](#1.5.2) |2016. január 26. |--- |
| [1.5.2](#1.5.2) |2016. január 22. |--- |
| [1.5.1](#1.5.1) |2016. január 4. |--- |
| [1.5.0](#1.5.0) |2015. december 31. |--- |
| [1.4.0](#1.4.0) |2015. október 06. |--- |
| [1.3.0](#1.3.0) |2015. október 06. |--- |
| [1.2.2](#1.2.2) |2015. szeptember 10. |--- |
| [1.2.1](#1.2.1) |2015. augusztus 15. |--- |
| [1.2.0](#1.2.0) |2015. augusztus 05. |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.3](#1.0.3) |2015. június 04. |--- |
| [1.0.2](#1.0.2) |2015. május 23. |--- |
| [1.0.1](#1.0.1) |2015. május 15. |--- |
| [1.0.0](#1.0.0) |2015. áprilisi 08 |--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

