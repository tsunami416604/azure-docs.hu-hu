<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>A minimális StorSimple eszközbeállítás befejezése
1. Az **Eszközök** lapon válassza ki az eszközt. A kívánt eszköz lapját az eszköz nevére mutató nyílra kattintva érheti el. 
   
    ![Az Eszközök lap online eszközökkel](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 
2. Kattintson a gyors üzembe helyezési ikonra ![Gyors üzembe helyezés ikonja](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) a gyors üzembe helyezési oldal megnyitásához. Az **Eszköz konfigurálása** varázsló elindításához kattintson az **Eszköz beállításának befejezése** lehetőségre.
   
    ![Az eszköz gyors üzembe helyezési oldala](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)
3. Az **Alapbeállítások** oldalon tegye a következőket:
   
   1. Adjon egy **rövid** nevet az eszköznek. Az alapértelmezett eszköznév olyan információkból áll, mint az eszköz típusa és sorozatszáma. Az eszköz kezelése érdekében egy legfeljebb 64 karakterből álló rövid nevet rendelhet hozzá.
   2. Az **időzóna** az eszköz üzembe helyezésének földrajzi helyétől függ. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   3. A **DNS-beállítások** területen adjon meg egy címet a **Másodlagos DNS-kiszolgálóhoz**. Ha IPv6-címeket használ, a mező a Windows PowerShell felületén megadott IPv6-előtag alapján lesz kitöltve. 
      Ha a másodlagos DNS-kiszolgáló nincs beállítva, akkor nem lesz lehetősége az eszközkonfigurációk mentésére.
   4. Az iSCSI-kompatibilis adapterek felületén engedélyezzen legalább egy hálózatot az iSCSI számára. Legalább egy hálózati adaptert engedélyezni kell a felhőhöz, és egyet az iSCSI-hoz. A DATA 0 automatikusan engedélyezve van a felhőhöz.
      
      ![A StorSimple minimális eszközbeállításának alapbeállításai](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)
4. Kattintson a nyíl ikonra. ![StorSimple nyíl ikon](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
5. A **Hálózati adapterek** lapon adja meg a Vezérlő 0 és a Vezérlő 1 fix IP-címét. Ha a DATA 0 adapter az IPv4 használatára van konfigurálva, akkor a fix IP-címeket is IPv4-formátumban kell megadni. Ha IPv6-konfigurációhoz tartozó előtagot adott meg, a fix IP-címek automatikusan ezekbe a mezőkbe lesznek betöltve.

    > [!NOTE] 
    > - A vezérlő fix IP-címeinek az alhálózaton belüli, az eszköz IP-címe alapján elérhető, szabad IP-címeknek kell lenniük.
    > - A vezérlőhöz tartozó fix IP-címek az eszköz frissítéseinek karbantartásához használatosak, ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.

    ![A StorSimple minimális eszközbeállításának hálózati adapterei](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

1. Kattintson a pipa ikonra ![StorSimple pipa ikon](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Ekkor visszakerül az eszköz **Gyors üzembe helyezés** oldalára.
   
   > [!NOTE]
   > Az eszköz többi beállítását bármikor módosíthatja a **Konfigurálás** lapon.
   > 
   > 

![Videó elérhető](./media/storsimple-complete-minimum-device-setup/Video_icon.png)**Videó elérhető**

A minimális eszközbeállítás befejezését bemutató videó megtekintéséhez kattintson [ide](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).

