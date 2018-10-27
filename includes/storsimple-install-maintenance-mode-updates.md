---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 01612b32e6c1b363df8a5c70405d0c709210328e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164314"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül a karbantartási módú frissítések telepítése
1. Ha még nem tette meg, az eszköz soros konzoljához és select 1. lehetőség – hozzáféréshez **jelentkezzen be a teljes hozzáféréssel**. 
2. Írja be a jelszót. Az alapértelmezett jelszó az **jelszó1**.
3. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability` 
4. Akkor kap értesítést, ha elérhető frissítések, és hogy a frissítések zavart okozó vagy zavart nem okozó legyenek-e. A alkalmazni zavart okozó frissítések, az eszköz karbantartási módba kell. Lásd: [2. lépés: Adja meg a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step2) útmutatást.
5. Amikor az eszköz karbantartási módban van, a parancsot, írja be a parancssorba: `Start-HcsUpdate`
6. Meg kell adnia megerősítést. Miután meggyőződött a frissítéseket, a tartományvezérlővel, amely jelenleg hozzáférnek lesz telepíthető. A frissítések telepítése után újraindul a tartományvezérlő. 
7. A frissítések állapotának figyelésére. Típus:
   
    `Get-HcsUpdateStatus`
   
    Ha a `RunInProgress` van `True`, a frissítés még folyamatban van. Ha `RunInProgress` van `False`, az azt jelzi, hogy a frissítés befejeződött.  
8. Ha a frissítés telepítve van a jelenlegi vezérlő, és azt újraindult, a másik vezérlőre csatlakozzon, és 1 – 6. lépéseket.
9. Mindkét vezérlő frissítve lett, miután kilépett a karbantartási módból. Lásd: [4. lépés: kilépési karbantartási módba](../articles/storsimple/storsimple-update-device.md#step4) útmutatást.

