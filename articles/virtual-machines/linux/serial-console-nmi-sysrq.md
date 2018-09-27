---
title: Azure-soros konzolon SysRq és NMI hívások |} A Microsoft Docs
description: Soros konzol használatával SysRq és NMI meghívja a Azure-beli virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: ac3e01756ad66dcb44869556bb103eb20bc2658c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221485"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Használja a soros konzol SysRq és NMI hívások

## <a name="system-request-sysrq"></a>Rendszer-kérés (SysRq)
Egy SysRq sorozata, a Linux-művelet rendszer kerneljének, is aktiválhatja az előre definiált műveletek egy csoportját, amely által ismert kulcsokkal. Ezek a parancsok gyakran használják, ha a virtuális gép hibáinak elhárítása vagy helyreállítási nem hajtható végre hagyományos felügyeleti keresztül (például, ha a virtuális gép lefagyott). Az Azure-soros konzolon SysRq szolgáltatásával tesztkörnyezeteknek lenyomja a SysRq kulcs és a egy fizikai billentyűzeten beírt karakterek.

Miután a SysRq feladatütemezési érkezik, a kernel konfigurációs szabályozza hogyan reagál a rendszer. Az engedélyezése és letiltása SysRq információkért lásd: a *SysRq rendszergazdai útmutató* [szöveg](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

Az Azure soros konzol segítségével egy SysRq küldeni egy Azure virtuális gépen a billentyűzet ikon használatával az alább látható a parancssávon.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Kiválasztása "SysRq parancs küldése" megnyílik egy párbeszédpanel, amely közös SysRq lehetőségeket nyújtanak, vagy fogadja el a párbeszédpanelen megadott SysRq parancsok sorozatát fog.  Ez lehetővé teszi a SysRq sorozatát egy magas szintű művelet, például a biztonságos újraindítás végrehajtásához a: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

A SysRq parancs nem használható a virtuális gépek, amelyek le vannak állítva, vagy amelyek kernel nem válaszol. (például kernelpánikot).

### <a name="enable-sysrq"></a>SysRq engedélyezése 
Leírtak szerint a *SysRq rendszergazdai útmutató* újabb SysRq konfigurálható úgy, hogy minden, none vagy csak bizonyos parancsok érhető el. Engedélyezheti az összes, az alábbi lépésben leírtak SysRq parancs, de a számítógép újraindítása nem rendelkezések továbbélnek:
```
echo "1" >/proc/sys/kernel/sysrq
```
Ahhoz, hogy a SysReq konfiguráció állandó, a következő lépésekkel engedélyezheti az összes SysRq parancsok teheti
1. Ezt a sort a hozzáadása */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Rendszer újraindítása vagy futtatásával sysctl frissítése <br>
    `sysctl -p`

### <a name="command-keys"></a>A parancs kulcsok 
A SysRq rendszergazdai útmutató a fenti:

|Parancs| Függvény
| ------| ----------- |
|``b``  |   Azonnal újraindul a rendszer szinkronizálása és a lemezek leválasztása nélkül.
|``c``  |   A rendszer összeomlása hajtja végre a NULL mutató visszakeresési. Egy crashdump megnyílik, ha konfigurálva.
|``d``  |   Az összes megtalálható zárolt mutatja.
|``e``  |   Egy SIGTERM küldése az összes folyamat, kivéve az init.
|``f``  |   A memória hog folyamat leállítása az oom ablak lelövés meghívja, de nem hibákat, ha semmit nem lehet leállítani.
|``g``  |   Kgdb (kernel-hibakereső) által használt
|``h``  |   Súgó jelenik meg (az itt felsorolt, mint bármilyen más kulcs megjeleníti is segítségre van szüksége, de ``h`` könnyen megjegyezhető :-)
|``i``  |    Egy SIGKILL küldése az összes folyamat, kivéve az init.
|``j``  |    Kényszerített "Csak olvasztása," - feladatszinten csatlakoztathatók a FIFREEZE ioctl által görgethesse.
|``k``  |    Biztonságos hozzáférés kulcs (SAK) az aktuális virtuális konzolon minden program megszakítja. Megjegyzés: Fontos megjegyzések alább SAK szakaszban találja.
|``l``  |    Minden, aktív CPU-verem backtrace mutatja be.
|``m``  |    Jelenlegi memória adatait a konzol fog dump.
|``n``  |    Jó tudni, hogy RT feladatok
|``o``  |    A rendszer le fog állni, (ha van konfigurálva, és a támogatott).
|``p``  |    Fog kiírása az aktuális nyilvántartások és jelzőket a konzolt.
|``q``  |    Minden fegyveres hrtimers CPU listája kiszolgálónként fog memóriakép (, de nem rendszeres timer_list időzítők) és a részletes adatok clockevent eszközökről.
|``r``  |    Kikapcsolja a billentyűzet nyers módban, és pedig XLATE.
|``s``  |    Próbálja meg az összes csatlakoztatott fájlrendszerek szinkronizálása.
|``t``  |    Fog dump aktuális tevékenységek és a konzol adataik listáját.
|``u``  |    Megkísérli újracsatlakoztathatja az összes csatlakoztatott fájlrendszerek csak olvasható.
|``v``  |    Kellett zárnia a visszaállítások framebuffer konzol
|``v``  |    Okok ETM puffer dump [ARM-specifikus]
|``w``  |    Szünetmentes (letiltott) állapotban lévő feladatok listázása.
|``x``  |    Ppc/powerpc platformokon xmon felületet használja. Globális PMU regisztrálja megjelenítése sparc64. Memóriakép MIPS minden TLB bejegyzését.
|``y``  |    Globális CPU regisztrálja [SPARC-64 specifikus] megjelenítése
|``z``  |    A ftrace puffer memóriakép
|``0``-``9`` | A konzol naplózási szint, szabályozásával, mely üzeneteket mely kernel nyomtatja a konzol állítja be. (``0``, például tenné, hogy a hasonló PANICs vagy OOPSes csak sürgős üzenetek tenné a konzolhoz.)

### <a name="distribution-specific-documentation"></a>Terjesztésipont-specifikus dokumentációja ###
Terjesztésipont-specifikus SysRq és lépések Linux konfigurálása egy összeomlási memóriakép létrehozása, amikor kap egy SysRq "Összeomlási" parancsot dokumentációjáért lásd: az alábbi hivatkozásokat:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel összeomlási memóriakép](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Mi a SysRq konstrukció, és hogyan használhatom?](https://access.redhat.com/articles/231663)
- [Hogyan használható a SysRq létesítmény kapcsolatos információk összegyűjtéséhez az RHEL-kiszolgálóról](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Kernel memóriakép core rögzítési konfigurálása](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Összeomlás-naplók gyűjtését.](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Nem maszkolható (NMI) 
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül hagyja. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva.  Ma, programozók és a rendszer a rendszergazdák gyakran használnak NMI mechanizmusként javításához vagy hibaelhárítása a rendszerek, amelyek leáll.

A soros konzol segítségével egy NMI küldeni egy Azure virtuális gépen a billentyűzet ikon használatával az alább látható a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan reagál a rendszer.  Linux operációs rendszerek beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása az operációs rendszer egy NMI kap.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Kernel-paraméterek konfigurálásának sysctl támogató Linux rendszerek esetében engedélyezheti egy kilépjen a NMI fogadásakor a következő használatával:
1. Ezt a sort a hozzáadása */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Rendszer újraindítása vagy futtatásával sysctl frissítése <br>
    `sysctl -p`

További információ a Linux kernel beállításokat, beleértve a `unknown_nmi_panic`, `panic_on_io_nmi`, és `panic_on_unrecovered_nmi`, lásd: [dokumentációja a/proc/sys/kernel / *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Terjesztésipont-specifikus NMI és lépések Linux konfigurálása egy összeomlási memóriakép létrehozása, amikor kap egy NMI dokumentációjáért lásd: az alábbi hivatkozásokat:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Kernel összeomlási memóriakép](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Mi az a-NMI, és milyen feladatokat lehet érdemes használni a?](https://access.redhat.com/solutions/4127)
 - [Hogyan konfigurálhatok a rendszer, amikor a NMI kapcsoló leküldéssel?](https://access.redhat.com/solutions/125103)
 - [Crash Dump rendszergazdai útmutatója](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Kernel memóriakép core rögzítési konfigurálása](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Összeomlás-naplók gyűjtését.](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>További lépések
* A soros konzol Linux fő dokumentációs oldal [Itt](serial-console.md).
* Indítsa el a soros konzol használatával [LÁRVAJÁRAT, és adja meg az egyfelhasználós mód](serial-console-grub-single-user-mode.md)
* A soros konzolon érhető el is [Windows](../windows/serial-console.md) virtuális gépek
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md)