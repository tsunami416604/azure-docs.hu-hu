---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179154"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Karbantartási mód frissítéseinek telepítése a StorSimple-hez használható Windows PowerShell rendszeren keresztül
1. Ha még nem tette meg, akkor férjen hozzá az eszköz soros konzoljához, és válassza az 1 lehetőséget, **a Teljes hozzáféréssel való bejelentkezés**lehetőséget. 
2. Írja be a jelszót. Az alapértelmezett jelszó a **Password1**.
3. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability` 
4. Értesítést kap, ha a frissítések elérhetők, és hogy a frissítések zavaróak vagy nem zavaróak.You will be be available if updates are available and whether the updates are disruptive or non-disruptive. A zavaró frissítések alkalmazásához az eszközt karbantartási módba kell helyeznie. Az utasításokat lásd [2.](../articles/storsimple/storsimple-update-device.md#step2)
5. Ha az eszköz karbantartási módban van, a parancssorba írja be a következőt:`Start-HcsUpdate`
6. A rendszer a művelet megerősítését kéri. Miután megerősítette a frissítéseket, azok az éppen elérhető vezérlőre lesznek telepítve. A frissítések telepítése után a vezérlő újraindul. 
7. A frissítések állapotának figyelése. Típus:
   
    `Get-HcsUpdateStatus`
   
    Ha `RunInProgress` az, `True`a frissítés még folyamatban van. Ha `RunInProgress` `False`van, az azt jelzi, hogy a frissítés befejeződött.  
8. Ha a frissítés telepítve van az aktuális vezérlőre, és újraindult, csatlakozzon a másik vezérlőhöz, és hajtsa végre az 1–6.
9. A két vezérlő frissítése után lépjen ki a Karbantartási módból. Lásd [a 4.](../articles/storsimple/storsimple-update-device.md#step4)

