---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0755c01fe8e13e8e39c0b453198f2b67c51a2bc4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125334"
---
#### <a name="to-download-hotfixes"></a>Gyorsjavítások letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

1. Indítsa el az Internet Explorert és keresse meg [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).
2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

    ![Katalógus telepítése](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. A Microsoft Update katalógus keresőmezőjében adja meg a letölteni kívánt gyorsjavítás tudásbázis- (KB-) számát (például: **4011839**), majd kattintson a **Keresés** gombra.
   
    Megjelenik a tételszerű gyorsjavítás, például. **A StorSimple 8000 sorozat összegző szoftvercsomag-frissítésének 4.0-s verziója**.
   
    ![Keresés a katalógusban](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. Kattintson a fájlokat, és töltse le a megadott hely és a mappa. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.
5. A fenti táblázatban felsorolt további gyorsjavításokra keresése (**4011841**), és töltse le a megfelelő fájlokat a bizonyos mappákra, az előző táblázatban felsorolt.

> [!NOTE]
> A gyorsjavítások mindkét vezérlő észleli a potenciális hibaüzenetek a társ vezérlőről elérhetőknek kell lenniük.
>
> A gyorsjavításokat 3 különböző mappába kell másolni. Például az szoftver/Cis/MDS ügynök frissítésére lehet másolni az _FirstOrderUpdate_ mappában, minden más zavart nem okozó frissítést sikerült másolni a _SecondOrderUpdate_ mappát, és másolja a karbantartási módú frissítések _ThirdOrderUpdate_ mappát.

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Normál módú gyorsjavítások telepítése és ellenőrzése

A normál módú gyorsjavítások telepítéséhez és ellenőrzéséhez hajtsa végre az alábbi lépéseket. Ha már telepítette ezeket a klasszikus Azure portál segítségével, folytassa a [karbantartási módú gyorsjavítások telepítésének és ellenőrzésének](#to-install-and-verify-maintenance-mode-hotfixes) lépésével.

1. A gyorsjavítások telepítéséhez nyissa meg a Windows PowerShell felületét a StorSimple-eszköz soros konzoljában. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt.
2. Válassza ki az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Javasoljuk, hogy előbb a passzív vezérlőre telepítse a gyorsjavítást.
3. A gyorsjavítás telepítéséhez írja be a következőt a parancssorba:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    A fenti parancsban a megosztás elérési útjában DNS-cím helyett inkább IP-címet adjon meg. A hitelesítési paramétert csak akkor kell használni, ha egy hitelesített megosztáshoz kíván hozzáférni.
   
    Javasoljuk, hogy a megosztások elérése során használja a hitelesítési paramétert. Még a „mindenki” számára elérhető megosztások sem elérhetőek a nem hitelesített felhasználók számára.
   
    Amikor a rendszer kéri, adja meg a jelszót.
   
    Alább látható egy példa az elsőrendű frissítések telepítésének kimenetére. Az első elsőrendű frissítés kell, hogy az adott fájlra mutasson.
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
4. Írja be az **Y** karaktert, amikor a rendszer kéri, hogy erősítse meg a gyorsjavítás telepítését.
5. A frissítést a `Get-HcsUpdateStatus` parancsmag használatával figyelheti. A frissítés előbb a passzív vezérlőn lesz végrehajtva. Miután a passzív vezérlő frissítve lett, az azt követő feladatátvétel során a frissítés a másik vezérlőre is alkalmazva lesz. A frissítés akkor fejeződött be, ha mindkét vezérlő frissítve lett.
   
    Az alábbi kimeneti példa azt mutatja, hogy a frissítés még folyamatban van. A `RunInprogress` értéke `True`, ha a frissítés folyamatban van.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Az alábbi kimeneti példa azt mutatja, hogy a frissítés befejeződött. A `RunInProgress` értéke `False`, ha a frissítés befejeződött.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Alkalmanként a parancsmag `False` értéket jelenít meg, amikor a frissítés még folyamatban van. A gyorsjavítás befejezéséhez várjon néhány percet, majd futtassa újra a parancsot, és ellenőrizze, hogy a `RunInProgress` értéke `False`. Ha így van, a gyorsjavítás befejeződött.

6. A szoftverfrissítés befejeztét követően ellenőrizze a rendszerszoftverek verzióját. Típus:
   
    `Get-HcsSystem`
   
    A következő verziónak kell megjelennie:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   * `HcsSoftwareVersion: 6.3.9600.17820`
   
     Ha a verziószám nem változik a frissítés telepítését követően, ez azt jelzi, hogy a gyorsjavítás nem lett telepítve. Ha ezt látja, további segítségért forduljon a [Microsoft támogatási szolgálatához](../articles/storsimple/storsimple-contact-microsoft-support.md).
     
     > [!IMPORTANT]
     > Újra kell indítania az aktív vezérlőt a `Restart-HcsController` parancsmag a következő frissítés alkalmazása előtt.
     
7. Ismételje meg a 3-5 letöltött Cis/MDS-ügynök telepítése a _FirstOrderUpdate_ mappát. 
8. A másodrendű frissítések telepítéséhez hajtsa ismét végre a 3–5. lépést. **A másodrendű frissítések, egyszerre több frissítést is telepíthető egyszerűen futtassa a `Start-HcsHotfix cmdlet` mutató a mappát, ahol a másodrendű frissítések találhatók. A parancsmag az összes rendelkezésre álló frissítések végrehajtja a mappában.** Ha valamely frissítés már telepítve lett, a frissítési logika észleli ezt, és nem alkalmazza újra az adott frissítést. 

Miután az összes gyorsjavítás telepítve lett, használja a `Get-HcsSystem` parancsmagot. A verziónak a következőknek kell lennie:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Karbantartási módú gyorsjavítások telepítése és ellenőrzése
A lemezfirmware-frissítések telepítéséhez használja a KB4011837 jelű csomagot. Ezek működési zavart okozó frissítések, amelyeknek a végrehajtása körülbelül 30 percet vesz igénybe. Ha úgy dönt, ezeket egy karbantartási időszakban is telepítheti, ha csatlakozik az eszköz soros konzoljához.

Vegye figyelembe, hogy amennyiben a lemezfirmware már naprakész, nem szükséges telepítenie ezeket a frissítéseket. Futtassa a `Get-HcsUpdateAvailability` parancsmagot az eszköz soros konzoljáról, és ellenőrizze, hogy vannak-e elérhető frissítések, és azok zavart okozó (karbantartási módú) vagy zavart nem okozó (normál módú) frissítések-e.

A lemezfirmware-frissítések telepítéséhez kövesse az alábbi utasításokat.

1. Helyezze az eszközt karbantartási módba. **Ügyeljen arra, hogy ne a Windows PowerShell távoli eljáráshívás segítségével csatlakozzon az eszközhöz a karbantartási módban. Ehelyett futtassa az alábbi parancsmagot az eszközvezérlőn, miközben az eszköz soros konzolján keresztül csatlakozik az eszközhöz.** Típus:
   
    `Enter-HcsMaintenanceMode`
   
    Az alábbiakban egy példa látható a kimenetre.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Ekkor mindkét vezérlő újraindul karbantartási módban.
2. A lemezfirmware-frissítés telepítéséhez írja be a következőt:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Az alábbiakban egy példa látható a kimenetre.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. A telepítési folyamatot a `Get-HcsUpdateStatus` parancs használatával figyelheti. A frissítés akkor fejeződött be, ha a `RunInProgress` `False` értékre vált.
4. Miután befejeződött a telepítés, a vezérlő, amelyre a karbantartási módú gyorsjavítás telepítve lett, újraindul. Jelentkezzen 1,. lehetőség a **jelentkezzen be a teljes hozzáféréssel**, és ellenőrizze a lemezfirmware verzióját. Típus:
   
   `Get-HcsFirmwareVersion`
   
   A várt lemezfirmware-verzió a következő:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   Az alábbiakban egy példa látható a kimenetre.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17820
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Futtassa a `Get-HcsFirmwareVersion` parancsot a második vezérlőn annak ellenőrzéséhez, hogy a szoftververzió frissítve lett-e. Ezután kiléphet a karbantartási módból. Ehhez írja be a következő parancsot mindkét eszközvezérlő esetében:
   
   `Exit-HcsMaintenanceMode`

5. A vezérlők újraindulnak, miután kilépett a karbantartási módból. A lemezfirmware-frissítések sikeres alkalmazását követően, miután az eszköz kilépett a karbantartási módból, térjen vissza a klasszikus Azure portálhoz. Vegye figyelembe, hogy a portálon esetleg még 24 órán keresztül nem jelenik meg, hogy a karbantartási módú frissítések telepítve lettek.

