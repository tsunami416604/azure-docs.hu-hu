---
title: 'Oktatóanyag: Azure Time Series Insights-környezet létrehozása | Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre szimulált eszközökről származó adatok egy Time Series Insights-környezet, amely fel van töltve.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244161"
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

A Time Series Insights-környezet, ha eszközadatok összegyűjtött és tárolt. Miután az adatok tárolását, a [Azure Time Series Insights explorer](time-series-quickstart.md) és [Time Series Insights lekérdezési API](/rest/api/time-series-insights/ga-query-api) lekérdezését és elemzését az adatok segítségével. Az Azure IoT Hub biztonságos kapcsolódást és küldi az adatokat az Azure-felhő minden eszköz (szimulált vagy fizikai) által használt csatlakozási pont. A [Time Series Insights áttekintése](time-series-insights-overview.md) tudomásul veszi, hogy az Azure IoT Hub is szolgál eseményforrás streamelési adatok egy Time Series Insights-környezetbe. Ebben az oktatóanyagban egy [IoT-megoldásgyorsítók](/azure/iot-accelerators/) hozhat létre és adatfolyam-minta telemetriai adatokat az IoT hubnak.

>[!TIP]
> IoT-megoldásgyorsítók nagyvállalati szintű, előre konfigurált megoldásokat használhatja, egyéni IoT-megoldások fejlesztésének felgyorsításához.

## <a name="create-an-environment"></a>Környezet létrehozása

