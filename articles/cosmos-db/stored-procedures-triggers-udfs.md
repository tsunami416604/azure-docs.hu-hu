---
title: Tárolt eljárások, eseményindítók és UDF-ek munkája az Azure Cosmos DB-ban
description: Ez a cikk bemutatja a fogalmakat, például a tárolt eljárások, eseményindítók és a felhasználó által definiált függvények az Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 13256377b8a8aaebf59196df57eef67d3b960cb8
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010545"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények

Az Azure Cosmos DB a JavaScript nyelvintegrált, tranzakciós végrehajtását nyújtja. Az SQL API használata esetén az Azure Cosmos DB, írhat **tárolt eljárások**, **eseményindítók**, és a **felhasználó által definiált függvények (UDFs)** a JavaScript-nyelven. JavaScript nyelven írhatja meg az adatbázismotorban végrehajtott logikát. Az [Azure Portal](https://portal.azure.com/), az [Azure Cosmos DB JavaScript-nyelvű integrált lekérdezési API-ja](javascript-query-api.md) vagy a [Cosmos DB SQL API-k SDK-k](how-to-use-stored-procedures-triggers-udfs.md)használatával hozhat létre és hajthat végre eseményindítókat, tárolt eljárásokat és UDF-eket.

## <a name="benefits-of-using-server-side-programming"></a>A kiszolgálóoldali programozás előnyei

A javascriptben tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF-ek) írása lehetővé teszi gazdag alkalmazások készítését, és a következő előnyökkel rendelkeznek:

* **Eljárási logika:** JavaScript, mint egy magas szintű programozási nyelv, amely gazdag és ismerős felület kifejezni az üzleti logika. Összetett műveletek sorozatát hajthatja végre az adatokon.

* **Atomi tranzakciók:** Az Azure Cosmos DB garantálja, hogy az egyetlen tárolt eljárásvagy egy eseményindító n belül végrehajtott adatbázis-műveletek atomi. Ez az atomi funkció lehetővé teszi, hogy egy alkalmazás egyetlen kötegbe kombinálja a kapcsolódó műveleteket, így vagy az összes művelet sikeres, vagy egyik sem sikerül.

* **Teljesítmény:** A JSON-adatok rendszerszinten a JavaScript nyelvtípus-rendszerhez vannak rendelve. Ez a leképezés lehetővé teszi a számos optimalizálás, mint a lusta materializálása JSON-dokumentumok a pufferkészletben, és elérhetővé teszi őket igény szerint a végrehajtó kódot. Az üzleti logika adatbázisba történő szállításával kapcsolatban további teljesítménybeli előnyök is vannak, amelyek a következőket tartalmazzák:

   * *Kötegelés:* Csoportosíthatja a műveleteket, például a beszúrásokat, és tömegesen elküldheti őket. A hálózati forgalom késési költségei és az áruház terhelése külön tranzakciók létrehozásához jelentősen csökken.

   * *Összeállítás előtti összeállítás:* A tárolt eljárások, eseményindítók és UDF-ek implicit módon előre le vannak fordítva a bájtkód-formátumra, hogy elkerüljék a fordítási költségeket az egyes parancsfájlok meghívásakor. Az előfordítás miatt a tárolt eljárások meghívása gyors és alacsony helyigényű.

   * *Szekvenálás:* Néha a műveletekhez olyan aktiváló mechanizmusra van szükség, amely egy vagy további frissítést hajt végre az adatokon. Az Atomicity mellett a kiszolgálóoldalon történő végrehajtás során is vannak teljesítménybeli előnyök.

* **Beágyazás:** A tárolt eljárások segítségével egy helyen csoportosíthatja a logikát. A beágyazás egy absztrakciós réteget ad az adatok hoz létre, amely lehetővé teszi az alkalmazások nak az adatoktól független fejlődését. Ez a réteg absztrakció akkor hasznos, ha az adatok séma nélküli, és nem kell kezelni e további logika hozzáadása közvetlenül az alkalmazásba. Az absztrakció lehetővé teszi, hogy az adatok biztonságban legyenek a parancsfájlokból való hozzáférés egyszerűsítésével.

> [!TIP]
> A tárolt eljárások olyan műveletekhez a legalkalmasabbak, amelyek írási nehéz, és egy partíciókulcs-értéken keresztüli tranzakciót igényelnek. Amikor eldönti, hogy használja-e a tárolt eljárásokat, optimalizálja a lehető legnagyobb mennyiségű írást. Általánosságban elmondható, hogy a tárolt eljárások nem a leghatékonyabb eszközök a nagy számú olvasási vagy lekérdezési műveletek elvégzésére, így a tárolt eljárások használatával nagy számú olvasást kötegeljen vissza az ügyfélnek, nem eredményezi a kívánt előnyt. A legjobb teljesítmény érdekében ezeket a leolvasási nehéz műveleteket az ügyféloldalon kell elvégezni a Cosmos SDK használatával. 

## <a name="transactions"></a>Tranzakciók

