---
title: Távoli felhőalapú IoT-monitorozási megoldás üzembe helyezése az Azure-ban | Microsoft Docs
description: Ezzel a rövid útmutatóval üzembe helyezheti a távoli monitorozási Azure IoT megoldásgyorsítót, és bejelentkezhet a megoldás irányítópultjának használatához.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: 8dd630650498f3698fea3ed03882fe8c5c3d338e
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004318"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Rövid útmutató: Távoli felhőalapú monitorozási megoldás kipróbálása

Ez a rövid útmutató bemutatja az Azure IoT távoli monitorozási megoldásgyorsító üzembe helyezését felhőalapú távoli monitorozási szimulációk futtatásához. A megoldásgyorsító telepítését követően a megoldás **Dashboard** (Irányítópult) lapjának segítségével jelenítheti meg a szimulált eszközöket egy térképen, és a **Maintenance** (Karbantartás) lapon válaszolhat a szimulált hűtőeszköz nyomással kapcsolatos riasztásaira. A megoldásgyorsítót saját megvalósítása kiindulópontjaként vagy képzési eszközként is használhatja.

A kezdeti üzembe helyezés a Contoso nevű vállalat számára konfigurálja a távoli monitorozási megoldásgyorsítót. A Contoso különféle fizikai környezetekben található, különböző típusú eszközöket (például hűtőeszközöket) felügyel. A hűtőeszköz a hőmérsékletre, a páratartalomra és a nyomásra vonatkozó telemetriai adatokat küld a távoli monitorozási megoldásgyorsítónak.

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Amikor üzembe helyezi a megoldásgyorsítót az Azure-előfizetéshez, néhány konfigurációs beállítást meg kell adnia.

Jelentkezzen be az [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) webhelyen az Azure-fiók hitelesítő adataival.

Kattintson a **Kipróbálom most** elemre a **Távoli monitorozás** csempén.

