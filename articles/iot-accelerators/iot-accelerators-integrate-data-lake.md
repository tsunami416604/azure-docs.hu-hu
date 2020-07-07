---
title: Stream-adatok távoli figyelésből Data Lake Storeba – Azure | Microsoft Docs
description: Megtudhatja, hogyan integrálhatja a távoli figyelési megoldást Azure Data Lake Store használatával egy Azure Stream Analytics feladatokkal.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73889238"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>A távoli figyelési megoldás integrálása a Azure Data Lake Store

A távoli figyelési megoldásban elérhető speciális elemzési követelmények is megjelenhetnek. A Azure Data Lake Store ideális ehhez az alkalmazáshoz, mert a nagy és változatos adatkészletekből származó adatok tárolására, valamint az igény szerinti elemzések biztosításához Azure Data Lake Analytics integrálására is alkalmas.

Ebben az útmutatóban egy Azure Stream Analytics feladatot fog használni a távoli figyelési megoldás IoT hub adatainak egy Azure Data Lake Storeba való továbbításához.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez a következőkre lesz szüksége:

* [Telepítse a távoli figyelési megoldás gyorsító](quickstart-remote-monitoring-deploy.md).
  * A távoli figyelési megoldás üzembe helyezi az IoT hub-t és a cikkben használt Azure Stream Analytics feladatot az Azure-előfizetésében.
* [Azure Data Lake Store üzembe helyezése](../data-lake-store/data-lake-store-get-started-portal.md)
  * A Data Lake Store a távoli figyelési megoldással megegyező régióba kell telepíteni.
  * [Hozzon létre egy](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) "streaming" nevű mappát a fiókjában.

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Hozzon létre egy dedikált fogyasztói csoportot a távoli figyelési megoldás IoT hub-ban. Ezt a Stream Analytics feladata fogja használni az adatoknak a Data Lake Storeba való továbbításához.

> [!NOTE]
> A fogyasztói csoportokat az alkalmazások használják az Azure-IoT Hub adatainak lekérésére. Minden öt kimeneti felhasználóhoz létre kell hoznia egy új fogyasztói csoportot. Akár 32 fogyasztói csoportot is létrehozhat.

1. Jelentkezzen be az Azure portálra.

