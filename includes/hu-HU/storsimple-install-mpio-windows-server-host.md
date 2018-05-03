#### <a name="to-install-mpio-on-the-host"></a>A gazdagép MPIO telepítése
1. Nyissa meg a Kiszolgálókezelőt a Windows Server-állomáson. Alapértelmezés szerint a Kiszolgálókezelő akkor kezdődik, amikor egy tagja a Rendszergazdák csoport bejelentkezik egy Windows Server 2012 R2 vagy Windows Server 2012 rendszert futtató számítógép. Ha a Kiszolgálókezelő még nincs megnyitva, kattintson a **Start > Kiszolgálókezelő**.
   
    ![Kiszolgálókezelő](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Kattintson a **Kiszolgálókezelő > irányítópult > szerepkörök és szolgáltatások hozzáadása**. Ekkor elindul a **szerepkörök és szolgáltatások hozzáadása** varázsló.
   
    ![Szerepkörök és szolgáltatások hozzáadása varázsló 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. Az a **szerepkörök és szolgáltatások hozzáadása** varázsló, tegye a következőket:
   
   * Az a **megkezdése előtt** kattintson **következő**.
   * Az a **telepítés típusának kiválasztása** lap, fogadja el az alapértelmezett **szerepköralapú vagy szolgáltatásalapú** telepítését. Kattintson a **Tovább** gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 2. régiója](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * Az a **jelölje be a célkiszolgáló** lapon, válassza ki **kiszolgáló kijelölése a kiszolgálókészletből**. A gazdagép-kiszolgálón kell automatikusan észlelhetők. Kattintson a **Tovább** gombra.
   * Az a **kiszolgálói szerepkörök kiválasztása** kattintson **következő**.
   * A a **szolgáltatások kiválasztása** lapon, hogy melyik **többutas i/o**, és kattintson a **következő**.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * Az a **erősítse meg a telepítendő** lapon ellenőrizze a kijelölést, majd válassza ki **a célkiszolgáló automatikus újraindítása, ha szükséges**lent látható módon. Kattintson az **Install** (Telepítés) gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Fog értesítést jelenít meg, ha a telepítés befejeződött. A varázsló bezárásához kattintson a **Bezárás** gombra.
     
       ![Szerepkörök és szolgáltatások hozzáadása varázsló 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

