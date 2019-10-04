---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179647"
---
Kövesse a lépéseket a konkrét körülmények között.

### <a name="unregister-a-connected-process-server"></a>Csatlakoztatott folyamatkiszolgáló regisztrációjának visszavonása

1. Rendszergazdaként a folyamatkiszolgáló távoli kapcsolatot létesíteni.
2. Az a **Vezérlőpult**, nyissa meg **programok > program eltávolítása**.
3. Távolítsa el a program **a Microsoft Azure Site Recovery mobilitási szolgáltatás/fő célkiszolgáló**.
4. Távolítsa el a program **a Microsoft Azure Site Recovery Configuration/Process Server**.
5. Eltávolítás után a program a 3. és 4 el lesznek távolítva, **a Microsoft Azure Site Recovery Configuration/Process kiszolgálóoldali függőségek**.

### <a name="unregister-a-disconnected-process-server"></a>Leválasztott folyamatkiszolgáló regisztrációjának visszavonása

Csak akkor használja ezeket a lépéseket, ha nem lehet helyreállítani azt a gép, amelyen a folyamatkiszolgáló telepítve van.

1. A konfigurációs kiszolgálón jelentkezzen be rendszergazdaként.
2. Nyisson meg egy rendszergazdai parancssort, és keresse meg a `%ProgramData%\ASR\home\svsystems\bin`.
3. Egy vagy több folyamat kiszolgálók listáját a következő parancs futtatásával.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nem: a folyamat kiszolgáló sorozatszám.
    - IP/neve: Az IP-cím és a folyamatkiszolgáló futtató gép neve.
    - Szívverés: Utolsó szívverés a folyamat kiszolgáló gépről.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Adja meg a folyamatkiszolgáló regisztrációjának szeretné sorozatszámát.
5. A folyamatkiszolgálók regisztrációjának távolítsa el az összes a részletek a rendszerből, és az üzenet jelenik meg: **Kiszolgálónév regisztrációja sikeresen törölve > (kiszolgáló-IP-cím)**

