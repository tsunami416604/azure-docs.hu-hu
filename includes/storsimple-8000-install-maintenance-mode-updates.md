---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166398"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül a karbantartási módú frissítések telepítése

StorSimple-eszköz karbantartási módú frissítések alkalmazza, ha az összes i/o-kérések fel van függesztve. Nem felejtő közvetlen elérésű memória (NVRAM) például szolgáltatások vagy a fürtözési szolgáltatás leáll. Mindkét vezérlő újraindítása, adja meg, vagy lépjen ki az ebben a módban. Ebben a módban való kilépéskor összes szolgáltatás folytatása, és kifogástalan állapotú. (Ez eltarthat néhány percig.)

> [!IMPORTANT]
> * Mielőtt belép a karbantartási módot, győződjön meg arról, hogy mindkét eszközvezérlő kifogástalan állapotú, az Azure Portalon. Ha a vezérlő állapota nem kifogástalan, [forduljon a Microsoft támogatási](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) a következő lépésekhez.
> * Karbantartási módban van, ha egy tartományvezérlő és a többi vezérlő először frissíteni szeretné.

1. Használja a PuTTY a soros konzoljához való csatlakozáshoz. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt. Válassza ki az 1. lehetőség, **jelentkezzen be a teljes hozzáféréssel**.

2. A tartományvezérlő karbantartási módba helyezni, írja be:
    
    `Enter-HcsMaintenanceMode`

    Mindkét vezérlő újraindul karbantartási módban.

3. A karbantartási módú frissítések telepítése. Típus:

    `Start-HcsUpdate`

    Megerősítést kér. Miután meggyőződött a frissítéseket, a tartományvezérlővel, amely jelenleg hozzáférnek telepítik őket. A frissítések telepítése után újraindul a tartományvezérlő.

4. A frissítések állapotának figyelésére. Jelentkezzen be a partner-tartományvezérlő az aktuális vezérlő frissítése folyamatban van, és nem tudja feldolgozni a más parancsok. Típus:

    `Get-HcsUpdateStatus`

    Ha a `RunInProgress` van `True`, a frissítés még folyamatban van. Ha `RunInProgress` van `False`, az azt jelzi, hogy a frissítés befejeződött.

5. Miután a lemezfirmware-frissítések sikeres alkalmazását, és a frissített vezérlő újraindítása, ellenőrizze a lemezfirmware verzióját. Adja meg a frissített vezérlő:

    `Get-HcsFirmwareVersion`
   
    A várt lemezfirmware-verzió a következők:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Lépjen ki a karbantartási módot. Írja be a következő parancsot mindkét eszközvezérlő esetében:

    `Exit-HcsMaintenanceMode`

    A vezérlők újraindulnak, miután kilépett a karbantartási módból.

7. Lépjen vissza az Azure Portalra. A portálon nem jelenhet meg, hogy telepítette-e a karbantartási módú frissítések 24 órán keresztül.