---
title: Azure Serial Console grub és egyfelhasználós módhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja a serial console grub az Azure virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883932"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>A GRUB és az egyfelhasználós mód eléréséhez használja a Soros konzolt
GRand Unified Bootloader (GRUB) valószínűleg az első dolog, amit látni, amikor elindítja a virtuális gép (VM). Mivel az operációs rendszer indulása előtt jelenik meg, a GRUB nem érhető el az SSH-n keresztül. A GRUB-ban módosíthatja a rendszerindítási konfigurációt, hogy egyfelhasználós módba induljon, többek között.

Az egyfelhasználós mód minimális környezet, minimális funkcionalitással. Hasznos lehet a rendszerindítási, fájlrendszeri vagy hálózati problémák kivizsgálásához. Kevesebb szolgáltatás futhat a háttérben, és a futtatási szinttől függően előfordulhat, hogy a fájlrendszer nem is lesz automatikusan csatlakoztatva.

Az egyfelhasználós mód olyan helyzetekben is hasznos, amikor a virtuális gép úgy konfigurálható, hogy csak SSH-kulcsokat fogadjon el a bejelentkezéshez. Ebben az esetben előfordulhat, hogy egyfelhasználós módban létrehozhat egy fiókot jelszó-hitelesítéssel. 

> [!NOTE]
> A Soros konzol szolgáltatás csak *a közreműködői* szintű vagy magasabb szintű hozzáféréssel rendelkező felhasználók számára teszi lehetővé a virtuális gép soros konzoljának elérését.

Az egyfelhasználós módba való belépéshez írja be a GRUB parancsot, amikor a virtuális gép elindul, és módosítsa a rendszerindítási konfigurációt a GRUB-ban. Lásd a GRUB beírásának részletes utasításait a következő szakaszban. Általában, ha a virtuális gép a GRUB megjelenítésére van konfigurálva, a virtuális gép soros konzolján lévő újraindítás gombbal újraindíthatja a virtuális gépés a GRUB megjelenítését.

