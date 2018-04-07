---
title: Az IoT-megoldás létrehozása a Azure Stream Analytics segítségével
description: A Stream Analytics IoT-megoldás az őrbódét forgatókönyv-bevezető oktatóanyag
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Az IoT-megoldás létrehozása a Stream Analytics segítségével

## <a name="introduction"></a>Bevezetés
Ebben a megoldásban megismerheti, hogyan Azure Stream Analytics segítségével valós idejű elemzések lekérése az adataiból. A fejlesztők könnyedén kombinálhatja adatstreamek, például kattintás-adatfolyamok, a naplókat és az eszköz által létrehozott események előzményrekordjaira vagy üzleti elemzéseket kapcsolattípusokból referenciaadatok. Teljes körűen felügyelt, valós idejű stream számítási szolgáltatás, amely a Microsoft Azure-ban Azure Stream Analytics biztosít, beépített hibatűrési, alacsony késéssel és méretezhetőség terén annak érdekében, hogy másolatot és futtató percben.

Ez a megoldás befejezése után is:

* Ismerkedjen meg az Azure Stream Analytics-portálon.
* Konfigurálhatja és telepítheti a folyamatos átviteli feladatnak.
* Fogalmazza meg valós problémákat és azok megoldási a Stream Analytics lekérdezési nyelv használatával.
* Hogyan megoldások streaming az ügyfeleknek a Stream Analytics az vetett bizalmat.
* A figyelés és naplózás a felhasználói élmény használja a probléma megoldásához.

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek teljesülését, ez a megoldás befejezéséhez lesz szüksége:
* Egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>A forgatókönyv bevezető: "Hello, téren!"
Téren állomás egy közös jelenség. Tapasztal őket több gyorsforgalmi, hidak és alagutakon keresztül történik. Minden téren állomás van több téren fülkéit foglalja magában. A manuális fülkéit foglalja magában le kell fizetnie a téren egy kísérő. Automatikus fülkéit foglalja magában: felett minden kiállítási érzékelő megvizsgálja egy RFID kártya, amely, ha a téren kiállítási, a vehicle szélvédőkeret rögzítettek. Akkor is könnyen egy esemény adatfolyamként, amelyben érdekes művelet végrehajtható ezen a téren állomások keresztül járművekről gyűjtött áthaladását megjelenítéséhez.

