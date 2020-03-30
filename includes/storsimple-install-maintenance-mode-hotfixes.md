---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179149"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Karbantartási mód gyorsjavításainak telepítése a StorSimple-hez szervizelt Windows PowerShell rendszeren keresztül
> [!IMPORTANT]
> Karbantartás módban először a gyorsjavítást kell alkalmazniaz egyik vezérlőre, majd a másik vezérlőre.
> 
> 

1. Helyezze a készüléket karbantartási üzemmódba. A Karbantartási [módba](../articles/storsimple/storsimple-update-device.md#step2) való belépésmódjáról a 2.
2. A gyorsjavítás alkalmazásához írja be a következőt:
   
     `Start-HcsHotfix` 
3. Amikor a rendszer kéri, adja meg a gyorsjavításfájlokat tartalmazó megosztott hálózati mappa elérési útját.
4. A rendszer a művelet megerősítését kéri. Írja be az **Y** típust a gyorsjavítás telepítésének folytatásához.
5. Miután alkalmazta a gyorsjavítást az egyik vezérlőn, jelentkezzen be a másik vezérlőbe. Alkalmazza a gyorsjavítást az előző vezérlőhöz képest.
6. A gyorsjavítások alkalmazása után lépjen ki a Karbantartási módból. Lásd [a 4.](../articles/storsimple/storsimple-update-device.md#step4)

