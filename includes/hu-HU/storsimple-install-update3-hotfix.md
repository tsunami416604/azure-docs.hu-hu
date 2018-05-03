<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>Gyorsjavítások letöltése
Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.
2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.
    ![Katalógus telepítése](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. A keresési mezőbe, a Microsoft Update katalógus, adja meg a Tudásbázis (KB) le szeretné tölteni, például a gyorsjavítás **3186843**, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **összegző szoftverfrissítési csomagot frissítés 3.0 a StorSimple 8000 Series**.
   
    ![Keresés a katalógusban](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Kattintson az **Add** (Hozzáadás) parancsra. Ezzel a frissítést hozzáadja a kosárhoz.
5. A fenti táblázatban szereplő további gyorsjavítások keresése (**3186859**), és adja hozzá a kosárhoz mindegyik.
6. Kattintson a **Kosár megtekintése** gombra.
7. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A szoftverfrissítések letöltése a megadott helyre és a neve megegyezik a frissítési alárendelt mappába helyezi. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

> [!NOTE]
> A gyorsjavítások mindkét vezérlők annak a partner tartományvezérlőről esetleges hibaüzeneteket észlelésére elérhetőknek kell lenniük.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Normál módú gyorsjavítások telepítése és ellenőrzése
A normál módú gyorsjavítások telepítéséhez és ellenőrzéséhez hajtsa végre az alábbi lépéseket. Ha már telepítette azokat az Azure portál használatával, ugorjon előre [telepítse, és ellenőrizze a karbantartási mód gyorsjavítások](#to-install-and-verify-maintenance-mode-hotfixes).

1. A gyorsjavítások telepítéséhez nyissa meg a Windows PowerShell felületét a StorSimple-eszköz soros konzoljában. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt.
2. Válassza az **1. lehetőséget** a teljes körű hozzáféréssel való bejelentkezéshez az eszközbe. Javasoljuk, hogy előbb a passzív vezérlőre telepítse a gyorsjavítást.
3. A gyorsjavítás telepítéséhez írja be a következőt a parancssorba:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    A fenti parancsban a megosztás elérési útjában DNS-cím helyett inkább IP-címet adjon meg. A hitelesítési paramétert csak akkor kell használni, ha egy hitelesített megosztáshoz kíván hozzáférni.
   
    Javasoljuk, hogy a megosztások elérése során használja a hitelesítési paramétert. Még a „mindenki” számára elérhető megosztások sem elérhetőek a nem hitelesített felhasználók számára.
   
    Amikor a rendszer kéri, adja meg a jelszót.
   
    Az alábbiakban egy példa látható a kimenetre.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Írja be az **Y** karaktert, amikor a rendszer kéri, hogy erősítse meg a gyorsjavítás telepítését.
5. A frissítést a `Get-HcsUpdateStatus` parancsmag használatával figyelheti. A frissítés előbb a passzív vezérlőn lesz végrehajtva. Miután a passzív vezérlő frissítve lett, az azt követő feladatátvétel során a frissítés a másik vezérlőre is alkalmazva lesz. A frissítés akkor fejeződött be, ha mindkét vezérlő frissítve lett.
   
    Az alábbi kimeneti példa azt mutatja, hogy a frissítés még folyamatban van. A `RunInprogress` értéke `True`, ha a frissítés folyamatban van.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Az alábbi kimeneti példa azt mutatja, hogy a frissítés befejeződött. A `RunInProgress` értéke `False`, ha a frissítés befejeződött.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > Alkalmanként a parancsmag `False` értéket jelenít meg, amikor a frissítés még folyamatban van. A gyorsjavítás befejezéséhez várjon néhány percet, majd futtassa újra a parancsot, és ellenőrizze, hogy a `RunInProgress` értéke `False`. Ha így van, a gyorsjavítás befejeződött.

1. A szoftverfrissítés befejeztét követően ellenőrizze a rendszerszoftverek verzióját. Típus:
   
    `Get-HcsSystem`
   
    A következő verziónak kell megjelennie:
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     A frissítés telepítését követően ne módosítsa a verziószámok, azt jelzi, hogy a gyorsjavítás alkalmazása sikertelen volt. Ha ezt látja, további segítségért forduljon a [Microsoft támogatási szolgálatához](../articles/storsimple/storsimple-contact-microsoft-support.md).
     
     > [!IMPORTANT]
     > A további frissítések végrehajtása előtt újra kell indítania az aktív vezérlőt a `Restart-HcsController` parancsmaggal. 
     > 
     > 
2. Ismételje meg a 3-5 LSI illesztőprogram és a belső vezérlőprogram gyorsjavítás telepítése **KB3186859**. A gyorsjavítás telepítését követően a `Get-HcsSystem` parancsmag. A LSI verziónak kell lennie:
   
   * `Lsisas2Version: 2.0.78.00`
3. Ismételje meg a 3-5 a Storport és Spaceport frissítés **KB3121261**.
4. 2. frissítés vagy korábbi verzió frissítésekor, is szüksége lesz töltheti le:
   
   * az iSCSI-javítás KB3146621 használatával
   * WMI-javítás KB3103616 használatával

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Karbantartási módú gyorsjavítások telepítése és ellenőrzése
Belső vezérlőprogram-frissítésekre telepítendő KB3121899 használja. Ezek működési zavart okozó frissítések, amelyeknek a végrehajtása körülbelül 30 percet vesz igénybe. Ha úgy dönt, ezeket egy karbantartási időszakban is telepítheti, ha csatlakozik az eszköz soros konzoljához.

Vegye figyelembe, hogy amennyiben a lemezfirmware már naprakész, nem szükséges telepítenie ezeket a frissítéseket. Futtassa a `Get-HcsUpdateAvailability` parancsmagot az eszköz soros konzoljáról, és ellenőrizze, hogy vannak-e elérhető frissítések, és azok zavart okozó (karbantartási módú) vagy zavart nem okozó (normál módú) frissítések-e.

A lemezfirmware-frissítések telepítéséhez kövesse az alábbi utasításokat.

1. Helyezze az eszközt a karbantartási módban. Vegye figyelembe, hogy ne használjon Windows PowerShell távoli eljáráshívás karbantartási módban lévő eszköz történő csatlakozás során. Ehelyett futtassa ezt a parancsmagot a eszköz tartományvezérlőn, amikor az eszköz soros konzolon keresztül csatlakoznak. Típus:
   
    `Enter-HcsMaintenanceMode`
   
    Az alábbiakban egy példa látható a kimenetre.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
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
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. A telepítési folyamatot a `Get-HcsUpdateStatus` parancs használatával figyelheti. A frissítés akkor fejeződött be, ha a `RunInProgress` `False` értékre vált.
4. Miután befejeződött a telepítés, a vezérlő, amelyre a karbantartási módú gyorsjavítás telepítve lett, újraindul. Jelentkezzen be az 1. lehetőséggel teljes hozzáféréssel, és ellenőrizze a lemezfirmware verzióját. Típus:
   
   `Get-HcsFirmwareVersion`
   
   A várt lemezfirmware-verzió a következő:
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   Az alábbiakban egy példa látható a kimenetre.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Futtassa a `Get-HcsFirmwareVersion` parancsot a második vezérlőn annak ellenőrzéséhez, hogy a szoftververzió frissítve lett-e. Ezután kiléphet a karbantartási módból. Ehhez írja be a következő parancsot mindkét eszközvezérlő esetében:
   
   `Exit-HcsMaintenanceMode`
5. A tartományvezérlők újraindítása a kilép karbantartási módból. A lemezfirmware-frissítések sikeres alkalmazását követően, miután az eszköz kilépett a karbantartási módból, térjen vissza a klasszikus Azure portálhoz. Vegye figyelembe, hogy a portál nem lehet, hogy megjelenítése, hogy telepítette-e a karbantartási mód frissítései 24 óra.

