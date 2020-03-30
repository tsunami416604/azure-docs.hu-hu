---
title: A távoli asztali licenckiszolgáló nem érhető el, ha Azure virtuális géphez csatlakozik | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatók el az RDP hibaelhárítási hibái, mert nem érhető el távoli asztali licenckiszolgáló | Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088520"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>A távoli asztali licenckiszolgáló nem érhető el, ha Azure-beli virtuális géphez csatlakozik

Ez a cikk segít megoldani a problémát, ha nem tud csatlakozni egy Azure virtuális géphez (VM), mert nem érhető el távoli asztali licenckiszolgáló licenc biztosításához.

## <a name="symptoms"></a>Probléma

Amikor megpróbál csatlakozni egy virtuális géphez (VM), a következő forgatókönyveket tapasztalhatja:

- A virtuális gép képernyőképe azt mutatja, hogy az operációs rendszer teljesen be van töltve, és a hitelesítő adatokra vár.
- A Microsoft Remote Desktop Protocol (RDP) kapcsolat létrehozásakor a következő hibaüzenetek jelennek meg:

  - A távoli munkamenet lelett választva, mert nincs elérhető távoli asztali licenckiszolgáló a licenc biztosításához.

  - Nem érhető el távoli asztali licenckiszolgáló. A Távoli asztali szolgáltatások nem fog működni, mert a számítógép lejárt a türelmi időszakon, és nem lépett legalább egy érvényes Windows Server 2008 licenckiszolgálóhoz. Válassza ezt az üzenetet a Távoli asztali munkamenetgazda-kiszolgáló konfigurációjának megnyitásához a licencelési diagnosztika használatához.

Azonban a virtuális géphez általában felügyeleti munkamenet használatával csatlakozhat:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy távoli asztali licenckiszolgáló nem tud licencet biztosítani egy távoli munkamenet indításához. Ezt több forgatókönyv is okozhatja, még akkor is, ha a virtuális gépen egy távoli asztali munkamenetgazda-szerepkör van beállítva:

- Soha nem volt távoli asztali licencelési szerepkör a környezetben, és a 180 napos türelmi időszaknak vége.
- A távoli asztali licenc telepítve volt a környezetben, de soha nem aktivált.
- A környezetben lévő távoli asztali licenc nem rendelkezik a kapcsolat beállításához befecskendezett ügyféllicencekkel.
- A környezetben távoli asztali licenc lett telepítve. Vannak elérhető hitelesítés- és listaházirendek, de nincsenek megfelelően konfigurálva.
- A távoli asztali licenc hez kétes azonosítók vannak, és aktiválva van. A távoli asztali licenckiszolgáló naegyéb problémái azonban megakadályozzák, hogy a rendszer licenceket biztosítson a környezetben.

## <a name="solution"></a>Megoldás

A probléma megoldásához [készüljön el az operációs rendszer lemezének biztonsági rezése,](../windows/snapshot-copy-managed-disk.md) és kövesse az alábbi lépéseket:

1. Csatlakozzon a virtuális géphez egy felügyeleti munkamenet használatával:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Ha nem tud csatlakozni a virtuális géphez egy felügyeleti munkamenet használatával, használhatja a [virtuális gép soros konzolaz Azure-ban](serial-console-windows.md) a virtuális gép eléréséhez az alábbiak szerint:

    1. A Soros konzol eléréséhez válassza **a Támogatás & hibaelhárítási** > **soros konzol (előzetes verzió)** lehetőséget választva. Ha a szolgáltatás engedélyezve van a virtuális gép, a virtuális gép sikeresen csatlakoztatható.

    2. Hozzon létre egy új csatornát egy CMD-példányhoz. Adja meg a **CMD-t** a csatorna elindításához és a csatorna nevének beírásához.

    3. Váltson a CMD-példányt futtató csatornára. Ebben az esetben az 1-es csatornának kell lennie:

       ```
       ch -si 1
       ```

    4. Válassza **az Enter** again lehetőséget, és adjon meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományazonosítót a virtuális géphez.

2. Ellenőrizze, hogy a virtuális gép rendelkezik-e távoli asztali munkamenetgazda-szerepkörrel. Ha a szerepkör engedélyezve van, győződjön meg arról, hogy megfelelően működik. Nyisson meg egy emelt szintű CMD-példányt, és kövesse az alábbi lépéseket:

    1. A Távoli asztali munkamenetgazda szerepkör állapotának ellenőrzéséhez használja a következő parancsot:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Ha ez a parancs 0 értéket ad vissza, az azt jelenti, hogy a szerepkör le van tiltva, és a 3.

    2. A következő paranccsal ellenőrizheti a házirendeket, és szükség szerint újrakonfigurálhatja:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Ha a **LicensingMode** érték felhasználónként 4-es értéknél eltérő értékre van állítva, akkor állítsa az értéket 4-re:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Ha a **SpecifiedLicenseServers** érték nem létezik, vagy helytelen licenckiszolgáló-adatokkal rendelkezik, módosítsa azt a következőképpen:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Miután módosította a rendszerleíró adatbázist, indítsa újra a virtuális gép.

    4. Ha nem rendelkezik hitelesítéskörrel, távolítsa el a Távoli asztali munkamenetgazda szerepkört. Ezután az RDP visszaáll a normális szintre. Csak két egyidejű RDP-kapcsolatot engedélyez a virtuális géphez:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Ha a virtuális gép rendelkezik a Távoli asztali licencelési szerepkör, és nem használják, eltávolíthatja a szerepkört is:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Győződjön meg arról, hogy a virtuális gép csatlakozhat a távoli asztali licenckiszolgálóhoz. Tesztelheti a 135-ös porthoz való csatlakozást a virtuális gép és a licenckiszolgáló között: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Ha a környezetben nincs távoli asztali licenckiszolgáló, és szeretne egyet, [telepíthet egy Távoli asztali licencelési szerepkör-szolgáltatást.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) Ezután [konfigurálja az RDS-licencelést.](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383)

4. Ha a Távoli asztali licenckiszolgáló konfigurálva van, és kifogástalan állapotú, győződjön meg arról, hogy a Távoli asztali licenckiszolgáló felhasználói licenccel van aktiválva.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, a probléma megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.
