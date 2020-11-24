---
title: Azure Linux-vendég ügynök hibaelhárítása
description: Az Azure Linux vendég ügynökével kapcsolatos hibák elhárítása nem működik
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
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549930"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Azure Linux-vendég ügynök hibaelhárítása

Az [Azure Linux Guest Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) egy virtuális gép (VM) ügynöke. Lehetővé teszi a virtuális gép számára, hogy kommunikáljon a háló vezérlővel (a mögöttes fizikai kiszolgáló, amelyen a virtuális gép fut) az IP-168.63.129.16. Ez az IP-cím egy virtuális nyilvános IP-cím, amely megkönnyíti a kommunikációt. További információ: [Mi az IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Az ügynök állapotának és verziójának ellenőrzése

Lásd: https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Nem üzemkész állapotú virtuálisgép-ügynök hibaelhárítása

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>1. lépés azt vizsgálja, hogy fut-e az Azure Linux Guest Agent szolgáltatás

A disztribúciótól függően a szolgáltatás neve lehet walinuxagent vagy waagent:

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


Ha a szolgáltatások megjelennek és futnak, indítsa újra a szolgáltatást, és ellenőrizze, hogy megoldódott-e a probléma. Ha a szolgáltatások leállnak, indítsa el őket, és várjon néhány percet. Ezután győződjön meg arról, hogy az **ügynök állapota** **készként** van-e bejelentve. A problémák további hibaelhárításához forduljon [Microsoft ügyfélszolgálatahoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>2. lépés annak megadásához, hogy engedélyezve van-e az automatikus frissítés

Ezt a beállítást a/etc/waagent.conf-ben kell megnéznie:

```
AutoUpdate.Enabled=y
```

Az Azure Linux-ügynök frissítésével kapcsolatos további információkért tekintse meg a következőt: https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>3. lépés azt vizsgálja, hogy a virtuális gép tud-e csatlakozni a háló vezérlőhöz

A curl használatával tesztelheti, hogy a virtuális gép tud-e csatlakozni a 168.63.129.16 a 80, 32526 és 443 portokon. Ha a virtuális gép nem a várt módon kapcsolódik, ellenőrizze, hogy az 80-es, 443-as és 32526-as porton keresztüli kimenő kommunikáció nyitva van-e a virtuális gépen a helyi tűzfalon. Ha ez az IP-cím le van tiltva, a virtuálisgép-ügynök esetében nem várt viselkedés fordulhat elő különböző helyzetekben.

## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Az Azure Linux Guest Agent hibaelhárítási eseményei a következő naplófájlokban vannak rögzítve:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nem lehet csatlakozni a WireServer IP-címhez (gazdagép IP-címe) 

A/var/log/waagent.log a következő hibaüzeneteket észleli:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Elemzés**

A virtuális gép nem tudja elérni a WireServer IP-címét a gazdagépen.

**Megoldás**

1. Mivel a WireServer IP-címe nem érhető el, csatlakozzon az SSH-val a virtuális géphez, majd próbálja meg elérni a következő URL-címet a curl használatával: http://168.63.129.16/?comp=versions 
1. Az IP-168.63.129.16 való hozzáférést blokkoló tűzfal, proxy vagy más forrás által okozott esetleges problémák keresése.
1. Győződjön meg arról, hogy a Linux iptables vagy egy harmadik féltől származó tűzfal blokkolja-e a 80, 443 és 32526 portokhoz való hozzáférést. További információ arról, hogy a cím miért nem tiltható le: [Mi az IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Következő lépések

A "Windows Azure Guest Agent ügynök nem működik" probléma megoldásához forduljon a [Microsoft ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
