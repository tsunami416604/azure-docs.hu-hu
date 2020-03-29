---
title: IoT-megoldás létrehozása az Azure Stream Analytics használatával
description: Első lépések a Stream Analytics IoT-megoldásához egy fizetős fizetési forgatókönyvhöz
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426443"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>IoT-megoldás létrehozása a Stream Analytics használatával

## <a name="introduction"></a>Bevezetés
Ebben a megoldásban megtudhatja, hogyan használhatja az Azure Stream Analytics segítségével valós idejű elemzési adatokat az adatokból. A fejlesztők könnyedén kombinálhatják az adatfolyamokat, például a kattintási adatfolyamokat, a naplókat és az eszköz által létrehozott eseményeket az előzményrekordokkal vagy hivatkozási adatokkal az üzleti elemzések létrehozásához. A Microsoft Azure-ban üzemeltetett, teljes körűen felügyelt, valós idejű adatfolyam-számítási szolgáltatásként az Azure Stream Analytics beépített rugalmasságot, alacsony késést és méretezhetőséget biztosít, hogy percek alatt működésbe léphet.

A megoldás befejezése után a következőkre van képes:

* Ismerkedjen meg az Azure Stream Analytics portállal.
* Streamelési feladat konfigurálása és üzembe helyezése.
* A Valós problémákat a Stream Analytics lekérdezési nyelvével fejtheti ki és oldhatja meg.
* Streamelési megoldásokat fejleszthet ügyfelei számára a Stream Analytics magabiztos használatával.
* A figyelési és naplózási élmény segítségével elháríthatja a problémákat.

