### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Egy leküldéses telepítését egy Windows-számítógép előkészítése

1. Győződjön meg arról, hogy nincs-e a Windows-számítógép és a folyamatkiszolgáló közötti hálózati kapcsolatot.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fiók vagy helyi vagy tartományi rendszergazdai jogosultsággal kell rendelkeznie. Használja ezt a fiókot, csak a leküldéses telepítéshez és az ügynökök frissítésével.

   > [!NOTE]
   > Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Tiltsa le a távoli felhasználói hozzáférés-vezérlést, a localaccounttokenfilterpolicy beállításjegyzékbeli kulcs hozzáadása egy új DWORD: **LocalAccountTokenFilterPolicy**. Állítsa az értékét **1**. A parancssorba a feladat végrehajtásához futtassa a következő parancsot:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. A Windows tűzfal a védeni kívánt számítógépen, válassza ki a **lehetővé teszik az alkalmazás vagy szolgáltatás tűzfalán keresztül**. Engedélyezése **fájl- és nyomtatómegosztás** és **Windows Management Instrumentation (WMI)**. Tartományi számítógépek esetében konfigurálhatja a tűzfal beállításai csoportházirend-objektum (GPO) használatával.

   ![Tűzfalbeállítások](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be a konfigurációs kiszolgálóra.

    b. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonja az asztalon, és a %ProgramData%\home\svsystems\bin mappában érhető el.

    c. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.

    d. Adja hozzá a létrehozott fiókot.

    e. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
