---
title: IoT-megoldás készítése az Azure Stream Analytics használatával
description: A Stream Analytics IoT-megoldás egy őrbódét forgatókönyv a kezdeti lépéseket ismertető oktatóanyag
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 820a918b9ab5dd7a399dc43bcfed29fb3b94e99b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971103"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>IoT-megoldás készítése a Stream Analytics használatával

## <a name="introduction"></a>Bevezetés
Ez a megoldás elsajátíthatja az Azure Stream Analytics használatával valós idejű információkhoz juthat az adataiból. A fejlesztők révén könnyedén egyesítheti az adatfolyamok, például kattintson-adatfolyamok, naplók és eszköz által generált események, a korábbi adatokat vagy referenciaadatok üzleti elemzéseket végezhet. Teljes körűen felügyelt, valós idejű stream számítási szolgáltatás, amely a Microsoft Azure-ban üzemeltetett az Azure Stream Analytics biztosít beépített rugalmasság, a közel valós idejű és a méretezhetőség üzembe, és perceken belül.

Miután befejezte ezt a megoldást, is tudja:

* Ismerje meg az Azure Stream Analytics-portálon.
* Konfigurálhatja és telepítheti a folyamatos átviteli feladatok.
* Valós problémákra szerepeltetésével, és oldja meg azokat a Stream Analytics lekérdezési nyelv használatával.
* Fejlesszen magabiztosan a Stream Analytics használatával folyamatos átviteli megoldásokat az ügyfelek számára.
* A monitorozási és naplózási felület használatával kapcsolatos problémák elhárítása.