## <a name="prerequisites"></a>Előfeltételek
A megoldás végrehajtásához a következő előfeltételekre van szükség:
* [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Forgatókönyv bevezetése: "Hello, Toll!"
A fizetős állomás gyakori jelenség. Találkozik velük sok gyorsforgalmi utak, hidak, és alagutak szerte a világon. Minden fizetőállomásnak több fizetős fülkéje van. A kézi fülkékben megáll, hogy kifizesse az útdíjat egy kísérőnek. Az automatizált fülkékben az egyes fülkék tetején lévő érzékelő beolvassa az RFID-kártyát, amely a jármű szélvédőjére van rögzítve, miközben elhalad a fizetős fülkén. Könnyű elképzelni a járművek áthaladását ezeken a fizetőállomásokon, mint egy eseményfolyamot, amelyen érdekes műveleteket lehet végrehajtani.

![Autók képe a fizetős fülkékben](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Bejövő adatok
Ez a megoldás két adatfolyammal működik. A fizetőállomások bejáratánál és kijáratánál telepített érzékelők termelik az első áramlást. A második adatfolyam egy statikus kikereshető adatkészlet, amely járműregisztrációs adatokat.

### <a name="entry-data-stream"></a>Belépési adatfolyam
A belépési adatfolyam információkat tartalmaz az autókról, amint belépnek a fizetőállomásokra. A kilépési adatok események élő ben streamelt egy Event Hub várólistába egy webalkalmazás a mintaalkalmazásban található.

| Tollazonosító | EntryTime (EntryTime) | Azonosító tábla | Állapot | Gyártmány | Modell | Járműtípus | Járműsúlya | Autópályadíj | Címke |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |Crv |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |Crv |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Itt van egy rövid leírást az oszlopok:

| Oszlop | Leírás |
| --- | --- |
| Tollazonosító |A fizetős fülke azonosítója, amely egyedileg azonosítja a díjszedő fülkét |
| EntryTime (EntryTime) |A jármű utc-i díjszedő fülkébe való belépésének dátuma és időpontja |
| Azonosító tábla |A jármű rendszáma |
| Állapot |Egy állam az Egyesült Államokban |
| Gyártmány |A gyártó az autó |
| Modell |Az autó modellszáma |
| Járműtípus |Vagy 1 személyszállító járművek, vagy 2 haszongépjárművek esetében |
| Súlytípus |A jármű tömege tonnában; 0 személygépkocsik esetében |
| Autópályadíj |Az útdíj értéke USD-ben |
| Címke |Az e-Tag az autó, amely automatizálja a fizetés; üres, ahol a fizetés manuálisan történt |

### <a name="exit-data-stream"></a>Kilépés az adatfolyamból
A kilépési adatfolyam a fizetőállomást elhagyó autókról tartalmaz információkat. A kilépési adatok események élő ben streamelt egy Event Hub várólistába egy webalkalmazás a mintaalkalmazásban található.

| **TollId között** | **ExitTime (Kilépési idő)** | **Azonosító tábla** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.00000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.00000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.00000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.00000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.00000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.00000000Z |CDE 1007 |

Itt van egy rövid leírást az oszlopok:

| Oszlop | Leírás |
| --- | --- |
| Tollazonosító |A fizetős fülke azonosítója, amely egyedileg azonosítja a díjszedő fülkét |
| ExitTime (Kilépési idő) |A jármű kilépésének dátuma és időpontja az UTC-ben található fizetős fülkéből |
| Azonosító tábla |A jármű rendszáma |

### <a name="commercial-vehicle-registration-data"></a>Haszongépjármű-nyilvántartási adatok
A megoldás egy haszongépjármű-nyilvántartási adatbázis statikus pillanatképét használja. Ezeket az adatokat a rendszer JSON-fájlként menti az Azure blob storage-ba, amely a mintában szerepel.

| Azonosító tábla | Regisztrációs azonosító | Lejárt |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Itt van egy rövid leírást az oszlopok:

| Oszlop | Leírás |
| --- | --- |
| Azonosító tábla |A jármű rendszáma |
| Regisztrációs azonosító |A jármű nyilvántartási azonosítója |
| Lejárt |A jármű nyilvántartási állapota: 0, ha a jármű nyilvántartásba vétele aktív, 1, ha a nyilvántartásba vétel lejárt |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>A környezet beállítása az Azure Stream Analytics számára
A megoldás végrehajtásához Microsoft Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-fiókkal, [ingyenes próbaverziót kérhet.](https://azure.microsoft.com/pricing/free-trial/)

Ügyeljen arra, hogy kövesse az Azure-fiók karbantartása című szakaszban a cikk végén található lépéseket, hogy a lehető legjobban kihasználhassa az Azure-kreditet.

## <a name="deploy-the-sample"></a>A minta telepítése
Számos erőforrás, amely könnyen telepíthető egy erőforráscsoportban együtt néhány kattintással. A megoldásdefiníció a GitHub-tárházban [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)található.

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>A TollApp-sablon üzembe helyezése az Azure Portalon
1. A TollApp-környezet Azure-ba való üzembe helyezéséhez használja ezt a hivatkozást [a TollApp Azure-sablon üzembe helyezéséhez.](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)

2. Jelentkezzen be az Azure Portalon, ha a rendszer kéri.

3. Válassza ki azt az előfizetést, amelyben a különböző erőforrások számlázása történik.

4. Adjon meg egy új erőforráscsoportot, például `MyTollBooth`egyedi névvel.

5. Válasszon egy Azure-beli helyet.

6. Adja meg **az intervallumot** másodpercként. Ez az érték a minta webapp, milyen gyakran küldadatokat az Event Hub.

7. **Ellenőrizze,** hogy elfogadja a feltételeket.

8. Válassza **a Rögzítés az irányítópulton** lehetőséget, hogy később könnyen megtalálhassa az erőforrásokat.

9. Válassza a **Vásárlás** lehetőséget a mintasablon üzembe helyezéséhez.

10. Néhány pillanat múlva megjelenik egy értesítés, amely megerősíti, hogy a **telepítés sikeres volt.**

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Tekintse át az Azure Stream Analytics TollApp-erőforrásait

1. Jelentkezzen be az Azure Portalra

2. Keresse meg az előző szakaszban megnevezett erőforráscsoportot.

3. Ellenőrizze, hogy a következő erőforrások szerepelnek-e az erőforráscsoportban:
   - Egy Cosmos DB-fiók
   - Egy Azure Stream Analytics-feladat
   - Egy Azure Storage-fiók
   - Egy Azure-eseményközpont
   - Két webalkalmazás

## <a name="examine-the-sample-tollapp-job"></a>Vizsgálja meg a minta TollApp-feladat
1. Az előző szakaszban az erőforráscsoportból kiindulva válassza ki a Stream Analytics streamelési feladatát a **tollapp** névvel kezdve (a név véletlenszerű karaktereket tartalmaz az egyediséghez).

2. A feladat **Áttekintés lapján** figyelje meg a **Lekérdezés** mezőt a lekérdezés szintaxisának megtekintéséhez.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   A lekérdezés szándékának parafrázisához tegyük fel, hogy meg kell számolnia a fizetőfülkébe belépő járművek számát. Mivel az autópálya fizetős fülkében van egy folyamatos patak járművek belépő, ezek a bejárati események hasonlóak a patak, hogy soha nem áll meg. Számszerűsíteni a patak, meg kell határoznia egy "időszak", hogy az intézkedés alatt. Nézzük finomítani a kérdést tovább, hogy "Hány jármű adja meg a fizetős stand három percenként?" Ez gyakran nevezik a bukdácsoló számít.

   Mint látható, az Azure Stream Analytics egy sql-hez hasonló lekérdezési nyelvet használ, és néhány bővítményt ad hozzá a lekérdezés időfüggéssel kapcsolatos szempontok meghatározásához.  További részletekért olvassa el a lekérdezésben használt [időgazdálkodás](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) és [ablakos](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) szerkezetek.

3. Vizsgálja meg a tollapp mintafeladat bemeneteit. Az aktuális lekérdezésben csak az EntryStream-bemenet et használja a rendszer.
   - **Az EntryStream-bemenet** egy Event Hub-kapcsolat, amely minden alkalommal várólistára helyezi az adatokat, amikor egy autó belép egy útdíjszedőfülkébe az autópályán. Egy webalkalmazás, amely része a minta létrehozza az eseményeket, és hogy az adatok várólistára ebben az Event Hub. Vegye figyelembe, hogy ezt a bemenetet a streamelési lekérdezés FROM záradéka kérdezi le.
   - **ExitStream** bemenet egy Event Hub-kapcsolat, amely várólisták adatok at minden alkalommal, amikor egy autó kilép egy útdíjszedő az autópályán. Ezt a streamelési bemenetet a lekérdezés szintaxisának későbbi változatai használják.
   - **A regisztrációs** bemenet egy Azure Blob storage-kapcsolat, amely egy statikus registration.json fájlra mutat, amely szükség szerint a támadásokhoz használatos. Ez a referencia-adatbevitel a lekérdezés szintaxisának későbbi változataiban használatos.

4. Vizsgálja meg a kimenetek a TollApp minta feladat.
   - **Cosmos DB** kimenet egy Cosmos adatbázis-tároló, amely megkapja a kimeneti fogadó eseményeket. Vegye figyelembe, hogy ez a kimenet a streamelési lekérdezés INTO záradékában használatos.

## <a name="start-the-tollapp-streaming-job"></a>A TollApp-streamelési feladat indítása
A streamelési feladat elindításához kövesse az alábbi lépéseket:

1. A **Feladat Áttekintés lapján** válassza a **Start**lehetőséget.

2. A **Feladat indítása** ablaktáblán válassza **a Now (Most) lehetőséget.**

3. Néhány pillanat múlva, miután a feladat fut, a **streamelési** feladat áttekintése lapon tekintse meg a **figyelési** grafikont. A diagramnak több ezer bemeneti eseményt és tíz kimeneti eseményt kell megjelenítenie.

## <a name="review-the-cosmosdb-output-data"></a>Tekintse át a CosmosDB kimeneti adatait
1. Keresse meg a TollApp-erőforrásokat tartalmazó erőforráscsoportot.

2. Válassza ki az Azure Cosmos DB-fiókot a **tollapp-random\<\>-cosmos**névmintával.

3. Az **Adatkezelő** lap megnyitásához jelölje ki az Adatkezelő fejlécét.

4. Bontsa ki a **tollAppDatabase** > **útdíjappcollection-dokumentumok** > **Documents**.

5. Az azonosítók listájában több dokumentum jelenik meg, amint a kimenet elérhetővé válik.

6. Válassza ki az egyes azonosítókat a JSON-dokumentum áttekintéséhez. Figyelje meg az egyes tollid, ablakvég idő, és a száma autók, hogy az ablakból.

7. További három perc elteltével egy másik négy dokumentumkészlet áll rendelkezésre, tollidonként egy dokumentum.


## <a name="report-total-time-for-each-car"></a>Az egyes kocsik teljes idejének jelentése
Az átlagos idő, amely szükséges egy autó átaz autópályadíj segít felmérni a hatékonyságot a folyamat és az ügyfél élményt.

A teljes idő megkereséséhez csatlakozzon az EntryTime-adatfolyamhoz az ExitTime-adatfolyammal. Csatlakozzon a két bemeneti adatfolyamhoz az egyenlő egyező TollId és LicensePlate oszlopokon. A **JOIN** operátor megköveteli, hogy adja meg az időbeli mozgástér, amely leírja az elfogadható időbeli különbség az illesztett események között. A **DATEDIFF** függvénnyel megadhatja, hogy az események ne legyenek 15 percnél tovább egymástól. Alkalmazza a **DATEDIFF** funkciót a kilépési és belépési időkhöz is, hogy kiszámítsa az autó által a fizetőállomáson töltött tényleges időt. Vegye figyelembe a **DATEDIFF** használatának különbségét, ha **a SELECT** utasításban **használja,** nem pedig JOIN feltételben.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>A TollApp-streamelési feladat lekérdezési szintaxisának frissítése:

1. A **Feladat Áttekintés lapján** válassza a **Leállítás**lehetőséget.

2. Várjon néhány percet a feladat leállításáról szóló értesítésre.

3. A FELADATTOPOLÓGIA fejléc alatt jelölje be **< > Lekérdezés lehetőséget.**

4. Illessze be a módosított streamelési SQL-lekérdezést.

5. A lekérdezés mentéséhez válassza a **Mentés** gombot. Erősítse meg **az Igen** gombot a módosítások mentéséhez.

6. A **Feladat Áttekintés lapján** válassza a **Start**lehetőséget.

7. A **Feladat indítása** ablaktáblán válassza **a Now (Most) lehetőséget.**

### <a name="review-the-total-time-in-the-output"></a>A teljes idő áttekintése a kimenetben
Ismételje meg az előző szakaszban a CosmosDB kimeneti adatok áttekintéséhez a streamelési feladat. Tekintse át a legújabb JSON dokumentumokat.

Ez a dokumentum például egy példaautót mutat be egy bizonyos rendszámtáblával, a belépési időt és a kilépési időt, és a DATEDIFF számított durationinminutes mezőt, amely az útdíjszedő asztal időtartamát két percként mutatja:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Lejárt regisztrációval rendelkező járművek jelentése
Az Azure Stream Analytics a referenciaadatok statikus pillanatképeit használhatja a historikus adatfolyamok összekapcsolására. Ennek a képességnek a bizonyításához használja a következő mintakérdést. A regisztrációs bemenet egy statikus blob json fájl, amely felsorolja a licenccímkék lejárati. A rendszámtábla összekapcsolásával a referenciaadatokat összehasonlítják az útdíjon áthaladó minden egyes járművel.

Ha egy haszongépjármű regisztrálva van az útdíjszedő társaságnál, az ellenőrzés nélkül áthaladhat az útdíjszedő fülkén. A regisztrációs keresvénytáblázat segítségével azonosíthatja az összes lejárt regisztrációval rendelkező haszongépjárművet.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Ismételje meg az előző szakaszban a TollApp-streamelési feladat lekérdezési szintaxisának frissítéséhez.

2. Ismételje meg az előző szakaszban a CosmosDB kimeneti adatok áttekintéséhez a streamelési feladat.

Példa a kimenetre:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>A feladat kicsinyítette
Az Azure Stream Analytics úgy lett kialakítva, hogy rugalmasan méretezhető legyen, így nagy mennyiségű adatot képes kezelni. Az Azure Stream Analytics-lekérdezés egy **PARTITION BY-záradék** használatával közölheti a rendszerrel, hogy ez a lépés horizontális felskálázás. **PartitionId** egy speciális oszlop, amely a rendszer hozzáteszi, hogy megfeleljen a partíció azonosítója a bemeneti (event hub).

A lekérdezés partíciókra való kiosztásához a lekérdezés szintaxisát a következő kódra kell csökkenteni:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

A streamelési feladat további streamelési egységekre való felskálázása:

1. **Állítsa le** az aktuális feladatot.

2. Frissítse a lekérdezés szintaxisát **a< > Lekérdezés** lapon, és mentse a módosításokat.

3. A streamelési feladat CONFIGURE fejléce alatt válassza a **Méretezés lehetőséget.**

4. Csúsztassa a **Streamelési egységek** csúszkáját 1-től 6-ig. A streamelési egységek határozzák meg, hogy a feladat milyen számítási teljesítményt kaphat. Kattintson a **Mentés** gombra.

5. **Indítsa el** a streamelési feladatot a további skálázás bemutatásához. Az Azure Stream Analytics több számítási erőforrás között osztja el a munkát, és jobb átviteli teljesítményt érhet el, a munka erőforrások közötti particionálásával a PARTITION BY záradékban kijelölt oszlop használatával.

## <a name="monitor-the-job"></a>A feladat figyelése
A **MONITOR** terület a futó feladatstatisztikáit tartalmazza. Az első konfigurálás szükséges a tárfiók ugyanabban a régióban (név díjköteles, mint a többi a dokumentum).

![Az Azure Stream Analytics feladatfigyelése](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

**A tevékenységnaplókat** a feladat **irányítópultjának Beállítások** területéről is elérheti.

## <a name="clean-up-the-tollapp-resources"></a>A TollApp-erőforrások karbantartása
1. Állítsa le a Stream Analytics-feladatot az Azure Portalon.

2. Keresse meg azt az erőforráscsoportot, amely a TollApp-sablonhoz kapcsolódó nyolc erőforrást tartalmaz.

3. Válassza az **Erőforráscsoport törlése** elemet. A törlés megerősítéséhez írja be az erőforráscsoport nevét.

## <a name="conclusion"></a>Összegzés
Ez a megoldás bemutatta az Azure Stream Analytics szolgáltatásnak. Bemutatja, hogyan konfigurálhatja a bemeneteket és kimeneteket a Stream Analytics-feladathoz. A díjköteles adatok forgatókönyv használatával a megoldás ismerteti a mozgásban lévő adatok terében felmerülő gyakori problémákat, és azt, hogy ezek hogyan oldhatók meg egyszerű SQL-szerű lekérdezésekkel az Azure Stream Analytics-ben. A megoldás leírt SQL-bővítmény konstrukciók időbeli adatokkal való munka. Bemutatja, hogyan lehet az adatfolyamokat összecsatlakoztatni, hogyan gazdagíthatja az adatfolyamot statikus referenciaadatokkal, és hogyan kell kibővíteni egy lekérdezést a nagyobb átviteli érték elérése érdekében.

Bár ez a megoldás jó bevezetést nyújt, semmilyen módon nem teljes. A SAQL-nyelv használatával további lekérdezési mintákat találhat a [Query példákban a Stream Analytics közös használati mintáihoz.](stream-analytics-stream-analytics-query-patterns.md)
