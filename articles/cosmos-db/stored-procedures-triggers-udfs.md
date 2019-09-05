---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata Azure Cosmos DB
description: Ez a cikk bemutatja az olyan fogalmakat, mint például a tárolt eljárások, eseményindítók és felhasználó által definiált függvények a Azure Cosmos DBban.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1c2bf53a610c566ac58df588f6d96389f2206563
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717538"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények

A Azure Cosmos DB a JavaScript nyelvvel integrált, tranzakciós végrehajtását teszi lehetővé. Ha az SQL API-t Azure Cosmos DB használja, a JavaScript nyelven írhat **tárolt eljárásokat**, **eseményindítókat**és **felhasználó által definiált függvényeket (UDF)** . Megírhatja az adatbázis-motoron belül végrehajtott JavaScript-logikát. Eseményindítókat, tárolt eljárásokat és UDF hozhat létre és futtathat [Azure Portal](https://portal.azure.com/)használatával, a [JavaScript nyelv integrált lekérdezési API-ját Azure Cosmos DB](javascript-query-api.md) vagy az [Cosmos db SQL API ügyféloldali SDK](how-to-use-stored-procedures-triggers-udfs.md)-kat.

## <a name="benefits-of-using-server-side-programming"></a>A kiszolgálóoldali programozás használatának előnyei

A tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF-EK) írása a JavaScriptben lehetővé teszi, hogy sokoldalú alkalmazásokat építsen ki, és az alábbi előnyökkel jár:

* **Eljárási logika:** JavaScript magas szintű programozási nyelv, amely gazdag és ismerős felületet biztosít az üzleti logika kiváltásához. Az adatokon összetett műveleteket is végrehajthat.

* **Atomi tranzakciók:** Azure Cosmos DB garantálja, hogy az egyetlen tárolt eljáráson vagy triggeren belül végrehajtott adatbázis-műveletek atomiak. Ez az atomi funkció lehetővé teszi, hogy egy alkalmazás a kapcsolódó műveleteket egyetlen kötegbe egyesítse, hogy az összes művelet sikeres legyen, vagy egyik sem sikeres legyen.

* **Teljesítmény** A JSON-adattípusok belsőleg le vannak képezve a JavaScript Language Type rendszerre. Ez a leképezés több optimalizációt is lehetővé tesz, például a JSON-dokumentumok lusta megvalósításának gyakorlatban a pufferben, és igény szerint elérhetővé teszi őket a végrehajtó kód számára. Más teljesítménybeli előnyökkel jár a hajózás üzleti logikája az adatbázishoz, amely a következőket tartalmazza:

   * *Adagoló* Csoportosíthatja a műveleteket, például a lapkákat és tömegesen is elküldheti azokat. A hálózati forgalom késési költségei és a tárolók terhelése a különálló tranzakciók létrehozásához jelentősen csökken.

   * *Előzetes fordítás:* A tárolt eljárásokat, eseményindítókat és UDF implicit módon előre le kell fordítani a bájtos kód formátumára, hogy el lehessen kerülni a fordítási költségeket az egyes parancsfájlok meghívásakor. Az előzetes fordítás miatt a tárolt eljárások meghívása gyors, és alacsony a lábnyoma.

   * *Sorrendvezérlő* Időnként a műveleteknek olyan kiváltó mechanizmusra van szükségük, amely az adatokhoz egy vagy több frissítést is végrehajthat. Az atomenergia mellett a kiszolgálói oldalon történő végrehajtás esetén is teljesítménybeli előnyökkel jár.

* **Beágyazás** A tárolt eljárások a logika egy helyen való csoportosítására használhatók. A beágyazás egy absztrakt réteget ad hozzá az adatokhoz, ami lehetővé teszi, hogy az alkalmazások egymástól függetlenül fejlődjön az adatokból. Ez az absztrakciós réteg akkor hasznos, ha az adatai séma nélküliek, és nem kell közvetlenül az alkalmazásba felvennie a további logika hozzáadását. Az absztrakció lehetővé teszi az adatok biztonságának megőrzését a parancsfájlok elérésének egyszerűsítésével.

> [!TIP]
> A tárolt eljárások az írható és nehéz műveletekhez legmegfelelőbbek, és a partíciós kulcs értékének megkövetelése a tranzakcióhoz. Ha eldönti, hogy a tárolt eljárásokat szeretné-e használni, optimalizálja a maximálisan megengedett írási mennyiség beágyazását. Általánosságban elmondható, hogy a tárolt eljárások nem a leghatékonyabb eszköz nagy mennyiségű olvasási vagy lekérdezési művelet elvégzéséhez, így a tárolt eljárások használatával a kötegek nagy számú olvasást adhatnak vissza az ügyfélnek. A legjobb teljesítmény érdekében ezeket az olvasási műveleteket az ügyféloldali, a Cosmos SDK használatával kell elvégezni. 

## <a name="transactions"></a>Tranzakciók

