---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB használata
description: Ez a cikk bemutatja a például a tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d1960fbc9fc9e8c1d672b66d3cf1f41399842059
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083198"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények

Az Azure Cosmos DB a JavaScript nyelvintegrált, a tranzakciós végrehajtását biztosítja. Ha az SQL API használatával az Azure Cosmos DB, írhat **tárolt eljárások**, **eseményindítók**, és **felhasználó által definiált függvények (UDF)** JavaScript nyelven. A logikai írhat a JavaScript, amely az adatbázis motorjában. Hozhat létre, és hajtsa végre az UDF-EK, eseményindítók és tárolt eljárások használatával [az Azure portal](https://portal.azure.com/), a [JavaScript nyelvű lekérdezési API-t az Azure Cosmos DB integrált](javascript-query-api.md) vagy a [Cosmos DB SQL API-ügyfél SDK-k](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Kiszolgálóoldali programozása használatának előnyei

Javascriptben tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF) írása lehetővé teszi, hogy olyan funkciógazdag alkalmazások létrehozására és rendelkeznek a következő előnyökkel jár:

* **Eljárási logikai:** A JavaScript egy magas szintű programozási nyelv, amely expressz üzleti logika gazdag és ismerős felületet biztosít. Az adatok összetett műveletek sorozata hajthat végre.

* **Elemi tranzakciókat:** Az Azure Cosmos DB garantálja, hogy egyetlen tárolt eljárás vagy eseményindító belül végzett adatbázis-műveletek elemiek. A atomi funkció lehetővé teszi, hogy egy alkalmazás az egy kötegben kapcsolódó műveletek egyesíteni, hogy minden művelet sikeres legyen, vagy egyiket sem sikerült.

* **Teljesítmény:** A JSON-adatokat a JavaScript nyelvű típus rendszer belsőleg képeződik le. Ez a leképezés optimalizálások Lusta materialization a pufferkészletben és az így elérhető igény szerinti a végrehajtó kód JSON-dokumentumok, például számos tesz lehetővé. Vannak más üzleti logikát az adatbázis, amely tartalmazza a szállítási társított teljesítmény előnyeit:

   * *Kötegelés:* Csoport műveletek, mint a beszúrások, és küldheti el ezeket a tömegesen. A hálózati forgalom késés költségeket és a tároló terhelését a létrehozott különálló tranzakciók jelentősen csökken.

   * *Üzem előtti fordítási:* Tárolt eljárások, eseményindítók és felhasználói függvények minden szkripthívás időpontjában fordítási költségek elkerülése érdekében olyan implicit módon előre lefordított kód bájt formátumra. Üzem előtti fordítás miatt a tárolt eljárások hívása gyors és alacsony erőforrás-igényű.

   * *Alkalmazás-előkészítés:* Műveletek igények egy eseményindító mechanizmust, amely végrehajthat egyet vagy az adatok további frissítései. Atomitást kívül is teljesítménybeli előnyök a kiszolgáló oldalán végrehajtása közben.

* **Beágyazás:** Tárolt eljárások segítségével egy helyen logikai csoportosítása. Beágyazás hozzáad egy olyan absztrakciós réteget az adatokat, ami lehetővé teszi a való összpontosításnak köszönhetően az alkalmazások egymástól függetlenül az adatok felett. Absztrakciós réteg akkor hasznos, ha az adatok séma nélküli, és nem kell további logikát hozzáadása közvetlenül az alkalmazásba való kezeléséhez. Az absztrakciós lehetővé teszi, hogy az adatok biztonságos egyszerűsítheti a parancsfájlokat a hozzáférést a megtartása.

> [!TIP]
> Tárolt eljárások leginkább alkalmasak arra, hogy vannak (nagy erőforrásigényű) írási műveleteket. Elhelyezésekor tárolt eljárások, optimalizálhatja körül fejlécbe foglalja az írások lehetséges maximális számát. Általánosan fogalmazva a tárolt eljárások nem állnak a lehető leghatékonyabb módon a batch nagy mennyiségű olvasási tárolt eljárások használatával térjen vissza az ügyfél nem eredményez, a kívánt juttatás nagy számú olvasási műveleteket végezni.

## <a name="transactions"></a>Tranzakciók

Egy tipikus adatbázisban lévő tranzakció munka egyetlen logikai egységként végrehajtott műveletek sorozataként lehet definiálni. Minden egyes tranzakciót biztosít **ACID tulajdonság garanciák**. ACID egy jól ismert rövidítése jelző: **A**tomicity, **C**onsistency, **I**solation, és **D**urability. 

