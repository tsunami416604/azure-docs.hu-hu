---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179146"
---
#### <a name="to-enter-maintenance-mode"></a>Az adja meg a karbantartási mód
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**.
2. Írja be a jelszót. Az alapértelmezett jelszó az **jelszó1**.
3. Írja be a parancssorba
   
     `Enter-HcsMaintenanceMode`
4. Egy figyelmeztető üzenet, amely arról tájékoztat, hogy karbantartási módba fog megszakítja az összes i/o-kérelmek és a kiszolgálót a kapcsolatot a klasszikus Azure portálra, és megerősítést fog kérni, látni fogja. Típus **Y** , adja meg a karbantartási módot.
   
    Mindkét vezérlő újraindul. Az újraindítás befejeződése után egy másik üzenet jelenik meg, amely azt jelzi, hogy az eszköz karbantartási módban van.

