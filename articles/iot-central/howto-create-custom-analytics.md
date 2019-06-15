---
title: Az egyéni analytics kiterjesztése az Azure IoT Central |} A Microsoft Docs
description: Megoldás a fejlesztők konfigurálása ehhez az egyéni analitikai és megjelenítési IoT Central alkalmazáshoz. Ez a megoldás az Azure Databricks használ.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743440"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Az egyéni analytics kiterjesztése az Azure IoT Central

Ez az útmutató bemutatja, hogy megoldás a fejlesztők az IoT-központ alkalmazás egyéni analitikai és megjelenítési bővítése. A példában egy [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) IoT-központ telemetriai adatok elemzésére és vizualizációkat hozhat létre például [grafikon mezőben](https://wikipedia.org/wiki/Box_plot).

Ez az útmutató bemutatja, hogyan már mire képes az IoT-központ túlnyúlnak a [beépített elemzési eszközök](howto-create-analytics.md).

Ez az útmutató, megtudhatja, hogyan lehet:

* Egy IoT-központ alkalmazást a származó telemetriai adatok Stream *folyamatos adatexportálás*.
* Hozzon létre egy Azure Databricks környezetet elemzése és eszköz telemetriai adatokat jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések elvégzéséhez, aktív Azure-előfizetés szükséges.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

### <a name="iot-central-application"></a>IoT Central alkalmazáshoz

Hozzon létre egy IoT-központ alkalmazás az [Azure IoT Central - alkalmazásaimat](https://aka.ms/iotcentral) lap a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Fizetési lehetőség | Utólagos, használatalapú fizetés |
| Alkalmazássablon | Minta: Contoso |
| Alkalmazásnév | Fogadja el az alapértelmezett, vagy válassza ki a saját nevét |
| URL-cím | Fogadja el az alapértelmezett, vagy válassza ki a saját egyedi URL-előtag |
| Címtár | Az Azure Active Directory-bérlő |
| Azure-előfizetés | Az Azure-előfizetése |
| Régió | USA keleti régiója |

A példák és képernyőképek a jelen cikk használja a **USA keleti Régiójában** régióban. Válassza az Önhöz helyet, és ellenőrizze, hogy ugyanabban a régióban létrehozott összes erőforrást.

### <a name="resource-group"></a>Erőforráscsoport

Használja a [hozzon létre egy erőforráscsoportot az Azure Portalon](https://portal.azure.com/#create/Microsoft.ResourceGroup) nevű **IoTCentralAnalysis** hoz létre a többi erőforrást tartalmaz. Az Azure-erőforrások létrehozása az IoT Central alkalmazásnak ugyanazon a helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

Használja a [Event Hubs-névtér létrehozása az Azure portal](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Name (Név)    | A névtér nevének kiválasztása |
| Tarifacsomag | Alapszintű |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalysis |
| Location egység | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="azure-databricks-workspace"></a>Az Azure Databricks-munkaterület

Használja a [Azure Databricks szolgáltatás létrehozása az Azure portal](https://portal.azure.com/#create/Microsoft.Databricks) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Munkaterület neve    | Válassza ki a munkaterület neve |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalysis |
| Location egység | USA keleti régiója |
| Tarifacsomag | Standard |

Ha létrehozta a szükséges erőforrásokkal, a **IoTCentralAnalysis** erőforráscsoportot a következő képernyőképhez hasonlóan néz ki:

![IoT-központ elemzési erőforráscsoport](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhatja az IoT-központ alkalmazás folyamatosan telemetria exportálása az eseményközpontba. Ebben a szakaszban a telemetriai adatok fogadása az IoT-központ alkalmazásból event hub létrehozása. Az event hubs kézbesíti a telemetriát a Stream Analytics-feladatot a feldolgozáshoz.

1. Az Azure Portalon keresse meg az Event Hubs-névtér, és válassza **+ Event Hub**.
1. Az eseményközpont neve **centralexport**, és válassza ki **létrehozás**.
1. A névtér az eseményközpontok listájában válassza ki a **centralexport**. Válassza a **megosztott elérési házirendek**.
1. Válassza a **+ Hozzáadás** lehetőséget. Nevű szabályzat létrehozása **figyelésére** együtt a **figyelésére** jogcím.
1. Amikor készen áll a szabályzatot, válassza ki a listából, és másolja a **kapcsolati karakterlánc – elsődleges kulcs** értéket.
1. Jegyezze fel ezt a kapcsolati karakterláncot, használja azt később a Databricks-jegyzetfüzet olvasásához az event hubs konfigurálásakor.

Az Event Hubs-névtér a következő képernyőképhez hasonlóan néz ki:

![Event Hubs-névtér](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása az IoT-központ

Keresse meg a [IoT Central alkalmazásnak](https://aka.ms/iotcentral) a Contoso-sablon alapján létrehozott. Ebben a szakaszban konfigurálja az alkalmazás segítségével a telemetriát a szimulált eszközökről az eseményközpontba. Az Exportálás konfigurálása:

1. Keresse meg a **folyamatos adatexportálás** lapon jelölje be **+ új**, majd **Azure Event Hubs**.
1. Az Exportálás konfigurálja, majd válassza ki a következő beállítások használatával **mentése**:

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenített név | Exportálás az Event hubs szolgáltatásba |
    | Enabled | Bekapcsolva |
    | Event Hubs-névtér | Az Event Hubs-névtér neve |
    | Eseményközpont | centralexport |
    | Mérések | Bekapcsolva |
    | Eszközök | Ki |
    | Eszköz-sablonok | Ki |

![Folyamatos exportálási konfiguráció](media/howto-create-custom-analytics/cde-configuration.png)

Várjon, amíg az Exportálás állapota **futó** a folytatás előtt.

## <a name="configure-databricks-workspace"></a>Databricks-munkaterület konfigurálása

Az Azure Portalon lépjen az Azure Databricks-szolgáltatásra, és válassza ki **munkaterület indítása**. Egy új lapot a böngészőben nyílik meg, és bejelentkezik, a munkaterülethez.

### <a name="create-a-cluster"></a>Fürt létrehozása

Az a **Azure Databricks** oldalon, a gyakori feladatokat, válassza ki a lista **új fürt**.

Az alábbi táblázatban szereplő információk segítségével a fürt létrehozása:

| Beállítás | Érték |
| ------- | ----- |
| Fürt neve | centralanalysis |
| Fürt üzemmód | Standard |
| Databricks futtatókörnyezet-verziója | 5.3 (Scala 2.11-et, a Spark 2.4.0) |
| Python-verzió | 3 |
| Automatikus skálázás engedélyezése | Nem |
| Ennyi perc inaktivitás után leáll | 30 |
| Feldolgozó típusa | Standard_DS3_v2 |
| Feldolgozók | 1 |
| Illesztőprogram típusa | Ugyanaz, mint a feldolgozó |

Fürt létrehozása több percet is igénybe vehet, várjon, amíg befejeződik, mielőtt folytatja a fürt létrehozása.

### <a name="install-libraries"></a>Kódtárak telepítése

Az a **fürtök** lapon várja meg, amíg a fürt állapota **futó**.

A következő lépések bemutatják, hogyan a könyvtárban, a minta kell importálnia a fürt:

1. Az a **fürtök** lapon várja meg, amíg az állapota, a **centralanalysis** interaktív fürt **futó**.

1. Válassza ki a fürtöt, és válassza a **kódtárak** fülre.

1. Az a **kódtárak** lapra, majd **telepítése új**.

1. Az a **telepítése kódtár** lapon a **Maven** könyvtár forrásaként.

1. Az a **koordinálja** szövegmezőbe írja be a következő értéket: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Válasszon **telepítése** a tár telepíthetők a fürtön.

1. A szalagtár állapota ezután **telepített**:

    ![Telepített könyvtár](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks-jegyzetfüzet importálása

A következő lépések segítségével importálja a Databricks-jegyzetfüzet, amely tartalmazza a Python-kód, elemzése és megjelenítése a IoT-központ-telemetria:

1. Keresse meg a **munkaterület** lap a Databricks-környezetben. Kattintson a legördülő listára, a fiók neve mellett, és válassza a **importálás**.

1. Válassza ki a importálása URL-címről, és adja meg a következő címre: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. A notebook importálásához válassza **importálása**.

1. Válassza ki a **munkaterület** az importált notebook megtekintése:

    ![Importált notebook](media/howto-create-custom-analytics/import-notebook.png)

1. A kód a korábban mentett az Event Hubs-kapcsolati karakterlánc hozzáadása az első Python-cellába szerkesztése:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Elemzés futtatása

Az elemzés futtatása, hozzá kell rendelni a notebookot a fürt:

1. Válassza ki **Detached** majd válassza ki a **centralanalysis** fürt.
1. Ha a fürt nem fut, indítsa el.
1. A notebook, válassza a Futtatás gombra.

Az utolsó cellában hiba jelenhet meg. Ha igen, ellenőrizze az előző cella futnak, várjon egy percet a néhány tárolóra írható adat, és futtassa újra az előző cella.

### <a name="view-smoothed-data"></a>Visszametszéses adatok megtekintése

A jegyzetfüzet cella 14, a működés közbeni átlagos páratartalmat eszköztípus szerint rajz megtekintéséhez görgessen. A diagram folyamatosan frissül, amint streamelési telemetriai adatok érkeznek:

![Telemetria diagram Görbített](media/howto-create-custom-analytics/telemetry-plot.png)

A diagram a jegyzetfüzet méretezheti.

### <a name="view-box-plots"></a>Nézet listában jeleníti meg

A notebook cella 20 megtekintéséhez görgessen a [grafikon mezőben](https://en.wikipedia.org/wiki/Box_plot). A box grafikon statikus adatokon alapulnak, így azokat újra futtatnia kell a cella:

![Box-grafikon](media/howto-create-custom-analytics/box-plots.png)

A grafikon a jegyzetfüzet méretezheti.

## <a name="tidy-up"></a>Elágazásából

Ebben az útmutatóban után elágazásából és a felesleges költségek elkerülése érdekében törölje a **IoTCentralAnalysis** erőforráscsoportot az Azure Portalon.

Az IoT Central alkalmazáshoz törölheti a **felügyeleti** lap az alkalmazásban.

## <a name="next-steps"></a>További lépések

Ez az útmutató, megtanulta, hogyan lehet:

* Egy IoT-központ alkalmazást a származó telemetriai adatok Stream *folyamatos adatexportálás*.
* Hozzon létre egy Azure Databricks környezetet elemzésére és a telemetriai adatokat jeleníti meg.

Most, hogy tudja, hogyan hozhat létre egyéni analytics, a javasolt következő lépésre megtudhatja, hogyan [Visualize és az Azure IoT Central adatelemzés a Power BI-irányítópulton](howto-connect-powerbi.md).
