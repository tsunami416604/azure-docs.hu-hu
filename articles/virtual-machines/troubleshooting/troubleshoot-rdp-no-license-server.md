---
title: A távoli asztali licenckiszolgáló nem érhető el, amikor csatlakozik az Azure virtuális Gépekhez |} A Microsoft Docs
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
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913853"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Távoli asztali licenckiszolgáló nem érhető el, amikor csatlakozik az Azure virtuális Gépekhez

Ez a cikk segít megoldani a problémát, ha Ön nem tud kapcsolódni egy Azure virtuális gép (VM), mert nincs távoli asztali licenckiszolgáló nem érhető el, amely licencet biztosíthatna.

## <a name="symptoms"></a>Probléma

Ha próbál kapcsolódni egy virtuális gépet (VM), a következő esetekben tapasztalja:

- A virtuális gép képernyőfelvételén azt mutatja, hogy az operációs rendszer teljesen betöltött és Várakozás a hitelesítő adatokat.
- Kapcsolat létrehozása a Microsoft Remote Desktop Protocol (RDP) megkísérlésekor a következő hibaüzeneteket kap:

  - A távoli munkamenet meg lett szakítva, mert nem érhető el, amely licencet biztosíthatna távoli asztali licenckiszolgálókat.

  - Nincsenek távoli asztali licenckiszolgáló nem érhető el. Távoli asztali szolgáltatások fog működni, mert ezen a számítógépen a türelmi időszak lejárt, és legalább egy érvényes Windows Server 2008-licenckiszolgáló nem lépett kapcsolatba. Válassza ki az üzenetre kattintva megnyithatja a távoli asztali munkamenetgazda kiszolgáló konfigurálása licencelés diagnosztikai használatára.

Azonban csatlakozhat a virtuális gép rendszerint felügyeleti munkamenet használatával:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy távoli asztali licenckiszolgáló nem érhető el egy távoli munkamenetet, amely licencet biztosíthatna. Ezt okozhatja számos forgatókönyv, annak ellenére, hogy a virtuális gép be lett állítva a távoli asztali munkamenetgazda szerepkör:

- Van soha nem volt a távoli asztali licencelési szerepkör a környezetben, és a türelmi időszak, 180 nap alatt.
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

    Ha nem sikerül a virtuális géphez felügyeleti munkamenet használatával, használhatja a [Azure-beli virtuális gépek soros konzolja](serial-console-windows.md) eléri a virtuális Gépet a következő:

    1. A soros konzol eléréséhez kiválasztásával **támogatás és hibaelhárítás** > **soros konzol (előzetes verzió)**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

    2. Hozzon létre egy új csatorna a CMD-példányhoz. Adja meg **CMD** elindítja a csatornát, és kérje le a csatorna nevét.

    3. Váltson a csatorna, amelyen a CMD-példány. Ebben az esetben 1 csatornát kell lennie:

       ```
       ch -si 1
       ```

    4. Válassza ki **Enter** újra, és adja meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományi azonosító, a virtuális gép.

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

        Ha a **licencelési** értéke 4, felhasználónként, nem értékre van állítva, majd állítsa az értékét 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Ha a **SpecifiedLicenseServers** érték nem létezik, vagy helytelen licencinformációk kiszolgáló van, majd módosítsa az alábbiak szerint:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. A rendszerleíró adatbázis bármely módosítása után indítsa újra a virtuális Gépet.

    4. Ha nem rendelkezik CAL-EK, távolítsa el a távoli asztali munkamenetgazda szerepkör. Vissza az RDP fogja állítani a normál értékre. Csak lehetővé teszi két egyidejű RDP-kapcsolatok a virtuális géphez:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Ha a virtuális gép rendelkezik a távoli asztali licencelési szerepkör, és nem használ, távolítsa el a szerepkör:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Győződjön meg arról, hogy a virtuális Géphez a távoli asztali licenckiszolgáló kapcsolódik. Tesztelheti a kapcsolatot a virtuális gép és a licenc-kiszolgáló között a 135-ös port: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Ha nem kiszolgáló, akkor a távoli asztal licenc a környezetben, és azt szeretné, [egy távoli asztali licencelés szerepkör-szolgáltatás telepítése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Ezután [konfigurálása a távoli asztali licencelés](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Ha a távoli asztali licenckiszolgálót be állítva és kifogástalan állapotú, ügyeljen arra, hogy a távoli asztali licenckiszolgáló aktiválva van-e az ügyféllicencek.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a megoldott probléma.
