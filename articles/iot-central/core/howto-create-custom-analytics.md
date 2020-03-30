---
title: Az Azure IoT Central bővítése egyéni elemzésekkel | Microsoft dokumentumok
description: Megoldásfejlesztőként konfiguráljon egy IoT Central-alkalmazást egyéni elemzések és vizualizációk hozásához. Ez a megoldás az Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158197"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Az Azure IoT Central bővítése egyéni elemzésekkel az Azure Databricks használatával

Ez az útmutató bemutatja Önnek, mint megoldásfejlesztőnek, hogyan bővítheti az IoT Central alkalmazást egyéni elemzésekkel és vizualizációkkal. A példa egy [Azure Databricks-munkaterületet](https://docs.microsoft.com/azure/azure-databricks/) használ az IoT-központi telemetriai adatfolyam elemzéséhez és a vizualizációk, például [a dobozos telkek](https://wikipedia.org/wiki/Box_plot)létrehozásához.

Ez az útmutató bemutatja, hogyan terjesztheti ki az IoT Central túl azokat, amelyeket már megtehet a [beépített elemzési eszközökkel.](./howto-create-custom-analytics.md)

Ebben az útmutatóútmutatóban megtudhatja, hogyan:

* Telemetriai adatok streamelése egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Azure Databricks-környezetet az eszköz telemetriai adatainak elemzéséhez és nyomtatásához.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépései végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre egy IoT Central alkalmazást az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Árképzési terv | Standard |
| Alkalmazássablon | Üzleten belüli elemzés – állapotfigyelés |
| Alkalmazásnév | Fogadja el az alapértelmezettet, vagy válassza ki a saját nevét |
| URL-cím | Az alapértelmezett érték elfogadása vagy saját egyedi URL-előtag kiválasztása |
| Címtár | Az Azure Active Directory bérlője |
| Azure-előfizetés | Az Azure-előfizetése |
| Régió | A legközelebbi régió |

A jelen cikkben szereplő példák és képernyőképek az **Egyesült Államok régióját** használják. Válasszon egy önhöz közeli helyet, és győződjön meg arról, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazássablon két szimulált termosztáteszközt tartalmaz, amelyek telemetriai adatokat küldenek.

### <a name="resource-group"></a>Erőforráscsoport

Az Azure Portal használatával [hozzon létre egy](https://portal.azure.com/#create/Microsoft.ResourceGroup) **IoTCentralAnalysis** nevű erőforráscsoportot a többi létrehozott erőforrás tárolására. Hozzon létre az Azure-erőforrások ugyanazon a helyen, mint az IoT Central alkalmazás.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

Az Azure Portal használatával [hozzon létre egy Event Hubs névteret](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A névtér nevének kiválasztása |
| Tarifacsomag | Basic |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalízis |
| Hely | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="azure-databricks-workspace"></a>Az Azure Databricks munkaterülete

Az Azure Portal használatával [hozzon létre egy Azure Databricks-szolgáltatást](https://portal.azure.com/#create/Microsoft.Databricks) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Munkaterület neve    | A munkaterület nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalízis |
| Hely | USA keleti régiója |
| Tarifacsomag | Standard |

Miután létrehozta a szükséges erőforrásokat, az **IoTCentralAnalysis** erőforráscsoport a következő képernyőképen jelenik meg:

![IoT Központi elemzéserőforrás-csoport](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhatja az IoT Central-alkalmazást, hogy folyamatosan exportálja a telemetriai adatokat egy eseményközpontba. Ebben a szakaszban hozzon létre egy eseményközpontot az IoT Central-alkalmazás telemetriai adatainak fogadásához. Az eseményközpont biztosítja a telemetriai adatokat a Stream Analytics-feladat feldolgozásra.

1. Az Azure Portalon keresse meg az Event Hubs névterét, és válassza a **+ Event Hub**lehetőséget.
1. Nevezze el az eseményközpontot **centralexportnak,** és válassza a **Létrehozás gombot.**
1. A névtérben lévő eseményközpontok listájában válassza a **centralexport**lehetőséget. Ezután válassza **a Megosztott hozzáférési házirendek lehetőséget.**
1. Válassza a **+ Hozzáadás** lehetőséget. Hozzon létre egy **Figyelés** nevű házirendet a **Figyelés** jogsival.
1. Amikor a házirend készen áll, jelölje ki a listában, majd másolja a **Kapcsolatkarakterlánc-elsődleges kulcs** értékét.
1. Jegyezze fel ezt a kapcsolati karakterláncot, később használja, amikor konfigurálja a Databricks notebook olvasni az eseményközpontból.

Az Event Hubs névtere a következő képernyőképen jelenik meg:

![Event Hubs-névtér](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása az IoT Centralban

Az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén keresse meg a Contoso-sablonból létrehozott IoT Central alkalmazást. Ebben a szakaszban konfigurálja az alkalmazást a telemetriai adatok streamelése a szimulált eszközökről az eseményközpontba. Az exportálás konfigurálása:

1. Nyissa meg az **Adatexportálás** lapot, válassza a **+ Új**, majd **az Azure Event Hubs lehetőséget.**
1. Az exportálás konfigurálásához használja az alábbi beállításokat, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás eseményközpontokba |
    | Engedélyezve | Bekapcsolva |
    | Event Hubs-névtér | Az Event Hubs névtér ének neve |
    | Eseményközpont | központi export |
    | Mérések | Bekapcsolva |
    | Eszközök | Ki |
    | Eszközsablonok | Ki |

![Adatexportálási konfiguráció](media/howto-create-custom-analytics/cde-configuration.png)

A folytatás előtt várja meg, amíg az exportálási állapot **fut.**

## <a name="configure-databricks-workspace"></a>Databricks munkaterület konfigurálása

Az Azure Portalon keresse meg az Azure Databricks-szolgáltatást, és válassza **a Munkaterület indítása**lehetőséget. Megnyílik egy új lap a böngészőben, és bejelentkezik a munkaterületre.

### <a name="create-a-cluster"></a>Fürt létrehozása

Az **Azure Databricks** lapon a gyakori feladatok listájában válassza az **Új fürt**lehetőséget.

A fürt létrehozásához használja az alábbi táblázatban található információkat:

| Beállítás | Érték |
| ------- | ----- |
| Fürt neve | központi elemzés |
| Fürt mód | Standard |
| Databricks futásidejű verzió | 5,5 LTS (Scala 2.11, Spark 2.4.3) |
| Python verzió | 3 |
| Automatikus skálázás engedélyezése | Nem |
| Az inaktivitás percje után megszűnik | 30 |
| Dolgozó típusa | Standard_DS3_v2 |
| Munkavállalók | 1 |
| Illesztőprogram típusa | Megegyezik a dolgozóval |

A fürt létrehozása több percet is igénybe vehet, a folytatás előtt várjon a fürt létrehozásának befejezésére.

### <a name="install-libraries"></a>Tárak telepítése

A **Fürtök** lapon várja meg, amíg a fürtállapot **fut.**

A következő lépések bemutatják, hogyan importálhatja a minta által a fürtbe szükséges könyvtárat:

1. A **Fürtök** lapon várjon, amíg a **központielemzési** interaktív fürt állapota **fut.**

1. Jelölje ki a fürtöt, majd kattintson a **Könyvtárak** fülre.

1. A **Könyvtárak** lapon válassza az **Új telepítése lehetőséget.**

1. A **Könyvtár telepítése** lapon válassza **a Maven** lehetőséget a tár forrásaként.

1. A **Koordináták** mezőbe írja be a következő értéket:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. A **Fürtkönyvtár** telepítéséhez válassza a Telepítés gombot.

1. A könyvtár állapota most **telepítve**van:

    ![Könyvtár telepítve](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks-jegyzetfüzet importálása

Az alábbi lépésekkel importálhatja a Python-kódot tartalmazó Databricks-jegyzetfüzetet az IoT-központi telemetria elemzéséhez és megjelenítéséhez:

1. Keresse meg a **Munkaterület** lapot a Databricks környezetben. Jelölje ki a fiókneve melletti legördülő menüt, majd válassza az **Importálás**lehetőséget.

1. Válassza az URL-címről történő importálást, és adja meg a következő címet:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. A jegyzetfüzet importálásához válassza az **Importálás gombot.**

1. Az importált jegyzetfüzet megtekintéséhez jelölje ki a **Munkaterületet:**

    ![Importált jegyzetfüzet](media/howto-create-custom-analytics/import-notebook.png)

1. A korábban mentett Event Hubs kapcsolati karakterlánc hozzáadásához az első Python-cellában szerkesztheti a kódot:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Elemzés futtatása

Az elemzés futtatásához csatolnia kell a jegyzetfüzetet a fürthöz:

1. Válassza **a Leválasztani lehetőséget,** majd jelölje ki a **központielemzési** fürtöt.
1. Ha a fürt nem fut, indítsa el.
1. A jegyzetfüzet elindításához válassza a futtatás gombot.

Előfordulhat, hogy hiba jelenik meg az utolsó cellában. Ha igen, ellenőrizze az előző cellák futását, várjon egy percet, amíg bizonyos adatokat a tárolóba ír, majd futtassa újra az utolsó cellát.

### <a name="view-smoothed-data"></a>Simított adatok megtekintése

A jegyzetfüzetben görgessen le a 14-es cellához, hogy megtekintse a gördülő átlagos páratartalom eszköztípusonkénti ábrázolását. Ez a telek folyamatosan frissül, ahogy a telemetriai adatok megérkezik:

![Simított telemetriai telek](media/howto-create-custom-analytics/telemetry-plot.png)

A jegyzetfüzetben átméretezheti a diagramot.

### <a name="view-box-plots"></a>Mezőtelkek megtekintése

A jegyzetfüzetben görgessen le a 20-as cellába a [mezőtelkek](https://en.wikipedia.org/wiki/Box_plot)megtekintéséhez . A mező telkek alapulnak statikus adatokat, így frissíteni őket meg kell futtatni a cella:

![Dobozos telkek](media/howto-create-custom-analytics/box-plots.png)

Átméretezheti a jegyzetfüzetben lévő telkeket.

## <a name="tidy-up"></a>Rendet rak

Az útmutató után rendezett és a szükségtelen költségek elkerülése érdekében törölje az **IoTCentralAnalysis** erőforráscsoportot az Azure Portalon.

Törölheti az IoT Central alkalmazást az alkalmazás on belül **a Kezelés** lapról.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóútmutatóban megtanulta, hogyan:

* Telemetriai adatok streamelése egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Azure Databricks-környezetet a telemetriai adatok elemzéséhez és nyomtatásához.

Most, hogy már tudja, hogyan hozhat létre egyéni elemzéseket, a javasolt következő lépés az [alkalmazás kezelésének megismerése.](howto-administer.md)
