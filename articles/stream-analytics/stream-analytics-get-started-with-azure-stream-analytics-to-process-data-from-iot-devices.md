<properties
    pageTitle="Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását. | Stream Analytics"
    description="IoT-érzékelőcímkék és -adatfolyamok streamelemzéssel és valós idejű adatfeldolgozással"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"
/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="05/03/2016"
    ms.author="jeffstok"
/>

# Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását

Ez az oktatóanyag bemutatja, hogyan hozhat létre streamfeldolgozó logikákat, és hogyan gyűjthet velük adatokat az eszközök internetes hálózatára kapcsolódó (IoT-) eszközökről. Egy valódi, az IoT-re épülő gyakorlati esettel fogjuk bemutatni, hogyan hozhat létre egy megoldást gyorsan és gazdaságosan.

## Előfeltételek

-   [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)
-   A mintalékérdezés és a mintaadatfájlok letölthetők a [GitHubból](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted)

## Forgatókönyv

A Contoso egy gyártóvállalat cég az ipari automatizálás területén, amely teljesen automatizálta a gyártási folyamatait. A gyár gépeinek érzékelői valós idejű adatstreameket hoznak létre. Ebben a forgatókönyvben a termelési szint egyik igazgatója valós idejű elemzéseket szeretne kapni az érzékelők adataiból, hogy mintákat keressen bennük, és ezek alapján tegyen további lépéseket. Az érzékelők adatain a SAQL nyelvet (Stream Analytics Query Language) használjuk, hogy érdekes szabályszerűségeket keressünk a beérkező streamben.

Itt az adatokat egy Texas Instrument Sensor Tag eszköz állítja elő.

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

A hasznos adatok JSON formátumban vannak, és a következőképpen néznek ki:

    
    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  
    
A valós forgatókönyvekben több száz ilyen érzékelő állít elő eseményeket streamként. Ideális esetben van egy átjáróeszköz, amely egy bizonyos kód futtatásával elküldi ezeket az eseményeket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) részére. A Stream Analytics-feladatok felhasználják az Event Hubs eseményeit, és lekérdezésekként kifejezett valós idejű elemzéseket futtatnak, majd az eredményeket elküldik a kívánt kimenetekre.

Ebben a Kezdeti lépések útmutatóban valódi SensorTag eszközökről származó mintaadatfájlokat adtunk meg, amelyeken különböző lekérdezéseket futtathat, és megtekintheti az eredményeiket. A következő útmutatókból megtudhatja, hogyan csatlakoztathatja a feladatait bemenetekhez és kimenetekhez, és hogyan helyezheti üzembe őket az Azure szolgáltatásban.

## Stream Analytics-feladat létrehozása

