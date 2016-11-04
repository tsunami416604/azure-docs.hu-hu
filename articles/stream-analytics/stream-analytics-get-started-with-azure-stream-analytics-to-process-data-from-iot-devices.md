---
title: Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását. | Microsoft Docs
description: IoT-érzékelőcímkék és -adatfolyamok streamelemzéssel és valós idejű adatfeldolgozással
keywords: iot-megoldás, bevezetés az iot használatába
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását
Ez az oktatóanyag bemutatja, hogyan hozhat létre streamfeldolgozó logikákat, és hogyan gyűjthet velük adatokat az eszközök internetes hálózatára kapcsolódó (IoT-) eszközökről. Egy valódi, az IoT-re épülő gyakorlati esettel fogjuk bemutatni, hogyan hozhat létre egy megoldást gyorsan és gazdaságosan.

## Előfeltételek
* [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)
* A mintalékérdezés és a mintaadatfájlok letölthetők a [GitHubból](https://aka.ms/azure-stream-analytics-get-started-iot)

## Forgatókönyv
A Contoso egy vállalat az ipari automatizálás területén, amely teljesen automatizálta a gyártási folyamatait. A gyár gépeinek érzékelői valós idejű adatstreamek létrehozására képesek. Ebben a forgatókönyvben a termelési szint egyik igazgatója valós idejű elemzéseket szeretne kapni az érzékelők adataiból, hogy mintákat keressen bennük, és ezek alapján tegyen további lépéseket. Az érzékelők adatain a SAQL nyelvet (Stream Analytics Query Language) használjuk, hogy érdekes szabályszerűségeket keressünk a beérkező streamben.

Itt az adatokat egy Texas Instrument Sensor Tag eszköz állítja elő.

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

A hasznos adatok JSON formátumban vannak, és a következőképpen néznek ki:

    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

A valós forgatókönyvekben több száz ilyen érzékelő állíthat elő eseményeket streamként. Ideális esetben van egy átjáróeszköz, amely egy bizonyos kód futtatásával elküldi ezeket az eseményeket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy az [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) részére. A Stream Analytics-feladatok felhasználják az Event Hubs eseményeit, és a streameken valós idejű lekérdezéseket futtatnak. Ezután elküldheti az eredményeket az egyik [támogatott kimenetre](stream-analytics-define-outputs.md).

A használat megkönnyítése érdekében ez a Kezdeti lépések útmutató valódi SensorTag eszközökről származó mintaadatfájlokat biztosít. Ezeken lekérdezéseket futtathat, és megtekintheti az eredményeiket. A következő útmutatókból megtudhatja, hogyan csatlakoztathatja a feladatait bemenetekhez és kimenetekhez, és hogyan helyezheti üzembe őket az Azure szolgáltatásban.

## Stream Analytics-feladat létrehozása
1. Az [Azure Portalon](http://manage.windowsazure.com) kattintson a **STREAM ANALYTICS** elemre, majd a lap bal alsó sarkában található **NEW** (Új) gombra kattintva hozzon létre egy új elemzési feladatot.
   
    ![Új Stream Analytics-feladat létrehozása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Kattintson a **QUICK CREATE** (Gyorslétrehozás) gombra.
3. A **REGIONAL MONITORING STORAGE ACCOUNT** (Régió szerinti megfigyelési tárfiók) beállításnál válassza az **CREATE NEW STORAGE ACCOUNT** (Új tárfiók létrehozása) lehetőséget, és adjon neki egy egyedi nevet. Az Azure Stream Analytics ezt a fiókot fogja használni a jövőbeli feladataiból származó megfigyelési információk tárolására.
   
   > [!NOTE]
   > Régiónként csak egyszer hozza létre ezt a tárfiókot. Ez a tároló az adott régióban létrehozott összes Stream Analytics-feladat között meg lesz osztva.
   > 
   > 
4. Kattintson a lap alján lévő **CREATE STREAM ANALYTICS JOB** (Stream Analytics-feladat létrehozása) elemre.
   
    ![Tárfiók konfigurálása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure Stream Analytics-lekérdezés
A **QUERY** (Lekérdezés) fülre kattintva lépjen a Lekérdezésszerkesztőhöz. A **QUERY** (Lekérdezés) lap egy olyan T-SQL-lekérdezést tartalmaz, amely végrehajtja az átalakítást a beérkező eseményadatokon.

## Nyers adatok archiválása
A lekérdezések legegyszerűbb formája a továbbított lekérdezés, amely a kijelölt kimeneten archiválja az összes bemeneti adatot.

![Feladatlekérdezés archiválása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Most töltse le a számítógépére a mintaadatfájlokat a [GitHubból](https://aka.ms/azure-stream-analytics-get-started-iot). Illessze be a lekérdezést a PassThrough.txt fájlból. Kattintson az alsó **Teszt** gombra, és válassza ki a letöltés helyéről a HelloWorldASA-InputStream.json nevű fájlt.

![Teszt gomb a Stream Analytics szolgáltatásban](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Teszt bemeneti stream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

A lekérdezés eredményeit az alábbi képernyőképen látható tallózóban tekintheti meg.

![Teszteredmények](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Adatok szűrése feltétel alapján
Próbáljuk meg egy feltétel alapján szűrni az eredményeket. Azt szeretnénk, hogy az eredmények között csak a „sensorA” érzékelőből érkező események jelenjenek meg. A lekérdezés a Filtering.txt fájlban található.

![Adatstream szűrése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Vegye figyelembe, hogy a kis- és nagybetűket megkülönböztető lekérdezés karakterlánc típusú értékkel végzi az összehasonlítást. A lekérdezés végrehajtásához kattintson az **Ismétlés** gombra. A lekérdezésnek 389 sort kell visszaadnia az 1860 eseményből.

![Második kimeneti eredmény a lekérdezéstesztelésből](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Riasztás az üzleti munkafolyamat elindítására
Tegyük részletesebbé a lekérdezést. Az átlaghőmérsékletet minden érzékelőtípus esetében 30 másodperces időközönként szeretnénk mérni, és csak akkor szeretnénk eredményeket megjeleníteni, ha az átlaghőmérséklet meghaladja a 100 fokot. Megírjuk az alábbi lekérdezést, majd az **Ismétlés** gombra kattintva megjelenítjük az eredményeket. A lekérdezés a ThresholdAlerting.txt fájlban található.

![30 másodperces szűrőlekérdezés](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Látható, hogy az eredmények most csak 245 sorban jelenítik meg azoknak az érzékelőknek a nevét, amelyeknél az átlaghőmérséklet nagyobb 100 foknál. Ez a lekérdezés az események streamjét a **dspl** szerint csoportosítja, ami az érzékelő neve, az **Átfedésmentes ablak** pedig 30 másodperc. A historikus lekérdezések esetében elengedhetetlen az időköz megadása. A **TIMESTAMP BY** záradékkal az **OUTPUTTIME** oszlopot adtuk meg, amely minden historikus számításhoz társít egy időközt. Részletes információkat az [időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és az [ablakkezelési függvényeket](https://msdn.microsoft.com/library/azure/dn835019.aspx) ismertető MSDN-témakörökben talál.

![Hőmérséklet 100 fok felett](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Események hiányának észlelése
Hogyan írható olyan lekérdezés, amely a bemeneti események hiányát keresi? Keressük meg a legutóbbi esetet, amikor az érzékelő adatokat küldött, majd a következő percben nem küldött eseményeket. A lekérdezés a AbsenseOfEvent.txt fájlban található.

![Események hiányának észlelése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Itt egy **BAL OLDALI KÜLSŐ** illesztést alkalmazunk ugyanarra az adatstreamre (önillesztés). **BELSŐ** illesztés esetén csak akkor kapunk eredményt, ha van egyezés.  **BAL OLDALI KÜLSŐ** illesztés esetében azonban, ha az illesztés bal oldalán lévő eseményhez nincs egyezés, akkor a rendszer olyan oszlopot ad vissza, amely a jobb oldal összes oszlopában NULL értéket tartalmaz. Ez a módszer nagyon hasznos, ha események hiányára kíván rákeresni. Az [ILLESZTÉS](https://msdn.microsoft.com/library/azure/dn835026.aspx) részleteit az MSDN-dokumentációnkban találja.

![Eredmények illesztése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Összegzés
A jelen oktatóanyag célja annak bemutatása, hogyan lehet Stream Analytics lekérdezési nyelven különböző lekérdezéseket írni, és az eredményeket megjeleníteni a képernyőn. Ezek azonban csak az első lépések. A Stream Analytics rengeteg lehetőséget rejt még magában. A Stream Analytics számos bemenetet és kimenetet támogat, és még az Azure Machine Learning függvényeinek előnyeit is ki tudja használni, ami az adatstreamek elemzésének hatékony eszközévé teszi. [Tanulási térképünk](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) használatával elkezdheti felfedezni a Stream Analytics további részleteit. Ha szeretne többet megtudni arról, hogyan kell lekérdezéseket írni, olvassa el a [Gyakori lekérdezési minták](stream-analytics-stream-analytics-query-patterns.md) című cikket.

<!--HONumber=Oct16_HO3-->


