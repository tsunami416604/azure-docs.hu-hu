---
title: Az Azure Linux-ügynök hibáinak megoldása
description: Az Azure Linux-ügynökkel kapcsolatos problémák megoldása.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 247324c30bbe0edaef78c0b0d5e6a6d593e8cac9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586397"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Az Azure Linux-ügynök hibáinak megoldása

Az [Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) lehetővé teszi egy virtuális gép (VM) számára, hogy kommunikáljon a háló vezérlővel (az alapul szolgáló fizikai kiszolgáló, AMELYEN a virtuális gép fut) az IP-168.63.129.16.

>[!NOTE]
>Ez az IP-cím egy virtuális nyilvános IP-cím, amely megkönnyíti a kommunikációt, és nem tiltható le. További információ: [Mi az az IP-168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze az ügynök állapotát és verzióját, és győződjön meg arról, hogy az továbbra is támogatott. A verzió támogatásának megtekintéséhez tekintse meg az Azure-beli [virtuálisgép-ügynökök minimális verziószám-támogatását](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version) , vagy tekintse meg az állapot és a verzió megkereséséhez szükséges [WALinuxAgent kapcsolatos gyakori kérdéseket](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) .

## <a name="troubleshoot-a-not-ready-status"></a>Nem üzemkész állapottal való hibakeresés

1. Ellenőrizze az Azure Linux-ügynök szolgáltatási állapotát, és győződjön meg róla, hogy fut. A szolgáltatás neve lehet **walinuxagent** vagy **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Ha a szolgáltatás fut, indítsa újra a probléma megoldásához. Ha a szolgáltatás le van állítva, indítsa el, várjon néhány percet, majd ellenőrizze újra az állapotot.

1. Győződjön meg arról, hogy az automatikus frissítés engedélyezve van. Keresse meg az automatikus frissítési beállítást a **/etc/waagent.conf**-ben.

   ```
   AutoUpdate.Enabled=y
   ```

   Az Azure Linux-ügynök frissítésével kapcsolatos további információkért lásd: [Az Azure Linux-ügynök frissítése egy virtuális gépen](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent).

1. Győződjön meg arról, hogy a virtuális gép tud csatlakozni a háló vezérlőhöz. A curl használatával tesztelheti, hogy a virtuális gép tud-e csatlakozni a 168.63.129.16 a 80, 443 és 32526 portokon. Ha a virtuális gép nem a várt módon kapcsolódik, ellenőrizze, hogy az 80-es, 443-as és 32526-as porton keresztüli kimenő kommunikáció nyitva van-e a virtuális gépen a helyi tűzfalon. Ha ez az IP-cím le van tiltva, előfordulhat, hogy a virtuálisgép-ügynök váratlan viselkedést jelez.

## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Az Azure Linux-ügynökkel kapcsolatos hibaelhárítási események a **/var/log/waagent.log** fájlban vannak rögzítve.

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nem lehet csatlakozni a WireServer IP-címhez (gazdagép IP-címe)

A **/var/log/waagent.log** fájlban a következő hiba jelenik meg, ha a virtuális gép nem tudja elérni a WireServer IP-címét a gazdagép kiszolgálóján.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

A probléma megoldása:

* Csatlakozzon az SSH-val a virtuális géphez, majd próbálja meg elérni a következő URL-címet a curlból: http://168.63.129.16/?comp=versions .
* Keresse meg a tűzfal, a proxy vagy más olyan probléma okozta problémákat, amelyek blokkolják az IP-168.63.129.16 való hozzáférést.
* Győződjön meg arról, hogy a Linux iptables vagy egy harmadik féltől származó tűzfal blokkolja-e a 80, 443 és 32526 portokhoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések

Az Azure Linux-ügynökkel kapcsolatos problémák további elhárításához [forduljon a Microsoft ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
