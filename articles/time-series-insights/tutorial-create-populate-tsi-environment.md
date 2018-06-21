---
title: Azure Time Series Insights-környezet létrehozása
description: Ismerje meg, hogyan hozhat létre szimulált eszközök adataival feltöltött Time Series Insights-környezeteket.
services: time-series-insights
author: BryanLa
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: bryanla
ms.openlocfilehash: 434f9f7890b909746fd1a0f72a2dea5d7bb27e92
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301354"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Oktatóanyag: Azure Time Series Insights-környezet létrehozása

Az oktatóanyag végigvezeti a szimulált eszközök adataival feltöltött Time Series Insights- (TSI-) környezetek létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * TSI-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * Az IoT Hub és a TSI-környezet összekapcsolása
> * Eszközszimuláció futtatása adatok streamelésére a TSI-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

Bejelentkezett Azure-fiókjának szintén az előfizetés tulajdonosi szerepköre tagjának kell lennie. További részletek: [Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása](/azure/billing/billing-add-change-azure-subscription-administrator)

## <a name="overview"></a>Áttekintés

Az eszközadatok gyűjtése és tárolása a TSI-környezetben történik. Az adatokat a TSI-környezetben való tárolásukat követően a [TSI Explorer](time-series-quickstart.md) és a [TSI-lekérdezési API](/rest/api/time-series-insights/time-series-insights-reference-queryapi) használatával kérdezheti le és elemezheti.

Mint minden – szimulált vagy fizikai – eszköz, az IoT Hub is egy, az eszközök által az Azure-felhőhöz való biztonságos csatlakozásra és az adatok továbbítására szolgáló csatlakozási pont. A [TSI áttekintésével](time-series-insights-overview.md) kapcsolatos szakaszban leírtak szerint az IoT Hub eseményforrásként is szolgál az adatok a TSI-környezetbe való streameléséhez. 

Az oktatóanyagban egy [IoT-megoldásgyorsítót](/azure/iot-accelerators/) is alkalmazunk a minta telemetriaadatok létrehozására és az IoT Hubba való streamelésére. Az IoT-megoldásgyorsítók előre konfigurált, vállalati szintű megoldásokat biztosítanak, amelyek lehetővé teszik az egyéni IoT-megoldások fejlesztésének felgyorsítását. 

## <a name="create-a-tsi-environment"></a>TSI-környezet létrehozása

