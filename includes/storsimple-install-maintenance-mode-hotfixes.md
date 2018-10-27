---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e4921be3116754f146ed0845513010f2642c97b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165625"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül a karbantartási módú gyorsjavítások telepítése
> [!IMPORTANT]
> Karbantartási módban kell egy tartományvezérlőre, majd a másik vezérlőre először telepítse a gyorsjavítást.
> 
> 

1. Az eszköz karbantartási módba. Lásd: [2. lépés: Adja meg a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step2) adja meg a karbantartási mód útmutatást.
2. Telepítse a gyorsjavítást, írja be:
   
     `Start-HcsHotfix` 
3. Amikor a rendszer kéri, adja meg a hálózati megosztott mappa, amely tartalmazza a gyorsjavítások fájljainak elérési útját.
4. Meg kell adnia megerősítést. Típus **Y** folytatásához a gyorsjavítás telepítését.
5. A gyorsjavítás az egyik vezérlő alkalmazását követően jelentkezzen be a másik vezérlőre. Telepítse a gyorsjavítást, ahogy az előző vezérlőhöz.
6. A gyorsjavítások is vonatkozik, miután kilépett a karbantartási módból. Lásd: [4. lépés: kilépési karbantartási módba](../articles/storsimple/storsimple-update-device.md#step4) útmutatást.

