<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét. Keresse meg a **Devices** (Eszközök) > **Maintenance** (Karbantartás) elemet.
2. Kattintson a lap alján található **Scan updates** (Frissítések keresése) gombra. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.
3. Ugyanezen oldal **Software Updates** (Szoftverfrissítések) szakaszán érhetők el az új szoftverfrissítések. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket.
4. Kattintson a lap alján található **Install Updates** (Frissítések telepítése) gombra, majd az **OK** gombra.
5. Az **Install updates** (Frissítések telepítése) párbeszédpanelen győződjön meg arról, hogy követte a javaslatokat, majd jelölje be az **I understand the above requirement and am ready to upgrade my device** (Megértettem a fenti követelményt, és készen állok az eszköz frissítésére) jelölőnégyzetet, és kattintson a pipa gombra.
   
    ![Megerősítő üzenet](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.
   * **DATA 2 és DATA 3 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 2 és DATA 3 hálózati adapterek nincsenek-e engedélyezve. Ha ezek az adapterek engedélyezettek, akkor le kell tiltania ezeket, majd meg kell próbálnia frissíteni az eszközt. Ezt az ellenőrzést csak akkor végzi el a rendszer, ha egy GA szoftvert futtató eszközből frissít. A 0.1, 0.2 vagy 0.3 verziót futtató eszközöknek nincs szükségük erre az ellenőrzésre.
   * **Átjáró ellenőrzése** az 1. frissítés előtti verziót futtató minden eszközön. Ezt az ellenőrzést a rendszer az 1. frissítés előtti szoftvert futtató összes eszközön elvégzi, de meghiúsul azokon az eszközökön, amelyek valamely átjárója nem a DATA 0 hálózati adapterhez van konfigurálva.
     
     A rendszer alkalmazza a frissítést, ha az összes ellenőrzés sikeresen befejeződik. Értesítést kap, amikor az ellenőrzések folyamatban vannak.
     
     ![Ellenőrzés előtti értesítés](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     A következő egy olyan példa, ahol az ellenőrzések meghiúsultak. Ellenőriznie kell, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen. A hardverösszetevők állapotát is ellenőriznie kell. Ebben a példában a Vezérlő 0 és a Vezérlő 1 összetevők igényelnek figyelmet. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.
     
       ![Az ellenőrzések meghiúsultak](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Az ellenőrzések sikeres elvégzése után létrejön egy frissítési feladat. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.
    
8. A frissítési feladat folyamatának figyeléséhez kattintson a **Feladat megtekintése** gombra. A **Feladatok** lapon láthatja a frissítési folyamatot.
9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.
10. A feladat elvégzése után lépjen a **Maintenance** (Karbantartás) lapra, és görgessen le a **Software Updates** (Szoftverfrissítések) elemhez.



<!--HONumber=Feb17_HO2-->