Először is hozzon létre egy TSI-környezetet az Azure-előfizetésében:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetése fiókjával.  
2. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban.  
3. Válassza az **Eszközök internetes hálózata** kategóriát, majd a **Time Series Insights** elemet.  
   
   [![A Time Series Insights-környezet erőforrás kiválasztása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. A **Time Series Insights-környezet** oldalon adja meg a szükséges paramétereket:
   
   Paraméter|Leírás
   ---|---
   **Környezet neve** | Válasszon egy egyedi nevet a TSI-környezet számára. A TSI Explorer és a lekérdezési API ezt a nevet használja.
   **Előfizetés** | Az előfizetés az Azure-erőforrások tárolója. Válassza ki, melyik előfizetésben kívánja létrehozni a TSI-környezetet.
   **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a TSI-környezet erőforrás számára.
   **Hely** | Válasszon ki egy adatközpont-régiót a TSI-környezet számára. A sávszélességgel kapcsolatos többletköltségek és a késés növekedésének elkerülése érdekében érdemes a TSI-környezetet a többi IoT-erőforrással azonos régióban futtatni.
   **Díjszabási termékváltozat** | Válassza ki a szükséges teljesítményt. Az alacsonyabb költségek és az indulókapacitás biztosítása érdekében válassza az S1-et.
   **Kapacitás** | A kapacitás a kiválasztott SKU bejövő forgalmi sebességére, tárkapacitására és költségeire alkalmazott szorzó.  A környezet kapacitása a létrehozást követően módosítható. A lehető legalacsonyabb költségek érdekében válassza az 1-es kapacitást. 

   Miután végzett, kattintson a **Létrehozás** gombra az üzembe helyezés megkezdéséhez.

   ![Time Series Insights-környezet erőforrás létrehozása](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Az **Értesítések** panelen követheti az üzembe helyezés állapotát, amely legfeljebb egy percet vesz igénybe:  

   ![Time Series Insights-környezet sikeresen üzembe helyezve](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ezután hozza létre az eszközszimulációs megoldást, amely előállítja majd a tesztadatokat a TSI-környezet feltöltéséhez:

1. Egy külön ablakban/lapon lépjen a https://www.azureiotsolutions.com helyre, jelentkezzen be ugyanazzal az Azure-előfizetési fiókkal, és válassza ki az „Eszközszimuláció” gyorsítót:

   ![Az Eszközszimuláció gyorsító futtatása](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Adja meg a szükséges paramétereket az **Eszközszimulációs megoldás létrehozása** oldalon:

   Paraméter|Leírás
   ---|---
   **Megoldás neve** | Egy egyedi érték, amelyet a rendszer új erőforráscsoportok létrehozásához használ. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz.
   **Előfizetés** | Adja meg az előző szakaszban a TSI-környezet létrehozásához használt előfizetést.
   **Régió** | Adja meg az előző szakaszban a TSI-környezet létrehozásához használt régiót. 
   **Választható Azure-erőforrások üzembe helyezése** | Hagyja bejelölve az **IoT Hub** lehetőséget, mivel a szimulált eszközök ezt használják majd az adatok csatlakoztatásához/streameléséhez.

   Ha végzett, kattintson a **Megoldás létrehozása** gombra a megoldás Azure-erőforrásainak üzembe helyezéséhez, ami nagyjából 6–7 percet vesz igénybe:

   ![Az eszközszimulációs megoldás üzembe helyezése](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Ha az üzembe helyezés befejeződött, az új megoldás felett az „Üzembe helyezés…” helyett a „Kész” felirat látható:

   >[!IMPORTANT]
   > Még ne kattintson az **Indítás** gombra! Ne zárja be ezt a weboldalt, mivel később még visszatérünk ide.

   ![Az eszközszimulációs megoldás üzembe helyezése befejeződött](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Most lépjen vissza az Azure Portalra, és vizsgálja meg az előfizetésben újonnan létrehozott erőforrásokat. A portál **Erőforráscsoportok** oldalán láthatja, hogy létrejött egy új erőforráscsoport az előző lépésben megadott **megoldásnévvel**. Ugyanitt láthatja az eszközszimulációs megoldás támogatására létrehozott összes erőforrást:

   [![Az eszközszimulációs megoldás erőforrásai](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Az IoT Hub és a TSI-környezet összekapcsolása 

Mostanra elsajátította, hogyan hozhat létre két erőforráskészletet, mindegyiket a maga erőforráscsoportjában: 
- Egy üres TSI-környezet 
- Az eszközszimulációs megoldás megoldásgyorsító által előállított erőforrásai, beleértve egy IoT Hubot

Emlékezzünk vissza, hogy a szimulált eszközöknek egy IoT Hubra kell csatlakozniuk eszközadatok streameléséhez. Az adatok a TSI-környezetbe való áramoltatásához módosítania kell az IoT Hub és a TSI-környezet konfigurációját. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub konfigurálása: fogyasztói csoport definiálása

Az IoT Hub különböző végpontokat biztosít a funkciók más aktorokkal való megosztásához. Az „Események” végpont révén más alkalmazások használhatják a valamely IoT Hub-példányból streamelt adatokat. A „fogyasztói csoportok” egy mechanizmust biztosítanak, amelynek segítségével az alkalmazások figyelhetik és lehívhatják az IoT Hub adatait. 

Ezután definiáljunk egy új „fogyasztói csoport” tulajdonságot az eszközszimulációs megoldás IoT Hubjának „Események” végpontján. 

1. Az Azure Portalon lépjen az eszközszimulációs megoldáshoz létrehozott erőforráscsoport **Áttekintés** oldalára, majd válassza ki az IoT Hub erőforrást:

   [![Az eszközszimulációs megoldás erőforráscsoportja](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Jegyezze is fel a megoldáshoz létrehozott IoT Hub erőforrás **Nevét**. mivel később erre még hivatkoznia kell majd.

2. Görgessen le, és válassza a **Végpontok** oldalt, majd az **Események** végpontot. A végpont **Tulajdonságok** oldalán adjon meg egy egyedi nevet a végpont számára a „$Default” fogyasztói csoport alatt, majd kattintson a **Mentés** gombra:

   [![Eszközszimulációs megoldás IoT Hub-végpontjai](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>TSI-környezet konfigurálása: eseményforrás definiálása

Most csatlakoztassa az új IoT Hub „fogyasztói csoport” esemény végpontját a TSI-környezethez „eseményforrásként”.

1. Lépjen a TSI-környezethez létrehozott erőforráscsoport **Áttekintés** oldalára, majd válassza ki a TSI-környezetet:

   [![TSI-környezet erőforráscsoportja és környezete](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. A TSI-környezet oldalán válassza az **Eseményforrások** elemet, majd kattintson a **+ Hozzáadás** gombra:

   ![TSI-környezet áttekintése](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Adja meg a szükséges paramétereket az **Új eseményforrás** oldalon: 

   Paraméter|Leírás
   ---|---
   **Eseményforrás neve** | Itt egyedi értéket kell megadni, amely az eseményforrás neveként szolgál majd.
   **Forrás** | Válassza az „IoT Hub” lehetőséget.
   **Importálási beállítás** | Használja az alapértelmezett „Az elérhető előfizetések IoT Hubjának használata” beállítást. Ennek a beállításnak a megadásával a következő legördülő listában az elérhető előfizetések jelennek meg.
   **Előfizetés** | Válassza ki ugyanazt az Azure-előfizetést, amelyikben a TSI-környezetet és az eszközszimulációs erőforrásokat létrehozta.
   **IoT Hub neve** | Ennek alapértelmezés szerint egyeznie kell az IoT Hub korábban feljegyzett nevével. Ha nem, válassza ki a megfelelő IoT Hubot.
   **Iot Hub szabályzatneve** | Hagyja meg az alapértelmezett „iothubowner” értéket.
   **IoT Hub fogyasztói csoport** | Ennek alapértelmezés szerint a korábban létrehozott IoT Hub fogyasztói csoport nevével kell egyeznie. Ha nem, válassza ki a megfelelő fogyasztói csoport nevét. 
   **Eseményszerializációs formátum** | Hagyja meg az alapértelmezett „JSON” értéket.
   **Időbélyeg-tulajdonság neve** | Adja meg a „timestamp” értéket.

   Miután végzett, kattintson a **Létrehozás** gombra az eseményforrás hozzáadásához. Miután visszatért az erőforráscsoport **Áttekintés** oldalára, a TSI-környezet erőforrás mellett az új "Time Series Insights-eseményforrás" erőforrás is megjelenik.

   ![TSI-környezet új eseményforrása](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Eszközszimuláció futtatása adatok streamelésére a TSI-környezetbe

Most, hogy végzett az összes konfigurációs feladattal, ideje feltölteni a TSI-környezetet a szimulált eszközökről származó mintaadatokkal.

Emlékezhet még, hogy az [Eszközszimuláció létrehozása](#create-a-device-simulation) szakaszban a gyorsító több Azure-erőforrást is létrehozott a megoldás támogatására. A korábban tárgyalt IoT Hubbal együtt egy Azure App Service-webalkalmazás is létrejött a szimulált eszköztelemetria létrehozására és továbbítására.

1. Lépjen vissza a [megoldásgyorsítók irányítópultjára](https://www.azureiotsolutions.com/Accelerators#dashboard). Szükség esetén jelentkezzen be újra, ugyanazzal az Azure-fiókkal, amelyet ebben az oktatóanyagban használt. Most már az **Indítás** gombra kattinthat az „Eszközszimuláció” megoldás alatt:

     ![Megoldásgyorsítók irányítópultja](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. Ekkor az eszközszimulációs webalkalmazás elindul, és a kezdeti betöltése néhány másodpercet is igénybe vehet. A rendszer hozzájárulást kér, hogy a webalkalmazás jogosult legyen beléptetni Önt és olvasni a profilját. Ennek a jogosultságnak a birtokában az alkalmazás le tudja kérni a felhasználói profil az alkalmazás működésének támogatásához szükséges adatait:

     ![Eszközszimulációs webalkalmazás – hozzájárulás](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Miután betöltődött a **Szimuláció beállítása** oldal, adja meg a szükséges paramétereket: 

   Paraméter|Leírás
   ---|---
   **Cél IoT Hub** | Válassza az „Előzetesen kiépített IoT Hub használata” lehetőséget.
   **Eszközmodell** | Válassza a „Hűtő” lehetőséget.
   **Eszközök száma**  | A **Mennyiség** mezőben adja meg az 1000 értéket.
   **Telemetria gyakorisága** | Adjon meg 10 másodpercet.
   **Szimuláció időtartama** | Válassza a **Befejezés ennyi idő múlva:** lehetőséget, és adjon meg értékként 5 percet.

   Ha végzett, kattintson a **Szimuláció indítása** gombra. A szimuláció összesen 5 percig fut, és 1000 szimulált eszközről származó adatokat állít elő minden 10 másodpercben.  

   ![Eszközszimuláció beállítása](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. A szimuláció futtatása közben láthatja, ahogy az **Összes üzenet** és az **Üzenetek másodpercenkénti száma** mezők nagyjából 10 másodpercenként frissülnek. A szimuláció nagyjából 5 perc elteltével befejeződik, és a rendszer visszalépteti a **Szimuláció beállítása** oldalra.

   ![Eszközszimuláció futtatása](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>A telemetriaadatok ellenőrzése

Ebben az utolsó szakaszban ellenőrizheti, hogy a telemetriaadatok létrejöttek-e és tárolva lettek-e a TSI-környezetben. Az adatok ellenőrzéséhez használja a Time Series Insights Explorert, amellyel a telemetriaadatok lekérdezhetők és elemezhetők.

1. Lépjen vissza a TSI-környezet erőforráscsoportjának **Áttekintés** oldalára, majd válassza ismét a TSI-környezetet:

   [![TSI-környezet erőforráscsoportja és környezete](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. A TSI-környezet **Áttekintés** oldalán kattintson a **Time Series Insights Explorer URL-címe** lehetőségre a TSI Explorer megnyitásához:

   [![TSI Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. A TSI Explorer betöltődik, és hitelesítést végez az Ön Azure Portal-fiókjával. Az első megtekintéskor a diagramterületen látható, hogy a TSI-környezet valóban fel volt töltve szimulált telemetriaadatokkal. Ha szeretné szűkíteni az időtartamot, kattintson a legördülő listára a bal felső sarokban. Ezután adjon meg egy elég tág időtartamot, amely az eszközszimuláció teljes időtartamát lefedi. Végül kattintson a keresés nagyító ikonjára:

   [![TSI Explorer időtartamszűrője](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Az időtartam szűkítésével a diagram fókuszálható az egyes adatátviteli csúcsokra – mind az IoT Hub, mind a TSI-környezet irányában. A jobb felső sarokban lévő **A streamelés befejeződött** felirat a talált események számát mutatja. Az **Intervallum mérete** csúszka segítségével szabályozhatja a diagram ábrázolási részletességét:

   [![TSI Explorer időtartamra szűrt nézete](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Végül pedig a bal gombbal egy régióra kattintva szűkítheti az időtartamot, majd a jobb gombbal kattintva és az **Események áttekintése** elemet választva az események részleteit megjelenítheti a táblázatos **Események** nézetben:

   [![TSI Explorer időtartamra szűrt nézete és eseményei](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban több futó Azure-szolgáltatást hoztunk létre a TSI-környezet és az eszközszimulációs megoldás támogatására. Ha félbe szeretné hagyni és/vagy később fejezné be ezt az oktatóanyag-sorozatot, javasoljuk, hogy a felesleges költségek elkerülése érdekében törölje az összes erőforrást. 

Az Azure Portal bal oldali menüjében:

1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki a TSI-környezethez létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra. 
2. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki az eszközszimulációs megoldásgyorsító által létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * TSI-környezet létrehozása 
> * IoT Hubot tartalmazó eszközszimulációs megoldás létrehozása
> * Az IoT Hub és a TSI-környezet összekapcsolása
> * Eszközszimuláció futtatása adatok streamelésére a TSI-környezetbe
> * A szimulált telemetriaadatok ellenőrzése

Most, hogy elsajátította, hogyan hozhat létre saját TSI-környezetet, a következő cikkre továbblépve további részleteket tudhat meg a tervezési feladatról:

> [!div class="nextstepaction"]
> [Azure Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)


