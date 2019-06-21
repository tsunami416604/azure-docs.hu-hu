---
title: 'Oktatóanyag: Azure Time Series Insights-környezet létrehozása | Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre szimulált eszközökről származó adatok egy Time Series Insights-környezet, amely fel van töltve.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 06a450c47c7264bdecb663c9f71e3a9753df5e1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273542"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Ez az oktatóanyag végigvezeti egy Azure Time Series Insights-környezet, amely fel van töltve a szimulált eszközökről származó adatokkal történő létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy Time Series Insights környezetet hozhat létre.
> * Hozzon létre egy szimuláció megoldás, amely tartalmaz egy IoT hubot.
> * A Time Series Insights-környezet csatlakoztatása az IoT hubhoz.
> * Egy eszköz szimuláció futtatása az adatok streamelése a Time Series Insights-környezetbe.
> * Ellenőrizze a szimulált telemetriai adatokat.

## <a name="video"></a>Videó

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Megtudhatja, hogyan hozhat létre adatokat, és a Time Series Insights használatának első lépései az Azure IoT-megoldásgyorsítók használatával. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Az Azure bejelentkezési fiókot is tagjának kell lennie az előfizetés **tulajdonosa** szerepkör. További információkért lásd: [hozzáférés kezelése szerepköralapú hozzáférés-vezérlés és az Azure portal használatával](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Áttekintés

A Time Series Insights-környezet, ha eszközadatok összegyűjtött és tárolt. Tárolt, a [Azure Time Series Insights explorer](time-series-quickstart.md) és [Time Series Insights lekérdezési API](/rest/api/time-series-insights/ga-query-api) lekérdezését és elemzését az adatok segítségével.

Az Azure IoT Hub által használt minden eszköz (szimulált vagy fizikai) a jelen oktatóanyagban biztonságos kapcsolódást és az Azure-felhőbeli adatokat a forrás.

Ez az oktatóanyag is használ egy [IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) hozhat létre és adatfolyam-minta telemetriai adatokat az IoT hubnak.

>[!TIP]
> [IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) nagyvállalati szintű, előre konfigurált megoldások, amelyek használatával gyorsíthatja fel az egyéni IoT-megoldások fejlesztését.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Először hozza létre az eszköz szimulálása megoldást, amely Tesztadatok feltöltése a Time Series Insights-környezetet hoz létre.

1. Egy külön ablakban vagy a lapon lépjen a [azureiotsolutions.com](https://www.azureiotsolutions.com). Jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal, és válassza ki a **Eszközszimuláció** gyorsító.

   [![Futtassa az Eszközszimuláció megoldástámogató](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Adja meg a szükséges paramétereket a a **Eszközszimuláció létrehozása megoldás** lapot.

   Paraméter|Leírás
   ---|---
   **Üzemelő példány neve** | Ez az egyedi érték segítségével hozzon létre egy új erőforráscsoportot. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Azure-előfizetés** | Adja meg a Time Series Insights-környezet létrehozása az előző szakaszban használt ugyanahhoz az előfizetéshez.
   **Központi telepítési beállítások** | Válassza ki **új IoT hubot kiépítése** létrehozása adott ebben az oktatóanyagban egy új IoT hubot.
   **Azure-beli helyre** | Adja meg a Time Series Insights-környezet létrehozása az előző szakaszban használt ugyanabban a régióban.

   Ha elkészült, válassza ki a **megoldás létrehozása** a megoldás az Azure-erőforrások kiépítése. Ez a folyamat befejezéséhez akár 20 percig is eltarthat.

   [![Az eszköz szimulálása megoldás kiépítése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Kiépítés befejezése után, a szöveg felett az új megoldásokat a változik **kiépítési** való **készen**.

   >[!IMPORTANT]
   > Ne válassza **indítsa el a** még! Hagyja megnyitva ezt a weblapot, mert Ön lesz később még visszatérünk rá.

   [![Eszköz szimulálása megoldás teljes körű kiépítés](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat az Azure Portalon. Az a **erőforráscsoportok** lapon látható, hogy egy új erőforráscsoportot használatával lett létrehozva, a **megoldásnevet** az előző lépésben megadott. Jegyezze fel az alkalmazáshoz az eszközszimuláció létrehozott erőforrásokat.

   [![Eszköz szimulálása erőforrások](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Környezet létrehozása

Ezután hozzon létre egy Time Series Insights-környezet az Azure-előfizetésében.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetés fiók használatával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**. 

   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Az a **Time Series Insights-környezet** lap, adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válassza ki a Time Series Insights-környezet egyedi nevét. A Time Series Insights explorer által használt nevét, és a [lekérdezési API-k](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést a Time Series Insights környezetet hozhat létre.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Time Series Insights-környezet erőforrás.
   **Location** | Válassza ki a Time Series Insights-környezethez adatközponti régió. További késleltetés elkerülése érdekében hozzon létre a Time Series Insights-környezet egyéb IoT-erőforrások ugyanabban a régióban.
   **Tier** | Válassza ki a szükséges teljesítményt. Válassza ki **S1**.
   **Kapacitás** | Kapacitás a szorzó a alkalmazni a bejövő forgalom és a tárolási kapacitás a kiválasztott Termékváltozat társítva. Létrehozása után módosíthatja a kapacitást. Válassza ki a kapacitását **1**.

   Amikor végzett, válassza ki a **felülvizsgálat + létrehozása** , folytassa a következő lépéssel.

   [![A Time Series Insights környezetet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. A Time Series Insights-környezet, mostantól csatlakozhat az IoT hub által a megoldásgyorsító létrehozott. Állítsa be **válassza ki a hub** való `Select existing`. Ezután válassza ki az IoT hub beállításakor a megoldásgyorsító által létrehozott **az IoT Hub nevét**.

   [![A Time Series Insights-környezet csatlakozik a létrehozott IoT hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Ellenőrizze a **értesítések** panel figyelése a központi telepítés befejezését. 

   [![Time Series Insights környezetet üzembe helyezés sikeres volt](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Adatok streamelése az eszközszimuláció futtatása

Most, hogy az üzembe helyezés és a kezdeti konfigurációt a befejeződött, töltse ki a Time Series Insights-környezet mintaadatokat is abból az [szimulált eszközök a gyorsító által létrehozott](#create-a-device-simulation).

Az IoT hubban, és a egy Azure App Service-webalkalmazás hozhat létre és továbbítja a szimulált eszköz telemetriai lett létrehozva.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Jelentkezzen be újra, ha szükséges, azonos, de eddig ebben az oktatóanyagban használt Azure-fiók használatával. Most kiválaszthatja **indítsa el a** a "Eszközszimuláció" megoldás alatt található.

     [![Megoldás megoldásgyorsítók irányítópultja](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Az eszköz szimulálása webalkalmazás első lépése kérő üzenet, meg kell adnia a webalkalmazást, a "Bejelentkezés és a profilja olvasását" engedélyt. Ez az engedély lehetővé teszi, hogy az alkalmazás a felhasználói profil adatait az alkalmazás működéséhez szükséges.

     [![Eszköz szimulálása webes alkalmazás jóváhagyásának](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Után az **szimuláció telepítő** terhelések lap, adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Cél IoT Hub** | Válassza ki **használja az előzetesen kiépített az IoT Hub**.
   **Eszközmodell** | Válassza ki **hűtő**.
   **Eszközök száma**  | Adja meg `1000` alatt **összeg**.
   **Telemetria gyakorisága** | Adja meg `10` másodperc.
   **Szimuláció időtartama** | Válassza ki **végződhet:** , és adja meg `5` perc.

   Ha elkészült, válassza ki a **Start Simulation**. A szimuláció futtatja összesen 5 perc. Állít elő a adatok 1000 szimulált eszközökről 10 másodpercenként. 

   [![Eszköz szimulálása beállítása](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. A szimuláció végrehajtása közben figyelje meg, hogy a **üzenetek teljes** és **üzenetek / másodperc** mezők frissítése, körülbelül 10 másodpercenként. A szimuláció körülbelül 5 perc múlva befejeződik, és visszatér **szimuláció telepítő**.

   [![Eszközszimuláció fut](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>A telemetriaadatok ellenőrzése

Az utolsó szakaszban, győződjön meg arról, hogy a telemetriai adatokat jön létre, és tárolja a Time Series Insights-környezet. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Térjen vissza a Time Series Insights-környezet erőforráscsoport **áttekintése** lapot. Válassza ki a Time Series Insights-környezetet.

   [![Time Series Insights-környezet erőforráscsoport és a környezet](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. A Time Series Insights környezetet **áttekintése** lapon válassza ki a **Time Series Insights explorer URL-cím** a Time Series Insights explorer megnyitásához.

   [![Time Series Insights explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. A Time Series Insights explorer betölt, és az Azure portal-fiókját használva elvégzi a hitelesítését. Kezdeti megtekintése után láthatja a diagramterületen, hogy a Time Series Insights-környezet lett feltöltve szimulált telemetriát. Egy szűkebb időtartományon szűréséhez válassza a bal felső sarokban lévő legördülő. Adjon meg egy időtartományt elég nagy az eszközszimuláció időtartama span. Ezután válassza ki a keresési nagyító ikonra.

   [![Idő Series Insights explorer idő dátumtartomány-szűrő](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. A időtartományát szűkítheti lehetővé teszi, hogy az adatforgalom, az IoT hub és a Time Series Insights-környezet különböző adatlöketekkel Nagyítás a diagramot. Szintén figyelje meg, hogy a **Streamelés kész** szöveget a jobb felső sarokban található események teljes számát jeleníti meg. Is húzhatja a **intervallumának mérete** csúszka vezérlőelemet a diagram granularitási a diagramra.

   [![Time Series Insights explorer időtartomány szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Végül is is kattint egy régióban, széles szűréséhez. Ezután kattintson a jobb gombbal, és használhatja az **események tallózása** esemény részleteinek megjelenítéséhez a táblázatos **események** megtekintése.

   [![Time Series Insights explorer időtartomány szűrt nézet és az események](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban létrehoz több futó Azure-szolgáltatások támogatásához a Time Series Insights környezetet és az eszköz szimulálása megoldás. Távolítsa el őket, lépjen vissza az Azure Portalon.

Az Azure portal bal oldali menüből:

1. Válassza ki a **erőforráscsoportok** ikonra. Ezután válassza ki a Time Series Insights-környezethez létrehozott erőforráscsoportot. A lap tetején válassza **erőforráscsoport törlése**, adja meg az erőforráscsoport nevét, és válassza ki **törlése**.

1. Válassza ki a **erőforráscsoportok** ikonra. Ezután válassza ki az erőforráscsoportot, amely az eszköz szimulálása megoldásgyorsító hozta létre. A lap tetején válassza **erőforráscsoport törlése**, adja meg az erőforráscsoport nevét, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy Time Series Insights környezetet hozhat létre.
> * Hozzon létre egy szimuláció megoldás, amely tartalmaz egy IoT hubot.
> * A Time Series Insights-környezet csatlakoztatása az IoT hubhoz.
> * Egy eszköz szimuláció futtatása az adatok streamelése a Time Series Insights-környezetbe.
> * Ellenőrizze a szimulált telemetriai adatokat.

Most, hogy tudja, hogyan hozhat létre a saját Time Series Insights-környezet, megtudhatja, hogyan hozhat létre egy webalkalmazást, amely egy Time Series Insights-környezetből származó adatokat használ fel:

> [!div class="nextstepaction"]
> [Azure Time Series Insights egyoldalas webalkalmazás létrehozása](tutorial-create-tsi-sample-spa.md)
