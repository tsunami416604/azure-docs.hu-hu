---
title: A távoli figyelési megoldás integrálása az Azure Data Lake Store |} A Microsoft Docs
description: Ismerje meg, hogyan integrálható a távoli figyelési megoldás az Azure Data Lake Store használatával az Azure Stream Analytics-feladat.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5ba9b5534e986be1cbe55043a9acdd981d2ed7fd
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971737"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>A távoli figyelési megoldás integrálása az Azure Data Lake Store

Előfordulhat, hogy a fejlett analitikai követelmények meghaladják a távoli figyelési megoldás érhető el. Az Azure Data Lake Store ideális ehhez az alkalmazáshoz, mert azt is nagy és számos különböző adatkészletekből származó adatok tárolása, valamint integrálása az Azure Data Lake Analytics igény szerinti elemzést biztosít.

Ebben az útmutatóban használhatjuk a Azure Stream Analytics-feladat adatok streamelése az IoT hubról a távoli figyelési megoldás, az Azure Data Lake Store.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához a következőkre lesz szüksége:

* [A távoli figyelési megoldásgyorsító üzembe helyezése](iot-accelerators-remote-monitoring-deploy.md).
  * A távoli figyelési megoldás telepíti az IoT hub és az Azure Stream Analytics-feladat az Azure-előfizetésben helyezi a cikk ezt használja.
* [Az Azure Data Lake Store üzembe helyezése](../data-lake-store/data-lake-store-get-started-portal.md)
  * A Data Lake Store és a távoli figyelési megoldás ugyanabban a régióban kell telepíteni.
  * [Hozzon létre egy mappát](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) nevű, "Stream" fiókjában található.

## <a name="create-a-consumer-group"></a>Hozzon létre egy fogyasztói csoportot

Hozzon létre egy dedikált fogyasztói csoportot, hogy a távoli figyelési megoldás az IoT hubban. Ez lesz használható a Stream Analytics-feladat által a Data Lake Store streamelési adatok.

> [!NOTE]
> Adatok lekérése az Azure IoT Hub fogyasztói csoportok alkalmazások használják. Minden öt kimeneti fogyasztók hozzunk létre egy új felhasználói csoportot. Legfeljebb 32 fogyasztói csoportot is létrehozhat.

1. Jelentkezzen be az Azure portálra.

