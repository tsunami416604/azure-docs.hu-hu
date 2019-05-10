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
ms.openlocfilehash: 42a7ba0c66bd603b19d60c7b3407ae5ca80db28e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209879"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Az oktatóanyag végigvezeti a szimulált eszközök adataival feltöltött Time Series Insights- (TSI-) környezetek létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * TSI-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * Az IoT Hub és a TSI-környezet összekapcsolása
> * Eszközszimuláció futtatása adatok streamelésére a TSI-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

## <a name="video"></a>Videó

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Megtudhatja, hogyan hozhat létre adatokat, és a Time Series Insights használatának első lépései az Azure IoT-Megoldásgyorsítók használatával. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Az Azure bejelentkezési fiókot is kell lennie az előfizetéshez tartozó tagja **tulajdonosa** szerepkör. Lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal) további részletekért.

## <a name="overview"></a>Áttekintés

Az eszközadatok gyűjtése és tárolása a TSI-környezetben történik. A TSI környezetben tárolt a [TSI Explorer](time-series-quickstart.md) és [TSI Query API](/rest/api/time-series-insights/ga-query-api) lekérdezését és elemzését az adatok segítségével. Az IoT Hub biztonságos kapcsolódást és küldi az adatokat az Azure-felhő minden eszköz (szimulált vagy fizikai) által használt csatlakozási pont. A [TSI áttekintése](time-series-insights-overview.md) tudomásul veszi, hogy az Azure IoT Hub is szolgál eseményforrás streamelési adatok a TSI-környezetbe. Ebben az oktatóanyagban egy [IoT-megoldásgyorsítók](/azure/iot-accelerators/) hozhat létre és adatfolyam-minta telemetriai adatokat az IoT hubnak.

>[!TIP]
> IoT-megoldásgyorsítók adja meg a nagyvállalati szintű, előre konfigurált megoldások egyéni IoT-megoldások fejlesztésének felgyorsítását lehetővé.

## <a name="create-a-tsi-environment"></a>TSI-környezet létrehozása

