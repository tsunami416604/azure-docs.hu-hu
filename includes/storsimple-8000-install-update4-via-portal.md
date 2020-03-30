---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a3ccf76b2722c04a9353fcc7020ff1387bc454c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179322"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Eszköz kiválasztása](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Nyissa meg az **Eszközbeállítások** > **Eszközfrissítések lapot.**

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Értesítés jelenik meg, ha új frissítések érhetők el. Másik lehetőségként az **Eszközfrissítések** panelen kattintson a **Frissítések bekeresése gombra.** Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. A frissítések alkalmazásához kattintson **a Frissítések telepítése gombra.** A **Rendszeres frissítések megerősítése** panelen tekintse át a frissítések alkalmazása előtt végrehajtandó előfeltételeket. Jelölje be a jelölőnégyzetet, jelezve, hogy készen áll az eszköz frissítésére, majd kattintson a **Telepítés gombra.**

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

     A frissítés csak akkor töltődik le és települ, ha az összes ellenőrzés sikeresen befejeződött. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha az előzetes ellenőrzések sikertelenek, akkor megkapja a hiba okait. Oldja meg ezeket a problémákat, majd próbálkozzon újra a művelettel. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. Az előzetes ellenőrzések sikeres befejezése után létrejön egy frissítési feladat. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update8.png)

     A frissítési feladat előrehaladását az **Eszközbeállítások > feladatok**területen is figyelheti. A **Feladatok** panelen láthatja a frissítés folyamatát.

     ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. A feladat befejezése után keresse meg az **Eszközbeállítások > Eszközfrissítések .** A szoftver verzióját most frissíteni kell.

   ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update9.png)

