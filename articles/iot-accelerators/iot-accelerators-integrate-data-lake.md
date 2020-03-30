---
title: Adatok streamelése a távoli figyelésről a Data Lake Store-ba - Azure | Microsoft dokumentumok
description: Ismerje meg, hogyan integrálhatja a távfigyelési megoldást az Azure Data Lake Store-ral egy Azure Stream Analytics-feladat használatával.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889238"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrálja a távfigyelési megoldást az Azure Data Lake Store-ral

Előfordulhat, hogy a távoli figyelési megoldásban kínált követelményeken túl speciális elemzési követelményekkel rendelkezik. Az Azure Data Lake Store ideális ehhez az alkalmazáshoz, mivel képes tárolni a nagy méretű és változatos adatkészletek adatait, valamint integrálni az Azure Data Lake Analytics szolgáltatással az igény szerinti elemzéshez.

Ebben a útmutatóban egy Azure Stream Analytics-feladatot fog használni az IoT hubról a távoli figyelési megoldásban egy Azure Data Lake Store-ba történő streameléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következőkre lesz szüksége:

* [Telepítse a távfigyelési megoldás gyorsítóját.](quickstart-remote-monitoring-deploy.md)
  * A távoli figyelési megoldás üzembe helyezi az IoT hubot és az Azure Stream Analytics-feladatot ebben a cikkben használt Azure-előfizetésében.
* [Azure Data Lake Áruház üzembe helyezése](../data-lake-store/data-lake-store-get-started-portal.md)
  * A Data Lake Store-t ugyanabban a régióban kell telepíteni, mint a távoli figyelési megoldás.
  * [Hozzon létre egy](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) "streaming" nevű mappát a fiókjában.

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Hozzon létre egy dedikált fogyasztói csoportot a távoli figyelési megoldás IoT-hubján. Ezt a Stream Analytics feladat fogja használni az adatok adattovábbításhoz a Data Lake Store-ba.

> [!NOTE]
> Fogyasztói csoportok az alkalmazások segítségével adatokat az Azure IoT Hub. Hozzon létre egy új fogyasztói csoportot minden öt kimeneti fogyasztó számára. Legfeljebb 32 fogyasztói csoportot hozhat létre.

1. Jelentkezzen be az Azure portálra.