Egy tipikus adatbázisban lévő tranzakció munka egyetlen logikai egységként végrehajtott műveletek sorozataként lehet definiálni. Minden tranzakció **savas Property garanciát**biztosít. A sav egy jól ismert betűszó, amely a következőt jelenti: **Tomicity,** **C**onsistency, **I**solation és **D**urability. 

* Az atomenergia garantálja, hogy a tranzakción belül végrehajtott összes művelet egyetlen egységként lesz kezelve, és ezek mindegyike véglegesítve van, vagy egyik sem. 

* A konzisztencia gondoskodik arról, hogy az adategységek mindig érvényes állapotban legyenek a tranzakciók között. 

* Az elkülönítés garantálja, hogy két tranzakció sem zavarja egymást – számos kereskedelmi rendszer több elkülönítési szintet is biztosít, amelyeket az alkalmazás igényei alapján használhat. 

* A tartósság biztosítja, hogy az adatbázisban véglegesített módosítások mindig jelen lesznek.

Azure Cosmos DB a JavaScript futtatókörnyezet az adatbázismotor belsejében van tárolva. Ezért a tárolt eljárásokban és az eseményindítókban végrehajtott kérelmek ugyanabban a hatókörben futnak, mint az adatbázis-munkamenet. Ez a funkció lehetővé teszi, hogy a Azure Cosmos DB a tárolt eljárás vagy egy trigger részét képező összes művelet savas tulajdonságainak garantálása érdekében. Példákat a [tranzakciók implementálása](how-to-write-stored-procedures-triggers-udfs.md#transactions) című cikkben talál.

### <a name="scope-of-a-transaction"></a>Tranzakció hatóköre

Ha egy tárolt eljárás egy Azure Cosmos-tárolóhoz van társítva, akkor a tárolt eljárást a logikai partíciós kulcs tranzakciós hatókörében hajtja végre a rendszer. Minden tárolt eljárás végrehajtásához tartalmaznia kell egy logikai partíció kulcsának értékét, amely megfelel a tranzakció hatókörének. További információ: [Azure Cosmos db particionálási](partition-data.md) cikk.

### <a name="commit-and-rollback"></a>Érvényesítés és visszaállítás

A tranzakciók natív módon vannak integrálva a Azure Cosmos DB JavaScript programozási modellbe. JavaScript-függvényen belül az összes művelet automatikusan egyetlen tranzakció alá lesz csomagolva. Ha egy tárolt eljárás JavaScript-logikája kivétel nélkül fejeződik be, a tranzakción belüli összes művelet véglegesítve lesz az adatbázisban. Az olyan `BEGIN TRANSACTION` utasítások `COMMIT TRANSACTION` , mint a és a (a kapcsolatok adatbázisaiban ismertek) implicitek Azure Cosmos db. Ha a szkript alól kivételek vannak, a Azure Cosmos DB JavaScript futtatókörnyezet visszaállítja a teljes tranzakciót. Ilyen esetben a kivételek a Azure Cosmos DBban ténylegesen egyenértékűek `ROLLBACK TRANSACTION` .

### <a name="data-consistency"></a>Adatkonzisztencia

A tárolt eljárásokat és eseményindítókat mindig az Azure Cosmos-tároló elsődleges replikáján hajtja végre a rendszer. Ez a funkció biztosítja, hogy a tárolt eljárásokból beolvasott adatok [erős konzisztenciát](consistency-levels-tradeoffs.md)biztosítanak. A felhasználó által definiált függvényeket használó lekérdezések az elsődleges vagy bármely másodlagos replikán hajthatók végre. A tárolt eljárások és eseményindítók a tranzakciós írások támogatásához szükségesek. a csak olvasási logikát az [Azure Cosmos db SQL API SDK](sql-api-dotnet-samples.md)-k használatával lehet legjobban megvalósítani az alkalmazás-és a lekérdezésekben, az adatbázis átviteli sebességének csökkentése érdekében. 

## <a name="bounded-execution"></a>Korlátozott végrehajtása

Az összes Azure Cosmos DB műveletnek a megadott időtúllépési időtartamon belül kell lennie. Ez a korlátozás a JavaScript-függvények – tárolt eljárások, eseményindítók és felhasználó által definiált függvények esetében érvényes. Ha egy művelet nem fejeződik be az adott időkorláton belül, a tranzakció vissza lesz állítva.

Gondoskodhat arról, hogy a JavaScript-függvények az adott időkorláton belül legyenek végrehajtva, vagy egy folytatáson alapuló modellt hajtsanak végre a Batch/folytatás végrehajtásához. A tárolt eljárások és eseményindítók az időbeli korlátok kezelésére szolgáló működésének egyszerűbbé tétele érdekében az Azure Cosmos tárolóban található összes funkció (például az elemek létrehozása, olvasása, frissítése és törlése) egy logikai értéket ad vissza, amely azt jelzi, hogy a művelet a következő lesz-e: teljes. Ha ez az érték hamis, akkor azt jelzi, hogy az eljárásnak le kell csomagolnia a végrehajtást, mert a parancsfájl a beállított értéknél több időt vagy kiépített átviteli sebességet használ. Az első elfogadhatatlan store művelet műveletek aszinkron előtt garantáltan elvégezni, ha a tárolt eljárás ideje alatt befejeződik, és nem várólistára további kérések eredményéről. Így a műveleteket a JavaScript visszahívási konvenciójának használatával, a parancsfájl vezérlési folyamatának kezeléséhez egy időben kell várólistába venni. Mivel a parancsfájlokat kiszolgálóoldali környezetben hajtják végre, szigorúan szabályozva vannak. A végrehajtási határokat ismételten sértő parancsfájlok inaktívként jelölhetők meg, és nem hajthatók végre, és újra létre kell őket hozni a végrehajtási határok tiszteletben tartásához.

A JavaScript-függvények kiosztott [átviteli kapacitásra](request-units.md)is vonatkoznak. A JavaScript-függvények egy rövid időn belül nagy mennyiségű kérést használhatnak, és a kiosztott átviteli kapacitásra vonatkozó korlát elérésekor a díjszabás korlátozott lehet. Fontos megjegyezni, hogy a szkriptek az adatbázis-műveletek végrehajtásával töltött átviteli sebesség mellett további átviteli sebességet is felhasználnak, bár ezek az adatbázis-műveletek valamivel kevésbé költségesek, mint az ügyféltől származó azonos műveletek végrehajtása.

## <a name="triggers"></a>Triggerek

Azure Cosmos DB az eseményindítók két típusát támogatja:

### <a name="pre-triggers"></a>Trigger előtti

A Azure Cosmos DB olyan eseményindítókat biztosít, amelyek meghívhatók egy Azure Cosmos DB elemen végrehajtott művelet végrehajtásával. Megadhat például egy előindítást egy elem létrehozásakor. Ebben az esetben a pre-trigger az elem létrehozása előtt fog futni. Üzem előtti eseményindítók nem lehet bemeneti paramétereket. Ha szükséges, a kérelem objektum használatával frissítheti a dokumentum törzsét az eredeti kérelemből. Eseményindítók regisztrálásakor a felhasználók megadhatják a műveletek, amelyek való futtatás. Ha egy triggert a- `TriggerOperation.Create`vel hoztak létre, akkor az trigger használata nem engedélyezett a csere műveletben. Példákat az [Eseményindítók írása](how-to-write-stored-procedures-triggers-udfs.md#triggers) című cikkben talál.

### <a name="post-triggers"></a>Triggerek utáni

Az eseményindítók előállításához hasonlóan az eseményindítók is társítva vannak egy Azure Cosmos DB elem egyik műveletéhez, és nincs szükségük bemeneti paraméterekre. A művelet befejezését *követően* futnak, és hozzáférnek az ügyfélnek küldött válaszüzenetekhez. Példákat az [Eseményindítók írása](how-to-write-stored-procedures-triggers-udfs.md#triggers) című cikkben talál.

> [!NOTE]
> A regisztrált eseményindítók nem futnak automatikusan, ha a kapcsolódó műveletek (létrehozás/törlés/csere/frissítés) történnek. A műveletek végrehajtásakor explicit módon meg kell hívni őket. További információt az eseményindítók [futtatása](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) című cikkben talál.

## <a id="udfs"></a>Felhasználó által definiált függvények

A felhasználó által definiált függvények (UDF-k) az SQL API-lekérdezés nyelvi szintaxisának kibővítésére és az egyéni üzleti logika egyszerű megvalósítására szolgálnak. Csak lekérdezéseken belül hívhatók. A UDF nem rendelkezik hozzáféréssel a környezeti objektumhoz, és csak számítási JavaScriptként használhatók. Ezért a UDF a másodlagos replikán is futtatható. Példákat a [felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md#udfs) című cikkben talál.

## <a id="jsqueryapi"></a>JavaScript nyelv – integrált lekérdezési API

Az SQL API-lekérdezési szintaxist használó lekérdezések kiadása mellett a [KISZOLGÁLÓOLDALI SDK](https://azure.github.io/azure-cosmosdb-js-server) lehetővé teszi, hogy az SQL ismerete nélkül JavaScript-interfész használatával végezzen lekérdezéseket. A JavaScript lekérdezési API lehetővé teszi, hogy programozott módon hozza létre a lekérdezéseket. A lekérdezéseket a JavaScript futtatókörnyezet elemzi, és a Azure Cosmos DBon belül hatékonyan hajtja végre. A JavaScript-lekérdezési API-támogatással kapcsolatos további információkért lásd: [a JavaScript nyelvi integrált lekérdezési API használata](javascript-query-api.md) című cikk. Példákat a [tárolt eljárások és eseményindítók írása JavaScript-lekérdezési API használatával](how-to-write-javascript-query-api.md) című cikkben talál.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan írhat és használhat tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket Azure Cosmos DB a következő cikkekkel:

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata](how-to-use-stored-procedures-triggers-udfs.md)

* [A JavaScript nyelvi integrált lekérdezési API használata](javascript-query-api.md)
