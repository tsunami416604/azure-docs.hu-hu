---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582759"
---
A folyamatkiszolgálók regisztrációjának visszavonásához szükséges lépések eltérőek lehetnek attól függően, hogy milyen a konfigurációs kiszolgálóval való kapcsolatuk állapota.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Csatlakoztatott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

1. Távolról csatlakozzon a folyamatkiszolgálóhoz rendszergazdaként.
2. Indítsa el a **Vezérlőpultot**, és nyissa meg a **Programok > Program eltávolítása** elemet
3. Távolítsa el a **Microsoft Azure Site Recovery Configuration/Process Server** programot
4. A 3. lépés befejezése után eltávolíthatja a **Microsoft Azure Site Recovery Configuration/Process Server Dependencies** elemet.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Leválasztott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

> [!WARNING]
> Ha nem lehet helyreállítani azt a virtuális gépet, amelyen a folyamatkiszolgáló telepítve van, használja az alábbi lépéseket.

1. Jelentkezzen be a konfigurációs kiszolgálóra rendszergazdaként.
2. Nyisson meg egy rendszergazdai parancssort, és keresse meg a `%ProgramData%\ASR\home\svsystems\bin` könyvtárat.
3. Most futtassa a parancsot.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. A fenti parancs biztosít a listáját (lehet több, ismétlődő bejegyzéseket esetén) folyamatkiszolgáló(k) soros number(S.No), IP-címét (IP), a virtuális gép neve (név) melyik folyamatkiszolgáló van telepítve, a szív beat a virtuális gép (szívverési), ahogy az alábbi.
    ![Cmd-regisztrációjának törlése](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Most adja meg a sorozatszámot szeretne folyamatkiszolgáló regisztrációjának.
6. Ez véglegesen törli a folyamatkiszolgáló adatait a rendszerből, és a következő üzenetet jeleníti meg: **regisztrációja sikeresen törölve a kiszolgálónév > (kiszolgáló-IP-cím)**

