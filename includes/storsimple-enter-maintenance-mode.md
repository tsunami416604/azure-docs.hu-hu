---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179146"
---
#### <a name="to-enter-maintenance-mode"></a>A karbantartási módba való belépéshez
1. A soros konzol menüjében **Log in with full access**válassza az 1.
2. Írja be a jelszót. Az alapértelmezett jelszó a **Password1**.
3. A parancssorba írja be a következőt:
   
     `Enter-HcsMaintenanceMode`
4. Megjelenik egy figyelmeztető üzenet, amely arról szól, hogy a Karbantartási mód megszakítja az összes I/O-kérést, és megszakítja a kapcsolatot az Azure klasszikus portállal, és megerősítést kér. A Karbantartási módba való belépéshez írja be az **Y** értéket.
   
    Mindkét vezérlő újraindul. Amikor az újraindítás befejeződött, egy másik üzenet jelenik meg, amely azt jelzi, hogy az eszköz karbantartási üzemmódban van.

