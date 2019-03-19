---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908535"
---
A folyamatkiszolgálók regisztrációjának visszavonásához szükséges lépések eltérőek lehetnek attól függően, hogy milyen a konfigurációs kiszolgálóval való kapcsolatuk állapota.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Csatlakoztatott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

1. Távolról csatlakozzon a folyamatkiszolgálóhoz rendszergazdaként.
2. Indítsa el a **Vezérlőpult** , és nyissa meg **programok > program eltávolítása**.
3. Nevű program eltávolítása **a Microsoft Azure Site Recovery mobilitási szolgáltatás/fő célkiszolgáló**.
4. Nevű program eltávolítása **a Microsoft Azure Site Recovery Configuration/Process Server**.
5. Amikor a program a 3. és 4 el lesznek távolítva, törölheti **a Microsoft Azure Site Recovery Configuration/Process kiszolgálóoldali függőségek**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Leválasztott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

> [!WARNING]
> Használja az alábbi lépéseket, ha nem lehet helyreállítani azt a virtuális gép, amelyen a Folyamatkiszolgáló telepítve van.

1. Jelentkezzen be a konfigurációs kiszolgálóra rendszergazdaként.
2. Nyisson meg egy rendszergazdai parancssort, és keresse meg a `%ProgramData%\ASR\home\svsystems\bin` könyvtárat.
3. Most futtassa a parancsot.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. A fenti parancs biztosít a listáját (lehet több, ismétlődő bejegyzéseket esetén) folyamatkiszolgáló(k) soros number(S.No), IP-címét (IP), a virtuális gép neve (név) melyik folyamatkiszolgáló van telepítve, a szív beat a virtuális gép (szívverési), ahogy az alábbi.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Most adja meg a sorozatszámot szeretne folyamatkiszolgáló regisztrációjának.
6. Ez véglegesen törli a folyamatkiszolgáló adatait a rendszerből, és a következő üzenetet jeleníti meg: **Kiszolgálónév regisztrációja sikeresen törölve > (kiszolgáló-IP-cím)**