![Távoli monitorozás kiválasztása](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

A **Megoldás létrehozása (Távoli monitorozás)** lapon válassza ki az **Alapszintű** üzembe helyezési lehetőséget. Ha a megoldásgyorsítót demonstráció céljából vagy azért helyezi üzembe, hogy megismerkedhessen a működésével, a költségek csökkentése érdekében mindenképpen az **Alapszintű** lehetőséget válassza.

Válassza a **.NET** nyelvet. A Java és a .NET implementáció azonos funkciókat tartalmaz.

Adjon meg egy egyedi **megoldásnevet** a távoli monitorozási megoldásgyorsítóhoz. Ebben a rövid útmutatóban a **contoso-rm2** nevet használjuk.

Válassza ki a megoldásgyorsító üzembe helyezéséhez használni kívánt **Előfizetést** és **Régiót**. Általában az Önhöz legközelebbi régiót érdemes választani. Ebben a rövid útmutatóban a **Visual Studio Enterprise** előfizetést és a **Nyugat-Európa** régiót választjuk. Mindehhez [globális rendszergazdának vagy felhasználónak](iot-accelerators-permissions.md) kell lennie az előfizetésben.

Kattintson a **Megoldás létrehozása** gombra az üzembe helyezés elkezdéséhez. A folyamat legalább öt percig tart:

![A távoli monitorozási megoldás részletei](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Bejelentkezés a megoldásba

Ha az üzembe helyezés befejeződött az Azure-előfizetésen, a megoldás csempéjén egy zöld pipa és a **Kész** felirat jelenik meg. Ezután bejelentkezhet a távoli monitorozási megoldásgyorsító irányítópultjába.

A **Kiépített megoldások** lapon kattintson az új távoli monitorozási megoldásgyorsítóra:

![Új megoldás kiválasztása](./media/quickstart-remote-monitoring-deploy/choosenew.png)

A megjelenő panelen megtekintheti a távoli monitorozási megoldásgyorsítóra vonatkozó információkat. Válassza ki a **Megoldások irányítópultja** elemet a távoli monitorozási megoldásgyorsító megjelenítéséhez:

![Megoldás panel](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Kattintson az **Elfogadás** gombra az engedélykérések elfogadásához. Ekkor a böngészőben megjelenik a távoli monitorozási megoldásgyorsító irányítópultja:

[![A megoldás irányítópultja](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Eszközök megtekintése

A megoldás irányítópultja a következő információkat jeleníti meg a Contoso szimulált eszközeiről:

* Az **eszközök statisztikái** a riasztások összegző információit és az eszközök teljes számát mutatják. Az alapértelmezett üzemelő példányban a Contoso 10, különböző típusú szimulált eszközzel rendelkezik.

* Az **eszközök helyénél** az eszközök fizikai helyét lehet ellenőrizni. A gombostű színe jelzi, ha egy eszköz riasztást váltott ki.

* A **riasztások** panelje az eszközöktől érkező riasztások részleteit tartalmazza.

* A **telemetria** panelje az eszközök telemetriai adatait jeleníti meg. A különböző telemetriastreameket a fent található telemetriatípusokra kattintva tekintheti meg.

* Az **elemzések** panelje az eszközöktől érkező riasztásokra vonatkozó egyesített információkat tartalmaz.

## <a name="respond-to-an-alert"></a>Válasz egy riasztásra

A Contoso operátoraként a megoldás irányítópultjáról monitorozhatja az eszközöket. Az **eszközök statisztikáinak** panelje azt mutatja, hogy volt néhány kritikus riasztás, a **riasztások** panelje pedig azt, hogy ezek nagy része egy hűtőeszköztől érkezett. A Contoso hűtőeszközei esetében a 250 PSI-t meghaladó belső nyomás azt jelzi, hogy az eszköz nem működik megfelelően.

### <a name="identify-the-issue"></a>A probléma azonosítása

Az **irányítópult** **Alerts** (Riasztások) paneljén a **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) riasztás látható. A hűtő piros színű gombostűvel van jelölve a térképen (lehetséges, hogy pásztáznia és nagyítania kell a megjelenítéséhez):

[![Irányítópult nyomásriasztással és a térképen megjelenített eszközzel](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

A **riasztások** paneljén kattintson a **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) szabály mellett található **vizsgálati** oszlop **...** gombjára. Ezzel továbblép a **karbantartási** lapra, ahol megtekintheti a riasztást kiváltó szabály részleteit.

A **Chiller pressure too high** (Hűtőeszköz nyomása túl magas) karbantartási lap megjeleníti a riasztást kiváltó szabály részleteit. A lap azt is megjeleníti, hogy mikor aktiválódtak a riasztások, és melyik eszköz váltotta ki őket:

[![A karbantartási lap az aktivált riasztások listájával](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Ezzel sikeresen azonosította a riasztást kiváltó problémát és az érintett eszközt. Operátorként a következő lépés a riasztás nyugtázása és a hiba kijavítása.

### <a name="fix-the-issue"></a>A probléma megoldása

Ha jelezni szeretné más operátorok felé, hogy már foglalkozik a riasztással, válassza ki, és módosítsa az **Alert status** (Riasztási állapot) értékét **Acknowledged** (Nyugtázva) állapotra:

[![Riasztás kiválasztása és nyugtázása](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

Az állapotoszlop **Acknowledged** (Nyugtázva) értékre módosul.

Ha műveletet szeretne végrehajtani a hűtőeszközön, görgessen le a **Related information** (Kapcsolódó információk) menüponthoz, válassza ki a hűtőeszközt a **riasztott eszközök** listájából, majd válassza a **Jobs** (Feladatok) lehetőséget:

[![Eszköz kiválasztása és művelet ütemezése](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

A **feladatok** paneljén válassza a **Run method** (Metódus futtatása) elemet, majd az **EmergencyValveRelease** metódust. Adja hozzá a **ChillerPressureRelease** feladatnevet, és kattintson az **Apply** (Alkalmaz) elemre. Ezek a beállítások egy olyan feladatot hoznak létre, amelyet a rendszer azonnal végrehajt.

A feladat állapotának megtekintéséhez lépjen vissza a **karbantartási** lapra, és a **feladatok** nézetében tekintse meg a feladatok listáját. Lehet, hogy néhány másodpercet várnia kell, amíg megjelenik, hogy a rendszer futtatta a hűtőeszköz szelepnyomásának csökkentésére irányuló feladatot:

[![A feladatok állapota a feladatok nézetében](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>A határértéken belüli nyomásérték ellenőrzése

A hűtő nyomástelemetriájának megtekintéséhez lépjen az **irányítópultra**, válassza ki a **Pressure** (Nyomás) telemetriapanelt, és ellenőrizze, hogy a **chiller-02.0** nyomása visszatért-e az engedélyezett tartományba:

[![A nyomás határértéken belüli](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Az incidens lezárásához lépjen a **karbantartási** lapra, válassza ki a riasztást, és állítsa **Closed** (Lezárva) értékre:

[![Riasztás kiválasztása és lezárása](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

Az állapotoszlop **Closed** (Lezárva) értékre módosul.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a további oktatóanyagokra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról. Ehhez jelölje ki, majd kattintson a **Megoldás törlése** gombra:

![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte a távoli monitorozási megoldásgyorsítót, és elvégzett egy monitorozási feladatot az alapértelmezett Contoso üzemelő példányban szereplő szimulált eszközökön.

Ha többet szeretne megtudni a szimulált eszközöket használó megoldásgyorsítóról, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [Oktatóanyag: IoT-eszközök monitorozása](iot-accelerators-remote-monitoring-monitor.md)