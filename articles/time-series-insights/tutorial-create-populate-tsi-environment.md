---
title: 'Oktatóanyag: Környezet létrehozása – Azure Time Series Insights | Microsoft dokumentumok'
description: Ismerje meg, hogyan hozhat létre egy Time Series Insights-környezetet, amely szimulált eszközökről származó adatokkal van feltöltve.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76981191"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Ez az oktatóanyag végigvezeti az Azure Time Series Insights-környezet létrehozásának folyamatán, amely szimulált eszközökről származó adatokkal van feltöltve. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Time Series Insights-környezetet.
> * Hozzon létre egy IOt-központot tartalmazó eszközszimulációs megoldást.
> * Csatlakoztassa a Time Series Insights-környezetet az IoT hubhoz.
> * Futtasson egy eszközszimulációt az adatok idősorozat-elemzési környezetbe való streameléséhez.
> * Ellenőrizze a szimulált telemetriai adatokat.

> [!IMPORTANT]
> Ha még nem rendelkezik [ingyenes Azure-előfizetéssel,](https://azure.microsoft.com/free/) regisztráljon.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure-bejelentkezési fióknak is az előfizetés **tulajdonosi** szerepkörének tagjának kell lennie. További információért olvassa el [a Hozzáférés kezelése szerepköralapú hozzáférés-vezérlés sel és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)című olvasni.

## <a name="review-video"></a>Videó áttekintése

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Ismerje meg, hogyan használhatja az Azure IoT-megoldásgyorsítót az adatok létrehozásához és a Time Series Insights használatának megkezdéséhez. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Áttekintés

A Time Series Insights környezetben gyűjtik és tárolják az eszközadatokat. A tárolás után az [Azure Time Series Insights explorer](time-series-quickstart.md) és a Time Series Insights Query [API](/rest/api/time-series-insights/ga-query-api) az adatok lekérdezésére és elemzésére használható.

Az Azure IoT Hub az az eseményforrás, amelyet az összes eszköz (szimulált vagy fizikai) az oktatóanyagban használ az Azure-felhőbe való biztonságos csatlakoztatáshoz és továbbításhoz.

Ez az oktatóanyag egy [IoT-megoldásgyorsítót](https://www.azureiotsolutions.com) is használ a minta telemetriai adatok létrehozásához és streameléséhez az IoT Hubba.

>[!TIP]
> [Az IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) nagyvállalati szintű, előre konfigurált megoldásokat kínálnak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Először hozza létre az eszközszimulációs megoldást, amely tesztadatokat hoz létre a Time Series Insights-környezet feltöltéséhez.

1. Egy külön ablakban vagy lapon nyissa meg [a azureiotsolutions.com.](https://www.azureiotsolutions.com) Jelentkezzen be ugyanazzal az Azure-előfizetési fiókkal, és válassza ki az **eszközszimulációgyorsítót.**

   [![Az Eszközszimuláció gyorsító futtatása](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Válassza a **Próbálja ki most**lehetőséget. Ezután adja meg a szükséges paramétereket az **Eszközszimuláció létrehozása megoldás** lapon.

   Paraméter|Leírás
   ---|---
   **Központi telepítés neve** | Ez az egyedi érték új erőforráscsoport létrehozásához használatos. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Azure-előfizetés** | Adja meg ugyanazt az előfizetést, amely et az előző szakaszban a Time Series Insights-környezet létrehozásához használt.
   **Telepítési lehetőségek** | Válassza **az új IoT Hub kiépítése** lehetőséget az oktatóanyagra jellemző új IoT-központ létrehozásához.
   **Az Azure helye** | Adja meg ugyanazt a régiót, amely et használt a Time Series Insights-környezet létrehozásához az előző szakaszban.

   Ha elkészült, válassza **a Create lehetőséget** a megoldás Azure-erőforrásainak kiépítéséhez. A folyamat akár 20 percet is igénybe vehet.

   [![Az eszközszimulációs megoldás üzembe helyezése](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. A kiépítés befejezése után két frissítés jelenik meg, amelyek értesítik, hogy a központi telepítési állapot a **kiépítésről** **a Kész állapotra**vált. 

   >[!IMPORTANT]
   > Még ne adja meg a megoldásgyorsítót! Tartsa nyitva ezt a weboldalt, mert később visszatér hozzá.

   [![Az eszközszimulációs megoldás üzembe helyezése befejeződött](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat az Azure Portalon. Az **Erőforráscsoportok** lapon figyelje meg, hogy egy új erőforráscsoportot hoztak létre az utolsó lépésben megadott **Megoldás név** használatával. Jegyezze fel az eszközszimulációhoz létrehozott erőforrásokat.

   [![Eszközszimulációs erőforrások](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Környezet létrehozása

Másodszor hozzon létre egy Time Series Insights-környezetet az Azure-előfizetésében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetési fiókjával. 
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban. 
1. Jelölje ki a **Dolgok internete kategóriát,** majd a **Time Series Insights**lehetőséget. 

   [![Válassza ki a Time Series Insights környezeti erőforrást](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. A **Time Series Insights környezetben** lapon töltse ki a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válasszon egyedi nevet a Time Series Insights környezethez. A neveket a Time Series Insights-kezelő és a [Lekérdezési API-k](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)használják.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válasszon egy előfizetést a Time Series Insights környezet létrehozásához.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a Time Series Insights környezeti erőforráshoz.
   **Helyen** | Válasszon egy adatközpont-régiót a Time Series Insights-környezethez. További késés elkerülése érdekében hozza létre a Time Series Insights-környezetet ugyanabban a régióban, mint más IoT-erőforrások.
   **Szint** | Válassza ki a szükséges teljesítményt. Válassza az **S1 lehetőséget.**
   **Kapacitás** | A kapacitás a kiválasztott termékváltozathoz társított be- és tárolási kapacitásra alkalmazott szorzó. A kapacitás a létrehozás után módosítható. **Válasszon 1**kapacitást.

   Ha végzett, válassza a **Tovább: Eseményforrása lehetőséget** a következő lépéssel.

   [![Time Series Insights-környezet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Most csatlakoztassa a Time Series Insights-környezetet a megoldásgyorsító által létrehozott IoT hub. Állítsa be a `Select existing`Hub **kiválasztása** lehetőséget. Ezután válassza ki a megoldásgyorsító által létrehozott IoT hubot az **IoT Hub nevének**beállításakor.

   [![A Time Series Insights-környezet csatlakoztatása a létrehozott IoT-központhoz](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Végül válassza **a Véleményezés + létrehozás**lehetőséget.

1. Ellenőrizze az **Értesítések** panelen a telepítés befejezésének figyeléséhez. 

   [![Time Series Insights-környezet sikeresen üzembe helyezve](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Eszközszimuláció futtatása

Most, hogy a központi telepítés és a kezdeti konfiguráció befejeződött, töltse fel a Time Series Insights környezetben a [modell által létrehozott szimulált eszközök](#create-a-device-simulation)mintaadatait.

Az IoT-központtal együtt egy Azure App Service-webalkalmazás jött létre szimulált eszköz telemetriai adatok létrehozásához és továbbításához.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Szükség esetén jelentkezzen be újra az oktatóanyagban használt Azure-fiókhasználatával. Válassza ki a "Device Solution", majd **menj a megoldásgyorsító** elindítani a telepített megoldás.

   [![Megoldásgyorsítók irányítópultja](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Az eszközszimulációs webalkalmazás azzal kezdi, hogy kéri, hogy adja meg a webalkalmazásnak a **Bejelentkezést, és olvassa el a profilengedélyét.** Ez az engedély lehetővé teszi, hogy az alkalmazás lekérje az alkalmazás működéséhez szükséges felhasználói profiladatokat.

   [![Eszközszimulációs webalkalmazás – hozzájárulás](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Válassza **a + Új szimuláció lehetőséget.** A **Szimuláció beállítási** oldalának betöltése után adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Cél IoT Hub** | Válassza **az Előre kiépített IoT Hub használata**lehetőséget.
   **Eszközmodell** | Válassza **a Hűtő**lehetőséget.
   **Eszközök száma**  | Írja `10` be **az Összeg mezőbe.**
   **Telemetria gyakorisága** | Adja `10` meg a másodperceket.
   **Szimuláció időtartama** | Válassza **a Vége: gombot,** és adja meg a `5` jegyzőkönyvet.

   Ha végzett, válassza **a Szimuláció indítása**lehetőséget. A szimuláció összesen 5 percig tart. 10 másodpercenként 1000 szimulált eszközről generál adatokat. 

   [![Eszközszimuláció beállítása](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. A szimuláció futtatása közben figyelje meg, hogy az **Összes üzenet** és **üzenet másodpercenként** mező körülbelül 10 másodpercenként frissül. A szimuláció körülbelül 5 perc után véget ér, és visszatér a **Szimuláció beállításához.**

   [![Eszközszimuláció futtatása](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>A telemetriaadatok ellenőrzése

Ebben az utolsó szakaszban ellenőrzi, hogy a telemetriai adatok at a Time Series Insights környezetben hozták létre és tárolták. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Térjen vissza a Time Series Insights környezet **erőforráscsoportjának áttekintése** lapra. Válassza ki a Time Series Insights környezetben.

   [![Time Series Insights környezeti erőforráscsoport és környezet](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. A Time Series Insights környezet **áttekintése** lapon válassza ki a **Time Series Insights intéző URL-címét** a Time Series Insights-kezelő megnyitásához.

   [![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. A Time Series Insights-kezelő betölti és hitelesíti magát az Azure Portal-fiók használatával. Kezdetben a diagramterület, amely a Time Series Insights környezetben volt feltöltve együtt a szimulált telemetriai adatok jelennek meg. Szűkebb időtartomány szűréséhez jelölje ki a legördülő menüt a bal felső sarokban. Adjon meg egy olyan időtartományt, amely elég nagy ahhoz, hogy átfogja az eszközszimuláció időtartamát. Ezután válassza ki a keresőnagyítót.

   [![A Time Series Insights-kezelő időtartomány-szűrője](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Az időtartomány szűkítése lehetővé teszi, hogy a diagram nagyítsa ki az IoT hubra és a Time Series Insights környezetbe irányuló adatátvitel különböző sorozatait. Is észre a **Streamelés teljes** szöveget a jobb felső sarokban, amely megmutatja a teljes események száma található. Az **Intervallumméret** csúszkával is szabályozhatja a diagram részletességét.

   [![A Time Series Insights-kezelő időtartományának szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Végül a tartomány szűréséhez bal gombbal is kattinthat egy területre. Ezután kattintson a jobb gombbal, és használja **az Események feltárása** segítségével az esemény részleteinek megjelenítéséhez a táblázatos **események** nézetben.

   [![A Time Series Insights explorer időtartományának szűrt nézete és eseményei](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag számos futó Azure-szolgáltatást hoz létre a Time Series Insights környezet és az eszközszimulációs megoldás támogatásához. Ezek eltávolításához keresse vissza az Azure Portalon.

Az Azure Portal bal oldali menüjéből:

1. Válassza az **Erőforráscsoportok** ikont. Ezután válassza ki a Time Series Insights környezethez létrehozott erőforráscsoportot. A lap tetején válassza az **Erőforráscsoport törlése**lehetőséget, írja be az erőforráscsoport nevét, és válassza a **Törlés gombot.**

1. Válassza az **Erőforráscsoportok** ikont. Ezután válassza ki az eszközszimulációs megoldásgyorsító által létrehozott erőforráscsoportot. A lap tetején válassza az **Erőforráscsoport törlése**lehetőséget, írja be az erőforráscsoport nevét, és válassza a **Törlés gombot.**

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Time Series Insights-környezetet.
> * Hozzon létre egy IOt-központot tartalmazó eszközszimulációs megoldást.
> * Csatlakoztassa a Time Series Insights-környezetet az IoT hubhoz.
> * Futtasson egy eszközszimulációt az adatok idősorozat-elemzési környezetbe való streameléséhez.
> * Ellenőrizze a szimulált telemetriai adatokat.

Most, hogy már tudja, hogyan hozhat létre saját Time Series Insights-környezetet, ismerje meg, hogyan hozhat létre olyan webalkalmazást, amely adatokat használ fel egy Time Series Insights-környezetből:

> [!div class="nextstepaction"]
> [Üzemeltetett ügyfél SDK-vizualizációs minták olvasása](https://tsiclientsample.azurewebsites.net/)
