---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67534639"
---
1. Hozzon létre egy Távoli asztali kapcsolat a Process Servert futtató gépről. 
2. Futtassa a cspsconfigtool. exe fájlt a Azure Site Recovery Process Server konfigurációs eszköz elindításához.
    - Az eszköz automatikusan elindul, amikor először jelentkezik be a Process Serverbe.
    - Ha nem nyílik meg automatikusan, kattintson a parancsikonra az asztalon.

3. A **konfigurációs kiszolgáló teljes tartományneve vagy IP**-címe mezőben adja meg annak a konfigurációs kiszolgálónak a nevét vagy IP-címét, amellyel regisztrálni szeretné a folyamat-kiszolgálót.
4. A **konfigurációs kiszolgáló portján**ellenőrizze, hogy a 443 meg van-e adva. Ez az a port, amelyen a konfigurációs kiszolgáló figyeli a kérelmeket.
5. A **kapcsolódási jelszó**mezőben adja meg a konfigurációs kiszolgáló beállításakor megadott jelszót. A jelszó megkeresése:
    -  A konfigurációs kiszolgálón navigáljon a Site Recovery telepítési mappájához **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Futtassa az alábbi parancsot az aktuális hozzáférési kód kinyomtatásához:
    ```
    genpassphrase.exe -n
    ```

6. **Adatátvitel porton**hagyja meg az alapértelmezett értéket, hacsak nem adott meg egyéni portot.

7. Kattintson a **Mentés** gombra a beállítások mentése elemre, és regisztrálja a Process Servert.

    
    ![A folyamat-kiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
