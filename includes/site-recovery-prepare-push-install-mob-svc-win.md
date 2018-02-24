### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Egy leküldéses telepítését a Windows-számítógép előkészítése

1. Győződjön meg arról, hogy van-e hálózati kapcsolat a Windows-számítógép és a folyamatkiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak rendszergazdai jogosultságokkal, vagy helyi vagy tartományi kell rendelkeznie. Ezt a fiókot használják, csak az ügyfélleküldéses telepítés és az ügynökök frissítésével.

   > [!NOTE]
   > Ha nem adja meg egy olyan tartományi fiók, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Tiltsa le a távoli felhasználó hozzáférés-vezérlés, HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System beállításkulcsban, hozzáadása egy új DWORD: **LocalAccountTokenFilterPolicy**. Állítsa be az értéket **1**. A parancssorba a feladat végrehajtásához futtassa a következő parancsot:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
3. A Windows tűzfal a védeni kívánt számítógépen, válassza ki a **lehetővé teszi egy alkalmazás vagy szolgáltatás átengedése a tűzfalon**. Engedélyezése **fájl- és nyomtatómegosztás** és **Windows Management Instrumentation (WMI)**. Egy tartományba tartozó számítógépek esetében egy csoportházirend-objektumot (GPO) segítségével konfigurálhatja a tűzfal beállításait.

   ![Tűzfalbeállítások](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

4. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be a konfigurációs kiszolgálóra.

    b. Nyissa meg a következőt: **cspsconfigtool.exe**. Egy parancsikont az asztalon és az %ProgramData%\home\svsystems\bin mappában érhető el.

    c. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.

    d. Adja hozzá a létrehozott fiókot.

    e. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
