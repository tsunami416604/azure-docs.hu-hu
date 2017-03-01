1. Futtassa az egyesített telepítő fájlját.
2. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget.
    ![Előkészületek](./media/site-recovery-add-configuration-server/combined-wiz1.png)
3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.

    ![Külső gyártótól származó szoftverek](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. A **Regisztráció** területen válassza ki a kulcstartóból letöltött regisztrációs kulcsot.

    ![Regisztráció](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül.

   * Ha a gépen jelenleg beállított proxyval szeretne csatlakozni, válassza a **Csatlakozás a meglévő proxybeállításokkal** lehetőséget.
   * Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza a **Connect directly without a proxy** (Közvetlen kapcsolódás proxy nélkül) lehetőséget.
   * Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni a Provider csatlakoztatásához, válassza a **Csatlakozás egyéni proxybeállításokkal** lehetőséget.

     * Ha az egyéni proxy használatát választja, meg kell adnia a címet, a portot és a hitelesítő adatokat.
     * Ha proxyt használt, ellenőrizze, hogy engedélyezte-e az [előfeltételekben](#prerequisites) leírt URL-címek elérését.

     ![Tűzfal](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha igen, a telepítő ellenőrzi, hogy a PowerCLI 6.0 telepítve van-e.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)

9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. A **Hálózat kiválasztása** területen adja meg a figyelőt (hálózati adaptert és SSL-portot), amelyen keresztül a konfigurációs kiszolgáló küldi és fogadja a replikált adatokat. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. A replikációs forgalom küldésére és fogadására ne használja a 443-as portot.

    ![Hálózat kiválasztása](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.

    ![Összefoglalás](./media/site-recovery-add-configuration-server/combined-wiz10.png)

A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** paneljén.


<!--HONumber=Feb17_HO4-->


