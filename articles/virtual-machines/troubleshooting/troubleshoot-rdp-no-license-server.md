---
title: Távoli asztali licenckiszolgáló nem érhető el, amikor csatlakozik az Azure virtuális gép |} A Microsoft Docs
description: Ismerje meg, hogyan RDP sikertelen problémáinak hibaelhárítása, mert nincs távoli asztali licenckiszolgáló nem érhető el |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5e54579a35140ee7cfc06358d60cf7a63292e107
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088842"
---
# <a name="remote-desktop-license-server-is-not-available-when-you-connect-to-azure-vm"></a>Távoli asztali licenckiszolgáló nem érhető el, amikor csatlakozik az Azure virtuális gép

Ez a cikk segít megoldani a problémát, amelyben nem sikerül, egy Azure virtuális gép (VM), mert nincs távoli asztali licenckiszolgáló nem érhető el, amely licencet biztosíthatna.

## <a name="symptoms"></a>Probléma

Ha egy virtuális Géphez való csatlakozáshoz próbál, a következő esetekben tapasztalja:

- A virtuális gép (VM) képernyőkép azt mutatja, hogy az operációs rendszer teljesen betöltött és Várakozás a hitelesítő adatokat.
- Kapcsolat létrehozása a Microsoft Remote Desktop Protocol (RDP) megkísérlésekor a következő hibaüzeneteket kap:

  - **A távoli munkamenet meg lett szakítva, mert nem érhető el, amely licencet biztosíthatna távoli asztali licenckiszolgálókat.**

  - **Nincsenek távoli asztali licenckiszolgáló nem érhető el. Távoli asztali szolgáltatások fog működni, mert ezen a számítógépen a türelmi időszak lejárt, és nem kapcsolódott legalább egy érvényes Windows Server 2008-licenckiszolgáló. Kattintson az üzenetre kattintva megnyithatja a távoli asztali munkamenetgazda kiszolgáló konfigurálása licencelés diagnosztikai használatára.**

Azonban csatlakozhat a virtuális gép rendszerint felügyeleti munkamenet használatával:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy távoli asztali licenckiszolgáló nem érhető el egy távoli munkamenetet, amely licencet biztosíthatna. Ez a probléma számos forgatókönyv okozhatja, annak ellenére, hogy a virtuális gép be lett állítva a távoli asztali munkamenetgazda szerepkör:

- Nincs soha nem volt a távoli asztali licencelési szerepkör a környezetben, és a türelmi időszak (180 nap) felett van.
- Távoli asztali licencet lett telepítve a környezetben, de soha nem aktiválódik.
- Egy távoli asztali licencet, a környezetben nincs ügyféllicenc (CAL) szúrhatja be a kapcsolat beállításához.
- Távoli asztali licencet lett telepítve a környezetben. Vannak kiosztható ügyféllicencek, de azok nem megfelelően konfigurálva.
- Távoli asztali licencet ügyféllicencekkel, és aktiválva volt. Azonban más problémák, a távoli asztali licenckiszolgálón meggátoló biztosító licenceket a környezetben.

## <a name="solution"></a>Megoldás

Ez a probléma megoldásához [készítsen biztonsági másolatot az operációsrendszer-lemez](../windows/snapshot-copy-managed-disk.md) , és kövesse az alábbi lépéseket:

1. Felügyeleti munkamenet használatával csatlakozzon a virtuális géphez:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

  Ha nem sikerül a virtuális géphez felügyeleti munkamenet használatával, használhatja a [soros konzol](serial-console-windows.md) eléri a virtuális Gépet a következő:

  1. A soros konzol eléréséhez kiválasztásával **támogatás és hibaelhárítás** > **soros konzol (előzetes verzió)**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

  2. Hozzon létre egy új csatorna a CMD-példányhoz. Típus **CMD** elindítja a csatornát a csatorna nevének lekérése érdekében.

  3. Váltson a csatornát a CMD-példányt futtató, ebben az esetben 1 csatornát kell lennie.

    ```
    ch -si 1
    ```

  4. Nyomja meg **Enter** újra és írja be egy érvényes felhasználónevet és jelszót (helyi vagy tartományi azonosító) a virtuális gép számára.

2. Ellenőrizze, hogy a virtuális gép rendelkezik-e a távoli asztali munkamenetgazda szerepkör engedélyezve van. Ha engedélyezve van a szerepkör, győződjön meg arról, hogy megfelelően működik-e. Nyisson meg egy rendszergazda jogú CMD-példányt, és kövesse az alábbi lépéseket:

  1. A következő paranccsal ellenőrizheti az állapotot, a távoli asztali munkamenetgazda szerepkör:

    ```
    reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
    ```

    Ez a parancs a 0 értéket ad vissza, ha az azt jelenti, hogy a szerepkör le van tiltva, és ugorhat a 3. lépés.

  2. A következő paranccsal ellenőrizheti a szabályzatokat, és igény szerint Újrakonfigurálhatók:

    ```
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
    ```

    Ha a **licencelési** érték 4 (felhasználónként), mint bármely más értékre állítva, majd állítsa az értékét 4:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
    ```

    Ha a **SpecifiedLicenseServers** érték nem létezik, vagy egy helytelen licenckiszolgáló adatai rendelkezik, majd módosítsa az alábbiak szerint:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
    ```

  3. A rendszerleíró adatbázis bármely módosítása után indítsa újra a virtuális Gépet.

  4. Ha nem rendelkezik CAL-EK, távolítsa el a távoli asztali munkamenetgazda szerepkör. Ezt követően az RDP vissza fog állítani a normál értékre, és csak lehetővé teszi két egyidejű RDP-kapcsolatok a virtuális géphez.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
    ```

    Ha a virtuális gép rendelkezik a távoli asztali licencelési szerepkör, és nem használja fel azokat, távolítsa el a szerepkör.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Licensing
    ```

  5. Győződjön meg arról, hogy a virtuális Géphez a távoli asztali licenckiszolgáló kapcsolódik. Tesztelheti a kapcsolatot a virtuális gép és a licenc-kiszolgáló között a 135-ös port. 

    ```
    telnet <FQDN / IP License Server> 135
    ```

3. Ha nem kiszolgáló, akkor a távoli asztal licenc a környezetben, és azt szeretné, [egy távoli asztali licencelés szerepkör-szolgáltatás telepítése ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)), majd [konfigurálása a távoli asztali licencelés](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Ha a távoli asztali licenckiszolgálót be állítva és kifogástalan állapotú, ügyeljen arra, hogy a távoli asztali licenckiszolgáló aktiválva van-e az ügyféllicencek.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