* Atomitást garantálja, hogy minden a tranzakción belül végzett műveletek kezeli a rendszer egyetlen egységet, és ezek közül bármelyik minden elkötelezettek, vagy egyikük vannak. 

* Konzisztencia gondoskodik arról, hogy az adatok mindig érvényes állapotban a tranzakciók között. 

* Elkülönítés garantálja, hogy két tranzakciók egymással ütköző – számos kereskedelmi rendszer adja meg, hogy több elkülönítési szintet, amely használható alkalmazás igényeinek megfelelően. 

* Tartóssági biztosítja, hogy bármilyen változás, amely egy adatbázisban előjegyzett mindig legyen jelen.

Az Azure Cosmos DB a JavaScript futásidejű üzemel az adatbázis motorjában. Ezért a tárolt eljárások és triggerek-kérelmek végrehajtása ugyanabban a hatókörben, mint az adatbázis-munkamenet. Ez a funkció lehetővé teszi, hogy az Azure Cosmos DB garantálja az ACID tulajdonságok tárolt eljárás vagy eseményindító részét képező összes művelethez. Példák: [tranzakció megvalósítása](how-to-write-stored-procedures-triggers-udfs.md#transactions) cikk.

### <a name="scope-of-a-transaction"></a>A tranzakciók hatóköre

Ha egy tárolt eljárást társítva az Azure Cosmos-tárolókat, majd a tárolt eljárás a tranzakció-hatókörben, egy logikai partíciókulcs végrehajtja a függvény. Minden egyes tárolt eljárás végrehajtása tartalmaznia kell egy logikai partíciókulcs-értékkel, amely megfelel a tranzakció hatókörén. További információkért lásd: [Azure Cosmos DB particionálási](partition-data.md) cikk.

### <a name="commit-and-rollback"></a>Érvényesítés és visszaállítás

Tranzakciók natív módon integrálva vannak az Azure Cosmos DB a JavaScript-programozási modellt. JavaScript-függvény, belül minden művelet automatikusan burkolja egyetlen tranzakció alatt. A JavaScript-logika egy tárolt eljárást az összes kivétel nélkül befejeződött, a tranzakción belül minden művelet esetén fontos, hogy az adatbázis. Utasítások, például `BEGIN TRANSACTION` és `COMMIT TRANSACTION` (jól ismert, relációs adatbázisok) az Azure Cosmos DB járnak. Ha vannak olyan kivételek, amelyek a parancsfájlból, az Azure Cosmos DB a JavaScript futásidejű rendszer állítja vissza a teljes tranzakció. Ennek megfelelően hatékonyan egyenértékű hívása kivétel egy `ROLLBACK TRANSACTION` Azure Cosmos DB-ben.

### <a name="data-consistency"></a>Adatkonzisztencia

Tárolt eljárások és eseményindítók mindig futtatja egy Azure Cosmos-tároló az elsődleges replikán. Ez a funkció biztosítja, hogy beolvassa a tárolt eljárások ajánlat [erős konzisztencia](consistency-levels-tradeoffs.md). Az elsődleges vagy bármely másodlagos replikája hajtható végre felhasználó által definiált függvények használatával lekérdezéseket. Tárolt eljárások és eseményindítók tranzakciós írások támogatására készültek – a csapattagok pedig nyugodtabban aludhatnak a csak olvasható logikai alkalmazás kiszolgálóoldali logikát legjobb megvalósítása, és végzett lekérdezések a [Azure Cosmos DB SQL API SDK-k](sql-api-dotnet-samples.md), segít telítsük a adatbázis-átviteli sebessége. 

## <a name="bounded-execution"></a>Korlátozott végrehajtása

Azure Cosmos DB minden művelet a megadott időtúllépési időtartam belül kell végrehajtania. Ez a korlátozás vonatkozik JavaScript-függvények - tárolt eljárások, eseményindítók és felhasználó által definiált függvények. Ha egy művelet adott időkorláton belül nem fejeződik, a tranzakció vissza lesz állítva.

Vagy biztosíthatja, hogy a JavaScript-függvények befejezni az időkorláton belül, vagy egy folytatási-alapú modell, batch és folytathat végrehajtási megvalósítására. Annak érdekében, hogy a tárolt eljárások és eseményindítók a kezeléséhez határidők fejlesszen, az összes functions az Azure Cosmos-tárolóban található (például létrehozása, olvasása, frissítése és az elemek törlése) logikai érték, amely azt jelöli, hogy a művelet vissza végezze el. Ha az értéke FALSE (hamis), utal, hogy az eljárást kell belefoglalni végrehajtása, mert a parancsfájlt is használja további idő vagy a kiosztott átviteli sebesség, mint a beállított érték. Az első elfogadhatatlan store művelet műveletek aszinkron előtt garantáltan elvégezni, ha a tárolt eljárás ideje alatt befejeződik, és nem várólistára további kérések eredményéről. Így az operations kell lennie az aszinkron egyszerre csak egy JavaScript visszahívási egyezmény használatával kezelheti a parancsfájl átvitelvezérlés. Szkriptek végrehajtása az egy kiszolgálóoldali környezetben, mert azok szigorúan szabályozza. Szkriptek végrehajtása határok ismételten megsértő inaktív lehet megjelölni, és nem hajtható végre, és, hogy tartsa tiszteletben a végrehajtási határok létre kell hozni.

JavaScript-függvények megkülönböztetik státuszban [kiosztott átviteli kapacitás](request-units.md). JavaScript-függvények sikerült potenciálisan végül a kérelemegység rövid időn belül nagy számú használatával, és lehet, hogy sebessége korlátozott, ha a kiosztott átviteli kapacitás határértékét. Fontos megjegyezni, hogy parancsfájlok használata mellett az átviteli sebesség töltött végrehajtó adatbázis-művelet átviteli sebességben, bár ezek az adatbázis-műveletek némileg kevésbé költséges, mint az ügyfél ugyanazokat a műveleteket végrehajtó.

## <a name="triggers"></a>Eseményindítók

Ez a szakasz ismerteti az eseményindítók két típusát:

### <a name="pre-triggers"></a>Üzem előtti eseményindítók

Az Azure Cosmos DB biztosít eseményindítókat, amelyek egy Azure Cosmos DB elemet egy művelet végrehajtásával is elindítható. Például megadhatja egy üzem előtti eseményindító egy elem létrehozásakor. Ebben az esetben az üzem előtti eseményindítót fog futni, az elem létrehozása előtt. Üzem előtti eseményindítók nem lehet bemeneti paramétereket. Ha szükséges, a támogatásikérelem-objektum használható frissíteni a dokumentum törzsében eredeti kérelemből. Eseményindítók regisztrálásakor a felhasználók megadhatják a műveletek, amelyek való futtatás. Ha az eseményindító készült `TriggerOperation.Create`, ez azt jelenti, hogy az eseményindító használatával a cserét nem fog tudni. Példák: [eseményindítók írásával](how-to-write-stored-procedures-triggers-udfs.md#triggers) cikk.

### <a name="post-triggers"></a>Utáni eseményindítók

Üzem előtti eseményindítók hasonlóan utáni eseményindítók is társítva legyenek egy Azure Cosmos DB elemet egy olyan műveletet, és nem kívánnak bemeneti paraméterek. Ezek futtatása *után* a művelet befejeződött, és hozzáférhetnek a az ügyfélnek küldött válaszüzenet. Példák: [eseményindítók írásával](how-to-write-stored-procedures-triggers-udfs.md#triggers) cikk.

## <a id="udfs"></a>Felhasználó által definiált függvények

Felhasználó által definiált függvények (UDF) kiterjesztése az SQL API-lekérdezési nyelv szintaxisát, és egyéni üzleti logika megvalósítása könnyen használhatók. Ezek csak a lekérdezések belül is meghívható. UDF-EK nem rendelkeznek hozzáféréssel a context objektumot, és van kialakítva, hogy a számítási csak JavaScript használható. Ezért UDF-et futtatni a másodlagos replikákon. Példák: [felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md#udfs) cikk.

## <a id="jsqueryapi"></a>A JavaScript nyelvintegrált lekérdezési API

Dokumentumkeresési lekérdezések használata SQL API-lekérdezési szintaxis, valamint a [kiszolgálóoldali SDK](https://azure.github.io/azure-cosmosdb-js-server) lehetővé teszi, hogy a lekérdezések végrehajtása SQL ismerete egy JavaScript-felület használatával. A JavaScript-lekérdezés API lehetővé teszi, hogy programozott módon hozhat létre lekérdezéseket predikátum funkciók függvény hívássorozatot való átadásával. Lekérdezések a JavaScript futásidejű rendszer elemzi, és hatékonyan az Azure Cosmos DB tevékenységében. JavaScript lekérdezési API-támogatással kapcsolatos további információkért lásd: [működő JavaScript nyelvvel integrált query API](javascript-query-api.md) cikk. Példák: [hogyan tárolt eljárások és eseményindítók lekérdezési API a Javascript használatával írhat](how-to-write-javascript-query-api.md) cikk.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan írási és tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB az alábbi cikkeket:

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata](how-to-use-stored-procedures-triggers-udfs.md)

* [Működő JavaScript nyelvvel integrált lekérdezési API](javascript-query-api.md)
