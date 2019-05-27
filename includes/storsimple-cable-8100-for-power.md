---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155793"
---
#### <a name="to-cable-for-power"></a>A bekapcsolási kábelezése
1. Ellenőrizze, hogy az egyes az energiagazdálkodási vált a power, és hűtéssel modulok (PCMs) OFF állásban van.
2. Csatlakozzon a tápkábelek az egyes elsődleges helyiségben a PCMs.
3. A tápkábelek csatlakoztatása az állvány Áramelosztó egységekből (PDU-k) a következő képen látható módon. Győződjön meg arról, hogy a két PCMs használatát különböző áramforrásokhoz.
   
   > [!IMPORTANT]
   > A rendszer a magas rendelkezésre állás biztosítása érdekében azt javasoljuk, Ön szigorúan betartsák a kábelezést séma a következő ábrán látható power. 
   > 
   > 
   
    ![Az energiagazdálkodási 2U eszköz kábelezése](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Energiagazdálkodási kábelek 8100-as eszközön**
   
   | Felirat | Leírás |
   |:--- |:--- |
   | 1. |PCM 0 |
   | 2 |1. vezérlő |
   | 3 |0. vezérlő |
   | 4 |PCM 1 |
   | 5 |PDU-k |
4. Kapcsolja be a rendszer, a kiemelt kapcsolók tükrözése mindkét PCMs ON helyére.

