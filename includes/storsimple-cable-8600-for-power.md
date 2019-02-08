---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889511"
---
#### <a name="to-cable-your-device-for-power"></a>A teljesítményre az eszköz bekábelezése
> [!NOTE]
> A StorSimple eszköz mindkét ház közé tartozik a redundáns PCMs. Az összes rendszerházon a PCMs telepítve legyen, és csatlakozik a különböző áramforrásokhoz magas rendelkezésre állás biztosítása érdekében.
> 
> 

1. Győződjön meg arról, hogy az összes PCMs power kapcsolói OFF állásban van.
2. A elsődleges tárolóeszközön csatlakozzon a tápkábelek mindkét PCMs. A tápkábelek azonosítja a kiemelt kábelezési ábrán pirossal alatt.
3. Győződjön meg arról, hogy a két PCMs a elsődleges tárolóeszközön különböző áramforrásokhoz.
4. A tápkábelek csatlakoztatása az állvány Áramelosztó egységekből bekapcsolási, ahogyan a kábelezést diagram a power.
5. Az EBOD ház ismételje meg a 2 – 4.
6. Kapcsolja be a EBOD ház által a főkapcsoló tükrözés, az egyes PCM ON helyére.
7. Győződjön meg arról, hogy a a EBOD ház ellenőrzésével, a zöld LED-ek az EBOD-vezérlő hátuljára be vannak kapcsolva be van kapcsolva.
8. Kapcsolja be az elsődleges ház által tükrözés, minden egyes PCM kapcsoló ON helyére.
9. Ellenőrizze, hogy a rendszer a úgy, hogy az eszközvezérlővel LED-ek be van kapcsolva.
10. Győződjön meg arról, hogy az EBOD-vezérlő és az eszközvezérlő közötti kapcsolat aktív szerint annak ellenőrzése, hogy a négy LED-ek a SAS-port az EBOD-vezérlő melletti zöld.
    
    > [!IMPORTANT]
    > A rendszer a magas rendelkezésre állás biztosítása érdekében azt javasoljuk, Ön szigorúan betartsák a kábelezést séma a következő ábrán látható power.
    > 
    > 
    
    ![Az energiagazdálkodási 4U eszköz kábelezése](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Energiagazdálkodási kábelezése**
    
    | Címke | Leírás |
    |:--- |:--- |
    | 1 |Elsődleges ház |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |0. vezérlő |
    | 5 |1. vezérlő |
    | 6 |Az EBOD-vezérlő 0 |
    | 7 |Az EBOD-vezérlő 1 |
    | 8 |EBOD ház |
    | 9 |PDU-k |

