---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179315"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Karbantartási módfrissítéseinek telepítése a StorSimple-hez letölthető Windows PowerShell segítségével

Ha karbantartási módfrissítéseket alkalmaz a StorSimple eszközre, az összes I/O-kérés szünetel. Az olyan szolgáltatások, mint a nem felejtő véletlen hozzáférésű memória (NVRAM) vagy a fürtözési szolgáltatás leállnak. Mindkét vezérlő újraindul, amikor belép vagy kilép ebből a módból. Amikor kilép ebből a módból, az összes szolgáltatás folytatódik, és kifogástalan állapotban van. (Ez eltarthat néhány percig.)

> [!IMPORTANT]
> * A karbantartási módba való belépés előtt ellenőrizze, hogy mindkét eszközvezérlő kifogástalan állapotú-e az Azure Portalon. Ha a kontroller nem kifogástalan, a következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](../articles/storsimple/storsimple-8000-contact-microsoft-support.md)
> * Ha karbantartási módban van, először frissítenie kell az egyik vezérlőt, majd a másik vezérlőt.

1. A Soros konzolhoz való csatlakozáshoz használja a PuTTY-t. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt. Válassza az **1.**

2. A vezérlő karbantartási üzemmódba való behelyezéséhez írja be a következőt:
    
    `Enter-HcsMaintenanceMode`

    Mindkét vezérlő újraindul karbantartási módba.

3. Telepítse a karbantartási mód frissítéseit. Típus:

    `Start-HcsUpdate`

    A rendszer megerősítést kér. Miután megerősítette a frissítéseket, azok azéppen elérhető vezérlőre települnek. A frissítések telepítése után a vezérlő újraindul.

4. A frissítések állapotának figyelése. Jelentkezzen be a társvezérlőbe, miközben az aktuális vezérlő frissül, és nem tud más parancsokat feldolgozni. Típus:

    `Get-HcsUpdateStatus`

    Ha `RunInProgress` az, `True`a frissítés még folyamatban van. Ha `RunInProgress` `False`van, az azt jelzi, hogy a frissítés befejeződött.

5. A lemez belső vezérlőprogramjának frissítései sikeres alkalmazása és a frissített vezérlő újraindítása után ellenőrizze a lemez belső vezérlőprogramjának verzióját. A frissített vezérlőn írja be a következőt:

    `Get-HcsFirmwareVersion`
   
    A lemez belső vezérlőprogramjának várt verziói a következők:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Lépjen ki a karbantartási módból. Írja be a következő parancsot az egyes eszközvezérlőkhöz:

    `Exit-HcsMaintenanceMode`

    A vezérlők újraindulnak, miután kilépett a karbantartási módból.

7. Lépjen vissza az Azure Portalra. Előfordulhat, hogy a portál nem jeleníti meg, hogy 24 órán keresztül telepítette a karbantartási mód frissítéseit.