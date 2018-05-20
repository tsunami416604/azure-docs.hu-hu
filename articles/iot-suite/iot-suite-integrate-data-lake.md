---
title: A távoli figyelésére szolgáló megoldás integrálása az Azure Data Lake Store |} Microsoft Docs
description: Ismerje meg, hogy a távoli figyelésére szolgáló megoldás integrálása az Azure Data Lake Store használatának az Azure Stream Analytics-feladat.
+services: ''
+suite: iot-suite
+author: philmea
+manager: timlt
+ms.author: philmea
+ms.date: 04/029/2018
+ms.topic: article
+ms.service: iot-suite
ms.openlocfilehash: 75ad9e9ac79dca4db902d7e72511a384dd769463
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>A távoli figyelésére szolgáló megoldás integrálása az Azure Data Lake Store

Előfordulhat, hogy a speciális elemzés követelmények túl mi van érhető el a távoli figyelésére szolgáló megoldás. Azure Data Lake Store ideális ehhez az alkalmazáshoz, mert azt is nagy és sokszínű adatkészletek tárolására, valamint az Azure Data Lake Analytics arra, hogy igény analytics integrálja.

Ez az útmutató a szüksége lesz egy Azure Stream Analytics-feladat adatfolyam adatokat az IoT hubból a távoli figyelésére szolgáló megoldás az Azure Data Lake Store.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató elvégzéséhez a következőkre lesz szüksége:

* [A távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md).
  * A távoli figyelésére szolgáló megoldás fogja központilag telepíteni, az IoT-központ és az a cikk ezt használja az Azure-előfizetéshez az Azure Stream Analytics-feladat.
