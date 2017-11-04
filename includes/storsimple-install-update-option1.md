<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>Gyorsjavítások letöltése
A következő lépésekkel letölti a szoftverfrissítést.

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.
2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.
    ![Katalógus telepítése](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. A keresési mezőbe, a Microsoft Update katalógus, adja meg a Tudásbázis (KB) le szeretné tölteni, például a gyorsjavítás **3063418**, és kattintson a **keresési**.
4. Látni fogja a **StorSimple frissítés 1.2-es készülékek frissítés** csomagot. Kattintson az **Add** (Hozzáadás) parancsra. A frissítés a rendszer a kosárhoz adja hozzá.
5. A fenti táblázatban szereplő további gyorsjavítások keresése (**3043005** és **3063416**), és adja hozzá mindegyik a kosárhoz.
6. Kattintson a **Kosár megtekintése** gombra.
   
    ![Kosár megtekintése](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A frissítések a megadott helyre lesznek letöltve, azon belül az egyes frissítések nevével egyező nevű almappákba. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

> [!NOTE]
> A gyorsjavítások mindkét vezérlők annak a partner tartományvezérlőről esetleges hibaüzeneteket észlelésére elérhetőknek kell lenniük.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Normál módú gyorsjavítások telepítése és ellenőrzése
A következő lépésekkel telepítse, és ellenőrizze a normál módú gyorsjavítások. Ha már telepítette azokat az Azure portál használatával, ugorjon előre [telepítse, és ellenőrizze a karbantartási mód gyorsjavítások](#to-install-and-verify-maintenance-mode-hotfixes).

1. A szoftverfrissítés telepítéséhez elérni a Windows PowerShell-felületet a StorSimple eszköz soros konzolon. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt.
2. Válassza az **1. lehetőséget** a teljes körű hozzáféréssel való bejelentkezéshez az eszközbe.
3. A frissítés telepítéséhez, a parancssorba írja be:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    A fenti parancsban a megosztás elérési útjában DNS-cím helyett inkább IP-címet adjon meg. A hitelesítési paramétert csak akkor kell használni, ha egy hitelesített megosztáshoz kíván hozzáférni.
   
    Javasoljuk, hogy a megosztások elérése során használja a hitelesítési paramétert. Még a „mindenki” számára elérhető megosztások sem elérhetőek a nem hitelesített felhasználók számára.
   
    Az alábbiakban egy példa látható a kimenetre.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. Írja be az **Y** karaktert, amikor a rendszer kéri, hogy erősítse meg a gyorsjavítás telepítését.
5. A frissítést a `Get-HcsUpdateStatus` parancsmag használatával figyelheti.
   
    Az alábbi kimeneti példa azt mutatja, hogy a frissítés még folyamatban van. A `RunInprogress` értéke `True`, ha a frissítés folyamatban van.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Az alábbi kimeneti példa azt mutatja, hogy a frissítés befejeződött. A `RunInProgress` értéke `False`, ha a frissítés befejeződött.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > Alkalmanként a parancsmag `False` értéket jelenít meg, amikor a frissítés még folyamatban van. A gyorsjavítás befejezéséhez várjon néhány percet, majd futtassa újra a parancsot, és ellenőrizze, hogy a `RunInProgress` értéke `False`. Ha így van, a gyorsjavítás befejeződött.
   > 
   > 
6. A szoftverfrissítés befejeztét követően ellenőrizze a rendszerszoftverek verzióját. Írja be a következő parancsot:
   
    `Get-HcsSystem`
   
    A következő verziónak kell megjelennie:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     A frissítés telepítését követően ne módosítsa a verziószámok, azt jelzi, hogy a gyorsjavítás alkalmazása sikertelen volt. Ha ezt látja, további segítségért forduljon a [Microsoft támogatási szolgálatához](../articles/storsimple/storsimple-contact-microsoft-support.md).
7. Telepítse a többi normál módú gyorsjavítást (KB3043005) ismételje meg a 3-5.

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Karbantartási módú gyorsjavítások telepítése és ellenőrzése
Belső vezérlőprogram-frissítésekre telepítendő KB3063416 használja. Ezek zavaró frissítések, és körülbelül 30-45 percig tarthat befejezéséhez. Ha úgy dönt, ezeket egy karbantartási időszakban is telepítheti, ha csatlakozik az eszköz soros konzoljához.

A lemezfirmware-frissítések telepítéséhez kövesse az alábbi utasításokat.

1. Állítsa az eszköz karbantartási módba. Vegye figyelembe, hogy ne használjon Windows PowerShell távoli eljáráshívás karbantartási módban lévő eszköz történő csatlakozás során. Szüksége lesz az eszköz történik az eszköz soros konzolon keresztül csatlakozáskor futtassa ezt a parancsmagot. Típus:
   
    `Enter-HcsMaintenanceMode`
   
    Az alábbiakban egy példa látható a kimenetre.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Mindkét vezérlőhöz majd indítsa újra a számítógépet karbantartási módba.
2. A lemezfirmware-frissítés telepítéséhez írja be a következőt:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Az alábbiakban egy példa látható a kimenetre.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. A telepítési folyamatot a `Get-HcsUpdateStatus` parancs használatával figyelheti. A frissítés akkor fejeződött be, ha a `RunInProgress` `False` értékre vált.
4. A telepítés befejezése után, amelyre a karbantartási mód gyorsjavítás telepítve lett a vezérlő újra kell indítani. Jelentkezzen be az 1. lehetőséggel teljes hozzáféréssel, és ellenőrizze a lemezfirmware verzióját. Típus:
   
   `Get-HcsFirmwareVersion`
   
   A várt lemezfirmware-verzió a következő:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Futtassa a `Get-HcsFirmwareVersion` parancsot a második vezérlőn annak ellenőrzéséhez, hogy a szoftververzió frissítve lett-e. Ezután kiléphet a karbantartási módból. Írja be a minden eszköz vezérlő a következő parancsot:
   
   `Exit-HcsMaintenanceMode`
5. A tartományvezérlők újraindítása a kilép karbantartási módból. A lemezfirmware-frissítések sikeres alkalmazását követően, miután az eszköz kilépett a karbantartási módból, térjen vissza a klasszikus Azure portálhoz. Vegye figyelembe, hogy a portál nem lehet, hogy megjelenítése, hogy telepítette-e a karbantartási mód frissítései 24 óra.

