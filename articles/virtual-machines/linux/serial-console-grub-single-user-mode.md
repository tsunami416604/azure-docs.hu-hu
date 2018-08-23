---
title: A grub-HIBÁT és egyfelhasználós üzemmódban Azure soros konzol |} A Microsoft Docs
description: Soros konzol használata az Azure-beli virtuális gépek grub-hibát.
services: virtual-machines-linux
documentationcenter: ''
author: alsin
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
ms.openlocfilehash: 059cb0cbc7e62af16dbf95693be421feebcc1ee0
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42059184"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Használja a soros konzol eléréséhez a grub-HIBÁT, és az egyfelhasználós módot
Egyfelhasználós módban, minimális környezetet minimális funkciókkal. Rendszerindító kapcsolatos problémák vizsgálatában vagy hálózati problémák hasznos lehet, mivel kevesebb services futhat a háttérben, és attól függően, a paraméterben megadott futtatási szint, a fájlrendszer előfordulhat, hogy még akkor is automatikusan csatlakoztatása nem. Ez akkor hasznos, amikor például egy sérült fájlrendszer, a tört fstab vizsgálata vagy a hálózati kapcsolat (helytelen engedélyezze az iptables konfigurálása).

Néhány disztribúciókhoz automatikusan csökken, egyfelhasználós módban vagy vészhelyzeti módban, ha a virtuális gép rendszerindítási nem lehet. További telepítési, mások azonban szükség van, mielőtt azok vethetők el, egyfelhasználós, vagy vészhelyzeti módban automatikusan.

Győződjön meg arról, hogy GRUB engedélyezve van a virtuális Gépen ahhoz, hogy hozzáférési egyfelhasználós mód célszerű. A disztribúció függően néhány beállítás munka győződjön meg arról, hogy engedélyezve van-e a grub-HIBÁT is lehet. 


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) hozzáférés
RHEL csökken, akkor egyfelhasználós üzemmódba automatikusan ha általában nem rendszerindító. Azonban nincs beállítva az egyfelhasználós mód rendszergazdai hozzáférést, ha Ön nem fog egy rendszergazdai jelszót és nem lehet bejelentkezni. Egy megkerülő megoldás (lásd a "Manuális megadásánál egyfelhasználós üzemmódban" alább), de a javaslat, hogy kezdetben a legfelső szintű hozzáférés beállítása.

### <a name="grub-access-in-rhel"></a>Az RHEL GRUB-hozzáférés
RHEL beépített engedélyezve grub-HIBÁT tartalmaz. Írja be a grub-HIBÁT, indítsa újra a virtuális Gépen való `sudo reboot` , és nyomja le bármelyik billentyűt. Látni fogja a grub-HIBÁT képernyőn jelennek meg.

