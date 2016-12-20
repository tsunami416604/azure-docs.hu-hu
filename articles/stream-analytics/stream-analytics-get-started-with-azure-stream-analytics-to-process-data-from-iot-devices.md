---
title: "Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását. | Microsoft Docs"
description: "IoT-érzékelőcímkék és -adatfolyamok streamelemzéssel és valós idejű adatfeldolgozással"
keywords: "iot-megoldás, bevezetés az iot használatába"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 3e829055-75ed-469f-91f5-f0dc95046bdb
ms.service: stream-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/19/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b4a725d55594885a5d9d4a62b4bf5fe6fd402849


---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Ismerje meg az Azure Stream Analytics használ azát és az IoT-eszközökről származó ad azok feldolgozását
Ez az oktatóanyag bemutatja, hogyan hozhat létre streamfeldolgozó logikákat, és hogyan gyűjthet velük adatokat az eszközök internetes hálózatára kapcsolódó (IoT-) eszközökről. Egy valódi, az IoT-re épülő gyakorlati esettel fogjuk bemutatni, hogyan hozhat létre egy megoldást gyorsan és gazdaságosan.

## <a name="prerequisites"></a>Előfeltételek
* [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)
* A mintalékérdezés és a mintaadatfájlok letölthetők a [GitHubból](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Forgatókönyv
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

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
1. Az [Azure Portalban](http://portal.azure.com) kattintson a plusz jelre, majd írja be **STREAM ANALYTICS** a jobb oldalon lévő szövegablakba. Ezután válassza ki **Stream Analytics-feladat** az eredménylistában.
   
    ![Új Stream Analytics-feladat létrehozása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Adjon meg egy egyedi feladat nevet, és ellenőrizze, hogy az előfizetés megfelelő a feladatához. Ezután hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt az előfizetésén.
3. Ezután válassza ki a feladat helyét. A feldolgozási sebesség és az adatátviteli költségek csökkentése érekében ajánlott ugyanazt a helyet választani, ahol az erőforráscsoport és a tervezett tárfiók van.
   
    ![Új Stream Analytics-feladat létrehozásának részletei](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > Régiónként csak egyszer hozza létre ezt a tárfiókot. Ez a tároló az adott régióban létrehozott összes Stream Analytics-feladat között meg lesz osztva.
   > 
   > 
4. Jelölje be a négyzetet a feladat irányítópultra való helyezéséhez, majd kattintson a **LÉTREHOZÁS** elemre.
   
    ![feladat létrehozása folyamatban](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. „Központi telepítés elindítva...” üzenetet kell látnia a böngészőablak jobb felső sarkában. Hamarosan a befejezést jelző ablakra vált, az alábbiakban látható módon.
   
    ![feladat létrehozása folyamatban](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Azure Stream Analytics-lekérdezés létrehozása
A feladat létrehozása után itt az idő, hogy megnyissa és készítsen egy lekérdezést. A feladatához könnyedén hozzáférhet, ha a csempéjére kattint.

![Feladat csempe](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

A **Feladattopológia** panelben kattintson a **LEKÉRDEZÉS** dobozra, a Lekérdezés szerkesztő megnyitásához. A **LEKÉRDEZÉS** szerkesztőben egy olyan T-SQL-lekérdezést adhat meg, amely végrehajtja az átalakítást a beérkező eseményadatokon.

![Lekérdezésmező](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Lekérdezés: Nyers adatok archiválása
A lekérdezések legegyszerűbb formája a továbbított lekérdezés, amely a kijelölt kimeneten archiválja az összes bemeneti adatot. Most töltse le a számítógépére a mintaadatfájlokat a [GitHubból](https://aka.ms/azure-stream-analytics-get-started-iot). 

1. Illessze be a lekérdezést a PassThrough.txt fájlból. 
   
    ![Teszt bemeneti stream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Kattintson a három pontra az adatbevitele mellett, majd válassza ki a **Mintaadatok feltöltése** fájlból mezőt.
   
    ![Teszt bemeneti stream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Ennek eredményeképp egy panel nyílik meg, benne válassza ki a HelloWorldASA-InputStream.json adatfájlt a letöltött helyről és kattintson az **OK** gombra a panel alján.
   
    ![Teszt bemeneti stream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Ezután kattintson a **Teszt** fogaskerékre az ablak bal felső területén, majd dolgozza fel a teszt lekérdezést a minta adathalmazokon. A feldolgozás befejezése után egy eredmények ablak fog megnyílni a lekérdezés alatt.
   
    ![Teszteredmények](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Lekérdezés: Adatok szűrése feltétel alapján
Próbáljuk meg egy feltétel alapján szűrni az eredményeket. Azt szeretnénk, hogy az eredmények között csak a „sensorA” érzékelőből érkező események jelenjenek meg. A lekérdezés a Filtering.txt fájlban található.

![Adatstream szűrése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Vegye figyelembe, hogy a kis- és nagybetűket megkülönböztető lekérdezés karakterlánc típusú értékkel végzi az összehasonlítást. Kattintson újra a **Teszt** fogaskerékre a lekérdezés végrehajtásához. A lekérdezésnek 389 sort kell visszaadnia az 1860 eseményből.

![Második kimeneti eredmény a lekérdezéstesztelésből](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Lekérdezés: Riasztás az üzleti munkafolyamat elindítására
Tegyük részletesebbé a lekérdezést. Az átlaghőmérsékletet minden érzékelőtípus esetében 30 másodperces időközönként szeretnénk mérni, és csak akkor szeretnénk eredményeket megjeleníteni, ha az átlaghőmérséklet meghaladja a 100 fokot. Megírjuk az alábbi lekérdezést, majd az **Teszt** gombra kattintva megjelenítjük az eredményeket. A lekérdezés a ThresholdAlerting.txt fájlban található.

![30 másodperces szűrőlekérdezés](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Látható, hogy az eredmények most csak 245 sorban jelenítik meg azoknak az érzékelőknek a nevét, amelyeknél az átlaghőmérséklet nagyobb 100 foknál. Ez a lekérdezés az események streamjét a **dspl** szerint csoportosítja, ami az érzékelő neve, az **Átfedésmentes ablak** pedig 30 másodperc. A historikus lekérdezések esetében elengedhetetlen az időköz megadása. A **TIMESTAMP BY** záradékkal az **OUTPUTTIME** oszlopot adtuk meg, amely minden historikus számításhoz társít egy időközt. Részletes információkat az [időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és az [ablakkezelési függvényeket](https://msdn.microsoft.com/library/azure/dn835019.aspx) ismertető MSDN-témakörökben talál.

### <a name="query-detect-absence-of-events"></a>Lekérdezés: Események hiányának észlelése
Hogyan írható olyan lekérdezés, amely a bemeneti események hiányát keresi? Keressük meg a legutóbbi esetet, amikor az érzékelő adatokat küldött, majd a következő percben nem küldött eseményeket. A lekérdezés a AbsenseOfEvent.txt fájlban található.

![Események hiányának észlelése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Itt egy **BAL OLDALI KÜLSŐ** illesztést alkalmazunk ugyanarra az adatstreamre (önillesztés). **BELSŐ** illesztés esetén csak akkor kapunk eredményt, ha van egyezés.  **BAL OLDALI KÜLSŐ** illesztés esetében azonban, ha az illesztés bal oldalán lévő eseményhez nincs egyezés, akkor a rendszer olyan oszlopot ad vissza, amely a jobb oldal összes oszlopában NULL értéket tartalmaz. Ez a módszer nagyon hasznos, ha események hiányára kíván rákeresni. Az [ILLESZTÉS](https://msdn.microsoft.com/library/azure/dn835026.aspx) részleteit az MSDN-dokumentációnkban találja.

## <a name="conclusion"></a>Összegzés
A jelen oktatóanyag célja annak bemutatása, hogyan lehet Stream Analytics lekérdezési nyelven különböző lekérdezéseket írni, és az eredményeket megjeleníteni a képernyőn. Ezek azonban csak az első lépések. A Stream Analytics rengeteg lehetőséget rejt még magában. A Stream Analytics számos bemenetet és kimenetet támogat, és még az Azure Machine Learning függvényeinek előnyeit is ki tudja használni, ami az adatstreamek elemzésének hatékony eszközévé teszi. [Tanulási térképünk](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) használatával elkezdheti felfedezni a Stream Analytics további részleteit. Ha szeretne többet megtudni arról, hogyan kell lekérdezéseket írni, olvassa el a [Gyakori lekérdezési minták](stream-analytics-stream-analytics-query-patterns.md) című cikket.




<!--HONumber=Dec16_HO1-->


