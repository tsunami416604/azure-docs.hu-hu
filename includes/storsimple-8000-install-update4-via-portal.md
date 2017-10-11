<!--author=alkohli last changed: 07/07/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Válassza ki az eszköz](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navigáljon a **eszközbeállítások** > **eszközfrissítésekhez**.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Megjelenik egy értesítés, ha új frissítések érhetők el. Másik lehetőségként a a **eszközfrissítésekhez** panelen kattintson a **frissítések keresése**. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. Frissítések alkalmazásához kattintson **frissítések telepítése**. Az a **erősítse meg a rendszeres frissítések** panelt, tekintse át az előfeltételek telepítése előtt. Válassza ki a jelölőnégyzet bejelölésével jelezze, hogy készen áll az eszköz frissítése, és kattintson a **telepítése**.

    ![Kattintson az eszköz frissítése](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

    A frissítés letöltése és telepítése csak akkor, ha minden ellenőrzés sikeresen befejeződtek. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha meghibásodik a prechecks, majd azt fogják ellátni a hiba lehetséges okait. Hárítsa el ezeket a problémákat, és próbálkozzon újra a művelettel. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. A prechecks sikeres elvégzése után létrejön egy frissítési feladatot. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Ugyanígy figyelheti a frissítési feladat előrehaladásának **eszközbeállítások > feladatok**. Az a **feladatok** panelen láthatja, hogy a frissítés folyamatban van.

     ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. A feladat befejezése után nyissa meg a **eszközbeállítások > eszközfrissítésekhez**. A szoftver verziója most frissíteni kell.

    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update9.png)

