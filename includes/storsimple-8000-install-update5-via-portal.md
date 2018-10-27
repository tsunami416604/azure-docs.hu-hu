---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d3a810523807a46ca3f410c7cb5e11884f2b06b7
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166316"
---
<!--author=alkohli last changed: 08/04/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Eszköz kiválasztása](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Navigáljon a **eszközbeállítások** > **Eszközfrissítések**.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Egy értesítés jelenik meg, ha új frissítések érhetők el. Másik lehetőségként a a **Eszközfrissítések** panelen kattintson a **frissítések keresése**. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. Frissítések alkalmazásához kattintson **frissítések telepítése**. Az a **általános frissítések megerősítése** panelen tekintse át az előfeltételeket a befejezéshez, mielőtt a frissítések telepítését. Válassza ki a jelölőnégyzet bejelölésével jelezze, hogy készen áll az eszközét, és kattintson a **telepítése**.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

    A frissítés letöltését és telepítését, csak akkor, ha az összes ellenőrzés sikeresen befejeződik. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha az Eszközfrissítések nem sikerül, majd kap az a hiba okait. Probléma megoldására, és próbálkozzon újra a művelettel. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. Az Eszközfrissítések sikeres elvégzése után létrejön egy frissítési feladat. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Az a frissítési feladat állapotát is figyelheti **eszközbeállítások > feladatok**. Az a **feladatok** panelen láthatja a frissítési folyamatot.

     ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. A feladat befejezése után lépjen a **eszközbeállítások > Eszközfrissítések**. A szoftververzió most frissíteni kell.

