---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179156"
---
#### <a name="to-cable-for-power"></a>A tápkábelhez
1. Győződjön meg arról, hogy az egyes táp- és hűtőmodulok (PCM-ek) tápkapcsolói KI állásban vannak.
2. Csatlakoztassa a tápkábeleket az elsődleges ház minden PCM-jéhez.
3. Csatlakoztassa a tápkábeleket a rack áramelosztó egységeihez (PDU-k) az alábbi képen látható módon. Győződjön meg arról, hogy a két PCM külön áramforrást használ.
   
   > [!IMPORTANT]
   > A rendszer magas rendelkezésre állásának biztosítása érdekében azt javasoljuk, hogy szigorúan tartsa be az alábbi ábrán látható hálózati kábelezési sémát. 
   > 
   > 
   
    ![Kábelezze a 2U-s készüléket a tápellátáshoz](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Hálózati kábelezés egy 8100-as eszközön**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Vezérlő 1 |
   | 3 |Vezérlő 0 |
   | 4 |PCM 1 |
   | 5 |PDUSz |
4. A rendszer bekapcsolásához fordítsa be mindkét PCM tápkapcsolóját BE állásba.

