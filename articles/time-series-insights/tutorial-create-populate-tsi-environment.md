---
title: 'Oktatóanyag: Azure Time Series Insights-környezet létrehozása | Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre szimulált eszközök adataival feltöltött Time Series Insights-környezeteket.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 3c097ec74f45248a41c6dd4df80cbbd927a9b5ed
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827448"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Ez az oktatóanyag végigvezeti Önt a folyamaton, létrehozhat egy Time Series Insight-környezetben, a szimulált eszközökről származó adatokat is tartalmaz. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Time Series Insights-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * A Time Series Insights-környezet csatlakoztatása az IoT hubhoz
> * Egy eszköz szimuláció futtatása az adatok a Time Series Insights-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

## <a name="video"></a>Videó

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Megtudhatja, hogyan hozhat létre adatokat, és a Time Series Insights használatának első lépései az Azure IoT-Megoldásgyorsítók használatával. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Az Azure bejelentkezési fiókot is kell lennie az előfizetéshez tartozó tagja **tulajdonosa** szerepkör. Lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal) további részletekért.

## <a name="overview"></a>Áttekintés

A Time Series Insights-környezet, ha eszközadatok összegyűjtött és tárolt. A Time Series Insights környezetben tárolt a [Time Series Insights Explorerben](time-series-quickstart.md) és [Time Series Insights lekérdezési API](/rest/api/time-series-insights/ga-query-api) lekérdezését és elemzését az adatok segítségével. Az IoT Hub biztonságos kapcsolódást és küldi az adatokat az Azure-felhő minden eszköz (szimulált vagy fizikai) által használt csatlakozási pont. A [Time Series Insights áttekintése](time-series-insights-overview.md) tudomásul veszi, hogy az Azure IoT Hub is szolgál eseményforrás streamelési adatok egy Time Series Insights-környezetbe. Ebben az oktatóanyagban egy [IoT-megoldásgyorsítók](/azure/iot-accelerators/) hozhat létre és adatfolyam-minta telemetriai adatokat az IoT hubnak.

>[!TIP]
> IoT-megoldásgyorsítók adja meg a nagyvállalati szintű, előre konfigurált megoldások egyéni IoT-megoldások fejlesztésének felgyorsítását lehetővé.

## <a name="create-an-environment"></a>Környezet létrehozása

