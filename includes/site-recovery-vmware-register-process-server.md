---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534639"
---
1. A folyamatkiszolgáló futtató gép távoli asztali kapcsolatot létesíteni. 
2. Indítsa el az Azure Site Recovery Folyamatkiszolgáló konfigurációs eszközt a cspsconfigtool.exe futtatásával.
    - Az eszköz automatikusan elindítja az első alkalommal jelentkezik be a folyamatkiszolgáló.
    - Ha ez nem nyílik meg automatikusan, kattintson a parancsikonnal az asztalról.

3. A **konfigurációs kiszolgáló teljes Tartományneve vagy IP-** , adja meg a nevét vagy IP-cím, amellyel a folyamatkiszolgáló regisztrálása konfigurációs kiszolgáló.
4. A **konfigurációs kiszolgáló portja**, győződjön meg arról, hogy a 443-as porton van megadva. Ez az a port, amelyen a konfigurációs kiszolgálót a kéréseket figyeli.
5. A **kapcsolat hozzáférési kódja**, adja meg a konfigurációs kiszolgáló beállításakor megadott jelszót. A hozzáférési kód keresése:
    -  A Site Recovery telepítési mappáját a konfigurációs kiszolgálón, lépjen * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Futtassa az alábbi parancsot a jelenlegi jelszava kinyomtatásához:
    ```
    genpassphrase.exe -n
    ```

6. A **adatátviteli Port**, hagyja bejelölve az alapértelmezett érték, ha megadott egy egyéni portot.

7. Kattintson a **mentése** a beállítások mentéséhez, és regisztrálja a folyamatkiszolgálót.

    
    ![A folyamatkiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
