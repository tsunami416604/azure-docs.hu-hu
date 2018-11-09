---
title: Felhőalapú IoT prediktív karbantartási megoldás kipróbálása az Azure-ban | Microsoft Docs
description: Ezzel a rövid útmutatóval üzembe helyezheti a prediktív karbantartási Azure IoT-megoldásgyorsítót, és bejelentkezhet a megoldás irányítópultjának használatához.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: c56bdb5968b5f5ada902aa9212282c3cfebe2eaf
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139839"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>Rövid útmutató: Felhőalapú megoldás kipróbálása prediktív karbantartási elemzés saját csatlakoztatott eszközökön való futtatásához

Ez a rövid útmutató bemutatja az Azure IoT-prediktív karbantartási megoldásgyorsító üzembe helyezését egy felhőalapú prediktív karbantartási szimuláció futtatásához. A megoldásgyorsító telepítése után a megoldás **Dashboard** (Irányítópult) lapján futtathat egy prediktív karbantartási elemzést egy szimulált repülőgépmotor adatai alapján. A megoldásgyorsítót saját megvalósítása kiindulópontjaként vagy képzési eszközként is használhatja.

A szimulációban a Fabrikam egy regionális légitársaság, amely a nagyszerű ügyfélélményre összpontosít versenyképes árakon. A járatok késésének egyik okai a karbantartási problémák, és a repülőmotorok karbantartása különösen nagy kihívást jelent. A Fabrikamnak minden áron el kell kerülnie a repülés közbeni motorhibákat, így rendszeresen megvizsgálja a motorokat, és tervszerűen ütemezi a karbantartást. A repülőgépek motorja azonban nem mindig ugyanolyan ütemben használódik el. Időnként feleslegesen végeznek karbantartást a motorokon. Még fontosabb, hogy olyan problémák merülnek fel, amelyek miatt a repülőgép nem szállhat fel a karbantartás elvégzéséig. Ezek a problémák különösen költségesek lehetnek, ha egy repülőgép olyan helyen van, ahol nincsenek megfelelő szerelők vagy pótalkatrészek.

A Fabrikam repülőmotorjai olyan érzékelőkkel vannak felszerelve, amelyek monitorozzák a motor állapotát repülés közben. A Fabrikam több évig gyűjtötte a motor működési és meghibásodási adatait ezekből az érzékelőkből. A Fabrikam adatszakértői ezekkel az adatokkal olyan modellt fejlesztettek, amely előrejelzi a repülőmotor fennmaradó hasznos élettartamát (RUL). A modell a négy motorérzékelőből érkező adatok és a meghibásodáshoz vezető motorkopás közötti összefüggést alkalmazza. A Fabrikam továbbra is rendszeres vizsgálatokat végez a biztonság érdekében, azonban mostantól a modellek használatával kiszámíthatja mindegyik motor RUL-értékét minden egyes repülőút után. A Fabrikam már képes előre megmondani a várható meghibásodási pontokat, ez alapján tervezni a karbantartást, így képes minimálisra csökkenteni a repülőgép földön töltött idejét. Ez a folyamat csökkenti a működési költségeket, miközben az utasok és a személyzet biztonságát is garantálja.

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Amikor üzembe helyezi a megoldásgyorsítót az Azure-előfizetéshez, néhány konfigurációs beállítást meg kell adnia.

Navigáljon a [Microsoft Azure IoT-megoldásgyorsítókhoz](https://www.azureiotsolutions.com), és jelentkezzen be az Azure-fiók hitelesítő adataival.

Kattintson a **Prediktív karbantartás** csempére. Kattintson a **Kipróbálom most** elemre a **Prediktív karbantartás** oldalon:

![Kipróbálom most](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

A **Prediktív karbantartási megoldás létrehozása** oldalon adjon meg egy **Megoldásnevet** a prediktív karbantartási megoldásgyorsítóhoz. Ebben a rövid útmutatóban a **MyPredictiveMaintenance** nevet használjuk.

Válassza ki a megoldásgyorsító üzembe helyezéséhez használni kívánt **Előfizetést** és **Régiót**. Általában az Önhöz legközelebbi régiót érdemes választani. Ebben a rövid útmutatóban a **Visual Studio Enterprise** és az **USA keleti régiója** értékeket választjuk. Mindehhez [globális rendszergazdának vagy felhasználónak](iot-accelerators-permissions.md) kell lennie az előfizetésben.

Kattintson a **Megoldás létrehozása** gombra az üzembe helyezés elkezdéséhez. A folyamat legalább öt percig tart:

![A prediktív karbantartási megoldás részletei](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Bejelentkezés a megoldásba

Ha az üzembe helyezés befejeződött az Azure-előfizetésen, a megoldás csempéjén egy zöld pipa és a **Kész** felirat jelenik meg. Ekkor bejelentkezhet a prediktív karbantartási megoldásgyorsító irányítópultjára.

A **Kiépített megoldások** lapon kattintson az új prediktív karbantartási megoldásgyorsítóra. A megjelenő panelen megtekintheti a prediktív karbantartási megoldásgyorsítóra vonatkozó információkat. Válassza ki a **Megoldások irányítópultja** elemet a prediktív karbantartási megoldásgyorsító megjelenítéséhez:

![Megoldás panel](./media/quickstart-predictive-maintenance-deploy/solutionpanel.png)

Kattintson az **Elfogadás** gombra az engedélykérések elfogadásához. Ekkor a böngészőben megjelenik a prediktív karbantartási megoldásgyorsító irányítópultja:

![A megoldás irányítópultja](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

Kattintson a **Start simulation** (Szimuláció indítása) elemre a szimuláció elindításához. Az irányítópulton az érzékelő előzményei, a RUL, a ciklusok és a RUL előzményei jelennek meg:

![Szimuláció futtatása](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

Ha az RUL 160-nál kisebb (amely egy bemutatási célra véletlenszerűen kiválasztott küszöbérték), a megoldás portálja egy figyelmeztető szimbólumot jelenít meg az RUL kijelzése mellett. A megoldásportál emellett a repülő motorját sárga színnel emeli ki. Látható, hogy az RUL értékek összesítve jellemzően csökkennek, de felfelé és lefelé is változnak. Ez a viselkedés a változó ciklushosszokból és modellpontosságból következik.

![Szimuláció – figyelmeztetés](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

A teljes szimuláció 148 ciklusának elvégzése körülbelül 35 percet vesz igénybe. Először körülbelül 5 perc után éri el a 160 RUL-es küszöbértéket, és körülbelül 8 perc után éri el mindkét motor a küszöbértéket.

A szimuláció 148 cikluson halad végig a teljes adatkészleten, és megállapítja a RUL és a ciklus végső értékeit.

Bármikor leállíthatja a szimulációt, de a **Start Simulation** (Szimuláció indítása) gombra kattintva az adatkészlet elejétől indul újra a szimuláció.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne ismerkedni az eszközzel, hagyja üzembe helyezve a prediktív karbantartási megoldást.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról. Ehhez jelölje ki, majd kattintson a **Megoldás törlése** gombra:

![Megoldás törlése](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban telepítette a prediktív karbantartási megoldásgyorsítót, és futtatott egy szimulációt.

Ha többet szeretne megtudni a megoldásgyorsítóról és a szimulált repülőgépmotorokról, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-walkthrough.md)