![Kép autók téren fülkéit foglalja magában:](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Bejövő adatforgalom
Ez a megoldás két adatstreamek működik. Be- és a téren állomások telepített érzékelők az első adatfolyam eredményez. A második adatfolyama statikus keresési adatkészletre mutató vehicle regisztrációs adatokat.

### <a name="entry-data-stream"></a>Bejegyzés adatfolyam
A bejegyzés adatfolyam autók információkat tartalmaz, téren állomások kapunál. A kilépési adatok események olyan élő folyamatos átviteli egy Eseményközpontba várólistán szereplő mintaalkalmazás webalkalmazásból.

| TollID | EntryTime | LicensePlate | Állapot | Ellenőrizze | Modell | VehicleType | VehicleWeight | Téren | Címke |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| TollID |A téren kiállítási azonosítója, amely egyedileg azonosít egy téren kiállítási |
| EntryTime |A dátum és idő a téren kiállítási UTC a vehicle-bejegyzés |
| LicensePlate |A vehicle licenc lemez számát |
| Állapot |Az Amerikai Egyesült Államokban állapotának |
| Ellenőrizze |A automobile gyártója |
| Modell |A automobile modell száma |
| VehicleType |Utas járművekről gyűjtött vagy a kereskedelmi járművekről gyűjtött 2 vagy 1 |
| WeightType |Vehicle súly tonna; az utasok járművekről gyűjtött 0 |
| Téren |USD téren értéke |
| Címke |Az e-címke a autó, amely automatizálja a fizetési; üres, ha a fizetési manuálisan végezhető el |

### <a name="exit-data-stream"></a>Kilépés adatfolyam
A kilépési adatfolyam a téren állomás elhagyása autók információkat tartalmaz. A kilépési adatok események olyan élő folyamatos átviteli egy Eseményközpontba várólistán szereplő mintaalkalmazás webalkalmazásból.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| TollID |A téren kiállítási azonosítója, amely egyedileg azonosít egy téren kiállítási |
| ExitTime |A dátum és idő, a vehicle elhagyásakor téren kiállítási UTC szerint |
| LicensePlate |A vehicle licenc lemez számát |

### <a name="commercial-vehicle-registration-data"></a>Kereskedelmi vehicle regisztrációs adatok
A megoldás pillanatnyi egy kereskedelmi vehicle adatbázist használ. Ezek az adatok Azure blob-tároló, a mintában szereplő mentéskor JSON-fájlként.

| LicensePlate | RegistrationId | Elévült |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| LicensePlate |A vehicle licenc lemez számát |
| RegistrationId |A vehicle regisztrációs azonosítója |
| Elévült |A regisztrációs állapotát a vehicle: 0, ha a vehicle regisztrációs aktív, ha lejárt a regisztrációs 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Az Azure Stream Analytics a környezet beállítása
Ez a megoldás, szüksége egy Microsoft Azure-előfizetést. Ha még nem rendelkezik Azure-fiókot, akkor [ingyenes próbaverzióját kérelem](http://azure.microsoft.com/pricing/free-trial/).

Győződjön meg arról, ez a cikk végén a "Az Azure-fiók törlése" című szakaszának lépéseit követni, így biztosíthatja, hogy az Azure-kreditjeinek a lehető legjobb felhasználását.

## <a name="deploy-the-sample"></a>A minta telepítése 
Nincsenek több erőforrást, amely egy erőforráscsoportban együtt mindössze néhány kattintással egyszerűen telepíthető. A megoldás meghatározása a github-tárházban található [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Az Azure-portálon TollApp sablon üzembe helyezése
1. Történő telepítéséhez a TollApp környezet az Azure-ba, használja az erre a hivatkozásra kattintva [TollApp Azure sablon telepítése](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Ha a rendszer kéri, jelentkezzen be az Azure-portálon.

3. Válassza ki az előfizetést, amelyben a különböző erőforrások vannak számlázva.

4. Adja meg egy új erőforráscsoportot, egyedi névvel ellátott `MyTollBooth`. 

5. Válasszon egy Azure-beli helyet.

6. Adjon meg egy **időköz** néhány másodpercig. Ez az érték a minta webalkalmazást, hogy milyen gyakran adatok küldése az Eseményközpont használatban van. 

7. **Ellenőrizze** gombra kattintva elfogadja a feltételeket és kikötéseket.

8. Válassza ki **rögzítés az irányítópulton** , hogy később könnyen elérhetők az erőforrások.

9. Válassza ki **beszerzési** a minta sablon telepítéséhez.

10. Néhány másodpercen belül megjelenik egy értesítés, ellenőrizze a **KözpontiTelepítés sikerült**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Tekintse át az Azure Stream Analytics TollApp erőforrások
1. Bejelentkezés az Azure Portalra

2. Az erőforráscsoport elnevezett az előző szakaszban találja.

3. Győződjön meg arról, hogy a következő erőforrások az erőforráscsoportban találhatók:
   - Egy Cosmos DB fiók
   - Egy Azure Stream Analytics-feladathoz.
   - Egy Azure Storage-fiók
   - One Azure Event Hub
   - Two Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Vizsgálja meg a minta TollApp feladat 
1. Kiindulva az erőforráscsoport az előző szakaszban válassza ki a kezdési nevű adatfolyam Stream Analytics-feladat **tollapp** (a név egyedi-e véletlenszerű karaktereket tartalmaz.).

2. Az a **áttekintése** a feladat, a figyelmeztetés lapon a **lekérdezés** szövegmezőre, tekintse meg a lekérdezés szintaxisát.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   A célt a lekérdezés ismétlő, hogy tegyük fel, hogy szeretné-e, adjon meg egy téren kiállítási gépjárműveket száma. Mivel a highway téren kiállítási állandó folyama miatt sűrítéses, most már be eseményeket is megfelel a olyan adatfolyamra, amely soha nem áll le. Az adatfolyam számszerűsítése, meg kell adnia egy "idő" keresztül mérését. Most pontosítsa további, a kérdés a "hány járművekről gyűjtött adja meg egy téren kiállítási három percenként?" Ezt gyakran nevezik az átfedésmentes száma.

   Ahogy látja, az Azure Stream Analytics használ lekérdezésnyelvet, amely például az SQL, és adja meg a lekérdezés idővel kapcsolatos szempontok néhány bővítmények hozzáadása.  További részletekért olvassa el [Időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és [Ablakozó](https://msdn.microsoft.com/library/azure/dn835019.aspx) a lekérdezésben használt szerkezeteket tartalmaz.

3. Vizsgálja meg az adatokat a TollApp minta feladat. Az aktuális lekérdezés csak a EntryStream bemeneti használatos.
   - **EntryStream** bemeneti érték egy Eseményközpont kapcsolatának, amely minden alkalommal, amikor egy autó egy őrbódét ír a highway képviselő adatokat várólisták. A webes alkalmazás, amely része a minta az eseményeket hoz létre, és adatokat az Event Hubs van várakozó. Vegye figyelembe, hogy a bemeneti lekérdeznek az adatfolyam-továbbítási lekérdezés FROM záradékában.
   - **ExitStream** bemeneti érték egy Eseményközpont kapcsolatának, amely minden alkalommal, amikor egy autó egy őrbódét kilép a highway a képviselő adatokat várólisták. Az adatfolyam-továbbítási bemeneti a lekérdezés szintaxisa újabb változatát használja.
   - **Regisztrációs** bemeneti érték egy Azure Blob storage kapcsolatot, szükség szerint használta a keresések statikus registration.json fájlra mutat. A lekérdezés szintaxisa újabb változatát a referenciaadat-bemenetek használatos.

4. Vizsgálja meg a TollApp minta feladat kimenetének.
   - **A cosmos DB** kimeneti gyűjteménye Cosmos adatbázis, amely megkapja a kimeneti fogadó események. Vegye figyelembe, hogy szolgál, hogy a kimeneti be a folyamatos átviteli lekérdezés záradékában.

## <a name="start-the-tollapp-streaming-job"></a>Indítsa el a TollApp folyamatos átviteli feladatok
Kövesse az alábbi lépéseket a folyamatos átviteli feladat elindításához:

1. Az a **áttekintése** a feladat, válassza a lap **Start**.

2. Az a **indítási feladat** ablaktáblán válassza előbb **most**.

3. Után néhány perc múlva, miután a feladat fut, a a **áttekintése** a folyamatos átviteli feladat, nézet lap a **figyelés** grafikon. A diagramnak több ezer bemeneti eseményt, és több tíz kimeneti események kell megjelennie.

## <a name="review-the-cosmosdb-output-data"></a>Tekintse át a CosmosDB kimeneti adatok
1. Keresse meg a TollApp erőforrásokat tartalmazó erőforráscsoportot.

2. Válassza ki a Azure Cosmos DB fiókot a minta **tollapp<random>– cosmos**.

3. Válassza ki a **adatkezelő** címsor adatkezelő lapjának megnyitásához.

4. Bontsa ki a **tollAppDatabase** > **tollAppCollection** > **dokumentumok**.

5. Az azonosítók listáját több docs jelennek meg a kimeneti elérhetővé válik.

6. Válassza ki az egyes tekintse át a JSON-dokumentum-azonosítót. Figyelje meg minden egyes tollid windowend idő, és ezt az ablakot az autók száma.

7. Egy további három perc elteltével négy dokumentumok egy másik készlete áll rendelkezésre, tollid egy dokumentumot. 


## <a name="report-total-time-for-each-car"></a>A jelentés minden egyes car fordított időt
Rendelkezik egy autó az áthaladás, a téren szükséges átlagos idő segít a folyamat, és a felhasználói élmény hatékonyságának ellenőrzéséhez.

A teljes időnek található, a EntryTime adatfolyam a ExitTime adatfolyam csatlakozik. Csatlakoztassa a két bemeneti adatfolyam egyenlő megfelelő TollId és LicencePlate oszlopok. A **csatlakozás** operátor szükséges, amely leírja a csatlakoztatott események elfogadható időeltérése historikus eltérést megadását. Használja a **DATEDIFF** működnek, adja meg, hogy legyen-e az események egymástól legfeljebb 15 perc. Is érvényesek a **DATEDIFF** való kilépéshez és az bejegyzés alkalommal kiszámítására, a tényleges idő egy autó a téren állomás futtatásával töltött. Vegye figyelembe a különbség az, felhasználásának **DATEDIFF** használat esetén a egy **válasszon** utasítás helyett egy **csatlakozás** feltétel.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>A folyamatos átviteli feladat lekérdezés szintaxisát TollApp frissítése:

1. Az a **áttekintése** a feladat, válassza a lap **leállítása**.

2. Várjon egy kis ideig, az értesítés a feladat leállt.

3. FELADAT TOPOLÓGIA fejléc alatt válassza ki a **< > lekérdezés**

4. Illessze be a módosított adatfolyam SQL-lekérdezést.

5. Válassza ki **mentése** a lekérdezés mentéséhez. Erősítse meg **Igen** menti a módosításokat.

6. Az a **áttekintése** a feladat, válassza a lap **Start**.

7. Az a **indítási feladat** ablaktáblán válassza előbb **most**.

### <a name="review-the-total-time-in-the-output"></a>Tekintse át a kimenetben teljes ideje
Ismételje az előző szakaszban, hogy tekintse át a folyamatos átviteli feladatok CosmosDB kimeneti adatait. Tekintse át a legújabb JSON-dokumentumokat. 

Ez a dokumentum bemutatja, például egy bizonyos licenc lemez, entrytime és kilépési idő és a DATEDIFF durationinminutes számított mező két perc téren kiállítási időtartam látható egy példa car: 
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

## <a name="report-vehicles-with-expired-registration"></a>A jelentés járművekről gyűjtött lejárt regisztrálása
Az Azure Stream Analytics statikus referenciaadatok pillanatképeinek segítségével csatlakozik a historikus adatfolyamokat. Ez a funkció bemutatásához, használja a következő minta kérdést. A regisztrációs bemeneti érték, amely tartalmazza a licenc címkék lejáratok, statikus blob json-fájlt. Való csatlakozással, a licenc lapra, a rendszer összehasonlítja a referenciaadatok minden mindkét téren áthaladó vehicle. 

Ha egy kereskedelmi vehicle regisztrálva van a téren vállalatot, azt is haladnak át a téren kiállítási nélkül lett a vizsgálathoz. A regisztrációs keresési tábla használatával azonosítsa az összes kereskedelmi járművekről gyűjtött regisztráció lejárt.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Ismételje meg a folyamatos átviteli feladat lekérdezés szintaxisát TollApp frissíteni az előző szakaszban leírt lépéseket.

2. Ismételje az előző szakaszban, hogy tekintse át a folyamatos átviteli feladatok CosmosDB kimeneti adatait. 

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

## <a name="scale-out-the-job"></a>A feladat kiterjesztése
Az Azure Stream Analytics célja, hogy rugalmasan méretezési, hogy a nagy mennyiségű adatot is képes kezelni. Az Azure Stream Analytics lekérdezési használhatja egy **PARTITION BY** záradékot, hogy a rendszer, hogy ezt a lépést méretezze át. **PartitionId** különleges oszlop, amely a rendszer hozzáadja a partícióazonosító: a bemeneti (eseményközpont) megfelelően.

Terjessze ki a lekérdezés partíciókra, szerkessze a lekérdezés szintaxisát, és a következő kódot:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

A további folyamatos átviteli egységeket a folyamatos átviteli feladat növelheti:

1. **Állítsa le** az aktuális feladatot. 

2. A lekérdezés szintaxisát, a frissítés a **< > lekérdezés** lapon, és menti a módosításokat.

3. A folyamatos átviteli feladatok KONFIGURÁLÁSA fejléc, alatt válassza ki a **méretezési**.
   
4. Húzza a **adatfolyam-egységek** 6 1 csúszkát. Streamelési egység határozzák meg a számítási teljesítményt, amely a feladatok fogadására. Kattintson a **Mentés** gombra.

5. **Start** a folyamatos átviteli feladat bemutatják a további skála. Az Azure Stream Analytics munkahelyi elosztása több számítási erőforrással és elérése javítható az átviteli teljesítmény, a particionálás a munkahelyi erőforrásokhoz a PARTITION BY záradékban a kijelölt oszlop között. 

## <a name="monitor-the-job"></a>A feladat figyelése
A **FIGYELŐ** terület tartalmazza a futó feladat statisztikája. Első beállításakor a tárfiók ugyanabban a régióban (Ez a dokumentum többi hasonlóan neve téren) használatára van szükség.   

![A figyelő képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Van-e hozzáférési **tevékenységi naplóit** a projekt irányítópultján **beállítások** terület is.

## <a name="clean-up-the-tollapp-resources"></a>A TollApp erőforrások törlése
1. Állítsa le a Stream Analytics-feladat az Azure portálon.

2. Keresse meg az erőforráscsoport, amely tartalmazza a TollApp sablon nyolc erőforrásaihoz.

3. Válassza az **Erőforráscsoport törlése** elemet. Írja be a törlés megerősítéséhez az erőforráscsoport nevét.

## <a name="conclusion"></a>Összegzés
Ez a megoldás az Azure Stream Analytics szolgáltatás bevezetett. Azt mutatja be- és kimenetekkel a Stream Analytics-feladat konfigurálása. A téren forgatókönyvet használja, a megoldás viszonylag problémák merülhetnek fel az adatok az Azure Stream Analytics egyszerű SQL-szerű lekérdezéseket mozgásérzékelési, és hogyan azok megoldhatók a területen található általános típusú. A megoldás leírt SQL bővítmény szerkezetek az ideiglenes adatai. Bemutatta, hogyan csatlakoztathatók a adatfolyamokat, az adatfolyamot a statikus referenciaadatok kiegészítése, és nagyobb átviteli sebesség eléréséhez lekérdezés horizontális.

Bár ez a megoldás megfelelő bemutatása, nincs kész bármilyen módon. A SAQL nyelvet további lekérdezési mintáinak található [gyakori Stream Analytics használati minták példák lekérdezése](stream-analytics-stream-analytics-query-patterns.md).