1. A Azure Portal kattintson a **Cloud Shell** gombra.

    ![Portál indítási ikonja](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Futtassa ezt a parancsot egy új fogyasztói csoport létrehozásához:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Használja az erőforráscsoportot és a IoT hub-neveket a távoli figyelési megoldásból.

## <a name="create-stream-analytics-job"></a>Stream Analytics-feladatok létrehozása

Hozzon létre egy Azure Stream Analytics feladatot az IoT hub adatainak a Azure Data Lake áruházba való továbbításához.

1. Kattintson az **erőforrás létrehozása**elemre, válassza ki eszközök internetes hálózata a piactéren, és kattintson **stream Analytics feladatokra**.

    ![Új Stream Analytics-feladatok](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Adja meg a feladatnév nevét, és válassza ki a megfelelő előfizetést és erőforráscsoportot.

1. Válasszon egy helyet a közeli vagy a régióban, ahol a Data Lake Store található. Itt az USA keleti régióját használjuk.

1. Győződjön meg arról, hogy az üzemeltetési környezetet az alapértelmezett **felhőként**hagyja.

1. Kattintson a **Létrehozás** lehetőségre.

    ![Stream Analytics-feladatok létrehozása](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>A Stream Analytics-feladatok konfigurálása

1. Nyissa meg a **stream Analytics feladatot** a távoli figyelési megoldás erőforráscsoporthoz.

1. Az Áttekintés lapon kattintson a **bemenetek**elemre.

    ![Áttekintő lap](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kattintson a **stream-bemenet hozzáadása** elemre, és válassza a **IoT hub** elemet a legördülő menüből.

    ![Bemenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Az új bemenet lapon adja meg a **IoTHub**bemeneti aliasát.

1. A fogyasztói csoport legördülő menüben válassza ki a korábban létrehozott fogyasztói csoportot. Itt használjuk a **streamanalyticsjob**-t.

    ![Bemenet kiválasztása](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kattintson a **Save** (Mentés) gombra.

1. Az Áttekintés lapon kattintson a **kimenetek**elemre.

    ![Data Lake Store hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kattintson a **Hozzáadás** gombra, és válassza ki a **Data Lake Store** elemet a legördülő menüből.

    ![Kimenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Az új kimenet lapon adja meg a **Data Lake Store**kimeneti aliasát.

1. Válassza ki az előző lépésekben létrehozott Data Lake Store fiókot, és adjon meg egy mappastruktúrát az adattárolóba való továbbításhoz.

1. A Dátumformátum mezőben adja meg a **/Streaming/{DATE}/{Time}**. Hagyja meg az alapértelmezett dátumformátum (éééé/hh/nn) és a HH időformátumát.

    ![Mappa szerkezetének megadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kattintson az **Engedélyezés**gombra.

    Engedélyeznie kell a Data Lake Store, hogy a stream Analytics-feladat írási hozzáférést nyújtson a fájlrendszerhez.

    ![Data Lake Store Stream Analytics engedélyezése](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Ekkor megjelenik egy előugró ablak, és ha az előugró ablak bezárása engedélyezés gomb szürkén jelenik meg az engedélyezés befejeződése után.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az előugró ablakban, nyisson meg egy új böngészőablakot az inkognitóban módban, és próbálkozzon újra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="edit-the-stream-analytics-query"></a>A Stream Analytics lekérdezés szerkesztése

A Azure Stream Analytics egy SQL-szerű lekérdezési nyelvet használ egy olyan bemeneti forrás megadására, amely adatokat küld, átalakítja a kívánt adatokat, és a kimenetet számos különböző tárolási vagy feldolgozási célhelyre.

1. Az Áttekintés lapon kattintson a **lekérdezés szerkesztése**elemre.

    ![Lekérdezés szerkesztése](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. A lekérdezés-szerkesztőben cserélje le a [YourOutputAlias] és a [YourInputAlias] helyőrzőket a korábban definiált értékekre.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics lekérdezés](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kattintson a **Save** (Mentés) gombra.
1. A módosítások elfogadásához kattintson az **Igen** gombra.

## <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladatok elindítása

1. Az Áttekintés lapon kattintson a **Start**gombra.

    ![Stream Analytics feladatok indítása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. A kezdési feladatok lapon kattintson az **Egyéni**elemre.

1. Állítsa be az egyéni időpontot, hogy néhány óra elteltével felvegye az adatait, amikor az eszköz elindította az adatfolyamot.

1. Kattintson a **Start**gombra.

    ![Egyéni dátum kiválasztása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Várjon, amíg a feladatok futása befejeződik, ha olyan hibákat lát, amelyek a lekérdezésből származnak, ellenőrizze, hogy helyes-e a szintaxis.

    ![Futó művelet](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    A folyamatos átviteli feladatok megkezdik az adatok beolvasását a IoT Hubból, és az adatok tárolása a Data Lake Store. Eltarthat néhány percig, amíg az adatai megjelennek a Data Lake Store.

## <a name="explore-the-streaming-data"></a>Ismerkedés a folyamatos átvitelsel

1. Lépjen a Data Lake Store.

1. Az Áttekintés lapon kattintson az **adatkezelő**elemre.

1. Az adatkezelőben bontsa ki a **/streaming** mappát. Ekkor az ÉÉÉÉ/HH/NN/HH formátumban létrehozott mappák láthatók.

    ![Az adatfolyamok megismerése](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    A JSON-fájlok óránként egy fájllal lesznek láthatók.

    ![Az adatfolyamok megismerése](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Következő lépések

A Azure Data Lake Analytics big data elemzések elvégzésére használható a Data Lake Store adatkészletekben. További információt a [Data Lake Analytics dokumentációjában talál](https://docs.microsoft.com/azure/data-lake-analytics).