## <a name="prerequisites"></a>Előfeltételek
Ez a megoldás befejezéséhez az alábbi előfeltételek lesz szüksége:
* [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>A forgatókönyv bemutatása: "Hello, nem ingyenes!"
Díjmentesen hívható állomás egy gyakori jelenség. Tapasztal őket a sok gyorsforgalmi, hidat és alagutak világszerte. Minden nem ingyenes állomás van több díjmentesen fülkéit foglalja magában. A manuális fülkéit foglalja magában le kell fizetnem a nem ingyenes egy kísérő. Automatizált fülkéit foglalja magában: minden egyes érzékelőadatainak felett érzékelő megvizsgálja az RFID-kártyán, hogy rögzíteni a szélvédőkeret a jármű méretét, ha a nem ingyenes érzékelőadatainak. Ezek nem ingyenes állomások járműveknek áthaladását megjelenítése az eseménystream, amelyen érdekes műveleteket is elvégezhető, könnyebbé vált.

! [Nem ingyenes fülkéit foglalja magában: autó kép] (media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth .jpg)

## <a name="incoming-data"></a>A bejövő adatok
Ez a megoldás két adatfolyamot együttműködik. Érzékelő telepítve van a be- és a nem ingyenes állomások hozhat létre az első stream. A második streamben egy statikus keresési adatkészletet, amely a vehicle regisztrációs adatokat.

### <a name="entry-data-stream"></a>Bejegyzés adatfolyam
A bejegyzés adatfolyam autók kapcsolatos információt tartalmazza, ajtónál vagy kapunál díjmentesen állomásokat. A kilépési adatok események olyan élő streameli az Event Hubs üzenetsorába szereplő mintaalkalmazás egy webalkalmazásból.

| TollID | EntryTime | LicensePlate | Állapot | Fordítás | Modell | VehicleType | VehicleWeight | Díjmentesen hívható | Címke |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Alátámaszt |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |JERSEY |Toyota |4x4 |1 |0 |6 |321987654 |

A következő oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| TollID |A nem ingyenes érzékelőadatainak azonosítója, amely egyedileg azonosítja az egy nem ingyenes érzékelőadatainak |
| EntryTime |Dátuma és időpontja (UTC) a nem ingyenes érzékelőadatainak a jármű-bejegyzés |
| LicensePlate |A járműtelemetria licenc lemez száma |
| Állapot |Olyan állapotban, az Egyesült Államokban |
| Fordítás |Az autó gyártója |
| Modell |Az autó modell száma |
| VehicleType |Kereskedelmi járművek közül többnek 2 vagy utas járművek vagy 1 |
| WeightType |Járműtelemetria súly tonna; 0 utas járművekhez |
| Díjmentesen hívható |USD-t díjmentesen értéke |
| Címke |Az autó, amely automatizálja a fizetési; az e-címke üres, ha a fizetési manuális elkészítése |

### <a name="exit-data-stream"></a>Kilépés adatfolyam
A kilépési adatfolyam autók és a nem ingyenes állomás kapcsolatos információt tartalmazza. A kilépési adatok események olyan élő streameli az Event Hubs üzenetsorába szereplő mintaalkalmazás egy webalkalmazásból.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

A következő oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| TollID |A nem ingyenes érzékelőadatainak azonosítója, amely egyedileg azonosítja az egy nem ingyenes érzékelőadatainak |
| ExitTime |Dátuma és időpontja (UTC) díjmentesen érzékelőadatainak a jármű méretét való kilépés |
| LicensePlate |A járműtelemetria licenc lemez száma |

### <a name="commercial-vehicle-registration-data"></a>Kereskedelmi járművek regisztrációs adatait
A megoldás egy kereskedelmi járművek regisztrációs adatbázis statikus pillanatképet használja. Ezek az adatok Azure blob storage, a mintában szereplő mentett JSON-fájlként.

| LicensePlate | RegistrationId | Elévült |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

A következő oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| LicensePlate |A járműtelemetria licenc lemez száma |
| RegistrationId |A járműtelemetria regisztrációs azonosító |
| Elévült |A regisztrációs állapot a jármű méretét: 0, ha jármű regisztrációs aktív, 1, ha a regisztráció lejárt |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Állítsa be a környezetet az Azure Stream Analytics
Végezze el ezt a megoldást, a Microsoft Azure-előfizetést kell. Ha nem rendelkezik Azure-fiókkal, akkor [ingyenes próbaverziójának igénylése](https://azure.microsoft.com/pricing/free-trial/).

Mindenképp kövesse a cikk végén található "Az Azure-fiók törlése" című szakaszának lépéseit, hogy az Azure-kredit használható a leghatékonyabban teheti.

## <a name="deploy-the-sample"></a>A minta üzembe helyezése
Számos forrásanyag is könnyen üzembe helyezhető egy erőforráscsoportba tartozó néhány kattintással együtt. A megoldás meghatározása üzemel, a GitHub-adattárban található [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Az Azure Portalon TollApp-sablon üzembe helyezése
1. Helyezze üzembe az Azure a TollApp környezet, használja ezt a hivatkozást [TollApp Azure-sablon telepítése](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Ha a rendszer kéri, jelentkezzen be az Azure Portalon.

3. Válassza ki az előfizetést, amely a különböző erőforrásokat kell fizetnie.

4. Adjon meg egy új erőforráscsoportot, egyedi névvel `MyTollBooth`.

5. Válasszon egy Azure-beli helyet.

6. Adjon meg egy **időköz** , hogy hány másodperc. Ez az érték szerepel a minta webalkalmazást, hogy milyen gyakran adatok küldése eseményközpontba.

7. **Ellenőrizze** , fogadja el a feltételeket és kikötéseket.

8. Válassza ki **rögzítés az irányítópulton** , így később könnyen elérhetők az erőforrásokat.

9. Válassza ki **beszerzési** a minta-sablon üzembe helyezéséhez.

10. Néhány pillanat múlva megjelenik egy értesítés, győződjön meg arról, hogy a **üzembe helyezés sikeres**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Az Azure Stream Analytics TollApp erőforrások áttekintése
1. Bejelentkezés az Azure Portalra

2. Keresse meg az erőforráscsoport, amely az előző szakaszban lettek elnevezve.

3. Győződjön meg arról, hogy a következő források az erőforráscsoporthoz tartozik, szerepel:
   - Egy Cosmos DB-fiók
   - Egy Azure Stream Analytics-feladat
   - Egy Azure Storage-fiók
   - One Azure Event Hub
   - Two Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Vizsgálja meg a minta TollApp feladat
1. Kiindulva az erőforráscsoport az előző szakaszban jelölje be a Stream Analytics streamelési feladat nevét kezdetű **tollapp** (a név egyedi-e véletlenszerű karaktereket tartalmaz.).

2. Az a **áttekintése** lap a feladat, figyelje meg a **lekérdezés** viszi a lekérdezés szintaxisa.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   A lekérdezés szándéka ismétlő, tegyük fel, hogy szeretne-e, adjon meg egy nem ingyenes érzékelőadatainak járművek számát. Mivel a highway díjmentesen érzékelőadatainak megadása járművek folyama, most már az bejárata események pedig hasonló olyan adatfolyamra, amely soha nem áll le. A stream számszerűsítése, meg kell adnia egy "idő" keresztül méréséhez. Nézzük finomíthatja a kérdést, "hány járművek adjon meg egy nem ingyenes érzékelőadatainak percen át 3 percenként?" Ezt gyakran nevezik átfedésmentes száma.

   Ahogy látható, az Azure Stream Analytics használ olyan lekérdezési nyelvet, például az SQL és kiegészíti néhány kiterjesztést adja meg a lekérdezés idővel kapcsolatos szempontokat.  További részletekért olvassa el [Időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és [Ablakkezelési](https://msdn.microsoft.com/library/azure/dn835019.aspx) a lekérdezésben használt szerkezeteket.

3. Vizsgálja meg a TollApp mintafeladat bemenetei. Csak a EntryStream bemeneti szerepel az aktuális lekérdezés.
   - **EntryStream** bemeneti egy Event Hub-kapcsolat, amely várólistára helyezi, amely minden alkalommal, amikor egy autó egy őrbódét ír a főutat ábrázoló. Az eseményeket hoz létre, amely része a mintául szolgáló webalkalmazás és az adatokat az eseményközpont várólistában van. Vegye figyelembe, hogy a bemeneti lekérik a streamelési lekérdezés a FROM záradékban.
   - **ExitStream** bemeneti egy Event Hub-kapcsolat, amely minden alkalommal, amikor egy autó egy őrbódét kilép a highway az üzenetsorok. A streamelési bemeneti használatban van a lekérdezés szintaxisa újabb változata.
   - **Regisztrációs** bemeneti adatok egy Azure Blob tárolási kapcsolat, a keresésekben használt szükség szerint statikus registration.json fájlra mutat. A referenciaadat-bemenetek használja a lekérdezés szintaxisa újabb változata.

4. Vizsgálja meg a TollApp minta feladat kimenetére.
   - **A cosmos DB** kimenete egy Cosmos adatbázis-gyűjtemény, amely megkapja a kimeneti fogadó eseményeket. Vegye figyelembe, hogy ez a kimenet be a folyamatos átviteli lekérdezési záradék szerepel.

## <a name="start-the-tollapp-streaming-job"></a>A TollApp streamelési feladat indítása
Kövesse az alábbi lépéseket a streamelési feladat indítása:

1. Az a **áttekintése** a feladatot, válassza az oldal **Start**.

2. Az a **indítási feladat** ablaktáblán válassza előbb **most**.

3. Pár pillanat, amikor a feladat fut, a a **áttekintése** a folyamatos átviteli feladat, nézet lapján a **figyelés** grafikon. A gráf több ezer bemeneti események és a kimeneti események tíz kell megjelennie.

## <a name="review-the-cosmosdb-output-data"></a>Tekintse át a cosmos db kimeneti adatok
1. Keresse meg a TollApp erőforrásokat tartalmazó erőforráscsoportot.

2. Válassza ki az Azure Cosmos DB-fiókot a névminta **tollapp\<véletlenszerű\>– cosmos**.

3. Válassza ki a **adatkezelő** fejlécre az adatkezelő oldal megnyitásához.

4. Bontsa ki a **tollAppDatabase** > **tollAppCollection** > **dokumentumok**.

5. Azonosítók listáját a több docs jelennek meg a kimeneti elérhetővé válik.

6. Válassza ki az egyes, tekintse át a JSON-dokumentum azonosítója. Figyelje meg, hogy minden egyes tollid windowend időt, és ezt az ablakot az autók száma.

7. Egy további három perccel később négy dokumentumok egy másik készlete áll rendelkezésre, tollid kiszolgálónként egy dokumentumot.


## <a name="report-total-time-for-each-car"></a>A jelentés minden egyes autó fordított időt
Az átlagos idő szükséges a nem ingyenes áthaladását egy autó segítségével felmérheti a folyamat és a vásárlói élményt hatékonyságát.

A teljes ideje megkereséséhez az EntryTime adatfolyam a ExitTime stream-csatlakozzon. Csatlakozzon a egyenlő TollId és LicencePlate oszlopokat két bemeneti streamekhez. A **CSATLAKOZZON** operátor szükséges, hogy adja meg, amely leírja a csatlakoztatott események elfogadható időeltérése historikus eltérést. Használja a **DATEDIFF** függvénnyel adhat meg, hogy legyen-e az események egymástól legfeljebb 15 perc. Emellett a alkalmazni a **DATEDIFF** függvény való kilépéshez és tételt hányszor számítási, a tényleges idő a nem ingyenes állomás egy autó futtatásával töltött. Vegye figyelembe a különbség a használatával **DATEDIFF** ha használatban van egy **kiválasztása** utasítás helyett egy **CSATLAKOZZON** feltétel.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>A folyamatos átviteli feladat lekérdezési szintaxis TollApp frissítése:

1. Az a **áttekintése** a feladatot, válassza az oldal **leállítása**.

2. Várjon néhány pillanatot a értesítést, amelyet a feladat leállt.

3. FELADATTOPOLÓGIA fejléc alatt válassza ki a **< > lekérdezés**

4. Illessze be a beállított streamelési SQL-lekérdezést.

5. Válassza ki **mentése** a lekérdezés mentéséhez. Győződjön meg róla **Igen** menti a módosításokat.

6. Az a **áttekintése** a feladatot, válassza az oldal **Start**.

7. Az a **indítási feladat** ablaktáblán válassza előbb **most**.

### <a name="review-the-total-time-in-the-output"></a>Tekintse át a kimenetben a teljes ideje
Ismételje meg az előző szakaszban áttekintheti a cosmos db kimeneti adatokat a folyamatos átviteli feladatból. Tekintse át a legújabb JSON-dokumentumokat.

Például ez a dokumentum látható egy példa autó egy bizonyos licenc lemezt, a entrytime és kilépési időt és a DATEDIFF durationinminutes számított mező a nem ingyenes érzékelőadatainak időtartama két perc megjelenítése:
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

## <a name="report-vehicles-with-expired-registration"></a>A jelentés járművek lejárt a regisztráció
Az Azure Stream Analytics statikus referenciaadatok pillanatképeinek használatával csatlakozik a historikus adatfolyamokat. Ez a funkció bemutatása érdekében használja a következő minta kérdést. A regisztrációs bemeneti adat egy statikus blob json-fájlt, amely felsorolja a licenc címkék lejárhat. Való csatlakozás után a licenc lemezre, a rendszer összehasonlítja a referenciaadatok mindkét díjmentesen áthaladó járművön.

Ha egy kereskedelmi járművek regisztrálva van a nem ingyenes vállalatot, azt is halad át a nem ingyenes érzékelőadatainak vizsgálatra leállítás nélkül. A regisztrációs keresési táblázat segítségével azonosíthatja a minden kereskedelmi járművek regisztrációk lejárt.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Ismételje meg a folyamatos átviteli feladat lekérdezési szintaxis TollApp frissíteni az előző szakaszban.

2. Ismételje meg az előző szakaszban áttekintheti a cosmos db kimeneti adatokat a folyamatos átviteli feladatból.

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

## <a name="scale-out-the-job"></a>Horizontális felskálázás a feladat
Az Azure Stream Analytics rugalmasan növelheti a célja, hogy nagy mennyiségű adatot képes kezelni. Az Azure Stream Analytics-lekérdezés használhatja egy **PARTITION BY** záradékot, hogy a rendszer, hogy ebben a lépésben elvégzi a horizontális felskálázást. **PartitionId** speciális oszlop, amely a rendszer hozzáadja a Partícióazonosító a bemenet (eseményközpontból) megfelelően.

A horizontális felskálázáshoz partíciókat a lekérdezés a lekérdezés szintaxisa a következő kód szerkesztése:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Vertikális felskálázása a folyamatos átviteli feladat több folyamatos átviteli egységet:

1. **Állítsa le** az aktuális feladatot.

2. A lekérdezés szintaxisa a frissítés a **< > lekérdezés** lapon, és mentse a módosításokat.

3. A folyamatos átviteli feladat KONFIGURÁLÁSA fejlécét, válassza a **méretezési**.

4. Húzza az ujját a **folyamatos átviteli egységek** csúszkát 1-től a 6. A folyamatos átviteli egységek határozzák meg a számítási teljesítmény, a feladatok fogadására. Kattintson a **Mentés** gombra.

5. **Indítsa el** a folyamatos átviteli feladat használatával mutatja be a további méretezéshez. Az Azure Stream Analytics munkahelyi elosztja további számítási erőforrásokat, és jobb teljesítményt érhet el a munka particionálása az oszlop a PARTITION BY záradékban található erőforrások között.

## <a name="monitor-the-job"></a>A feladat figyelése
A **FIGYELŐ** terület tartalmazza a futó feladat statisztikája. Első konfigurációs van szükség (például a dokumentum többi részén neve nem ingyenes) ugyanabban a régióban a storage-fiókot használni.

![Az Azure Stream Analytics feladat figyelése](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Elérheti **tevékenységeket tartalmazó naplók** a projekt irányítópultján **beállítások** területen is.

## <a name="clean-up-the-tollapp-resources"></a>TollApp erőforrásokat
1. Állítsa le a Stream Analytics-feladat az Azure Portalon.

2. Keresse meg a kapcsolódó TollApp sablon nyolc erőforrásokat tartalmazó erőforráscsoportot.

3. Válassza az **Erőforráscsoport törlése** elemet. Írja be a törlés megerősítéséhez az erőforráscsoport.

## <a name="conclusion"></a>Összegzés
Ez a megoldás mutatta be az Azure Stream Analytics szolgáltatás. Azt bizonyított bemeneteit és kimeneteit a Stream Analytics-feladat konfigurálása. A nem ingyenes adatok helyzet segítségével a megoldás az adatok az Azure Stream Analytics egyszerű SQL-szerű lekérdezéseket a mozgásban lévő adatoknak egyaránt, és hogyan azok megoldhatók a tárhelyen lévő felmerülő problémákat általános típusainak ismertetése. A megoldás a historikus adatok használata SQL-bővítmény szerkezetek ismerteti. Bemutatta, hogyan lehet az adatfolyamokat, hogyan kell az adatfolyamot, statikus referenciaadatok bővítését és hogyan skálázhatja fel horizontálisan a lekérdezést nagyobb átviteli sebességet érhet el.

Bár ez a megoldás lehetővé teszi egy jó bemutatása, hogy még nem fejeződött be bármilyen módon. További lekérdezési minták használatával a SAQL nyelvet a annak [példák a Stream Analytics-használat gyakori minták lekérdezése](stream-analytics-stream-analytics-query-patterns.md).