Először hozzon létre egy Time Series Insights-környezet az Azure-előfizetésében.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetés fiók használatával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**. 

   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Az a **Time Series Insights-környezet** lap, adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válassza ki a Time Series Insights-környezet egyedi nevét. A neveket a Time Series Insights explorer és a lekérdezési API-k által használt.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést a Time Series Insights környezetet hozhat létre.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Time Series Insights-környezet erőforrás.
   **Hely** | Válassza ki a Time Series Insights-környezethez adatközponti régió. A hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében ne a Time Series Insights-környezet és egyéb IoT-erőforrások ugyanabban a régióban.
   **Díjszabási termékváltozat** | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és az alapszintű kapacitás, válassza ki a `S1`.
   **Kapacitás** | A kapacitás a kiválasztott SKU bejövő forgalmi sebességére, tárkapacitására és költségeire alkalmazott szorzó. Létrehozása után módosíthatja a kapacitást. Válassza ki a legalacsonyabb költséget 1 kapacitását.

   Amikor végzett, válassza ki a **létrehozás** a kiépítési folyamat megkezdéséhez.

   [![A Time Series Insights környezetet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Ellenőrizze a **értesítések** panel figyelése a központi telepítés befejezését. 

   [![Time Series Insights környezetet üzembe helyezés sikeres volt](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ezután hozza létre az eszköz szimulálása megoldást, amely Tesztadatok feltöltése a Time Series Insights-környezetet hoz létre.

1. Egy külön ablakban vagy a lapon lépjen a [azureiotsolutions.com](https://www.azureiotsolutions.com). Jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal, és válassza ki a **Eszközszimuláció** gyorsító.

   [![Futtassa az Eszközszimuláció megoldástámogató](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Adja meg a szükséges paramétereket a a **Eszközszimuláció létrehozása megoldás** lapot.

   Paraméter|Leírás
   ---|---
   **Megoldás neve** | Ez az egyedi érték segítségével hozzon létre egy új erőforráscsoportot. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Előfizetés** | Adja meg a Time Series Insights-környezet létrehozása az előző szakaszban használt ugyanahhoz az előfizetéshez.
   **Régió** | Adja meg a Time Series Insights-környezet létrehozása az előző szakaszban használt ugyanabban a régióban.
   **Választható Azure-erőforrások üzembe helyezése** | Hagyja **az IoT Hub** be van jelölve. A szimulált eszközök használatával csatlakozni vagy adatokat.

   Ha elkészült, válassza ki a **megoldás létrehozása** a megoldás az Azure-erőforrások kiépítése. Ez a folyamat befejezéséhez 6-7 percbe is telhet.

   [![Az eszköz szimulálása megoldás kiépítése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Kiépítés befejezése után, a szöveg felett az új megoldásokat a változik **kiépítési** való **készen**.

   >[!IMPORTANT]
   > Ne válassza **indítsa el a** még! Hagyja megnyitva ezt a weblapot, mert Ön lesz később még visszatérünk rá.

   [![Eszköz szimulálása megoldás teljes körű kiépítés](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Most lépjen vissza az Azure Portalra, és vizsgálja meg az újonnan létrehozott erőforrást az előfizetésében. A portálon **erőforráscsoportok** lapon látható, hogy egy új erőforráscsoportot használatával lett létrehozva, a **megoldásnevet** az előző lépésben megadott. Szintén figyelje meg, az eszköz szimulálása megoldás támogatásához létrehozott erőforrásokat.

   [![Az eszközszimulációs megoldás erőforrásai](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>A környezet csatlakoztatása az IoT hubhoz

Mostanra elsajátította, hogyan hozhat létre két erőforráskészletet, mindegyiket a maga erőforráscsoportjában:

- Üres a Time Series Insights-környezet.
- Eszköz szimulálása megoldás erőforrásokat, amelyek megoldásgyorsítók által létrehozott IoT hub tartalmaz.

Emlékezzünk vissza, hogy a szimulált eszközöknek egy IoT Hubra kell csatlakozniuk eszközadatok streameléséhez. Az adatok áramlását a Time Series Insights-környezetbe, szüksége az IoT hub és a Time Series Insights-környezet konfigurációs módosításokat.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT hub-konfiguráció: Adjon meg egy fogyasztói csoportot

IoT Hub által biztosított különböző végpontok megosztása más actors funkciókat. Az "Események" végpont teszi lehetővé más alkalmazásokat adatok felhasználásához, ahogy azt adatfolyamként történő egy IoT hub-példány. Pontosabban a "fogyasztói csoportok" alkalmazások figyelését, és kérje le az IoT hub adatainak mechanizmus biztosítására.

Ezután megadhat egy új **fogyasztói csoportot** tulajdonság az eszköz szimulálása megoldás az IoT hub **események végpont**.

1. Az Azure Portalon nyissa meg a **áttekintése** lap az szimuláció megoldás számára létrehozott erőforráscsoport. Válassza ki az IoT hub-erőforrást.

   [![Az eszközszimulációs megoldás erőforráscsoportja](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Jegyezze fel a **neve** az IoT Hub erőforrás jön létre a megoldáshoz. Fog hivatkozni, azt később.

1. Görgessen lefelé, és válassza a **végpontok** oldalra, és kattintson a **események** végpont. A végpont **tulajdonságok** lap, adja meg egy egyedi nevet a végpont alatt a "$Default" fogyasztói csoportot. Kattintson a **Mentés** gombra.

   [![Eszközszimulációs megoldás IoT Hub-végpontjai](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Környezet konfigurációjának: eseményforrás definiálása

Most csatlakoztassa az új IoT hub **fogyasztói csoportot** esemény végpont a Time Series Insights környezetben egy **eseményforrás**.

1. Nyissa meg a **áttekintése** lapján a Time Series Insights-környezethez létrehozott erőforráscsoportot. Válassza ki a Time Series Insights-környezetet.

   [![Time Series Insights-környezet erőforráscsoport és a környezet](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. A Time Series Insights-környezet oldalán válassza **eseményforrások**. Válassza ki **+ Hozzáadás**.

   [![Time Series Insights-környezet áttekintése](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Adja meg a szükséges paramétereket a a **új eseményforrás** lapot.

   Paraméter|Leírás
   ---|---
   **Eseményforrás neve** | Itt egyedi értéket kell megadni, amely az eseményforrás neveként szolgál majd.
   **Forrás** | Válassza ki **az IoT Hub**.
   **Importálási beállítás** | Válassza az alapértelmezett `Use IoT hub from available subscriptions`. Ezen beállítás hatására a következő legördülő listából válassza ki az elérhető előfizetésekkel kell feltöltenie.
   **Előfizetés** | Válassza ki, amelyben létrehozta a Time Series Insights-környezet és az Eszközszimuláció erőforrások ugyanahhoz az előfizetéshez.
   **IoT Hub neve** | Ennek alapértelmezés szerint egyeznie kell az IoT Hub korábban feljegyzett nevével. Ha nem, válassza ki a megfelelő IoT Hubot.
   **Iot Hub szabályzatneve** | Válassza ki **iothubowner**.
   **IoT Hub fogyasztói csoport** | Ennek alapértelmezés szerint a korábban létrehozott IoT Hub fogyasztói csoport nevével kell egyeznie. Ha nem, válassza ki a megfelelő fogyasztói csoport nevét.
   **Eseményszerializációs formátum** | Hagyja meg az kilistázott alapértelmezett értéke `JSON`.
   **Időbélyeg-tulajdonság neve** | Adja meg a `timestamp`.

   Ha elkészült, válassza ki a **létrehozás** az eseményforrás hozzáadása. Amikor visszatér az erőforráscsoport **áttekintése** lapon a Time Series Insights-környezet erőforrás, valamint egy új "Time Series Insights-eseményforrás" erőforrás megjelenik.

   [![Time Series Insights környezetet új eseményforrás](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Adatok streamelése az eszközszimuláció futtatása

Most, hogy a teljes konfigurációs munkát befejezése, ideje a Time Series Insights környezetet a szimulált eszközök a mintaadatok feltöltése.

Előfordulhat, hogy a már ismert a [hozzon létre egy eszköz szimulálása szakaszt](#create-a-device-simulation), a gyorsító támogatásához a megoldás több Azure-erőforrások létrejött. A korábban tárgyalt IoT Hubbal együtt egy Azure App Service-webalkalmazás is létrejött a szimulált eszköztelemetria létrehozására és továbbítására.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Jelentkezzen be újra, ha szükséges, azonos, de eddig ebben az oktatóanyagban használt Azure-fiók használatával. Most kiválaszthatja **indítsa el a** a "Eszközszimuláció" megoldás alatt található.

     [![Megoldás megoldásgyorsítók irányítópultja](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Az eszköz szimulálása webes alkalmazás kezdődik a pontot, majd néhány másodpercig kezdeti betöltés után is igénybe vehet. Is kér hozzájárulást adni a webes alkalmazás a "Bejelentkezés és a profilja olvasását" engedélyt. Ez az engedély lehetővé teszi, hogy az alkalmazás a felhasználói profil adatait az alkalmazás működéséhez szükséges.

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

Ebben az oktatóanyagban létrehoz több futó Azure-szolgáltatások támogatásához a Time Series Insights környezetet és az eszköz szimulálása megoldás. Ha szeretne abandon vagy halassza el az oktatóanyag-sorozat végzett munka, a felesleges költségek elkerülése érdekében összes erőforrást törölheti.

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
