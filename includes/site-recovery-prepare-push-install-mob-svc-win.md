### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Egy leküldéses telepítését a Windows-számítógép előkészítése

1. Győződjön meg arról, hogy van-e hálózati kapcsolat a Windows-számítógép és a folyamatkiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fiók (helyi vagy tartományi) rendszergazdai jogosultságokkal kell rendelkeznie. (Ez a fiók csak a leküldéses telepítés és használjon frissítéseihez.)

   > [!NOTE]
   > Ha nem használ egy olyan tartományi fiók, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Tiltsa le a távoli felhasználó hozzáférés-vezérlés, a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System beállításkulcs alatt hozzáadása egy új DWORD: **LocalAccountTokenFilterPolicy**. Állítsa be az értéket **1**. Ehhez a parancssorba a következő parancsot:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. A Windows tűzfal a védeni kívánt számítógépen, válassza ki a **lehetővé teszi egy alkalmazás vagy szolgáltatás átengedése a tűzfalon**. Engedélyezése **fájl- és nyomtatómegosztás** és **Windows Management Instrumentation (WMI)**. Egy tartományba tartozó számítógépek esetében egy csoportházirend-objektumot (GPO) segítségével konfigurálhatja a tűzfal beállításait.

   ![Tűzfalbeállítások](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot.
    1.  Jelentkezzen be a konfigurációs kiszolgálóra.
    2.  Nyissa meg a következőt: **cspsconfigtool.exe**. (A parancsikonja elérhető az asztalon, az alkalmazás pedig a %ProgramData%\home\svsystems\bin mappában található.)
    3.  Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.
    4.  Adja hozzá a létrehozott fiókot.
    5.  Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
