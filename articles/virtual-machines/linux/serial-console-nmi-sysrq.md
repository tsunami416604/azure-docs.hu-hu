---
title: Azure soros konzol SysRq-és NMI-hívásokhoz
description: Soros konzol használata az Azure Virtual Machines szolgáltatásban található SysRq-és NMI-hívásokhoz.
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5541dec748f31818a0e9485fc0c56b7926ccaae7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758479"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Serial Console használata SysRq- és NMI-hívásokhoz

## <a name="system-request-sysrq"></a>Rendszerkérelem (SysRq)
A SysRq a Linux operációs rendszer kernele által értelmezett kulcsok sorozata, amely előre definiált műveletekből álló készletet indíthat el. Ezeket a parancsokat gyakran használják, ha a virtuális gép hibaelhárítását vagy helyreállítását nem lehet a hagyományos felügyeleten keresztül végrehajtani (például ha a virtuális gép nem válaszol). Az Azure soros konzol SysRq funkciójának használatával a rendszer a SysRq kulcs és a fizikai billentyűzeten megadott karakterek lenyomását fogja utánozni.

A SysRq-sorozatot követően a kernel konfigurációja vezérli, hogy a rendszer hogyan reagáljon. A SYSRQ engedélyezésével és letiltásával kapcsolatos információkért tekintse meg a *SYSRQ felügyeleti útmutató* [szöveges](https://aka.ms/kernelorgsysreqdoc) | [Markdown](https://aka.ms/linuxsysrq).  

Az Azure soros konzol használatával SysRq küldhet egy Azure-beli virtuális gépre az alább látható parancssáv billentyűzet ikonjával.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Ha a "Send SysRq parancs" lehetőséget választja, megnyílik egy párbeszédpanel, amely általános SysRq lehetőségeket biztosít, vagy elfogadja a párbeszédpanelen megadott SysRq parancsok sorrendjét.  Ez lehetővé teszi, hogy a SysRq sorozata magas szintű műveletet végezzen, például biztonságos újraindítás a következő használatával: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

A SysRq parancs nem használható olyan virtuális gépeken, amelyek leállnak, vagy amelyek kernele nem válaszoló állapotban van. (például egy kernel pánik).

### <a name="enable-sysrq"></a>SysRq engedélyezése 
A fenti, a *SYSRQ felügyeleti útmutatóban* leírtak szerint a SYSRQ konfigurálható úgy, hogy az összes, none vagy csak bizonyos parancsok elérhetők legyenek. Az összes SysRq-parancsot az alábbi lépésekkel engedélyezheti, de nem fogja túlélni az újraindítást:
```
echo "1" >/proc/sys/kernel/sysrq
```
Ahhoz, hogy a SysReq-konfiguráció állandó legyen, a következőket teheti az összes SysRq-parancs engedélyezéséhez
1. A sor hozzáadása a */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. A sysctl újraindítása vagy frissítése a futtatásával <br>
    `sysctl -p`

### <a name="command-keys"></a>Parancsgombok 
A fenti SysRq felügyeleti útmutatóból:

|Parancs| Függvény
| ------| ----------- |
|``b``  |   A automatikusan újraindítja a számítógépet a lemezek szinkronizálása vagy leválasztása nélkül.
|``c``  |   A rendszer összeomlik egy NULL értékű mutatóval. Ha be van állítva, A rendszer crashdump.
|``d``  |   Megjeleníti az összes tárolt zárolást.
|``e``  |   SIGTERM küldése minden folyamatnak, az init kivételével.
|``f``  |   Meghívja a bácsi gyilkosát, hogy megöli a memóriát, de ne essen pánikba, ha semmi sem lehetséges.
|``g``  |   A kgdb (kernel Debugger) használja
|``h``  |   Megjeleníti a súgót (az itt felsorolt egyéb kulcsok is megjelennek a súgóban, de ``h`` könnyen megjegyezhető:-)
|``i``  |    SIGKILL küldése minden folyamatnak, az init kivételével.
|``j``  |    Kényszerített "csak felolvasztás" – a FIFREEZE IOCTL által megfagyott fájlrendszerek.
|``k``  |    Biztonságos hozzáférési kulcs (SAK) az aktuális virtuális konzolon lévő összes program kiölése. Megjegyzés: lásd az alábbi fontos megjegyzéseket a SAK szakaszban.
|``l``  |    Megjeleníti a verem backtrace az összes aktív processzorhoz.
|``m``  |    Az aktuális memória adatait a konzolon fogja kimutatni.
|``n``  |    Az RT-feladatok Nice-ablevé tételéhez használatos
|``o``  |    Kikapcsolja a rendszerét (ha konfigurálva van és támogatott).
|``p``  |    Az aktuális regisztereket és jelzőket a konzolon fogja kivezetni.
|``q``  |    Az összes fegyveres hrtimers (de nem rendszeres timer_list időzítők) és az összes clockevent-eszköz részletes információit tartalmazó CPU-listát jeleníti meg.
|``r``  |    Kikapcsolja a billentyűzet RAW üzemmódját, és beállítja a XLATE.
|``s``  |    Megkísérli az összes csatlakoztatott fájlrendszer szinkronizálását.
|``t``  |    A kiírja az aktuális feladatok listáját és az adataikat a konzolra.
|``u``  |    A megkísérli az összes írásvédett csatlakoztatott fájlrendszer újracsatlakoztatását.
|``v``  |    A framebuffer konzol kényszerített visszaállítása
|``v``  |    ETM-puffer kiírásának okai [ARM-specifikus]
|``w``  |    A nem megszakított (letiltott) állapotú feladatok kiírása.
|``x``  |    A xmon Interface használja a PPC/PowerPC platformokon. Globális PMU-regisztrációk megjelenítése a sparc64. Az összes TLB-bejegyzés kiírása a MIPS-ben.
|``y``  |    Globális CPU-regisztrációk megjelenítése [SPARC-64-specifikus]
|``z``  |    A ftrace puffer kiírása
|``0``-``9`` | Beállítja a konzol naplózási szintjét, amely szabályozza, hogy mely kernel-üzenetek lesznek kinyomtatva a konzolra. (``0``például úgy tenné, hogy csak olyan vészhelyzeti üzenetek legyenek a konzolon, mint a pánik vagy a Hoppá.)

### <a name="distribution-specific-documentation"></a>Distribution-specifikus dokumentáció ###
A SysRq-re és a Linux konfigurálására vonatkozó, az összeomlási memóriakép létrehozásához szükséges telepítési dokumentációt az alábbi hivatkozásokra kattintva tekintheti meg:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel összeomlási memóriaképe](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Mi a SysRq-létesítmény, és hogyan használhatom?](https://access.redhat.com/articles/231663)
- [A SysRq létesítmény használata adatok gyűjtésére egy RHEL-kiszolgálóról](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [A rendszermag alapvető memóriaképének beállítása](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Összeomlási naplók gyűjtése](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Nem maszkolt megszakítás (NMI) 
A nem maszkolt megszakítás (NMI) úgy van kialakítva, hogy egy olyan jelet hozzon létre, amelyet a virtuális gépen lévő szoftver nem hagy figyelmen kívül. A NMIs korábban a hardveres problémák figyelésére használták az adott válaszidőt igénylő rendszereken.  Napjainkban a programozók és a rendszergazdák gyakran használják a NMI-t a nem válaszoló rendszerek hibakeresésére vagy hibaelhárítására.

A soros konzol segítségével NMI küldhet egy Azure-beli virtuális gépre az alább látható parancssáv billentyűzet ikonjával. A NMI elszállítása után a virtuális gép konfigurációja vezérli, hogy a rendszer hogyan reagáljon.  A Linux operációs rendszerek összeomolhatnak, és a memóriakép létrehozásával az operációs rendszer NMI kap.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Olyan Linux rendszerekhez, amelyek támogatják a sysctl-t a rendszermag paramétereinek konfigurálásához, a következő lépésekkel engedélyezheti a pánikot a NMI fogadásakor:
1. A sor hozzáadása a */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. A sysctl újraindítása vagy frissítése a futtatásával <br>
    `sysctl -p`

A Linux kernel-konfigurációkkal kapcsolatos további információkért `unknown_nmi_panic`, `panic_on_io_nmi`beleértve a `panic_on_unrecovered_nmi`, a és a rendszert, lásd: [a/proc/sys/kernel/dokumentációja *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). A NMI-re és a Linux konfigurálására vonatkozó, az összeomlási memóriakép létrehozásához szükséges, az alábbi hivatkozásokra kattintva részletesen tájékozódhat:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Kernel összeomlási memóriaképe](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Mi az a NMI, és mire használható?](https://access.redhat.com/solutions/4127)
 - [Hogyan állíthatom be, hogy a rendszer összeomlik a NMI kapcsoló leküldésekor?](https://access.redhat.com/solutions/125103)
 - [Összeomlási memóriakép rendszergazdai útmutatója](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [A rendszermag alapvető memóriaképének beállítása](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Összeomlási naplók gyűjtése](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>További lépések
* [Itt](serial-console.md)található a soros konzolhoz tartozó fő Linux Dokumentációs oldal.
* A soros konzol használatával indítsa el a [grub-t, és adjon meg egy felhasználói módot](serial-console-grub-single-user-mode.md)
* A soros konzol a [Windows](../windows/serial-console.md) rendszerű virtuális gépekhez is elérhető
* További információ a [rendszerindítási diagnosztika](boot-diagnostics.md) szolgáltatásról