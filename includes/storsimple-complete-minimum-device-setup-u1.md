<!--author=alkohli last changed: 9/17/15-->

#### A minimális StorSimple eszközbeállítás befejezése

1. Válassza ki az eszközt, és kattintson a **Gyors üzembe helyezés** gombra. Az Eszköz konfigurálása varázsló elindításához kattintson az **Eszköz beállításának befejezése** lehetőségre.

2. Az Eszköz konfigurálása varázsló **Alapbeállítások** párbeszédpaneljén tegye a következőket:
  1. Adjon egy **rövid** nevet az eszköznek. Az alapértelmezett eszköznév olyan információkból áll, mint az eszköz típusa és sorozatszáma. Az eszköz kezelése érdekében egy legfeljebb 64 karakterből álló rövid nevet rendelhet hozzá.
  2. Az **időzóna** az eszköz üzembe helyezésének földrajzi helyétől függ. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
  3. A **DNS-beállítások** területen adjon meg egy címet a **Másodlagos DNS-kiszolgálóhoz**. Ha IPv6-címeket használ, a mező a Windows PowerShell felületén megadott IPv6-előtag alapján lesz kitöltve. 
  Ha a másodlagos DNS-kiszolgáló nincs beállítva, akkor nem lesz lehetősége az eszközkonfigurációk mentésére.
  4. Az iSCSI-kompatibilis adapterek felületén engedélyezzen legalább egy hálózatot az iSCSI számára. Legalább egy hálózati adaptert engedélyezni kell a felhőhöz, és egyet az iSCSI-hoz. A DATA 0 automatikusan engedélyezve van a felhőhöz.
 
      ![A StorSimple minimális eszközbeállításának alapbeállításai](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Kattintson a nyíl ikonra. ![StorSimple nyíl ikon](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. A **Hálózati adapterek** párbeszédpanelen adja meg a Vezérlő 0 és a Vezérlő 1 fix IP-címét. **A vezérlő fix IP-címeinek az alhálózaton belüli, az eszköz IP-címe alapján elérhető, szabad IP-címeknek kell lenniük.** Ha a DATA 0 adapter az IPv4 használatára van konfigurálva, akkor a fix IP-címeket is IPv4-formátumban kell megadni. Ha IPv6-konfigurációhoz tartozó előtagot adott meg, a fix IP-címek automatikusan ezekbe a mezőkbe lesznek betöltve.


    ![A StorSimple minimális eszközbeállításának hálózati adapterei](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    A vezérlőhöz tartozó fix IP-címek az eszköz frissítéseinek karbantartásához használatosak, ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez. A fix vezérlő IP-k irányíthatóságát a [Test-HcsmConnection][Test] parancsmaggal ellenőrizheti. Az alábbi példán olyan, az internetre átirányított fix vezérlő IP-k láthatók, amelyek hozzáférnek a Microsoft Update-kiszolgálókhoz. 

     ![Test-HcsmConnection irányítható IP-kkel](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

5. Kattintson a pipa ikonra ![StorSimple pipa ikon](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
  Ekkor visszakerül az eszköz **Gyors üzembe helyezés** oldalára.

 > [AZURE.NOTE] Az eszköz többi beállítását bármikor módosíthatja a **Konfigurálás** lapon.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx


<!--HONumber=Jun16_HO2-->