Először hozzon létre egy Time Series Insights-környezet az Azure-előfizetésben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetése fiókjával.  
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban.  
1. Válassza az **Eszközök internetes hálózata** kategóriát, majd a **Time Series Insights** elemet.  

   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. A **Time Series Insights-környezet** oldalon adja meg a szükséges paramétereket:

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válassza ki a Time Series Insights-környezet egyedi nevét. A nevet használja a Time Series Insights Explorerben és a lekérdezési API-kat.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést hozhat létre a Time Series Insights környezetet.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Time Series Insights-környezet erőforrás.
   **Hely** | Válassza ki a Time Series Insights-környezethez adatközponti régió. A hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében a legjobb, ha a Time Series Insights-környezet egyéb IoT-erőforrások ugyanabban a régióban.
   **Díjszabási termékváltozat** | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és az alapszintű kapacitás, válassza ki a `S1`.
   **Kapacitás** | A kapacitás a kiválasztott SKU bejövő forgalmi sebességére, tárkapacitására és költségeire alkalmazott szorzó.  Létrehozása után módosíthatja a kapacitást. Válassza ki a legalacsonyabb költséget 1 kapacitását.

   Miután végzett, kattintson a **Létrehozás** gombra az üzembe helyezés megkezdéséhez.

   [![A Time Series Insights környezetet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Ellenőrizze a **értesítések** panel figyelése a központi telepítés befejezését.  

   [![Time Series Insights környezetet üzembe helyezés sikeres volt](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ezután hozza létre az eszköz szimulálása megoldást, amely a Tesztadatok feltöltése a Time Series Insights-környezetet hoz létre:

1. Egy külön ablakban vagy a lapot, lépjen a [azureiotsolutions.com](https://www.azureiotsolutions.com). Jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal, és válassza ki a **Eszközszimuláció** gyorsító.

   [![Futtassa az Eszközszimuláció megoldástámogató](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Adja meg a szükséges paramétereket a a **Eszközszimuláció létrehozása megoldás** lapot.

   Paraméter|Leírás
   ---|---
   **Megoldás neve** | Egy egyedi érték, amelyet a rendszer új erőforráscsoportok létrehozásához használ. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Előfizetés** | Adja meg a Time Series Insights-környezet, az előző szakaszban, a létrehozásához használt ugyanahhoz az előfizetéshez.
   **Régió** | Adja meg a Time Series Insights-környezet, az előző szakaszban, a létrehozásához használt ugyanabban a régióban.
   **Választható Azure-erőforrások üzembe helyezése** | Hagyja bejelölve az **IoT Hub** lehetőséget, mivel a szimulált eszközök ezt használják majd az adatok csatlakoztatásához/streameléséhez.

   Ha befejezte, kattintson a **megoldás létrehozása** a megoldás az Azure-erőforrások kiépítése. Ez a folyamat befejezéséhez 6 – 7 perc is eltarthat.

   [![Az eszköz szimulálása megoldás kiépítése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Miután a kiépítés befejeződött, a szöveg felett az új megoldásokat változik **kiépítési...**  való **készen**:

   >[!IMPORTANT]
   > Még ne kattintson az **Indítás** gombra! Ne zárja be ezt a weboldalt, mivel később még visszatérünk ide.

   [![Eszköz szimulálása megoldás teljes körű kiépítés](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Most lépjen vissza az Azure Portalra, és vizsgálja meg az előfizetésben újonnan létrehozott erőforrásokat. A portál **Erőforráscsoportok** oldalán láthatja, hogy létrejött egy új erőforráscsoport az előző lépésben megadott **megoldásnévvel**. Ugyanitt láthatja az eszközszimulációs megoldás támogatására létrehozott összes erőforrást:

   [![Az eszközszimulációs megoldás erőforrásai](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>A környezet csatlakoztatása az IoT hubhoz

Mostanra elsajátította, hogyan hozhat létre két erőforráskészletet, mindegyiket a maga erőforráscsoportjában:

- Üres a Time Series Insights-környezet.
- Eszköz szimulálása megoldás erőforrások, például egy IoT hubot, megoldásgyorsítók által generált.

Emlékezzünk vissza, hogy a szimulált eszközöknek egy IoT Hubra kell csatlakozniuk eszközadatok streameléséhez. Az adatok áramlását a Time Series Insights-környezetbe, szüksége az IoT hub és a Time Series Insights-környezet konfigurációs módosításokat hajthat végre.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub konfigurálása: fogyasztói csoport definiálása

Az IoT Hub különböző végpontokat biztosít a funkciók más aktorokkal való megosztásához. Az „Események” végpont révén más alkalmazások használhatják a valamely IoT Hub-példányból streamelt adatokat. A „fogyasztói csoportok” egy mechanizmust biztosítanak, amelynek segítségével az alkalmazások figyelhetik és lehívhatják az IoT Hub adatait.

Ezután megadhat egy új **fogyasztói csoportot** tulajdonság, az eszköz szimulálása megoldás az IoT hub **események végpont**.

1. Az Azure Portalon lépjen az eszközszimulációs megoldáshoz létrehozott erőforráscsoport **Áttekintés** oldalára, majd válassza ki az IoT Hub erőforrást:

   [![Az eszközszimulációs megoldás erőforráscsoportja](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Jegyezze is fel a megoldáshoz létrehozott IoT Hub erőforrás **Nevét**. mivel később erre még hivatkoznia kell majd.

1. Görgessen le, és válassza a **Végpontok** oldalt, majd az **Események** végpontot. A végpont **Tulajdonságok** oldalán adjon meg egy egyedi nevet a végpont számára a „$Default” fogyasztói csoport alatt, majd kattintson a **Mentés** gombra:

   [![Eszközszimulációs megoldás IoT Hub-végpontjai](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Környezet konfigurációjának: eseményforrás definiálása

Most már csatlakoztatható az új IoT hub **fogyasztói csoportot** esemény végpont, a Time Series Insights-környezethez, mint egy **eseményforrás**.

1. Nyissa meg a **áttekintése** oldalon az erőforráscsoport, a Time Series Insights-környezethez létrehozott, majd válassza ki a Time Series Insights környezetet:

   [![Time Series Insights-környezet erőforráscsoport és a környezet](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. A Time Series Insights-környezet oldalán válassza **eseményforrások**, majd kattintson a **+ Hozzáadás**.

   [![Time Series Insights-környezet áttekintése](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Adja meg a szükséges paramétereket a a **új eseményforrás** lapot.

   Paraméter|Leírás
   ---|---
   **Eseményforrás neve** | Itt egyedi értéket kell megadni, amely az eseményforrás neveként szolgál majd.
   **Forrás** | Válassza ki **az IoT Hub**.
   **Importálási beállítás** | Válassza az alapértelmezett `Use IoT hub from available subscriptions`. Ennek a beállításnak a megadásával a következő legördülő listában az elérhető előfizetések jelennek meg.
   **Előfizetés** | Válassza ki, amelyben létrehozta a Time Series Insights-környezet és az Eszközszimuláció erőforrások ugyanahhoz az előfizetéshez.
   **IoT Hub neve** | Ennek alapértelmezés szerint egyeznie kell az IoT Hub korábban feljegyzett nevével. Ha nem, válassza ki a megfelelő IoT Hubot.
   **Iot Hub szabályzatneve** | Válassza ki **iothubowner**.
   **IoT Hub fogyasztói csoport** | Ennek alapértelmezés szerint a korábban létrehozott IoT Hub fogyasztói csoport nevével kell egyeznie. Ha nem, válassza ki a megfelelő fogyasztói csoport nevét.
   **Eseményszerializációs formátum** | Hagyja meg az kilistázott alapértelmezett értéke `JSON`.
   **Időbélyeg-tulajdonság neve** | Adja meg a `timestamp`.

   Miután végzett, kattintson a **Létrehozás** gombra az eseményforrás hozzáadásához. Amikor visszatér az erőforráscsoport **áttekintése** lapon a Time Series Insights-környezet erőforrás, valamint egy új "Time Series Insights-eseményforrás" erőforrás megjelenik.

   [![Time Series Insights környezetet új eseményforrás](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Adatok streamelése az eszközszimuláció futtatása

Most, hogy az összes konfigurációs munkát befejeződése után eljött az idő a Time Series Insights környezetet a szimulált eszközök a mintaadatok feltöltése.

Emlékezhet még, hogy az [Eszközszimuláció létrehozása](#create-a-device-simulation) szakaszban a gyorsító több Azure-erőforrást is létrehozott a megoldás támogatására. A korábban tárgyalt IoT Hubbal együtt egy Azure App Service-webalkalmazás is létrejött a szimulált eszköztelemetria létrehozására és továbbítására.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Szükség esetén jelentkezzen be újra, ugyanazzal az Azure-fiókkal, amelyet ebben az oktatóanyagban használt. Most kattintson a **indítsa el a** gombra a "Eszközszimuláció" megoldás alatt található.

     [![Megoldás megoldásgyorsítók irányítópultja](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Ekkor az eszközszimulációs webalkalmazás elindul, és a kezdeti betöltése néhány másodpercet is igénybe vehet. A rendszer hozzájárulást kér, hogy a webalkalmazás jogosult legyen beléptetni Önt és olvasni a profilját. Ez az engedély lehetővé teszi, hogy az alkalmazás a felhasználói profil adatait az alkalmazás működéséhez szükséges.

     [![Eszköz szimulálása webes alkalmazás jóváhagyásának](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Egyszer a **szimuláció telepítő** terhelések lap, adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Cél IoT Hub** | Válassza ki **használja az előzetesen kiépített az IoT Hub**.
   **Eszközmodell** | Válassza ki **hűtő**.
   **Eszközök száma**  | Adja meg `1000` alatt **összeg**.
   **Telemetria gyakorisága** | Adja meg `10` másodperc.
   **Szimuláció időtartama** | Válassza ki **végződhet:** , és adja meg `5` perc.

   Ha végzett, kattintson a **Szimuláció indítása** gombra. A szimuláció összesen 5 percig fut, és 1000 szimulált eszközről származó adatokat állít elő minden 10 másodpercben.  

   [![Eszköz szimulálása beállítása](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. A szimuláció futtatása közben láthatja, ahogy az **Összes üzenet** és az **Üzenetek másodpercenkénti száma** mezők nagyjából 10 másodpercenként frissülnek. A szimuláció nagyjából 5 perc elteltével befejeződik, és a rendszer visszalépteti a **Szimuláció beállítása** oldalra.

   [![Eszközszimuláció fut](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>A telemetriaadatok ellenőrzése

Az utolsó szakaszban, győződjön meg arról, hogy a telemetriai adatokat jön létre, és tárolja a Time Series Insights-környezet. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Térjen vissza a Time Series Insights-környezet erőforráscsoport **áttekintése** lapot. Válassza ki a Time Series Insights-környezetet.

   [![Time Series Insights-környezet erőforráscsoport és a környezet](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. A Time Series Insights környezetet **áttekintése** lap, kattintson a **Time Series Insights explorer URL-cím** a Time Series Insights explorer megnyitásához.

   [![Time Series Insights explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights explorer betölti és az Azure portal fiók használatával hitelesíteni. Kezdeti megtekintése után láthatja a diagramterületen, hogy a Time Series Insights-környezet valóban lett-e töltve a szimulált telemetriai adatokat. Ha szeretné szűkíteni az időtartamot, kattintson a legördülő listára a bal felső sarokban. Ezután adjon meg egy elég tág időtartamot, amely az eszközszimuláció teljes időtartamát lefedi. Ezután kattintson a keresés nagyítása osztály.

   [![Idő Series Insights explorer idő dátumtartomány-szűrő](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. A időtartományát szűkítheti lehetővé teszi, hogy az adatforgalom, az IoT hub és a Time Series Insights-környezet különböző adatlöketekkel Nagyítás a diagramot. A jobb felső sarokban lévő **A streamelés befejeződött** felirat a talált események számát mutatja. Is húzhatja a **intervallumának mérete** csúszka vezérlőelemet a diagram granularitási a diagramra.

   [![Time Series Insights explorer időtartomány szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Végül, akkor is is bal kattintással egy régiót, és számos szűréséhez, majd kattintson a jobb gombbal, és használja **események tallózása** esemény részleteinek megjelenítéséhez a táblázatos **események** megtekintése.

   [![Time Series Insights explorer időtartomány szűrt nézet és az események](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban létrehoz több futó Azure-szolgáltatások, a Time Series Insights környezetet és az eszköz szimulálása megoldás támogatja. Ha félbe szeretné hagyni és/vagy később fejezné be ezt az oktatóanyag-sorozatot, javasoljuk, hogy a felesleges költségek elkerülése érdekében törölje az összes erőforrást.

Az Azure Portal bal oldali menüjében:

1. Kattintson a **erőforráscsoportok** ikonra, majd válassza ki a Time Series Insights-környezetet a létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra.

1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki az eszközszimulációs megoldásgyorsító által létrehozott erőforráscsoportot. Kattintson a lap tetején **erőforráscsoport törlése**, adja meg az erőforráscsoport nevét, majd kattintson a **törlése**

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Time Series Insights-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * A Time Series Insights-környezet csatlakoztatása az IoT hubhoz
> * Egy eszköz szimuláció futtatása az adatok a Time Series Insights-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

Most, hogy tudja, hogyan hozhat létre a saját Time Series Insights-környezet, megtudhatja, hogyan hozhat létre egy webalkalmazást, amely egy Time Series Insights-környezetből származó adatokat használ fel:

> [!div class="nextstepaction"]
> [Azure Time Series Insights egyoldalas webalkalmazás létrehozása](tutorial-create-tsi-sample-spa.md)
