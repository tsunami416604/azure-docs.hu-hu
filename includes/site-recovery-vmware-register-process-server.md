---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534639"
---
1. Távoli asztali kapcsolat létrehozása a folyamatkiszolgálót futtató géppel. 
2. Az Azure Site Recovery Process Server konfigurációs eszközének elindításához futtassa a cspsconfigtool.exe segédprogramot.
    - Az eszköz automatikusan elindul, amikor először jelentkezik be a folyamatkiszolgálóra.
    - Ha nem nyílik meg automatikusan, kattintson a parancsikonra az asztalon.

3. A **Konfigurációs kiszolgáló teljes tartományneve vagy IP-címe csoportban**adja meg annak a konfigurációs kiszolgálónak a nevét vagy IP-címét, amelyhez a folyamatkiszolgálót regisztrálni szeretné.
4. A **Konfigurációs kiszolgáló portjában**győződjön meg arról, hogy a 443-as meg van adva. Ez az a port, amelyen a konfigurációs kiszolgáló figyeli a kéréseket.
5. A **Kapcsolati jelszó csoportban**adja meg a konfigurációs kiszolgáló beállításakor megadott jelszót. A jelszó megkeresése:
    -  A konfigurációs kiszolgálón keresse meg a Site Recovery telepítési mappát **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Az alábbi parancs futtatásával nyomtassa ki az aktuális jelszót:
    ```
    genpassphrase.exe -n
    ```

6. Az **Adatátviteli portban**hagyja meg az alapértelmezett értéket, hacsak nem adott meg egyéni portot.

7. Kattintson a **Beállítások** mentése gombra, és regisztrálja a folyamatkiszolgálót.

    
    ![A folyamatkiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
