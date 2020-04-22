---
title: Azure soros konzol SysRq és NMI-hívásokhoz
description: Soros konzol használata SysRq és NMI-hívások az Azure virtuális gépeken.
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5541dec748f31818a0e9485fc0c56b7926ccaae7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758479"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Serial Console használata SysRq- és NMI-hívásokhoz

## <a name="system-request-sysrq"></a>Rendszerkérelem (SysRq)
A SysRq a Linux operációs rendszer kerneláltal értett kulcssorozat, amely előre meghatározott műveletek et indíthat el. Ezeket a parancsokat gyakran használják, ha a virtuális gép hibaelhárítása vagy helyreállítása nem hajtható végre a hagyományos felügyelet (például ha a virtuális gép nem válaszol). Az Azure Serial Console SysRq szolgáltatásának használatával a SysRq billentyű lenyomása és a fizikai billentyűzeten megadott karakterek lesznek.

A SysRq-szekvencia kézbesítése után a rendszer konfigurációja szabályozza a rendszer válaszát. A SysRq engedélyezéséről és letiltásáról a *SysRq Felügyeleti útmutató* [szövegjelölésében](https://aka.ms/linuxsysrq) [text](https://aka.ms/kernelorgsysreqdoc) | talál további információt.  

Az Azure Serial Console segítségével egy SysRq egy Azure virtuális gép a billentyűzet ikonja a parancssávon az alábbi parancssávon.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

A "Send SysRq Command" (SysRq parancs küldése) beállítással megnyílik egy párbeszédpanel, amely általános SysRq-beállításokat biztosít, vagy elfogadja a párbeszédbe bevitt SysRq parancsok sorozatát.  Ez lehetővé teszi, hogy a SysRq sorozata magas szintű műveletet hajtson végre, például biztonságos újraindítást hajtson végre a következők használatával: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

A SysRq parancs nem használható olyan virtuális gépeken, amelyek leálltak, vagy amelyek kernele nem válaszoló állapotban van. (például kernel pánik).

### <a name="enable-sysrq"></a>SysRq engedélyezése 
A fenti *SysRq felügyeleti útmutatóban leírtak* szerint a SysRq úgy konfigurálható, hogy minden, nincs vagy csak bizonyos parancsok érhetők el. Engedélyezheti az összes SysRq parancsok segítségével az alábbi lépés, de ez nem éli túl az újraindítás:
```
echo "1" >/proc/sys/kernel/sysrq
```
A SysReq konfiguráció állandóvá tétele érdekében az alábbi műveletekkel engedélyezheti az összes SysRq parancsot
1. A sor hozzáadása az */etc/sysctl.conf fájlhoz* <br>
    `kernel.sysrq = 1`
1. A sysctl újraindítása vagy frissítése futással <br>
    `sysctl -p`

### <a name="command-keys"></a>Parancsbillentyűk 
A Fenti SysRq Admin útmutatóból:

|Parancs| Függvény
| ------| ----------- |
|``b``  |   Azonnal újraindítja a rendszert a lemezek szinkronizálása vagy leválasztása nélkül.
|``c``  |   A rendszer összeomlását null mutató dereferencia hajtja végre. Ha konfigurálva van, összeomlási memóriakép kerül sor.
|``d``  |   Az összes tartott zárolás megjelenítése.
|``e``  |   SIGTERM küldése minden folyamatnak, kivéve az init.Send a SIGTERM to all processes, except init.
|``f``  |   Fogja hívni az oom gyilkos megölni egy memória disznó folyamat, de ne ess pánikba, ha semmit sem lehet megölni.
|``g``  |   Által használt kgdb (kernel debugger)
|``h``  |   Megjelenik a súgó (az itt felsoroltakon kívül ``h`` bármely más kulcs is megjelenik a súgó, de könnyen megjegyezhető :-)
|``i``  |    Küldjön egy SIGKILL-t minden folyamatnak, kivéve az init-et.
|``j``  |    Erőszakkal "Csak felolvasztani" - fájlrendszerek fagyasztott a FIFREEZE ioctl.
|``k``  |    Biztonságos hozzáférési kulcs (SAK) Az aktuális virtuális konzol összes programjának elvetése. MEGJEGYZÉS: Lásd az alábbi fontos megjegyzéseket a SAK részben.
|``l``  |    Az összes aktív processzor verem-visszakövetését jeleníti meg.
|``m``  |    Az aktuális memóriaadatokat a konzolra dobja ki.
|``n``  |    Arra szolgál, hogy az RT feladatok attasé
|``o``  |    Kikapcsolja a rendszert (ha konfigurálva és támogatott).
|``p``  |    Az aktuális regiszterek és jelzők kiírása a konzolra.
|``q``  |    Dump egy CPU listák az összes fegyveres hrtimers (de nem rendszeres timer_list időzítők) és részletes információkat az összes clockevent eszközök.
|``r``  |    Kikapcsolja a billentyűzet nyers üzemmódját, és xlate-re állítja.
|``s``  |    Megpróbálja szinkronizálni az összes csatlakoztatott fájlrendszert.
|``t``  |    Az aktuális feladatok és azok adatainak listáját a konzolra dobja.
|``u``  |    Megkísérli az összes csatlakoztatott fájlrendszer újracsatlakoztatására, csak olvasható.
|``v``  |    A framebuffer konzol erőteljes visszaállítása
|``v``  |    EtM puffermemóriakép okai [ARM-specifikus]
|``w``  |    Szünetmentes (blokkolt) állapotú feladatok kiírása.
|``x``  |    Által használt xmon interfész ppc / powerpc platformokon. Globális PMU-regiszterek megjelenítése a sparc64-en. Az összes TLB-bejegyzés kiírása a MIPS-en.
|``y``  |    Globális CPU-regiszterek megjelenítése [SPARC-64-specifikus]
|``z``  |    A ftrace puffer kiírása
|``0``-``9`` | Beállítja a konzolnapló szintjét, amely azt szabályozza, hogy mely kernelüzenetek et fogja kinyomtatni a konzolra. (``0``például úgy tenné, hogy csak az olyan vészhelyzeti üzenetek, mint a PANIC-ek vagy az OOPSes jutnának el a konzolra.)

### <a name="distribution-specific-documentation"></a>Disztribúció-specifikus dokumentáció ###
A SysRq disztribúció-specifikus dokumentációját és a Linux összeomlási memóriakép létrehozásának lépéseit, amikor megkapja a SysRq "Crash" parancsot, lásd az alábbi hivatkozásokat:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel összeomlási memóriakép](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Mi az a SysRq létesítmény, és hogyan használhatom?](https://access.redhat.com/articles/231663)
- [A SysRq eszköz használata információk gyűjtésére rhel szerverről](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Kernel magmemória-memóriakép rögzítésének konfigurálása](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Összeomlási naplók gyűjtése](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Nem maszkolható megszakítás (NMI) 
A nem maszkolható megszakítás (NMI) olyan jelet hoz létre, amelyet a virtuális gépen lévő szoftverek nem hagynak figyelmen kívül. Korábban az NM-eket arra használták, hogy figyeljék a hardverproblémákat a konkrét válaszidőt megkövetelő rendszereken.  Napjainkban a programozók és a rendszergazdák gyakran használják az NMI-t a nem válaszoló rendszerek hibakeresésére vagy hibaelhárítására.

A soros konzol segítségével nmi-t küldhet egy Azure virtuális gépnek az alábbi parancssávon található billentyűzet ikon használatával. Az NMI kézbesítése után a virtuális gép konfigurációja szabályozza a rendszer válaszát.  Linux operációs rendszerek konfigurálható összeomlik, és hozzon létre egy memóriakép az operációs rendszer kap egy NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

A sysctl-t a kernel paraméterek konfigurálását támogató Linux-rendszerek esetében az NMI fogadásakor pánikot okozhat az alábbi használatával:
1. A sor hozzáadása az */etc/sysctl.conf fájlhoz* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. A sysctl újraindítása vagy frissítése futással <br>
    `sysctl -p`

A Linux kernel konfigurációiról `unknown_nmi_panic`, `panic_on_io_nmi`például a , és a, `panic_on_unrecovered_nmi`a dokumentáció a [/proc/sys/kernel/* fájlról](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt)talál további információt. Az NMI-n történő terjesztésspecifikus dokumentációt és a Linux összeomlási memóriakép létrehozására vonatkozó beállításokat, amikor NMI-t kap, lásd az alábbi hivatkozásokat:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Kernel összeomlási memóriakép](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Mi az NMI, és mire használhatom?](https://access.redhat.com/solutions/4127)
 - [Hogyan állíthatom be, hogy a rendszer összeomlik, amikor az NMI kapcsolót megnyomják?](https://access.redhat.com/solutions/125103)
 - [Összeomlási memóriakép felügyeleti útmutatója](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Kernel magmemória-memóriakép rögzítésének konfigurálása](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Összeomlási naplók gyűjtése](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>További lépések
* A fő Soros Konzol Linux dokumentációs oldal [itt](serial-console.md)található .
* A Serial Console használata grub rendszerbe való [rendszerindításhoz és egyfelhasználós módba való belépéshez](serial-console-grub-single-user-mode.md)
* A soros konzol [Windows](../windows/serial-console.md) virtuális gépekhez is elérhető
* További információ a [rendszerindítási diagnosztikáról](boot-diagnostics.md)