Nyissa meg az [Azure portálon](http://manage.windowsazure.com) a Stream Analytics elemet, és a lap bal alsó sarkában található **„Új”** gombra kattintva hozzon létre egy új elemzési feladatot.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Kattintson a „**Gyors létrehozás**” gombra.

A **„Regional Monitoring Storage Account”** (Régió szerinti megfigyelési tárfiók) beállításnál válassza az **Új tárfiók létrehozása** lehetőséget, és adjon neki egy egyedi nevet. Az Azure Stream Analytics ezt a fiókot fogja használni a jövőbeli feladataiból származó megfigyelési információk tárolására.

> [AZURE.NOTE] Régiónként csak egyszer hozza létre ezt a tárfiókot, és ez a tároló az adott régióban létrehozott összes Stream Analytics-feladat között meg lesz osztva.

Kattintson az oldal alján lévő „**Stream Analytics-feladat létrehozása**” elemre.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure Stream Analytics-lekérdezés

A Lekérdezés fülre kattintva lépjen a Lekérdezésszerkesztőhöz. A Lekérdezés lap egy olyan SQL-lekérdezést tartalmaz, amely végrehajtja az átalakítást a beérkező adatokon.

## Nyers adatok archiválása

A lekérdezések legegyszerűbb formája az áthaladás, amely a kijelölt kimeneten archiválja az összes bemeneti adatot.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Most töltse le a számítógépére a mintaadatfájlokat a [GitHubból](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted). Másolja és illessze be a lekérdezést a **PassThrough.txt** fájlból. Kattintson az alsó Teszt gombra, és válassza ki a letöltés helyéről a **HelloWorldASA-InputStream.json** nevű fájlt.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

A lekérdezés eredményeit az alábbi tallózóban láthatja.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Adatok tisztítása egy feltétel alapján

Próbáljuk meg egy feltétel alapján szűrni az eredményeket. Azt szeretnénk, ha az eredmények között csak a „SensorA” érzékelőből érkező események jelenjenek meg. A lekérdezés a **Filtering.txt** fájlban található.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Vegye figyelembe, hogy jelen esetben egy karakterláncértéket hasonlítunk össze, amely megkülönbözteti a kis- és nagybetűket. A lekérdezés végrehajtásához kattintson az **Ismétlés** gombra. A lekérdezésnek csak 389 sort kell visszaadnia az 1860 eseményből.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Riasztás az üzleti munkafolyamat elindítására

Most tegyük egy kicsit érdekesebbé a lekérdezést. Ha az átlaghőmérsékletet 30 másodperces időközönként szeretnénk mérni, és csak akkor szeretnénk eredményeket megjeleníteni, ha az átlaghőmérséklet meghaladja a 100 fokot, akkor minden érzékelőtípus esetében leírjuk az alábbi lekérdezést, majd az Ismétlés gombra kattintva megjelenítjük az eredményeket. A lekérdezés a **ThresholdAlerting.txt** fájlban található.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Látható, hogy az eredmények most csak 245 sorban jelenítik meg azokat az érzékelőket, amelyeknél az átlaghőmérséklet nagyobb, mint 100 fok. Ebben a lekérdezésben az események streamjét a dspl szerint csoportosítottuk, ami az érzékelő neve, az **Átfedésmentes ablak** pedig 30 másodperc. Az ilyen jellegű időalapú lekérdezéseknél esetében elengedhetetlen az időköz megadása. A **TIMESTAMP BY** záradékkal az „idő” oszlopot adtuk meg, amely minden időalapú számításhoz megadja az időközt. Részletes információkat az [időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és az [ablakkezelést](https://msdn.microsoft.com/library/azure/dn835019.aspx) ismertető MSDN-témakörökben talál.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Szabályszerűségek hiányának keresése

Hogyan írhatok olyan lekérdezést, amely a szabályszerűségek hiányát keresi? Például keressük meg a legutóbbi esetet, amikor az érzékelő adatokat küldött, majd a következő egy percben egyáltalán nem küldött ki eseményt. A lekérdezés az **AbsenseOfEvent.txt** fájlban található.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Itt egy **BAL OLDALI KÜLSŐ ILLESZTÉST** alkalmazunk ugyanarra az adatstreamre (önillesztés). Belső illesztés esetén csak akkor kapunk eredményt, ha van egyezés.  **BAL OLDALI KÜLSŐ** illesztés esetében azonban, ha az illesztés bal oldalán lévő eseményhez nincs egyezés, akkor a rendszer olyan oszlopot ad vissza, amely a jobb oldali sor összes oszlopában NULL értéket tartalmaz. Ez a módszer nagyon hasznos, ha események hiányára kíván rákeresni. Az [ILLESZTÉS](https://msdn.microsoft.com/library/azure/dn835026.aspx) részleteit az MSDN-dokumentációnkban találja.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Összegzés

Ez az oktatóprogram alapvetően bevezetést nyújt azokba az ismeretekbe, amelyek segítségével különböző SAQL-lekérdezéseket írhat, majd az eredményeket az adatok különböző szabályszerűségei alapján tekintheti meg a böngészőben. Ezek azonban csak az első lépések. A Stream Analytics rengeteg lehetőséget rejt még magában. A következőkben megtudhatja, hogyan csatlakoztathatja a Stream Analytics-feladatot bemenetekhez és kimenetekhez, és hogyan helyezheti üzembe az Azure szolgáltatásban. A [Tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) útmutatónk segítségével részletesebben megismerheti a Stream Analyticset, a lekérdezések írásáról pedig további információkat olvashat a [Gyakori lekérdezési minták](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events) című cikkben.



<!--HONumber=Jun16_HO2-->