1. Az Azure Portalon kattintson a **Cloud Shell** gombra.

    ![Portál indítása ikon](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Hajtsa végre ezt a parancsot egy új felhasználói csoport létrehozása:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Az erőforráscsoportot és az IoT hub nevét a távoli figyelési megoldást használja.

## <a name="create-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Az IoT hub az adatokat az Azure Data Lake store-adatfolyam egy Azure Stream Analytics-feladat létrehozása.

1. Kattintson a **erőforrás létrehozása**, válassza ki az eszközök internetes hálózatát a piactérről, és kattintson **Stream Analytics-feladat**.

    ![Új Stream Analytics-feladat](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Adja meg a feladat nevét, és válassza ki a megfelelő előfizetést és erőforráscsoportot.

1. Válasszon helyet a near vagy a Data Lake Store ugyanabban a régióban. Itt használjuk USA keleti RÉGIÓJA.

1. Győződjön meg arról, hogy az üzemeltetési környezet alapértelmezett **felhőalapú**.

1. Kattintson a **Create** (Létrehozás) gombra.

    ![Stream Analytics-feladat létrehozása](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>A Stream Analytics-feladat konfigurálása

1. Nyissa meg a **Stream Analytics-feladat** a távoli figyelési megoldás erőforráscsoportban.

1. Az Áttekintés oldalon kattintson a **bemenetek**.

    ![– Áttekintés oldalra](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kattintson a **streambemenet hozzáadása** válassza **az IoT Hub** a legördülő listából.

    ![Bemenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. A bemeneti új lapon adja meg a bemeneti alias **IoTHub**.

1. A fogyasztói csoport legördülő listában jelölje ki a korábban létrehozott fogyasztói csoportot. Itt **streamanalyticsjob**.

    ![Válassza ki a bemenet](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kattintson a **Save** (Mentés) gombra.

1. Az Áttekintés oldalon kattintson a **kimenetek**.

    ![Adja hozzá a Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kattintson a **Hozzáadás** válassza **Data Lake Store** a legördülő listából.

    ![Kimenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Az új kimeneti lapon adja meg a kimeneti alias **Data Lake store**.

1. Válassza ki a Data Lake Store-fiókot az előző lépésekben létrehozott, és adja meg a mappastruktúra az áruház adatok.

1. Adja meg a dátum formátuma mezőben **/streaming/ {date} / {idő}**. Hagyja az alapértelmezett dátum formátuma éééé/hh/nn- és időformátum ÓÓ.

    ![Adja meg a mappastruktúra](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kattintson a **engedélyezése**.

    A Stream analytics-feladat írási hozzáférést biztosíthat a fájlrendszer, a Data Lake Store engedélyezésére kell.

    ![Stream Analytics a Data Lake Store engedélyezése](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Megjelenik egy előugró ablak, és a helyi menü bezárása után engedélyezés gomb szürkén jelennek meg engedélyezés befejezését követően.

    > [!NOTE]
    > Ha az előugró ablakban hibaüzenet jelenik meg, nyisson meg egy új böngészőablakot, Inkognitó módban, és próbálkozzon újra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="edit-the-stream-analytics-query"></a>A Stream Analytics-lekérdezés szerkesztése

Az Azure Stream Analytics egy SQL-szerű lekérdezési nyelvet adja meg az adatokat streamelő bemeneti forrást, módosíthatja az adatokat a kívánt, és a kimeneti számos különböző tárolási és feldolgozási célhelyek, használ.

1. Kattintson az Áttekintés lapon **lekérdezés szerkesztése**.

    ![Lekérdezés szerkesztése](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. A lekérdezés-szerkesztőben cserélje le a [YourOutputAlias] és [YourInputAlias] helyőrzőt a korábban megadott értékeket.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics-lekérdezés](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kattintson a **Save** (Mentés) gombra.
1. Kattintson a **Igen** a módosítások elfogadásához.

## <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása

1. Kattintson az Áttekintés lapon **Start**.

    ![Stream Analytics-feladat indítása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Kattintson a Start feladat lap **egyéni**.

1. Térjen vissza néhány óra folytattuk a munkát, ha az eszköz megkezdődött streamelési adatok az egyéni idő beállítása.

1. Kattintson a **Start**.

    ![Egyéni dátum kiválasztása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Várjon, amíg a feladat hiányzóra futó állapotban, ha hibába ütközik, előfordulhat, hogy a lekérdezés, ügyeljen arra, hogy ellenőrizze, hogy a szintaxis helyes-e.

    ![Feladat futtatása](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    A folyamatos átviteli feladat adatokat olvasni az IoT Hub és az adatok tárolása a Data Lake Store megkezdődik. Eltarthat néhány percig, az adatok első lépéseként a Data Lake Store jelennek meg.

## <a name="explore-the-streaming-data"></a>A streamelési adatok megismerése

1. Nyissa meg a Data Lake Store.

1. Kattintson az Áttekintés lapon **adatkezelő**.

1. Az adatkezelőben lefúrással megjelenítheti a **/ streamelési** mappát. ÉÉÉÉ/HH/NN/HH formátum használatával létrehozott mappa megjelenik.

    ![Ismerje meg a Streamelési adatok](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Látni fogja a json-fájlok óránként egy fájllal.

    ![Ismerje meg a Streamelési adatok](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>További lépések

Az Azure Data Lake Analytics használható a Data Lake Store-adatkészleteken alapuló big Data típusú adatok elemzését. További információ a a [Data Lake Analytics-dokumentáció](https://docs.microsoft.com/azure/data-lake-analytics).
