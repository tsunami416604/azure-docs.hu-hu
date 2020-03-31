---
title: Valós idejű IoT-adatfolyamok feldolgozása az Azure Stream Analytics segítségével
description: IoT-érzékelőcímkék és -adatfolyamok streamelemzéssel és valós idejű adatfeldolgozással
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426251"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Valós idejű IoT-adatfolyamok feldolgozása az Azure Stream Analytics segítségével

Ebben a cikkben megtudhatja, hogyan hozhat létre adatfolyam-feldolgozási logikát az eszközök internetéről (IoT) eszközökről történő adatok gyűjtéséhez. A valós dolgok internete (IoT) használati esethasználatával bemutathatja, hogyan építheti fel gyorsan és gazdaságosan a megoldást.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy ingyenes [Azure-előfizetést.](https://azure.microsoft.com/pricing/free-trial/)
* Töltsön le mintalekérdezést és adatfájlokat a [GitHubról.](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Forgatókönyv

A Contoso egy vállalat az ipari automatizálás területén, amely teljesen automatizálta a gyártási folyamatait. A gyár gépeinek érzékelői valós idejű adatstreamek létrehozására képesek. Ebben a forgatókönyvben a termelési szint egyik igazgatója valós idejű elemzéseket szeretne kapni az érzékelők adataiból, hogy mintákat keressen bennük, és ezek alapján tegyen további lépéseket. A Stream Analytics lekérdezési nyelv (SAQL) segítségével az érzékelő adatok érdekes mintákat kereshet a bejövő adatfolyamból.

Ebben a példában az adatok egy Texas Instruments érzékelőcímke-eszközből jönnek létre. A hasznos adatok JSON formátumban vannak, és a következőképpen néznek ki:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

A valós forgatókönyvekben több száz ilyen érzékelő állíthat elő eseményeket streamként. Ideális esetben van egy átjáróeszköz, amely egy bizonyos kód futtatásával elküldi ezeket az eseményeket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy az [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) részére. A Stream Analytics-feladatok felhasználják az Event Hubs eseményeit, és a streameken valós idejű lekérdezéseket futtatnak. Ezután elküldheti az eredményeket az egyik [támogatott kimenetre.](stream-analytics-define-outputs.md)

A használat megkönnyítése érdekében ez a Kezdeti lépések útmutató valódi SensorTag eszközökről származó mintaadatfájlokat biztosít. Ezeken lekérdezéseket futtathat, és megtekintheti az eredményeiket. A következő útmutatókból megtudhatja, hogyan csatlakoztathatja a feladatait bemenetekhez és kimenetekhez, és hogyan helyezheti üzembe őket az Azure szolgáltatásban.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza **a + Erőforrás létrehozása** a bal oldali navigációs menüben. Ezután válassza a **Stream Analytics-feladatot** az **Analytics**alkalmazásban.
   
    ![Új Stream Analytics-feladat létrehozása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Adjon meg egy egyedi feladat nevet, és ellenőrizze, hogy az előfizetés megfelelő a feladatához. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt az előfizetéséből.

1. Válassza ki a feladat helyét. Használja ugyanazt a helyet az erőforráscsoporthoz és az összes erőforráshoz a nagyobb feldolgozási sebesség és a költségek csökkentése érdekében. A konfigurációk elvégzése után válassza a **Létrehozás lehetőséget.**
   
    ![Új Stream Analytics-feladat létrehozásának részletei](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Stream Analytics-lekérdezés létrehozása
A feladat létrehozása utáni következő lépés egy lekérdezés írása. A lekérdezéseket a mintaadatok alapján tesztelheti anélkül, hogy egy bemenetet vagy kimenetet a feladathoz csatlakoztatna.

Töltse le a [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) a GitHub. Ezután keresse meg az Azure Stream Analytics-feladatot az Azure Portalon.

Válassza a bal oldali menü **Feladattopológiája** csoport **Lekérdezés** parancsát. Ezután válassza **a Mintabevitel feltöltése**lehetőséget. Töltse `HelloWorldASA-InputStream.json` fel a fájlt, és válassza **az Ok gombot.**

![A Stream Analytics irányítópultjának lekérdezési csempéje](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Figyelje meg, hogy az adatok előnézete automatikusan kitöltődik a **Bemeneti előnézeti** táblában.

![A mintabemeneti adatok előnézete](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Lekérdezés: Nyers adatok archiválása

A lekérdezések legegyszerűbb formája a továbbított lekérdezés, amely a kijelölt kimeneten archiválja az összes bemeneti adatot. Ez a lekérdezés az alapértelmezett lekérdezés egy új Azure Stream Analytics-feladatban feltöltve.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Válassza **a Lekérdezés tesztelése** lehetőséget, és tekintse meg az eredményeket a **Teszteredmények** táblában.

![A Stream Analytics-lekérdezés teszteredményei](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Lekérdezés: Adatok szűrése feltétel alapján

Próbáljuk meg szűrni az eredményeket egy feltétel alapján. Csak az "A" érzékelőből származó események eredményeit szeretnénk megjeleníteni.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Illessze be a lekérdezést a szerkesztőbe, és válassza a **Lekérdezés tesztelése** lehetőséget az eredmények áttekintéséhez.

![Adatstream szűrése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Lekérdezés: Riasztás az üzleti munkafolyamat elindítására

Tegyük részletesebbé a lekérdezést. Az átlaghőmérsékletet minden érzékelőtípus esetében 30 másodperces időközönként szeretnénk mérni, és csak akkor szeretnénk eredményeket megjeleníteni, ha az átlaghőmérséklet meghaladja a 100 fokot.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 másodperces szűrőlekérdezés](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Olyan eredményeket kell látnia, amelyek csak 245 sort és olyan érzékelők nevét tartalmazzák, ahol az átlagos mérsékelt érték nagyobb, mint 100. Ez a lekérdezés az események streamjét a **dspl** szerint csoportosítja, ami az érzékelő neve, az **Átfedésmentes ablak** pedig 30 másodperc. Az időbeli lekérdezéseknek meg kell adniuk, hogyan szeretné, hogy az idő haladjon. A **TIMESTAMP BY** záradék használatával megadta a **OUTPUTTIME** oszlopot az összes időszámításhoz való társításához. Részletes információt az [Időgazdálkodás](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) és [ablakkezelő függvényekről](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)talál.

### <a name="query-detect-absence-of-events"></a>Lekérdezés: Események hiányának észlelése

Hogyan írható olyan lekérdezés, amely a bemeneti események hiányát keresi? Keressük meg az utolsó alkalommal, amikor egy érzékelő adatokat küldött, majd nem küldött eseményeket a következő 5 másodpercben.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Események hiányának észlelése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Itt egy **BAL OLDALI KÜLSŐ** illesztést alkalmazunk ugyanarra az adatstreamre (önillesztés). **BELSŐ** illesztés esetén csak akkor kapunk eredményt, ha van egyezés.  **BAL OLDALI KÜLSŐ** illesztés esetében azonban, ha az illesztés bal oldalán lévő eseményhez nincs egyezés, akkor a rendszer olyan oszlopot ad vissza, amely a jobb oldal összes oszlopában NULL értéket tartalmaz. Ez a módszer nagyon hasznos, ha események hiányára kíván rákeresni. További információ: [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Összegzés

A cikk célja, hogy bemutassa, hogyan írhat különböző Stream Analytics-lekérdezési nyelvi lekérdezéseket, és hogyan láthatja az eredményeket a böngészőben. Azonban ez csak a kezdéshez. A Stream Analytics számos bemenetet és kimenetet támogat, és még az Azure Machine Learning függvényeinek előnyeit is ki tudja használni, ami az adatstreamek elemzésének hatékony eszközévé teszi. Ha szeretne többet megtudni arról, hogyan kell lekérdezéseket írni, olvassa el a [Gyakori lekérdezési minták](stream-analytics-stream-analytics-query-patterns.md) című cikket.