Egy tipikus adatbázisban a tranzakció egyetlen logikai munkaegységként végrehajtott műveletek sorozataként határozható meg. Minden tranzakció **biztosítja az ACID tulajdongaranciáit.** ACID egy jól ismert rövidítése, hogy áll: **A**tomicity, **C**onsistency, **Én**solation, és **d**urability. 

* Az atomicitás garantálja, hogy a tranzakción belül végzett összes műveletet egyetlen egységként kezelia kontrájának, és vagy mindegyik véglegesítve van, vagy egyik sem. 

* A konzisztencia biztosítja, hogy az adatok mindig érvényes állapotban vannak a tranzakciók között. 

* Az elkülönítés garantálja, hogy nincs két tranzakció zavarja egymást – számos kereskedelmi rendszer több elkülönítési szintet biztosít, amelyek az alkalmazás igényei alapján használhatók. 

* A tartósság biztosítja, hogy az adatbázisban véglegesített módosítások mindig jelen legyenek.

Az Azure Cosmos DB-ben a JavaScript-futásidejű az adatbázis-motoron belül található. Ezért a tárolt eljárásokon és az eseményindítókon belül végrehajtott kérelmek az adatbázis-munkamenettel azonos hatókörben futnak. Ez a funkció lehetővé teszi, hogy az Azure Cosmos DB garantálja az ACID-tulajdonságokat minden olyan művelethez, amely egy tárolt eljárás vagy egy eseményindító részét képezi. Példák: [tranzakciók megvalósítása](how-to-write-stored-procedures-triggers-udfs.md#transactions) cikk.

### <a name="scope-of-a-transaction"></a>A tranzakció hatóköre

A tárolt eljárások egy Azure Cosmos-tárolóhoz vannak társítva, és a tárolt eljárás végrehajtása egy logikai partíciókulcsra van kitéve. A tárolt eljárásoknak tartalmazniuk kell egy logikai partíciókulcs-értéket a végrehajtás során, amely meghatározza a tranzakció hatókörének logikai partícióját. További információ: [Azure Cosmos DB particionálási](partition-data.md) cikk.

### <a name="commit-and-rollback"></a>Véglegesítés és visszaállítás

A tranzakciók natív módon integrálva vannak az Azure Cosmos DB JavaScript programozási modellbe. Egy JavaScript-függvényen belül az összes művelet automatikusan egyetlen tranzakció alá kerül. Ha a tárolt eljárás JavaScript-logikája kivétel nélkül befejeződik, a tranzakción belüli összes művelet véglegesítve lesz az adatbázisban. Utasítások, `BEGIN TRANSACTION` mint `COMMIT TRANSACTION` és (a relációs adatbázisok ismerőse) implicit az Azure Cosmos DB. Ha vannak kivételek a parancsfájlból, az Azure Cosmos DB JavaScript futásidejű visszaállítja a teljes tranzakciót. Mint ilyen, egy kivétel dobása `ROLLBACK TRANSACTION` gyakorlatilag egyenértékű az Azure Cosmos DB-vel.

### <a name="data-consistency"></a>Adatkonzisztencia

A tárolt eljárások és eseményindítók mindig egy Azure Cosmos-tároló elsődleges replikáján hajthatók végre. Ez a funkció biztosítja, hogy a tárolt eljárásokból származó [olvasások erős konzisztenciát](consistency-levels-tradeoffs.md)biztosítsanak. A felhasználó által definiált függvényeket használó lekérdezések az elsődleges vagy bármely másodlagos replikán végrehajthatók. A tárolt eljárások és eseményindítók a tranzakciós írások támogatására szolgálnak – eközben a csak olvasható logika az alkalmazásoldali logika és az [Azure Cosmos DB SQL API SDK-k](sql-api-dotnet-samples.md)használatával végzett lekérdezések segítségével valósul meg az adatbázis-átviteli sebességgel. 

## <a name="bounded-execution"></a>Kötött végrehajtás

Minden Azure Cosmos DB-műveletnek a megadott időmeghagyási időtartamon belül kell befejeződnie. Ez a megkötés a JavaScript-függvényekre vonatkozik – a tárolt eljárásokra, az eseményindítókra és a felhasználó által definiált függvényekre. Ha egy művelet nem fejeződik be az adott határidőn belül, a tranzakció visszalesz állítva.

Biztosíthatja, hogy a JavaScript-függvények a megadott határidőn belül befejeződjenek, vagy megvalósíthat egy folytatás-alapú modellt a kötegelt/újravégrehajtás folytatásához. A tárolt eljárások és a határidők kezelésére szolgáló eseményindítók fejlesztésének egyszerűsítése érdekében az Azure Cosmos tárolóban található összes függvény (például elemek létrehozása, olvasása, frissítése és törlése) egy logikai értéket ad vissza, amely azt jelzi, hogy a művelet befejeződik-e. Ha ez az érték hamis, azt jelzi, hogy az eljárásnak le kell zárnia a végrehajtást, mert a parancsfájl több időt vagy kiépített átviteli értéket fogyaszt, mint a beállított érték. Az első el nem fogadott tárművelet előtt várólistára helyezett műveletek garantáltan befejeződnek, ha a tárolt eljárás időben befejeződik, és nem áll sorba több kérésre. Így a műveleteket egyenként kell várólistára tenni a JavaScript visszahívási konvenciójának használatával a parancsfájl vezérlési folyamatának kezeléséhez. Mivel a parancsfájlok végrehajtása kiszolgálóoldali környezetben történik, szigorúan szabályozzák őket. Parancsfájlok, amelyek ismételten megsértik a végrehajtási határokat lehet megjelölni inaktív, és nem hajtható végre, és újra kell létrehozni, hogy tartsák tiszteletben a végrehajtási határokat.

A JavaScript-függvények a [kiosztott átviteli kapacitás](request-units.md)hatálya alá is tartoznak. JavaScript-függvények potenciálisan a végén használ nagy számú kérelem egységek rövid időn belül, és lehet, hogy a sebesség korlátozott, ha a kiosztott átviteli kapacitás korlát elérése. Fontos megjegyezni, hogy a parancsfájlok az adatbázis-műveletek végrehajtása során az átviteli kapacitáson kívül további átviteli kapacitást használnak fel, bár ezek az adatbázis-műveletek valamivel olcsóbbak, mint az ügyféltől származó azonos műveletek végrehajtása.

## <a name="triggers"></a>Eseményindítók

Az Azure Cosmos DB az eseményindítók két típusát támogatja:

### <a name="pre-triggers"></a>Előzetes eseményindítók

Az Azure Cosmos DB olyan eseményindítókat nyújt, amelyek egy Azure Cosmos-elemen végzett művelet végrehajtásával indíthatók. Meghatározhat például egy előzetes eseményindítót, amikor elemet hoz létre. Ebben az esetben az előzetes eseményindító az elem létrehozása előtt fut. Az előzetes eseményindítóknak nem lehetnek bemeneti paramétereik. Szükség esetén a kérésobjektummal frissíthető a dokumentum törzse az eredeti kéréshez képest. Az eseményindítók regisztrálásakor a felhasználók meghatározhatják azokat a műveleteket, amelyekkel futhatnak. Ha a `TriggerOperation.Create`használatával létrehozott egy eseményindítót, ez azt jelenti, hogy a csereműveletben az eseményindító használata nem engedélyezett. Példák: [Hogyan kell írni az eseményindítók](how-to-write-stored-procedures-triggers-udfs.md#triggers) cikket.

### <a name="post-triggers"></a>Utólagos eseményindítók

Az elő-eseményindítókhoz hasonlóan az eseményindítók is társítva vannak egy Azure Cosmos-elemen végzett művelettel, és nem igényelnek bemeneti paramétereket. A művelet befejezése *után* futnak, és hozzáférhetnek az ügyfélnek küldött válaszüzenethez. Példák: [Hogyan kell írni az eseményindítók](how-to-write-stored-procedures-triggers-udfs.md#triggers) cikket.

> [!NOTE]
> A regisztrált eseményindítók nem futnak automatikusan, amikor a megfelelő műveletek (létrehozás / törlés / csere / frissítés) megtörténnek. Explicit módon kell meghívni őket a műveletek végrehajtásakor. További információ: [az eseményindítók cikkfuttatása.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a name="user-defined-functions"></a><a id="udfs"></a>Felhasználó által meghatározott függvények

[A felhasználó által definiált függvények](sql-query-udfs.md) (UDF-ek) az SQL API lekérdezési nyelvének szintaxisának kiterjesztésére és az egyéni üzleti logika egyszerű megvalósítására szolgálnak. Csak lekérdezéseken belül hívhatók meg. Az UDF-ek nem férnek hozzá a környezeti objektumhoz, és csak a JavaScript-alapú számítási ként használhatók. Ezért udf-ek másodlagos replikákon futtathatók. Példák: [A felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md#udfs) című cikkben.

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>JavaScript nyelvbe integrált lekérdezési API

Az SQL API lekérdezési szintaxissal történő lekérdezések kiadása mellett a [kiszolgálóoldali SDK](https://azure.github.io/azure-cosmosdb-js-server) lehetővé teszi a lekérdezések sql-kapcsolat on-t használó JavaScript-felület használatával történő végrehajtásához. A JavaScript lekérdezési API lehetővé teszi, hogy programozott módon építsen lekérdezéseket azáltal, hogy predikátumfüggvények sorozata függvényhívások sorrendje. A javascript-futásidejű lekérdezéseket a JavaScript-futásidő elemzi, és hatékonyan hajtják végre az Azure Cosmos DB-n belül. A JavaScript query API-támogatásról a [JavaScript-nyelv integrált lekérdezési API-cikke](javascript-query-api.md) című témakörben olvashat. Példák: [Tárolt eljárások és eseményindítók írása javascript query API-cikk használatával.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan írhat és használhat tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket az Azure Cosmos DB-ben az alábbi cikkekkel:

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása](how-to-write-stored-procedures-triggers-udfs.md)

* [A tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata](how-to-use-stored-procedures-triggers-udfs.md)

* [A JavaScript nyelvi integrált lekérdezési API-jának](javascript-query-api.md)
