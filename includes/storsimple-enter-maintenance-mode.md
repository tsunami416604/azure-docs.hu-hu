---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166183"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Az adja meg a karbantartási mód
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**.
2. Írja be a jelszót. Az alapértelmezett jelszó az **jelszó1**.
3. Írja be a parancssorba
   
     `Enter-HcsMaintenanceMode`
4. Egy figyelmeztető üzenet, amely arról tájékoztat, hogy karbantartási módba fog megszakítja az összes i/o-kérelmek és a kiszolgálót a kapcsolatot a klasszikus Azure portálra, és megerősítést fog kérni, látni fogja. Típus **Y** , adja meg a karbantartási módot.
   
    Mindkét vezérlő újraindul. Az újraindítás befejeződése után egy másik üzenet jelenik meg, amely azt jelzi, hogy az eszköz karbantartási módban van.

