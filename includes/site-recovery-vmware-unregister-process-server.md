---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179647"
---
Kövesse az adott körülményekhez szükséges lépéseket.

### <a name="unregister-a-connected-process-server"></a>Csatlakoztatott folyamatkiszolgáló regisztrációjának megszüntetése

1. Hozzon létre távoli kapcsolatot a folyamatkiszolgálóval rendszergazdaként.
2. A **Vezérlőpulton**nyissa meg a **Programok > A program eltávolítása segédprogramot.**
3. Távolítsa el a **microsoft azure site recovery mobilitási szolgáltatás/fő célkiszolgáló programot.**
4. Távolítsa el a **microsoft azure site recovery konfigurációs/folyamatkiszolgáló programot.**
5. A **3.**

### <a name="unregister-a-disconnected-process-server"></a>Leválasztott folyamatkiszolgáló regisztrációjának megszüntetése

Csak akkor használja ezeket a lépéseket, ha nincs mód arra, hogy felélessze azt a gépet, amelyre a folyamatkiszolgáló telepítve van.

1. Jelentkezzen be a konfigurációs kiszolgálón rendszergazdaként.
2. Nyisson meg egy Felügyeleti `%ProgramData%\ASR\home\svsystems\bin`parancssort, és keresse meg a tallózást.
3. Futtassa ezt a parancsot egy vagy több folyamatkiszolgáló listájának leéséhez.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nem: a folyamatkiszolgáló sorozatszáma.
    - IP/név: A folyamatkiszolgálót futtató számítógép IP-címe és neve.
    - Szívverés: A folyamatkiszolgáló-gép utolsó szívverése.
    ![Regisztráció megszüntetése](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Adja meg a regisztrációról törölni kívánt folyamatkiszolgáló sorozatszámát.
5. A folyamatkiszolgáló regisztrációjának megszüntetése eltávolítja az összes részletet a rendszerből, és a következő üzenetet jeleníti meg: **Sikeresen regisztrált kiszolgálónév-> (kiszolgáló-IP-cím)**