![A Linux soros konzol újraindítása gomb](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Általános GRUB hozzáférés
A GRUB eléréséhez indítsa újra a virtuális gépet, amíg a Soros konzol ablaktábla meg van nyitva. Egyes disztribúciók billentyűzetbevitelt igényelnek a GRUB megjelenítéséhez, míg mások néhány másodpercig automatikusan megjelenítik a GRUB-ot, hogy a felhasználó billentyűzetbevitele megszakíthassa az időtúlcukrot.

Az egyfelhasználós mód eléréséhez győződjön meg arról, hogy a GRUB engedélyezve van a virtuális gépen. A disztribúciótól függően szükség lehet bizonyos beállítási munkára a GRUB engedélyezésének biztosításához. A disztribúció-specifikus információk: a következő szakasz és [a Linux-támogatás az Azure-ban](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) oldalon.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>A virtuális gép újraindítása a GRUB serial console eléréséhez
A virtuális gép újraindítása a Soros konzolon úgy, hogy az Újraindítás gombra viszi az **egérmutatót,** majd a **Virtuális gép újraindítása**lehetőséget választja. Az újraindításról szóló értesítés jelenik meg az ablaktábla alján.

A virtuális gép újraindításához a SysRq "b" parancs futtatásával, ha [a SysRq](./serial-console-nmi-sysrq.md) engedélyezve van. Ha meg szeretné tudni, hogy mire számíthat a GRUB-tól újraindításkor, tekintse meg a disztribúció-specifikus utasításokat a következő szakaszokban.

![Linux soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Általános egyfelhasználós hozzáférés
Előfordulhat, hogy manuálisan kell hozzáférnie az egyfelhasználós módhoz, ha még nem konfigurált a jelszó-hitelesítéssel rendelkező fiókot. Módosítsa a GRUB konfigurációt, hogy manuálisan lépjen be egyfelhasználós módba. Miután ezt megtette, további útmutatásért olvassa el az "Egyfelhasználós mód használata alaphelyzetbe állításhoz vagy jelszó hozzáadása" című szakaszt.

Ha a virtuális gép nem tudja elindítani, a disztribúciók gyakran automatikusan egyfelhasználós vagy vészhelyzeti módba dobják. Más disztribúciók azonban további beállításokat igényelnek, például egy gyökérjelszót, mielőtt automatikusan egyfelhasználós vagy vészhelyzeti módba kapcsolhatnának.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Jelszó alaphelyzetbe állítása vagy hozzáadása egyfelhasználós móddal
Az egyfelhasználós módba való beadás után adjon hozzá egy sudo jogosultságokkal rendelkező új felhasználót az alábbi módon:
1. Futtassa `useradd <username>` felhasználó hozzáadásához.
1. Futtassa `sudo usermod -a -G sudo <username>` az új felhasználói gyökérjogosultságok megadásához.
1. Itt `passwd <username>` állíthatja be az új felhasználó jelszavát. Ezután új felhasználóként jelentkezhet be.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Hozzáférés a Red Hat Enterprise Linux (RHEL)
Ha az RHEL nem tud normálisan elindulni, automatikusan egyfelhasználós módba vált. Ha azonban nem állított be root hozzáférést az egyfelhasználós módhoz, nem rendelkezik gyökérjelszóval, és nem tud bejelentkezni. Van egy kerülő megoldás (lásd a "Manuálisbelépés az egyfelhasználós mód megadása az RHEL-ben" című szakaszt), de javasoljuk, hogy kezdetben állítsa be a root hozzáférést.

### <a name="grub-access-in-rhel"></a>GRUB hozzáférés az RHEL-ben
RHEL jön grub engedélyezve a dobozból. A GRUB beírásához indítsa `sudo reboot`újra a virtuális gépet a futtatásával, majd nyomja meg bármelyik billentyűt. A GRUB ablaktáblának meg kell jelennie. Ha nem, győződjön meg arról, hogy a`/etc/default/grub`következő sorok vannak jelen a GRUB fájlban ( ):

**RHEL 8 esetén**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**RHEL 7 esetén**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> A Red Hat dokumentációt is biztosít a mentési módba, a vészhelyzeti módba vagy a Debug módba történő rendszerindításhoz, valamint a gyökérjelszó alaphelyzetbe állításához. További információt a [Terminál menü szerkesztése indítás kor című](https://aka.ms/rhel7grubterminal)témakörben talál.

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Root hozzáférés beállítása egyfelhasználós módhoz RHEL-ben
A gyökérfelhasználó alapértelmezés szerint le van tiltva. Az RHEL egyfelhasználós üzemmódja a gyökérfelhasználó engedélyezését igényli. Ha engedélyeznie kell az egyfelhasználós módot, kövesse az alábbi utasításokat:

1. Jelentkezzen be a Red Hat rendszerbe SSH-n keresztül.
1. Váltson gyökérre.
1. Engedélyezze a jelszót a legfelső szintű felhasználó számára az alábbi módon:
    * Futtatás `passwd root` (erős gyökérjelszó beállítása).
1. Győződjön meg arról, hogy a legfelső szintű felhasználó csak a ttyS0-n keresztül tud bejelentkezni az alábbi módon:  
    a. Futtassa `edit /etc/ssh/sshd_config`a t, és győződjön meg arról, hogy az PermitRootLogIn beállítása a. `no`  
    b. Futtassa `edit /etc/securetty file` a bejelentkezés engedélyezéséhez csak ttyS0 keresztül.

Most, ha a rendszer elindul egyfelhasználós módban, akkor jelentkezzen be a gyökér jelszót.

Másik lehetőségként az RHEL 7.4+ vagy 6.9+esetén az egyfelhasználós mód engedélyezéséhez a GRUB-kérésekben olvassa el a [Indítás egyfelhasználós módban című témakört.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Egyfelhasználós mód manuális megadása RHEL-ben
Ha az előző utasítások használatával állította be a GRUB és a root hozzáférést, az alábbi módon léphet be az egyfelhasználós módba:

1. A GRUB beírásához nyomja le az Esc billentyűt a virtuális gép újraindítása kor.
1. A GRUB-ban nyomja meg az E billentyűt a rendszer indításához kívánt operációs rendszer szerkesztéséhez. Az operációs rendszer általában az első sorban szerepel.
1. Keresse meg a kernel vonalat. Az Azure-ban *linux16-tal*kezdődik.
1. Nyomja le a Ctrl+E billentyűkombinációt a sor végére való ugráshoz.
1. A sor végén adja hozzá a *systemd.unit=rescue.target fájlt.*
    
    Ez a művelet egyfelhasználós módba vált. Ha vészhelyzeti üzemmódot szeretne használni, adja hozzá a *systemd.unit=emergency.target parancsot* a sor végéhez (a *systemd.unit=rescue.target*helyett).

1. Nyomja le a Ctrl+X billentyűkombinációt a kilépéshez és az újraindításhoz az alkalmazott beállításokkal.

   Az egyfelhasználós módba való belépés előtt a rendszer kérni fogja a rendszergazdai jelszót. Ez a jelszó az előző utasításokban létrehozott jelszó.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Adja meg az egyfelhasználós módot anélkül, hogy az RHEL-ben root fiók engedélyezve lenne
Ha nem engedélyezte a root felhasználót a korábbi utasítások követésével, a gyökérjelszó visszaállítása a következő módon:

> [!NOTE]
> Ha SELinuxot használsz, amikor visszaállítja a gyökérjelszót, mindenképpen kövesd a [Red Hat dokumentációjában](https://aka.ms/rhel7grubterminal)ismertetett további lépéseket.

1. A GRUB beírásához nyomja le az Esc billentyűt a virtuális gép újraindítása kor.

1. A GRUB-ban nyomja meg az E billentyűt a rendszer indításához kívánt operációs rendszer szerkesztéséhez. Az operációs rendszer általában az első sorban szerepel.
1. Keresse meg a kernel vonalat. Az Azure-ban *linux16-tal*kezdődik.
1. A sor végén adja hozzá a *rd.break-et* a sor végéhez. Hagyjon szóközt a kernelsor és a *rd.break*között.

    Ez a művelet megszakítja a rendszerindítási folyamatot, mielőtt a vezérlés a rendszerből a rendszerbe `initramfs` továbbítaná `systemd`a Red Hat [dokumentációjában](https://aka.ms/rhel7rootpassword)leírtak szerint.
1. Nyomja le a Ctrl+X billentyűkombinációt a kilépéshez és az újraindításhoz az alkalmazott beállításokkal.

   Az újraindítás után a rendszer vészüzemmódba kapcsol egy csak olvasható fájlrendszerrel. 
   
1. A rendszerhéjban `mount -o remount,rw /sysroot` írja be a gyökérfájlrendszer újracsatlakoztatásához olvasási/írási engedélyekkel.
1. Miután elindult egyfelhasználós módban, adja `chroot /sysroot` `sysroot` meg váltani a börtönbe.
1. Most már gyökerestül vagy. A gyökérjelszó alaphelyzetbe `passwd` állításával visszaállíthatja a gyökérjelszót, majd az előző utasításokat használva lépjen be az egyfelhasználós módba. 
1. Miután elkészült, `reboot -f` írja be az újraindítást.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Az előző utasítások on-át futva a vészhelyzeti rendszerhéjba `fstab`kerül, így olyan feladatokat is végrehajthat, mint a szerkesztés. Azonban általában azt javasoljuk, hogy állítsa vissza a gyökér jelszót, és használja azt, hogy adja meg az egyfelhasználós módban.

## <a name="access-for-centos"></a>Hozzáférés a CentOS-hoz
Hasonlóan a Red Hat Enterprise Linuxhoz, a CentOS egyfelhasználós módjához hasonlóan a GRUB és a root felhasználó is engedélyezve van.

### <a name="grub-access-in-centos"></a>GRUB hozzáférés a CentOS-ban
CentOS jön grub engedélyezve a dobozból. A GRUB beírásához indítsa `sudo reboot`újra a virtuális gépet a beírásával, majd nyomja meg bármelyik billentyűt. Ez a művelet megjeleníti a GRUB ablaktáblát.

### <a name="single-user-mode-in-centos"></a>Egyfelhasználós mód a CentOS rendszerben
Az egyfelhasználós mód engedélyezéséhez a CentOS rendszerben kövesse az RHEL korábbi utasításait.

## <a name="access-for-ubuntu"></a>Hozzáférés az Ubuntuhoz
Az Ubuntu-képek nem igényelnek root jelszót. Ha a rendszer egyfelhasználós módba vált, további hitelesítő adatok nélkül is használhatja.

### <a name="grub-access-in-ubuntu"></a>GRUB hozzáférés az Ubuntuban
A GRUB eléréséhez tartsa lenyomva az Esc billentyűt, miközben a virtuális gép elindul.

Alapértelmezés szerint előfordulhat, hogy az Ubuntu-képek nem jelenítik meg automatikusan a GRUB ablaktáblát. A beállítást az alábbi módon módosíthatja:
1. A szövegszerkesztőben nyissa meg az */etc/default/grub.d/50-cloudimg-settings.cfg* fájlt.

1. Módosítsa `GRUB_TIMEOUT` az értéket nem nulla értékre.
1. A szövegszerkesztőben nyissa meg *az /etc/default/grub kapcsolót.*
1. Kommentálva `GRUB_HIDDEN_TIMEOUT=1` a sort.
1. Győződjön meg róla, hogy van egy `GRUB_TIMEOUT_STYLE=menu` vonal.
1. Futtassa az `sudo update-grub` parancsot.

### <a name="single-user-mode-in-ubuntu"></a>Egyfelhasználós mód az Ubuntuban
Ha az Ubuntu nem tud normálisan elindulni, automatikusan egyfelhasználós módba vált. Ha manuálisan szeretne egyfelhasználós módba lépni, tegye a következőket:

1. A GRUB-ban nyomja meg az E billentyűt a rendszerindító bejegyzés szerkesztéséhez (az Ubuntu bejegyzés).
1. Keresse meg a sort, hogy kezdődik *linux*, majd keresse meg a *ro*.
1. Add *után egyetlen* *ro*, biztosítva van egy hely előtt és után *egyetlen*.
1. A Ctrl+X billentyűkombinációval indítsa újra ezeket a beállításokat, és lépjen be egyfelhasználós módba.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Használja a GRUB-ot a bash megmeghívásához az Ubuntuban
Miután megpróbálta az előző utasításokat, előfordulhat, hogy olyan helyzet áll fenn (például egy elfelejtett gyökérjelszó), amelyben továbbra sem tudja elérni az egyfelhasználós módot az Ubuntu VM-ben. Azt is megmondhatja `/bin/bash` a kernelnek, hogy initként fusson, ne pedig a rendszer init. Ez a művelet bash shell-t ad, és lehetővé teszi a rendszer karbantartását. Kövesse a következő utasításokat:

1. A GRUB-ban nyomja meg az E billentyűt a rendszerindító bejegyzés szerkesztéséhez (az Ubuntu bejegyzés).

1. Keresse meg a sort, hogy kezdődik *linux*, majd keresse meg a *ro*.
1. Cserélje le *a ro-t* *rw init=/bin/bash-re.*

    Ez a művelet a fájlrendszert írási `/bin/bash` és írási műveletekként csatlakoztatja, és init folyamatként használja.
1. A Ctrl+X billentyűkombinációval indítsa újra ezeket a beállításokat.

## <a name="access-for-coreos"></a>Hozzáférés a CoreOS-hoz
A CoreOS egyfelhasználós üzemmódjában engedélyezni kell a GRUB-ot.

### <a name="grub-access-in-coreos"></a>GRUB hozzáférés a CoreOS-ban
A GRUB eléréséhez nyomja meg bármelyik billentyűt a virtuális gép indítása közben.

### <a name="single-user-mode-in-coreos"></a>Egyfelhasználós mód a CoreOS-ban
Ha a CoreOS nem tud normálisan elindulni, automatikusan egyfelhasználós módba vált. Ha manuálisan szeretne egyfelhasználós módba lépni, tegye a következőket:

1. A GRUB-ban nyomja meg az E billentyűt a rendszerindító bejegyzés szerkesztéséhez.

1. Keresse meg a sort, hogy kezdődik *linux $*. A vonalnak két példányban kell lennie, mindegyik másba van ágyazva, *ha... más záradék.*
1. Fűzz *a coreos.autologin=ttyS0-t* az egyes *linux$* sorok végére.
1. A Ctrl+X billentyűkombinációval indítsa újra ezeket a beállításokat, és lépjen be egyfelhasználós módba.

## <a name="access-for-suse-sles"></a>Hozzáférés a SUSE SLES-hez
Az SLES 12 SP3+ újabb képei lehetővé teszik a hozzáférést a soros konzolon keresztül, ha a rendszer vészhelyzeti üzemmódba kapcsol.

### <a name="grub-access-in-suse-sles"></a>GRUB hozzáférés a SUSE SLES-ben
Grub hozzáférés SLES igényel bootloader konfiguráció keresztül YaST. A konfiguráció létrehozásához tegye a következőket:

1. Az SSH használatával jelentkezzen be az SLES `sudo yast bootloader`virtuális gépbe, majd futtassa a futtassa. Nyomja le a Tab billentyűt, az Enter billentyűt, majd a nyílbillentyűkkel navigáljon a menüben.

1. Nyissa meg a **Kernel paraméterek**lehetőséget, és jelölje be a Soros **konzol használata** jelölőnégyzetet.
1. Hozzáadás `serial --unit=0 --speed=9600 --parity=no` a **konzol** argumentumaihoz.
1. Nyomja le az F10 billentyűt a beállítások mentéséhez és a kilépéshez.
1. A GRUB beírásához indítsa újra a virtuális gépet, és a rendszerindítási folyamat során nyomja meg bármelyik billentyűt, hogy a GRUB ablaktábla megjelenjen.

    A GRUB alapértelmezett időmeghosszabbítása **1s**. Ezt a beállítást az `GRUB_TIMEOUT` */etc/default/grub* fájl változójának módosításával módosíthatja.

![A rendszertöltő konfigurációjának inicializálása](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Egyfelhasználós mód a SUSE SLES-ben
Ha az SLES nem tud normálisan elindulni, akkor automatikusan a vészhéjba kerül. A vészjelző manuális beviteléhez tegye a következőket:

1. A GRUB-ban nyomja meg az E billentyűt a rendszerindító bejegyzés (az SLES bejegyzés) szerkesztéséhez.

1. Keresse meg a kernel sort, hogy kezdődik *linux*.
1. Hozzáfűzés *systemd.unit=emergency.target* a kernelsor végéhez.
1. Nyomja le a Ctrl+X billentyűkombinációt az újraindításhoz ezekkel a beállításokkal, és írja be a vészhéjat.

   > [!NOTE]
   > Ez a művelet a vészjelző rendszerbe cseppel egy csak olvasható fájlrendszerrel. Fájlok szerkesztéséhez csatlakoztassa újra a fájlrendszert olvasási és írási engedélyekkel. Ehhez írja be `mount -o remount,rw /` a héj.

## <a name="access-for-oracle-linux"></a>Hozzáférés az Oracle Linuxhoz
Hasonlóan a Red Hat Enterprise Linuxhoz, az Oracle Linux egyfelhasználós módjához hasonlóan a GRUB és a root felhasználó is engedélyezve van.

### <a name="grub-access-in-oracle-linux"></a>GRUB hozzáférés Oracle Linux-ban
Oracle Linux jön a GRUB engedélyezve ki a dobozból. A GRUB-ba való belépéshez `sudo reboot`indítsa újra a virtuális gépet a futtatásával, majd nyomja le az Esc billentyűt. Ez a művelet megjeleníti a GRUB ablaktáblát. Ha a GRUB ablaktábla nem jelenik meg, győződjön meg arról, hogy a `GRUB_TERMINAL` sor értéke soros *konzolt* tartalmaz `GRUB_TERMINAL="serial console"`(azaz). Újjáépíteni grub `grub2-mkconfig -o /boot/grub/grub.cfg`a .

### <a name="single-user-mode-in-oracle-linux"></a>Egyfelhasználós mód oracle Linux-ban
Az egyfelhasználós mód oracle Linux ban való engedélyezéséhez kövesse az RHEL korábbi utasításait.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Soros konzolról, olvassa el a következő témakört:
* [Linux soros konzol dokumentációja](serial-console-linux.md)
* [A SERIAL Console használata a GRUB különböző disztribúciókban való engedélyezéséhez](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [A Soros konzol használata NMI- és SysRq-hívásokhoz](serial-console-nmi-sysrq.md)
* [Windows-virtuális gépek soros konzolja](serial-console-windows.md)
* [Rendszerindítási diagnosztika](boot-diagnostics.md)