1. Az Azure Portalon kattintson a **Cloud Shell** gombra.

    ![Portál indítása ikon](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. A parancs végrehajtása új fogyasztói csoport létrehozásához:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Használja az erőforráscsoport és az IoT hub nevét a távoli figyelési megoldásból.

## <a name="create-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Hozzon létre egy Azure Stream Analytics-feladatot az IoT-központból az Azure Data Lake áruházba való adatfolyamként.

1. Kattintson **az Erőforrás létrehozása**elemre, válassza a Dolgok internete lehetőséget a Piactéren, és kattintson a Stream **Analytics-feladat elemre.**

    ![Új Stream Analytics feladat](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Adja meg a feladat nevét, és válassza ki a megfelelő Előfizetés és erőforrás csoportot.

1. Válasszon ki egy helyet a közeli vagy a Data Lake Store-val azonos régióban. Itt használjuk az USA keleti részét.

1. Győződjön meg arról, hogy az üzemeltetési környezet et alapértelmezett **felhőként**hagyja el.

1. Kattintson **a Létrehozás gombra.**

    ![Stream Analytics-feladat létrehozása](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>A Stream Analytics feladat konfigurálása

1. Nyissa meg a **Stream Analytics feladatot** a távfigyelési megoldás erőforráscsoportjában.

1. Az Áttekintés lapon kattintson a **Bemenetek gombra.**

    ![Áttekintő lap](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kattintson **az Adatfolyam-bevitel hozzáadása** elemre, és válassza az **IoT-központ** lehetőséget a legördülő menüből.

    ![Bemenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Az Új bevitel lapon adja meg az **IoTHub**bemeneti aliasát.

1. A Fogyasztói csoport legördülő menüben válassza ki a korábban létrehozott fogyasztói csoportot. Itt használjuk **streamanalyticsjob**.

    ![Bevitel kiválasztása](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kattintson a **Mentés** gombra.

1. Az Áttekintés lapon kattintson a **Kimenetek gombra.**

    ![Data Lake Store hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kattintson a **Hozzáadás** gombra, és válassza a Legördülő menüből a **Data Lake Store elemet.**

    ![Kimenet hozzáadása](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Az Új kimenet lapon adja meg a **DataLakeStore**kimeneti aliasát.

1. Válassza ki az előző lépésekben létrehozott Data Lake Áruház-fiókot, és adja meg a mappastruktúrát az adatok áruházba való streameléséhez.

1. A Dátumformátum mezőbe írja be a **/streaming/{date}/{time} parancsot.** Hagyja meg az alapértelmezett Dátum formátumot: YYYY/MM/DD és Time formátuma HH.

    ![Mappastruktúra biztosítása](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kattintson **az Engedélyezés gombra.**

    A Data Lake Store-nál engedélyeznie kell, hogy a Stream analytics-feladat írási hozzáférését biztosítsa a fájlrendszerhez.

    ![A Stream Analytics engedélyezése a Data Lake Store áruházban](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Megjelenik egy felugró ablak, és amint a felugró ablak bezárja az Engedélyezés gombot, az engedélyezés befejezése után szürkén jelenik meg.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az előugró ablakban, nyisson meg egy új böngészőablakot inkognitómódban, és próbálkozzon újra.

1. Kattintson a **Mentés** gombra.

## <a name="edit-the-stream-analytics-query"></a>A Stream Analytics-lekérdezés szerkesztése

Az Azure Stream Analytics sql-szerű lekérdezési nyelvet használ egy olyan bemeneti forrás megadásához, amely adatokat továbbít, igény szerint átalakítja az adatokat, és különböző tárolási vagy feldolgozási célhelyekre adja ki a kimenetet.

1. Az Áttekintés lapon kattintson a **Lekérdezés szerkesztése gombra.**

    ![Lekérdezés szerkesztése](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. A Lekérdezés szerkesztőben cserélje le a [YourOutputAlias] és a [YourInputAlias] helyőrzőket a korábban megadott értékekre.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics-lekérdezés](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kattintson a **Mentés** gombra.
1. A módosítások elfogadásához kattintson az **Igen** gombra.

## <a name="start-the-stream-analytics-job"></a>A Stream Analytics feladat indítása

1. Az Áttekintés lapon kattintson a **Start**gombra.

    ![Stream Analytics-feladat indítása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. A Feladat indítása lapon kattintson az **Egyéni gombra.**

1. Állítsa be az egyéni időt, hogy néhány órával visszalépjen az adatok felvételéhez, amikor az eszköz megkezdte a streamelést.

1. Kattintson a **Start gombra.**

    ![Egyéni dátum választása](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Várjon, amíg a feladat futó állapotba kerül, ha hibákat lát, amelyek a lekérdezésből származhatnak, ellenőrizze, hogy a szintaxis helyes-e.

    ![Futó feladat](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    A streamelési feladat megkezdi az adatok olvasását az IoT Hubról, és tárolja az adatokat a Data Lake Store-ban. Eltarthat néhány percig, amíg az adatok megjelennek a Data Lake Store-ban.

## <a name="explore-the-streaming-data"></a>A streamelési adatok felfedezése

1. Nyissa meg a Data Lake Store áruházat.

1. Az Áttekintés lapon kattintson az **Adatkezelő gombra.**

1. Az Adatkezelőben részletezze a **/streaming** mappát. Látni fogja, hogy a mappák YYYY/MM/DD/HH formátumban vannak létrehozva.

    ![Streamelési adatok felfedezése](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Látni fogja json fájlokat egy fájlt óránként.

    ![Streamelési adatok felfedezése](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Következő lépések

Az Azure Data Lake Analytics segítségével big data-elemzésvégezhető a Data Lake Áruház-adatkészleteken. További információ a [Data Lake Analytics dokumentációról.](https://docs.microsoft.com/azure/data-lake-analytics)
