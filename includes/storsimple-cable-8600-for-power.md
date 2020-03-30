---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179155"
---
#### <a name="to-cable-your-device-for-power"></a>A készülék kábelezése a tápellátáshoz
> [!NOTE]
> A StorSimple-eszköz mindkét háza redundáns PCM-eket tartalmaz. A magas rendelkezésre állás biztosítása érdekében minden egyes házhoz telepíteni és különböző áramforrásokhoz kell csatlakoztatni a PCM-eket.
> 
> 

1. Győződjön meg arról, hogy az összes PCM tápkapcsolója KI állásban van.
2. Az elsődleges burkolaton csatlakoztassa a tápkábeleket mindkét PCM-hez. A tápkábeleket pirosan azonosítják az alábbi kábelezési ábrán.
3. Győződjön meg arról, hogy az elsődleges ház két PCM-je külön áramforrást használ.
4. Csatlakoztassa a tápkábeleket az állványelosztó egységek áramellátásához, ahogy az a hálózati kábelezési ábrán látható.
5. Ismételje meg a 2-4.
6. Kapcsolja be az EBOD burkolatot úgy, hogy minden PCM-en a tápkapcsolót BE állásba fordítja.
7. Ellenőrizze, hogy az EBOD-ház be van-e kapcsolva, ha ellenőrzi, hogy az EBOD vezérlő hátoldalán lévő zöld LED-ek be vannak-e kapcsolva.
8. Kapcsolja be az elsődleges burkolatot úgy, hogy minden PCM-kapcsolót BE állásba fordít.
9. Ellenőrizze, hogy a rendszer be van-e kapcsolva, biztosítva, hogy az eszközvezérlő LED-jei be vannak kapcsolva.
10. Győződjön meg arról, hogy az EBOD-vezérlő és az eszközvezérlő közötti kapcsolat aktív-e annak ellenőrzésével, hogy az EBOD-vezérlő SAS-portja melletti négy LED zöld-e.
    
    > [!IMPORTANT]
    > A rendszer magas rendelkezésre állásának biztosítása érdekében azt javasoljuk, hogy szigorúan tartsa be az alábbi ábrán látható hálózati kábelezési sémát.
    > 
    > 
    
    ![Kábelezze a 4U-s készüléket a tápellátáshoz](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Tápkábelezés**
    
    | Címke | Leírás |
    |:--- |:--- |
    | 1 |Elsődleges burkolat |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Vezérlő 0 |
    | 5 |Vezérlő 1 |
    | 6 |EBOD vezérlő 0 |
    | 7 |EBOD-vezérlő 1 |
    | 8 |EBOD ház |
    | 9 |PDUSz |

