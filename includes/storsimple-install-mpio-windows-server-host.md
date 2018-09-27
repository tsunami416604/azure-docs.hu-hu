#### <a name="to-install-mpio-on-the-host"></a>Az MPIO szolgáltatás telepítésének részletei a gazdagépen
1. Nyissa meg a Kiszolgálókezelőt a Windows Server-gazdagépen. Alapértelmezés szerint a Kiszolgálókezelő elindul, ha tagja a Rendszergazdák csoport jelentkezik be Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató számítógépre. Ha a Kiszolgálókezelő még nem nyitott, kattintson a **Start > Kiszolgálókezelő**.
   
    ![Kiszolgálókezelő](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Kattintson a **Kiszolgálókezelő > irányítópult > szerepkörök és szolgáltatások hozzáadása**. Ekkor elindul a **szerepkörök és szolgáltatások hozzáadása** varázsló.
   
    ![Szerepkörök és szolgáltatások hozzáadása varázsló 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. Az a **szerepkörök és szolgáltatások hozzáadása** varázsló, tegye a következőket:
   
   * Az a **megkezdése előtt** kattintson **tovább**.
   * Az a **telepítés típusának kiválasztása** lap, fogadja el az alapértelmezett beállítás a **szerepköralapú vagy szolgáltatásalapú** telepítését. Kattintson a **Tovább** gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * Az a **célkiszolgáló kijelölése** lapon a **kiszolgáló kijelölése a kiszolgálókészletből**. A gazdakiszolgálón kell automatikus felderítése. Kattintson a **Tovább** gombra.
   * Az a **kiszolgálói szerepkörök kiválasztása** kattintson **tovább**.
   * A a **jellemzőket** lapra, jelölje be **többutas i/o**, és kattintson a **tovább**.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * Az a **telepítendő összetevők megerősítése** lapon. a kijelölés megerősítéséhez, majd válassza ki **a célkiszolgáló automatikus újraindítása, ha szükséges**lent látható módon. Kattintson az **Install** (Telepítés) gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Értesíteni fogjuk, ha a telepítés befejeződött. A varázsló bezárásához kattintson a **Bezárás** gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

