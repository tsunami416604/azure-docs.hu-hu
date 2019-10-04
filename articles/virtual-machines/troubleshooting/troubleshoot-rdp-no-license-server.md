---
title: Az Távoli asztal licenckiszolgáló nem érhető el, ha Azure-beli virtuális géphez csatlakozik | Microsoft Docs
description: Ismerje meg, hogyan oldhatja meg az RDP-hibák problémáit, mert nincs elérhető Távoli asztal licenckiszolgálóra | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088520"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Távoli asztal licenckiszolgáló Azure-beli virtuális géphez való csatlakozáskor nem érhető el

Ez a cikk segít a probléma megoldásában, ha nem tud csatlakozni egy Azure-beli virtuális géphez (VM), mert nem érhető el Távoli asztal licenckiszolgáló a licencek megadásához.

## <a name="symptoms"></a>Probléma

Amikor megpróbál csatlakozni egy virtuális géphez (VM), a következő helyzeteket tapasztalja:

- A virtuális gép képernyőképe azt mutatja, hogy az operációs rendszer teljesen be van töltve, és a hitelesítő adatokra vár.
- A következő hibaüzenetek jelennek meg, amikor egy Microsoft Távoli asztal protokoll (RDP) kapcsolatát próbálja meg létrehozni:

  - A távoli munkamenet le lett választva, mert nincs elérhető Távoli asztal licenckiszolgálóra a licenc biztosításához.

  - Nem érhető el Távoli asztal licenckiszolgálóra. Távoli asztali szolgáltatások leáll, mert a számítógép türelmi időszaka lejárt, és nem lépett kapcsolatba legalább egy érvényes Windows Server 2008-licenccel. Válassza ezt az üzenetet a távoli asztali munkamenetgazda kiszolgáló konfigurációjának megnyitásához a licencelési diagnosztika használatához.

A virtuális géphez azonban általában rendszergazdai munkamenet használatával kapcsolódhat:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy Távoli asztal licenckiszolgáló nem érhető el a távoli munkamenet elindításához szükséges licenc biztosításához. Számos forgatókönyv okozhatja, még akkor is, ha a virtuális gépen be van állítva egy Távoli asztal munkamenet-gazda szerepkör:

- A környezetben soha nem volt Távoli asztal licencelési szerepkör, és a türelmi időszak (180 nap) túl van.
- Egy Távoli asztal licenc lett telepítve a környezetben, de soha nem aktiválódik.
- A környezet Távoli asztal licence nem rendelkezik a kapcsolat beállításához használt ügyféllicencekkel (CAL).
- Távoli asztal licenc lett telepítve a környezetben. Vannak elérhető ügyféllicencek, de nem megfelelően vannak konfigurálva.
- A Távoli asztal licencek ügyféllicencekkel rendelkeznek, és aktiválva lettek. A Távoli asztal licenckiszolgálójára azonban néhány további probléma miatt nem biztosít licenceket a környezetben.

## <a name="solution"></a>Megoldás

A probléma megoldásához végezze el [az operációsrendszer-lemez biztonsági mentését](../windows/snapshot-copy-managed-disk.md) , és kövesse az alábbi lépéseket:

1. Kapcsolódás a virtuális géphez felügyeleti munkamenet használatával:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Ha nem tud csatlakozni a virtuális GÉPHEZ egy felügyeleti munkamenet használatával, az Azure-beli [virtuálisgép-soros konzollal](serial-console-windows.md) a következőképpen érheti el a virtuális gépet:

    1. A soros konzolt a **support & hibaelhárítás** > **Serial Console (előzetes verzió)** lehetőség kiválasztásával érheti el. Ha a szolgáltatás engedélyezve van a virtuális gépen, akkor a virtuális gép sikeresen csatlakoztatható.

    2. Hozzon létre egy új csatornát egy CMD-példányhoz. Írja be a **cmd parancsot** a csatorna elindításához és a csatorna nevének lekéréséhez.

    3. Váltson arra a csatornára, amelyen a CMD-példány fut. Ebben az esetben az 1. csatornát kell megadnia:

       ```
       ch -si 1
       ```

    4. Válassza újra az **ENTER billentyűt** , és adjon meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományi azonosítót a virtuális géphez.

2. Győződjön meg arról, hogy a virtuális gépnek engedélyezve van-e Távoli asztal munkamenet-Gazdagépi szerepköre. Ha a szerepkör engedélyezve van, ellenőrizze, hogy megfelelően működik-e. Nyisson meg egy rendszergazda jogú CMD-példányt, és kövesse az alábbi lépéseket:

    1. Használja az alábbi parancsot a Távoli asztal munkamenet-gazdagép szerepkör állapotának vizsgálatához:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Ha a parancs 0 értéket ad vissza, az azt jelenti, hogy a szerepkör le van tiltva, és a 3. lépésre léphet.

    2. A következő paranccsal tekintheti meg a házirendeket, és szükség szerint konfigurálja újra:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Ha a **LicensingMode** értéke a 4-es értéktől eltérő értékre van beállítva, felhasználónként, majd állítsa be az értéket 4-re:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Ha a **SpecifiedLicenseServers** értéke nem létezik, vagy helytelen a licenckiszolgáló adatai, módosítsa a következő módon:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. A beállításjegyzék módosítása után indítsa újra a virtuális gépet.

    4. Ha nem rendelkezik ügyféllicencekkel, távolítsa el a Távoli asztal munkamenet-gazda szerepkört. Ezután a rendszer visszaállítja az RDP-t a normál értékre. Ez csak két egyidejű RDP-kapcsolat használatát teszi lehetővé a virtuális gép számára:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Ha a virtuális gép rendelkezik a Távoli asztal licencelési szerepkörrel, és nem használja, akkor a szerepkört is eltávolíthatja:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Győződjön meg arról, hogy a virtuális gép tud csatlakozni a Távoli asztal licenckiszolgálóra. Tesztelheti a 135-as port kapcsolatát a virtuális gép és a licenckiszolgáló között: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Ha a környezetben nincs Távoli asztal licenckiszolgálóra, és azt szeretné, hogy egy [Távoli asztal licencelési szerepkör-szolgáltatást telepítsen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Ezután [konfigurálja az RDS licencelést](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Ha egy Távoli asztal licenckiszolgáló konfigurálva van és kifogástalan állapotú, győződjön meg arról, hogy a Távoli asztal licenckiszolgáló engedélyezve van-e az Cal-ban.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és kérje meg a probléma megoldását.