* [Egy Azure Data Lake Store telepítése](/data-lake-store/data-lake-store-get-started-portal.md)
  * A Data Lake Store és a távoli figyelésére szolgáló megoldás ugyanabban a régióban kell elhelyezni.
  * [Hozzon létre egy mappát](/data-lake-store/data-lake-store-get-started-portal.md#createfolder) nevű, "streaming" a fiókját.

## <a name="create-a-consumer-group"></a>Egy felhasználói csoport létrehozása

A távoli figyelésére szolgáló megoldás az IoT-központ egy dedikált fogyasztói csoportot létrehozni. Ez által használható a Stream Analytics-feladat a Data Lake Store-adatfolyam-továbbítási adatok.

> [!NOTE]
> Alkalmazások az fogyasztói csoportok segítségével olvasnak be adatokat Azure IoT-központot. Minden öt kimeneti fogyasztók számára egy új fogyasztói csoportot kell létrehoznia. Akár 32 felhasználói csoportokat hozhat létre.

1. Jelentkezzen be az Azure portálra.

1. Az Azure portálon kattintson a **felhő rendszerhéj** gombra.

    ![Portál indítási ikon](media/iot-suite-integrate-data-lake/portal-launch-icon.png)

1. Hozzon létre egy új felhasználói csoportot a parancs végrehajtásához:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Az erőforráscsoport és az IoT hub nevek a távoli figyelésére szolgáló megoldás használja.

## <a name="create-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

Hozzon létre egy Azure Stream Analytics-feladat, hogy adatfolyamként küldje el az adatokat az Azure Data Lake store az IoT hub.

1. Kattintson a **hozzon létre egy erőforrást**, jelölje ki az eszközök internetes hálózatát a piactéren, és kattintson **Stream Analytics-feladat**.

    ![Új Stream Analytics-feladathoz.](media/iot-suite-integrate-data-lake/new-stream-analytics-job.png)

1. Adja meg a feladat nevét, és válassza ki a megfelelő előfizetésbe és erőforráscsoportba.

1. Válassza ki a helyet, a közelében vagy a Data Lake Store ugyanabban a régióban. Itt használjuk USA keleti régiója.

1. Győződjön meg arról, hogy az üzemeltetési környezetben alapértelmezett **felhő**.

1. Kattintson a **Create** (Létrehozás) gombra.

    ![A Stream Analytics-feladat létrehozása](media/iot-suite-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>A Stream Analytics-feladat konfigurálása

1. Lépjen a **Stream Analytics-feladat** a távoli figyelési megoldást erőforráscsoportban.

1. A áttekintése lapon kattintson a **bemenetek**.

    ![– Áttekintés oldalra](media/iot-suite-integrate-data-lake/stream-analytics-overview.png)

1. Kattintson a **adatfolyam-bemenet hozzáadása** válassza **IoT-központ** a legördülő lista a.

    ![Bemenet hozzáadása](media/iot-suite-integrate-data-lake/stream-analytics-add-input.png)

1. Az új bemeneti lapon adja meg a bemeneti alias **IOT hubbal**.

1. A felhasználói csoport legördülő menüben válassza ki a korábban létrehozott fogyasztói csoportot. Itt **streamanalyticsjob**.

    ![Válassza ki a bemenetben](media/iot-suite-integrate-data-lake/stream-analytics-new-input.png)

1. Kattintson a **Save** (Mentés) gombra.

1. A áttekintése lapon kattintson a **kimenetek**.

    ![Data Lake Store hozzáadása](media/iot-suite-integrate-data-lake/stream-analytics-overview-2.png)

1. Kattintson a **Hozzáadás** válassza **Data Lake Store** a legördülő lista a.

    ![Kimenet hozzáadása](media/iot-suite-integrate-data-lake/stream-analytics-output.png)

1. Az új kimeneti lapon adja meg a kimeneti alias **DataLakeStore**.

1. Válassza ki az előző lépésben létrehozott Data Lake Store-fiókot, és adja meg a gyökérmappa-szerkezetében adatfolyam az adatokat a tárolóban.

1. A dátum formátum mezőben adja meg a **/streaming/ {date} / {time}**. Hagyja az alapértelmezett dátum formátuma éééé/hh/nn és az időformátum ÓÓ.

    ![Adja meg a mappastruktúra](media/iot-suite-integrate-data-lake/stream-analytics-new-output.png)

1. Kattintson a **engedélyezik**.

    A Stream analytics-írási hozzáférést biztosítanak a fájlrendszer a Data Lake Store engedélyezni kell.

    ![A Stream Analytics Data Lake Store engedélyezése](media/iot-suite-integrate-data-lake/stream-analytics-out-authorize.png)

    Megjelenik egy előugró ablak, és az előugró ablak bezárása után Engedélyezés gombra fog kell szürke engedélyezés befejezését követően.

    > [!NOTE]
    > Ha az előugró ablakban hiba megtekintéséhez nyisson meg egy új böngészőablakot Incognito módban, és próbálkozzon újra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="edit-the-stream-analytics-query"></a>A Stream Analytics-lekérdezés szerkesztése

Az Azure Stream Analytics egy SQL-szerű lekérdező nyelv használatával adja meg a bemeneti forrása, amely az adatfolyamokat, adatok, a kívánt és tároló- és feldolgozási célok számos különböző kimeneti adatok.

1. Kattintson az Áttekintés lap **lekérdezés szerkesztése**.

    ![Lekérdezés szerkesztése](media/iot-suite-integrate-data-lake/stream-analytics-edit-query.png)

1. A lekérdezés-szerkesztő, cserélje le a [YourOutputAlias] és [YourInputAlias] helyőrzőt a korábban megadott értékeket.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics-lekérdezés](media/iot-suite-integrate-data-lake/stream-analytics-query.png)

1. Kattintson a **Save** (Mentés) gombra.
1. Kattintson a **Igen** a módosítások elfogadásához.

## <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása

1. Kattintson az Áttekintés lap **Start**.

    ![A Stream Analytics-feladat indítása](media/iot-suite-integrate-data-lake/stream-analytics-start.png)

1. Kattintson a Start feladat lap **egyéni**.

1. Térjen vissza néhány óra válassza ki, amikor az eszköz megkezdődött adatfolyam-adatainak mentése az egyéni idő beállítása.

1. Kattintson a **Start**.

    ![Válassza ki az egyéni dátuma](media/iot-suite-integrate-data-lake/stream-analytics-start-custom.png)

    Várjon, amíg a feladat futó állapotban, állapotba kerül, ha a lekérdezés lehet hibába ütközik, ellenőrizze, hogy a szintaxisának helyességét.

    ![Futó feladat](media/iot-suite-integrate-data-lake/stream-analytics-running.png)

    A folyamatos átviteli feladatok adatokat olvasni az IoT Hub és az adatok tárolása a Data Lake Store megkezdődik. Az adatok jelennek meg a Data Lake Store első lépéseként néhány percig is eltarthat.

## <a name="explore-the-streaming-data"></a>A streamelési adatok felfedezése

1. Nyissa meg a Data Lake Store-bA.

1. Kattintson az Áttekintés lap **adatkezelő**.

1. Az adatkezelő, nyissa meg annak a **/ streaming** mappát. ÉÉÉÉ/HH/NN/HH formátumú létrehozott mappák jelenik meg.

    ![Adatfolyam-továbbítási adatokba](media/iot-suite-integrate-data-lake/data-lake-store-data-explorer.png)

    Látni fogja a json-fájlok óránként egy fájllal.

    ![Adatfolyam-továbbítási adatokba](media/iot-suite-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>További lépések

Az Azure Data Lake Analytics segítségével a Data Lake Store adatkészletek big data elemzést. A további a [Data Lake Analytics-dokumentáció](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