> Megjegyzés: A Red Hat is biztosít a dokumentáció készenléti módban, a helyreállító módban, a hibakeresési módban indul, és a legfelső szintű jelszó alaphelyzetbe állítása. [Kattintson ide az eléréséhez](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Egyetlen felhasználói mód RHEL a legfelső szintű hozzáférés beállítása
Az RHEL egyfelhasználós üzemmódra van szükség a gyökér szintű felhasználó engedélyezni kell, amely alapértelmezés szerint le van tiltva. Ha nincs szüksége egyfelhasználós mód engedélyezéséhez, kövesse az alábbi utasításokat:

1. Jelentkezzen be SSH-n keresztül a Red Hat rendszer
1. Váltson át a legfelső szintű
1. Gyökér szintű felhasználó számára jelszó engedélyezése 
    * `passwd root` (erős legfelső szintű jelszó beállítása)
1. Győződjön meg, hogy a gyökér szintű felhasználó csak bejelentkezhet ttyS0 keresztül
    * `edit /etc/ssh/sshd_config` és ellenőrizze, hogy PermitRootLogIn van beállítva, nem
    * `edit /etc/securetty file` a kizárólag a naplót a ttyS0 keresztül 

Mostantól Ha a rendszer betölti a egyfelhasználós módban bejelentkezhet gyökér szintű jelszó keresztül.

Másik lehetőségként az RHEL 7.4 + vagy 6.9 + engedélyezheti egyetlen felhasználói mód a grub-HIBÁT a kérni fogja, tudnivalókat [Itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Adja meg manuálisan az RHEL egyfelhasználós üzemmódban
Ha meg van adva grub-HIBÁT, és a legfelső szintű eléréséhez a fenti lépéseket, majd adhatja meg az alábbi utasítások egyfelhasználós módban:

1. Az ESC billentyűt "" adja meg a grub-HIBÁT a virtuális gép újraindítása közben
1. A grub-HIBÁT nyomja le az "e" a kiválasztott operációs rendszer indítsa el a (általában az első sor) szeretné szerkesztése
1. Keresse meg a kernel sort – az Azure-ban, ezzel kezdődik `linux16`
1. Nyomja le a Ctrl + E, nyissa meg a sor végére
1. A sor végét adja hozzá a következőket: `systemd.unit=rescue.target`
    * Ez tölti be egyfelhasználós üzemmódra vált. Ha szeretne vészhelyzeti módban használja, vegye fel `systemd.unit=emergency.target` helyett a sor végére `systemd.unit=rescue.target`
1. A kilépéshez, és indítsa újra a alkalmazott beállítások a Ctrl + X
1. Meg kell adnia a rendszergazdai jelszó előtt képes arra, hogy adja meg az egyfelhasználós módban – Ez az a fenti utasítások létrehozott ugyanazt a jelszót    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Adja meg a egyfelhasználós mód engedélyezve van a RHEL root fiók nélkül
Ha nem tudta a gyökér szintű felhasználó engedélyezéséhez a fenti lépéseket, a legfelső szintű jelszavát továbbra is visszaállíthatja. Kövesse az alábbi utasításokat:

> Megjegyzés: Ha SELinux használ, győződjön meg a további, a Red Hat dokumentációjában leírt lépések elvégzése [Itt](https://aka.ms/rhel7grubterminal) a rendszergazdai jelszó visszaállítása.

1. Az ESC billentyűt "" adja meg a grub-HIBÁT a virtuális gép újraindítása közben
1. A grub-HIBÁT nyomja le az "e" a kiválasztott operációs rendszer indítsa el a (általában az első sor) szeretné szerkesztése
1. Keresse meg a kernel sort – az Azure-ban, ezzel kezdődik `linux16`
1. Adjon hozzá `rd.break` a sor végére van biztosítva egy szóközt, mielőtt `rd.break` (lásd az alábbi példa)
    - Ezzel megszakítja a rendszerindítási folyamat, mielőtt a vezérlő `initramfs` való `systemd`a Red Hat-dokumentációjában leírtak szerint [Itt](https://aka.ms/rhel7rootpassword).
1. A kilépéshez, és indítsa újra a alkalmazott beállítások a Ctrl + X
1. Miután indítja, eldobott fájl csak olvasható rendszerrel vészhelyzeti módban. Adja meg `mount -o remount,rw /sysroot` újracsatlakoztathatja az olvasási/írási engedéllyel a legfelső szintű fájlrendszer a rendszerhéjba van beépítve
1. Miután egyfelhasználós módban indul, írja be a `chroot /sysroot` lehet átváltani a `sysroot` egyes
1. Ön mostantól legfelső szintű. A legfelső szintű jelszavát is alaphelyzetbe `passwd` , majd a fenti utasítások egyfelhasználós üzemmódra. Típus `reboot -f` elkészült újraindítását.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Megjegyzés: Keresztül a fenti utasítások futtató csökken, akkor vészhelyzeti shellbe, így is elvégezheti a feladatok, például `fstab`. Azonban a általánosan elfogadott javaslat, hogy a legfelső szintű jelszó alaphelyzetbe állítása, és azt használja az egyfelhasználós módot adja meg. 


## <a name="access-for-centos"></a>Hozzáférés a CentOS
Sokkal például a Red Hat Enterprise Linux, CentOS az egyfelhasználós módot szükséges grub-HIBÁT, és a gyökér szintű felhasználó engedélyezni kell. 

### <a name="grub-access-in-centos"></a>A CentOS GRUB-hozzáférés
CentOS beépített engedélyezve grub-HIBÁT tartalmaz. Írja be a grub-HIBÁT, indítsa újra a virtuális Gépen való `sudo reboot` , és nyomja le bármelyik billentyűt. Látni fogja a grub-HIBÁT képernyőn jelennek meg.

### <a name="single-user-mode-in-centos"></a>Egyetlen felhasználói mód a CentOS
Kövesse az utasításokat a CentOS egyfelhasználós mód engedélyezéséhez a fenti RHEL-hez.

## <a name="access-for-ubuntu"></a>Ubuntu hozzáférés 
Ubuntu-rendszerképek nem szükséges egy rendszergazdai jelszót. Ha a rendszer egyfelhasználós módban indul el, további hitelesítő adatok nélkül használhatja. 

### <a name="grub-access-in-ubuntu"></a>Az Ubuntu GRUB-hozzáférés
Hozzáférhet a grub-HIBÁT, tartsa nyomva 'Esc' közben a virtuális gép van másolatából.

### <a name="single-user-mode-in-ubuntu"></a>Az Ubuntu egyfelhasználós üzemmódban
Ubuntu csökken, akkor egyfelhasználós üzemmódba automatikusan ha általában nem rendszerindító. Manuálisan adja meg az egyfelhasználós mód, használja az alábbi utasításokat:

1. A grub-HIBÁT nyomja le az "e" a rendszerindítási bejegyzés (Ubuntu bejegyzés) szerkesztése
1. Keresse meg a kezdetű sor `linux`, majd keresse meg `ro`
1. Adjon hozzá `single` után `ro`biztosítása, nincs olyan hely, előtt és után `single`
1. Indítsa újra ezeket a beállításokat, és írja be a egyfelhasználós üzemmódban a Ctrl + X

## <a name="access-for-coreos"></a>Hozzáférés a CoreOS
Egyetlen felhasználói mód a CoreOS kell engedélyezni kell a grub-HIBÁT. 

### <a name="grub-access-in-coreos"></a>A CoreOS GRUB-hozzáférés
Hozzáférhet a grub-HIBÁT, nyomja le bármelyik billentyűt, amikor a virtuális gép van másolatából.

### <a name="single-user-mode-in-coreos"></a>A CoreOS egyfelhasználós üzemmódban
CoreOS csökken, akkor egyfelhasználós üzemmódba automatikusan ha általában nem rendszerindító. Manuálisan adja meg az egyfelhasználós mód, használja az alábbi utasításokat:
1. A grub-HIBÁT nyomja le az "e" a rendszerindítási bejegyzés szerkesztése
1. Keresse meg a kezdetű sor `linux$`. 2., kell lennie beágyazva különböző if/else szolgáló szerződéses klauzulák
1. Hozzáfűzés `coreos.autologin=ttyS0` , mindkét végén `linux$` sorok
1. Indítsa újra ezeket a beállításokat, és írja be a egyfelhasználós üzemmódban a Ctrl + X

## <a name="access-for-suse-sles"></a>Hozzáférés SUSE SLES-hez
Újabb képek, SLES 12 SP3 + engedélyezi a hozzáférést a soros konzolon keresztül, abban az esetben a rendszer vészhelyzeti módban indul. 

### <a name="grub-access-in-suse-sles"></a>SUSE SLES GRUB-hozzáféréshez
SLES GRUB-hozzáféréshez YaST rendszerbetöltőt konfiguráció szükséges. Ehhez kövesse az alábbi utasításokat:

1. ssh a SLES virtuális gépek és a Futtatás `sudo yast bootloader`. Használja a `tab` kulcs `enter` kulcsot, és a nyílbillentyűkkel navigálhat a menüben. 
1. Navigáljon a `Kernel Parameters`, és ellenőrizze `Use serial console`. 
1. Adjon hozzá `serial --unit=0 --speed=9600 --parity=no` , a konzol argumentumok

1. A beállítások mentéséhez és bezárásához F10 billentyűkombinációval
1. Adja meg a grub-HIBÁT, indítsa újra a virtuális gép, és nyomja le bármelyik billentyűt, hogy hagyja el a képernyőn lévő GRUB rendszerindítási folyamat során
    - Az alapértelmezett időtúllépési érték GRUB 1s. Ez módosításával módosíthatja a `GRUB_TIMEOUT` változót `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>A SUSE SLES egyfelhasználós üzemmódban
Ön automatikusan eldobja vészhelyzeti shellbe SLES általában nem indítható. Adja meg manuálisan a vészhelyzeti rendszerhéj, használja az alábbi utasításokat:

1. A grub-HIBÁT nyomja le az "e" a rendszerindítási bejegyzés (SLES-bejegyzés) szerkesztése
1. Keresse meg a kernel sor azt kezdődik `linux`
1. Hozzáfűzés `systemd.unit=emergency.target` a sor végére
1. Indítsa újra ezeket a beállításokat, és írja be a vészhelyzeti rendszerhéj a Ctrl + X
> Vegye figyelembe, hogy azt a rendszer eldobja a vészhelyzeti shellbe egy _csak olvasható_ fájlrendszer. Ha azt szeretné, hogy módosítani szeretné a fájlokat, szüksége lesz az olvasási és írási engedélyekkel fájlrendszer újbóli csatlakoztatásához. Ehhez adja meg a `mount -o remount,rw /` a rendszerhéjba van beépítve

## <a name="access-for-oracle-linux"></a>Oracle Linux-hozzáférés
Sokkal például a Red Hat Enterprise Linux, az Oracle Linux egyfelhasználós üzemmódban szükséges grub-HIBÁT, és a gyökér szintű felhasználó engedélyezni kell. 

### <a name="grub-access-in-oracle-linux"></a>Az Oracle Linux GRUB-hozzáférés
Oracle Linux beépített engedélyezve grub-HIBÁT tartalmaz. Írja be a grub-HIBÁT, indítsa újra a virtuális Gépen való `sudo reboot` az Esc ' ". Látni fogja a grub-HIBÁT képernyőn jelennek meg.

### <a name="single-user-mode-in-oracle-linux"></a>Az Oracle Linux egyfelhasználós üzemmódban
Kövesse az utasításokat az Oracle Linux egyfelhasználós mód engedélyezéséhez a fenti RHEL-hez.

## <a name="next-steps"></a>További lépések
* A soros konzol fő Linux dokumentációs oldal [Itt](serial-console.md).
* A soros konzol [NMI és SysRq hívások](serial-console-nmi-sysrq.md)
* A soros konzolon érhető el is [Windows](../windows/serial-console.md) virtuális gépek
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md)