Először is hozzon létre egy TSI-környezetet az Azure-előfizetésében:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetése fiókjával.  
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban.  
1. Válassza az **Eszközök internetes hálózata** kategóriát, majd a **Time Series Insights** elemet.  

   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. A **Time Series Insights-környezet** oldalon adja meg a szükséges paramétereket:

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válasszon egy egyedi nevet a TSI-környezet számára. A nevet használja a TSI Explorerben és a lekérdezési API-k.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést hozhat létre a TSI-környezetből.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a TSI-környezet erőforrás.
   **Hely** | Válasszon ki egy adatközpont-régiót a TSI-környezet számára. A sávszélességgel kapcsolatos többletköltségek és a késés növekedésének elkerülése érdekében érdemes a TSI-környezetet a többi IoT-erőforrással azonos régióban futtatni.
   **Díjszabási termékváltozat** | Válassza ki a szükséges teljesítményt. A legalacsonyabb költségek és az alapszintű kapacitás, válassza ki a `S1`.
   **Kapacitás** | A kapacitás a kiválasztott SKU bejövő forgalmi sebességére, tárkapacitására és költségeire alkalmazott szorzó.  Létrehozása után módosíthatja a kapacitást. Válassza ki a legalacsonyabb költséget 1 kapacitását.

   Miután végzett, kattintson a **Létrehozás** gombra az üzembe helyezés megkezdéséhez.

   [![A Time Series Insights környezetet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Ellenőrizze a **értesítések** panel figyelése a központi telepítés befejezését.  

   [![Time Series Insights környezetet üzembe helyezés sikeres volt](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ezután hozza létre az eszközszimulációs megoldást, amely előállítja majd a tesztadatokat a TSI-környezet feltöltéséhez:

1. Egy külön ablakban vagy a lapot, lépjen a [azureiotsolutions.com](https://www.azureiotsolutions.com). Jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal, és válassza ki a **Eszközszimuláció** gyorsító.

   [![Futtassa az Eszközszimuláció megoldástámogató](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Adja meg a szükséges paramétereket a a **Eszközszimuláció létrehozása megoldás** lapot.

   Paraméter|Leírás
   ---|---
   **Megoldás neve** | Egy egyedi érték, amelyet a rendszer új erőforráscsoportok létrehozásához használ. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Előfizetés** | Adja meg az előző szakaszban a TSI-környezet létrehozásához használt előfizetést.
   **Régió** | Adja meg az előző szakaszban a TSI-környezet létrehozásához használt régiót.
   **Választható Azure-erőforrások üzembe helyezése** | Hagyja bejelölve az **IoT Hub** lehetőséget, mivel a szimulált eszközök ezt használják majd az adatok csatlakoztatásához/streameléséhez.

   Ha befejezte, kattintson a **megoldás létrehozása** a megoldás az Azure-erőforrások kiépítése. Ez a folyamat befejezéséhez 6 – 7 perc is eltarthat.

   [![Az eszköz szimulálása megoldás kiépítése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Miután a kiépítés befejeződött, a szöveg felett az új megoldásokat változik **kiépítési...**  való **készen**:

   >[!IMPORTANT]
   > Még ne kattintson az **Indítás** gombra! Ne zárja be ezt a weboldalt, mivel később még visszatérünk ide.

   [![Eszköz szimulálása megoldás teljes körű kiépítés](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Most lépjen vissza az Azure Portalra, és vizsgálja meg az előfizetésben újonnan létrehozott erőforrásokat. A portál **Erőforráscsoportok** oldalán láthatja, hogy létrejött egy új erőforráscsoport az előző lépésben megadott **megoldásnévvel**. Ugyanitt láthatja az eszközszimulációs megoldás támogatására létrehozott összes erőforrást:

   [![Az eszközszimulációs megoldás erőforrásai](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Az IoT Hub és a TSI-környezet összekapcsolása

Mostanra elsajátította, hogyan hozhat létre két erőforráskészletet, mindegyiket a maga erőforráscsoportjában:

- Egy üres TSI-környezetben.
- Eszköz szimulálása megoldás erőforrások, például egy IoT hubot, megoldásgyorsítók által generált.

Emlékezzünk vissza, hogy a szimulált eszközöknek egy IoT Hubra kell csatlakozniuk eszközadatok streameléséhez. Az adatok a TSI-környezetbe való áramoltatásához módosítania kell az IoT Hub és a TSI-környezet konfigurációját.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub konfigurálása: fogyasztói csoport definiálása

Az IoT Hub különböző végpontokat biztosít a funkciók más aktorokkal való megosztásához. Az „Események” végpont révén más alkalmazások használhatják a valamely IoT Hub-példányból streamelt adatokat. A „fogyasztói csoportok” egy mechanizmust biztosítanak, amelynek segítségével az alkalmazások figyelhetik és lehívhatják az IoT Hub adatait.

Ezután megadhat egy új **fogyasztói csoportot** tulajdonság, az eszköz szimulálása megoldás az IoT hub **események végpont**.

1. Az Azure Portalon lépjen az eszközszimulációs megoldáshoz létrehozott erőforráscsoport **Áttekintés** oldalára, majd válassza ki az IoT Hub erőforrást:

   [![Az eszközszimulációs megoldás erőforráscsoportja](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Jegyezze is fel a megoldáshoz létrehozott IoT Hub erőforrás **Nevét**. mivel később erre még hivatkoznia kell majd.

1. Görgessen le, és válassza a **Végpontok** oldalt, majd az **Események** végpontot. A végpont **Tulajdonságok** oldalán adjon meg egy egyedi nevet a végpont számára a „$Default” fogyasztói csoport alatt, majd kattintson a **Mentés** gombra:

   [![Eszközszimulációs megoldás IoT Hub-végpontjai](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-configuration-define-an-event-source"></a>A TSI-konfiguráció: eseményforrás definiálása

Most már csatlakoztatható az új IoT hub **fogyasztói csoportot** esemény végpont, a TSI-környezethez, mint egy **eseményforrás**.

1. Lépjen a TSI-környezethez létrehozott erőforráscsoport **Áttekintés** oldalára, majd válassza ki a TSI-környezetet:

   [![TSI-környezet erőforráscsoportja és környezete](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. A TSI környezet oldalon válassza ki a **eseményforrások**, majd kattintson a **+ Hozzáadás**.

   [![A TSI a környezet áttekintése](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Adja meg a szükséges paramétereket a a **új eseményforrás** lapot.

   Paraméter|Leírás
   ---|---
   **Eseményforrás neve** | Itt egyedi értéket kell megadni, amely az eseményforrás neveként szolgál majd.
   **Forrás** | Válassza ki **az IoT Hub**.
   **Importálási beállítás** | Válassza az alapértelmezett `Use IoT hub from available subscriptions`. Ennek a beállításnak a megadásával a következő legördülő listában az elérhető előfizetések jelennek meg.
   **Előfizetés** | Válassza ki ugyanazt az Azure-előfizetést, amelyikben a TSI-környezetet és az eszközszimulációs erőforrásokat létrehozta.
   **IoT Hub neve** | Ennek alapértelmezés szerint egyeznie kell az IoT Hub korábban feljegyzett nevével. Ha nem, válassza ki a megfelelő IoT Hubot.
   **Iot Hub szabályzatneve** | Válassza ki **iothubowner**.
   **IoT Hub fogyasztói csoport** | Ennek alapértelmezés szerint a korábban létrehozott IoT Hub fogyasztói csoport nevével kell egyeznie. Ha nem, válassza ki a megfelelő fogyasztói csoport nevét.
   **Eseményszerializációs formátum** | Hagyja meg az kilistázott alapértelmezett értéke `JSON`.
   **Időbélyeg-tulajdonság neve** | Adja meg a `timestamp`.

   Miután végzett, kattintson a **Létrehozás** gombra az eseményforrás hozzáadásához. Miután visszatért az erőforráscsoport **Áttekintés** oldalára, a TSI-környezet erőforrás mellett az új "Time Series Insights-eseményforrás" erőforrás is megjelenik.

   [![A TSI környezet új forrás](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data-into-tsi"></a>Eszköz szimuláció futtatása az adatok a TSI-be

Most, hogy végzett az összes konfigurációs feladattal, ideje feltölteni a TSI-környezetet a szimulált eszközökről származó mintaadatokkal.

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

Ebben az utolsó szakaszban ellenőrizheti, hogy a telemetriaadatok létrejöttek-e és tárolva lettek-e a TSI-környezetben. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Térjen vissza a TSI-környezet erőforráscsoport **áttekintése** lapot. Válassza ki a TSI-környezetet.

   [![TSI-környezet erőforráscsoportja és környezete](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. A TSI környezetre **áttekintése** lap, kattintson a **Time Series Insights explorer URL-cím** a TSI explorer megnyitásához.

   [![TSI Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. A TSI Explorer betöltődik, és hitelesítést végez az Ön Azure Portal-fiókjával. Az első megtekintéskor a diagramterületen látható, hogy a TSI-környezet valóban fel volt töltve szimulált telemetriaadatokkal. Ha szeretné szűkíteni az időtartamot, kattintson a legördülő listára a bal felső sarokban. Ezután adjon meg egy elég tág időtartamot, amely az eszközszimuláció teljes időtartamát lefedi. Ezután kattintson a keresés nagyítása osztály.

   [![TSI Explorer időtartamszűrője](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Az időtartam szűkítésével a diagram fókuszálható az egyes adatátviteli csúcsokra – mind az IoT Hub, mind a TSI-környezet irányában. A jobb felső sarokban lévő **A streamelés befejeződött** felirat a talált események számát mutatja. Is húzhatja a **intervallumának mérete** csúszka vezérlőelemet a diagram granularitási a diagramra.

   [![TSI Explorer időtartamra szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Végül, akkor is is bal kattintással egy régiót, és számos szűréséhez, majd kattintson a jobb gombbal, és használja **események tallózása** esemény részleteinek megjelenítéséhez a táblázatos **események** megtekintése.

   [![TSI Explorer időtartamra szűrt nézete és eseményei](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban több futó Azure-szolgáltatást hoztunk létre a TSI-környezet és az eszközszimulációs megoldás támogatására. Ha félbe szeretné hagyni és/vagy később fejezné be ezt az oktatóanyag-sorozatot, javasoljuk, hogy a felesleges költségek elkerülése érdekében törölje az összes erőforrást.

Az Azure Portal bal oldali menüjében:

1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki a TSI-környezethez létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra.

1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki az eszközszimulációs megoldásgyorsító által létrehozott erőforráscsoportot. Kattintson a lap tetején **erőforráscsoport törlése**, adja meg az erőforráscsoport nevét, majd kattintson a **törlése**

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * TSI-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * Az IoT Hub és a TSI-környezet összekapcsolása
> * Eszközszimuláció futtatása adatok streamelésére a TSI-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

Most, hogy elsajátította, hogyan hozhat létre saját TSI-környezetet, megtudhatja, hogyan készíthet olyan webalkalmazást, amely a TSI-környezet adatait használja:

> [!div class="nextstepaction"]
> [Azure Time Series Insights egyoldalas webalkalmazás létrehozása](tutorial-create-tsi-sample-spa.md)