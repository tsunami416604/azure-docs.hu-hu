---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0e8db8779958afe1fd3cf4bf12f2a6fd4a97c61c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165706"
---
<!--author=alkohli last changed: 9/16/15-->

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
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0-BAN |
   | 2 |1. vezérlő |
   | 3 |0. vezérlő |
   | 4 |A PCM 1 |
   | 5 |PDU-k |
4. Kapcsolja be a rendszer, a kiemelt kapcsolók tükrözése mindkét PCMs ON helyére.

