---
title: Azure soros konzol a GRUB-hoz és az egyfelhasználós üzemmódhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a soros konzol a GRUB-hoz az Azure Virtual Machines szolgáltatásban.
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
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439540"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>A soros konzol használata a GRUB és a single-user mód eléréséhez
A virtuális gép (VM) rendszerindításakor a rendszer valószínűleg elsőként látja a GRand Unified bootloadert (GRUB). Mivel az operációs rendszer elindítása előtt megjelenik, a GRUB nem érhető el SSH-n keresztül. A GRUB-ban módosíthatja a rendszerindítási konfigurációt úgy, hogy az egyfelhasználós módba induljon, egyebek között.

Az egyfelhasználós mód minimális funkcionalitással rendelkező minimális környezet. Hasznos lehet a rendszerindítási problémák, fájlrendszeri problémák vagy hálózati problémák kivizsgálásához. Kevesebb szolgáltatás futhat a háttérben, és a runlevel függően előfordulhat, hogy a fájlrendszer még nem lesz automatikusan csatlakoztatva.

Az egyfelhasználós mód olyan helyzetekben is hasznos, amikor a virtuális gép úgy van konfigurálva, hogy csak az SSH-kulcsokat fogadja a bejelentkezéshez. Ebben az esetben előfordulhat, hogy az egyfelhasználós mód használatával jelszót használó fiókot hozhat létre.

> [!NOTE]
> A soros konzol szolgáltatás csak *közreműködő* vagy magasabb szintű jogosultsággal rendelkező felhasználókat engedélyez a virtuális gépek soros konzoljának eléréséhez.

Az egyfelhasználós mód megadásához írja be a GRUB-ot a virtuális gép indításakor, és módosítsa a rendszerindítási konfigurációt a GRUB-ban. A következő szakaszban részletes utasítások találhatók a GRUB beírásához. Általánosságban elmondható, hogy ha a virtuális gép a GRUB megjelenítésére lett konfigurálva, a virtuális gép soros konzolján található Újraindítás gombra kattintva indítsa újra a virtuális gépet, és jelenítse meg a GRUB-t.

![A Linux soros konzol újraindítása gombja](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Általános GRUB-hozzáférés
A GRUB eléréséhez indítsa újra a virtuális gépet, amíg a soros konzol ablaktábla meg van nyitva. Egyes disztribúciók billentyűzet-bevitelt igényelnek a GRUB megjelenítéséhez, mások pedig néhány másodpercig automatikusan megmutatják a GRUB használatát, hogy a felhasználói billentyűzet bemenete megszakítsa az időkorlátot.

Az egyfelhasználós üzemmód eléréséhez biztosítania kell, hogy a GRUB engedélyezve legyen a virtuális gépen. A terjesztéstől függően előfordulhat, hogy bizonyos beállítási munkák szükségesek ahhoz, hogy a GRUB engedélyezve legyen. A Distribution-specifikus információk a következő szakaszban olvashatók.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Indítsa újra a virtuális gépet a soros konzolon a GRUB eléréséhez
A virtuális gép újraindításához a soros konzolon vigye az egérmutatót az **Újraindítás** gombra, majd válassza a **virtuális gép újraindítása**lehetőséget. A panel alján megjelenik egy értesítés az újraindításról.

A virtuális gépet a "b" SysRq futtatásával is újraindíthatja, ha a [SYSRQ](./serial-console-nmi-sysrq.md) engedélyezve van. Ha szeretné megtudni, hogy mire számíthat a GRUB-ból, az újraindításkor tekintse meg a következő részekben található terjesztésre vonatkozó utasításokat.

![Linux soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Általános egyfelhasználós módú hozzáférés
Előfordulhat, hogy manuális hozzáférésre van szüksége az egyfelhasználós üzemmódhoz, ha nem konfigurált jelszó-hitelesítéssel rendelkező fiókot. Módosítsa a GRUB-konfigurációt úgy, hogy manuálisan adja meg az egyfelhasználós üzemmódot. Ennek elvégzése után további útmutatásért tekintse meg az "egyfelhasználós mód használata az alaphelyzetbe állításához vagy a jelszó hozzáadása" című szakaszt.

Ha a virtuális gép nem tud elindulni, a disztribúciók gyakran automatikusan elvesznek egy egyfelhasználós módba vagy vészhelyzeti módba. Más disztribúciók esetében azonban szükség van további beállításra, például a root-jelszó beállítására, mielőtt a rendszer automatikusan egy felhasználói vagy vészhelyzeti üzemmódba vonja a felhasználókat.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Jelszó alaphelyzetbe állítása vagy hozzáadása egy felhasználói módban
Ha az egyfelhasználós módban van, a következő lépésekkel adhat hozzá új felhasználót a sudo-jogosultságokkal:
1. `useradd <username>`Felhasználó hozzáadásához futtassa a parancsot.
1. Futtassa `sudo usermod -a -G sudo <username>` az parancsot az új felhasználói gyökérszintű jogosultságok megadásához.
1. Az `passwd <username>` új felhasználó jelszavának beállításához használja a következőt:. Ezután bejelentkezhet új felhasználóként.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Hozzáférés Red Hat Enterprise Linux (RHEL)
Ha a RHEL nem indítható el rendesen, a rendszer automatikusan egy egyszeri felhasználói üzemmódba helyezi az adatvesztést. Ha azonban még nem állította be a gyökérszintű hozzáférést egyfelhasználós módban, nem rendelkezik rendszergazdai jelszóval, és nem tud bejelentkezni. Megkerülő megoldás (lásd a "egyfelhasználós mód manuális megadása a RHEL" szakaszban), de javasoljuk, hogy először állítsa be a gyökérszintű hozzáférést.

### <a name="grub-access-in-rhel"></a>GRUB-hozzáférés a RHEL-ben
A RHEL a GRUB engedélyezve van a dobozból. A GRUB megadásához futtassa újra a virtuális gépet `sudo reboot` , majd nyomja le bármelyik billentyűt. Ekkor meg kell jelennie a GRUB panelnek. Ha nem, győződjön meg arról, hogy az alábbi sorok találhatók a GRUB-fájlban ( `/etc/default/grub` ):

**RHEL 8**

>[!NOTE]
> A Red Hat azt javasolja, hogy a kivágás használatával konfigurálja a kernel parancssori paramétereit a RHEL 8 +-ban. Jelenleg nem lehet frissíteni a grub-időtúllépést és a terminál paramétereit a piszkos használatával. Ha módosítani szeretné az összes rendszerindítási bejegyzés GRUB_CMDLINE_LINUX argumentumának frissítését, futtassa a parancsot `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . További részletek [itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)érhetők el.

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**A RHEL 7 esetében**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> A Red Hat dokumentációt is biztosít a mentési módba, a vészhelyzeti módba vagy a hibakeresési módba való rendszerindítás, valamint a gyökér jelszavának alaphelyzetbe állításához. Útmutatásért lásd: [terminál-menü szerkesztése a rendszerindítás során](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Rendszergazdai hozzáférés beállítása egyfelhasználós módban a RHEL-ben
A legfelső szintű felhasználó alapértelmezés szerint le van tiltva. A RHEL egyfelhasználós módja megköveteli, hogy a root felhasználó engedélyezzen. Ha engedélyezni szeretné az egyfelhasználós üzemmódot, kövesse az alábbi utasításokat:

1. Jelentkezzen be a Red Hat rendszerébe SSH-n keresztül.
1. Váltson a gyökérre.
1. Engedélyezze a legfelső szintű felhasználó jelszavát a következő módon:
    * Futtatás `passwd root` (erős legfelső szintű jelszó beállítása).
1. Győződjön meg arról, hogy a legfelső szintű felhasználó csak a ttyS0 keresztül tud bejelentkezni a következő módon: a. Futtassa a parancsot `edit /etc/ssh/sshd_config` , és győződjön meg arról, hogy a PermitRootLogIn beállítása `no` .
    b. Futtassa `edit /etc/securetty file` a parancsot, hogy csak a ttyS0 használatával engedélyezze a bejelentkezést.

Ha a rendszer egyfelhasználós módban indul, bejelentkezhet a legfelső szintű jelszóval.

Azt is megteheti, hogy a RHEL 7.4 + vagy 6.9 + esetében engedélyezi az egyfelhasználós üzemmódot a GRUB-kérésekben: [rendszerindítás egyfelhasználós módban](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Egyfelhasználós mód manuális megadása a RHEL
Ha a fenti utasítások alapján beállította a GRUB-és a gyökérszintű hozzáférést, az alábbi módon adhatja meg az egyfelhasználós módot:

1. A GRUB megadásához nyomja le az ESC billentyűt a virtuális gép újraindításakor.
1. A GRUB-ban nyomja meg az E gombot a rendszerindításhoz használni kívánt operációs rendszer szerkesztéséhez. Az operációs rendszer általában az első sorban jelenik meg.
1. A kernel vonalának megkeresése. Az Azure-ban a *linux16*-vel kezdődik.
1. Nyomja le a CTRL + E billentyűkombinációt a sor végére való ugráshoz.
1. A sor végén adja hozzá a *systemed. Unit = Rescue. Target*parancsot.

    Ez a művelet egy egyfelhasználós módba kerül. Ha vészhelyzeti módot szeretne használni, vegyen fel *systemed. Unit = Emergency. Target* értéket a sor végére (a *systemed. Unit = Rescue. Target*helyett).

1. Nyomja le a CTRL + X billentyűkombinációt az alkalmazott beállításokkal való kilépéshez és az újraindításhoz.

   Az egyfelhasználós mód megadása előtt meg kell adnia a rendszergazdai jelszót. Ez a jelszó az előző utasításokban létrehozott.

    ![Egy parancssori felületet bemutató animált kép. A felhasználó kiválaszt egy kiszolgálót, megkeresi a kernel vonalának végét, majd belép a megadott szövegbe.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Adja meg az egyfelhasználós üzemmódot anélkül, hogy a gyökérszintű fiók engedélyezve van a RHEL
Ha nem engedélyezte a legfelső szintű felhasználót a korábbi utasítások követésével, a következő lépésekkel továbbra is alaphelyzetbe állíthatja a legfelső szintű jelszót:

> [!NOTE]
> Ha a SELinux használja, a legfelső szintű jelszó alaphelyzetbe állításakor kövesse a [Red Hat dokumentációjában](https://aka.ms/rhel7grubterminal)leírt további lépéseket.

1. A GRUB megadásához nyomja le az ESC billentyűt a virtuális gép újraindításakor.

1. A GRUB-ban nyomja meg az E gombot a rendszerindításhoz használni kívánt operációs rendszer szerkesztéséhez. Az operációs rendszer általában az első sorban jelenik meg.
1. A kernel vonalának megkeresése. Az Azure-ban a *linux16*-vel kezdődik.
1. A sor végén adja hozzá a (z) *Rd. break* sort a sor végéhez. Hagyjon szóközt a kernel és a *Rd. break*között.

    Ez a művelet megszakítja a rendszerindítási folyamatot, mielőtt a rendszer átadja a vezérlőt a rendszernek `initramfs` `systemd` a [Red Hat dokumentációjában](https://aka.ms/rhel7rootpassword)leírtaknak megfelelően.
1. Nyomja le a CTRL + X billentyűkombinációt az alkalmazott beállításokkal való kilépéshez és az újraindításhoz.

   Az újraindítást követően a rendszer a csak olvasható fájlrendszerrel rendelkező vészhelyzeti módban van.

1. A rendszerhéjban írja be, `mount -o remount,rw /sysroot` hogy az olvasási/írási engedélyekkel csatlakoztassa újra a rendszerindító fájlrendszert.
1. Az egyfelhasználós üzemmódba történő rendszerindítás után írja be a `chroot /sysroot` kapcsolót a `sysroot` börtönbe.
1. Most már a root-on. A legfelső szintű jelszót a következő utasítások beírásával állíthatja alaphelyzetbe `passwd` , majd megadhatja az egyfelhasználós üzemmódot.
1. Ha elkészült, adja meg `reboot -f` az újraindítást.

![Egy parancssori felületet bemutató animált kép. A felhasználó kiválaszt egy kiszolgálót, megkeresi a kernel vonalának végét, és belép a megadott parancsokra.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Az előző utasításokon keresztül futtatva elkerülheti a vészhelyzeti rendszerhéjba, így olyan feladatokat is végrehajthat, mint például a Szerkesztés `fstab` . Azonban általában azt javasoljuk, hogy állítsa alaphelyzetbe a legfelső szintű jelszót, és használja azt az egyfelhasználós mód megadásához.

## <a name="access-for-centos"></a>CentOS-hozzáférés
Akárcsak Red Hat Enterprise Linux, a CentOS-ben az egyfelhasználós mód a GRUB és a root felhasználó engedélyezését igényli.

### <a name="grub-access-in-centos"></a>GRUB-hozzáférés a CentOS-ben
A CentOS a GRUB-ban engedélyezve van a dobozból. A GRUB megadásához a beírásával indítsa újra a virtuális gépet, `sudo reboot` majd nyomja le bármelyik billentyűt. Ez a művelet a GRUB panelt jeleníti meg.

### <a name="single-user-mode-in-centos"></a>Egyfelhasználós mód a CentOS-ben
Ha engedélyezni szeretné az egyfelhasználós üzemmódot a CentOS-ben, kövesse a RHEL korábbi utasításait.

## <a name="access-for-ubuntu"></a>Hozzáférés Ubuntu rendszerhez
Az Ubuntu-lemezképekhez nem szükséges a gyökér jelszava. Ha a rendszer egyfelhasználós módba indul, további hitelesítő adatok nélkül használhatja.

### <a name="grub-access-in-ubuntu"></a>GRUB-hozzáférés Ubuntuban
A GRUB eléréséhez nyomja le és tartsa lenyomva az ESC billentyűt a virtuális gép indításakor.

Alapértelmezés szerint az Ubuntu-lemezképek nem jelenítik meg automatikusan a GRUB panelt. A beállítást a következő módon módosíthatja:
1. A szövegszerkesztőben nyissa meg a */etc/default/grub.d/50-cloudimg-Settings.cfg* fájlt.

1. Módosítsa az `GRUB_TIMEOUT` értéket nullától eltérő értékre.
1. Egy szövegszerkesztőben nyissa meg a */etc/default/grub*.
1. Megjegyzés a `GRUB_HIDDEN_TIMEOUT=1` sorban.
1. Győződjön meg arról, hogy van egy `GRUB_TIMEOUT_STYLE=menu` sor.
1. Futtassa az `sudo update-grub` parancsot.

### <a name="single-user-mode-in-ubuntu"></a>Single-user mód az Ubuntuban
Ha az Ubuntu nem indítható el rendesen, akkor a rendszer automatikusan egy egyszeri felhasználói üzemmódba helyezi az adatvesztést. Az egyfelhasználós mód manuális megadásához tegye a következőket:

1. A GRUB-ban nyomja meg az E gombot a rendszerindítási bejegyzés (Ubuntu-bejegyzés) szerkesztéséhez.
1. Keresse meg a *Linux*rendszerű vonalat, és keresse meg a *ro*-t.
1. Adja *hozzá* a t-t a *ro*után, és győződjön meg arról, hogy van egy szóköz az *egyszer*és után.
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adja meg az egyfelhasználós üzemmódot.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Bash meghívása az Ubuntuban a GRUB használatával
Az előző utasítások elvégzése után előfordulhat, hogy a helyzet (például elfelejtett root password), ahol továbbra sem tud hozzáférni az egyfelhasználós üzemmódhoz az Ubuntu-beli virtuális gépen. Azt is megteheti, hogy a kernelt `/bin/bash` init néven futtatja, nem pedig a rendszer inicializálását. Ez a művelet egy bash-rendszerhéjt biztosít, és lehetővé teszi a rendszer karbantartását. Kövesse az alábbi utasításokat:

1. A GRUB-ban nyomja meg az E gombot a rendszerindítási bejegyzés (Ubuntu-bejegyzés) szerkesztéséhez.

1. Keresse meg a *Linux*rendszerű vonalat, és keresse meg a *ro*-t.
1. Cserélje le a *ro* -t az *RW init =/bin/bash*.

    Ez a művelet írható-olvashatóként csatlakoztatja a fájlrendszert, és `/bin/bash` az inicializálási folyamatként használja.
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához.

## <a name="access-for-coreos"></a>Hozzáférés a CoreOS
A CoreOS egyfelhasználós üzemmódjában engedélyezni kell a GRUB használatát.

### <a name="grub-access-in-coreos"></a>GRUB-hozzáférés a CoreOS-ben
A GRUB eléréséhez nyomja le bármelyik billentyűt a virtuális gép indításakor.

### <a name="single-user-mode-in-coreos"></a>Egyfelhasználós mód a CoreOS
Ha a CoreOS nem indítható el rendesen, a rendszer automatikusan egy egyszeri felhasználói üzemmódba helyezi az adatvesztést. Az egyfelhasználós mód manuális megadásához tegye a következőket:

1. A GRUB-ban nyomja meg az E gombot a rendszerindítási bejegyzés szerkesztéséhez.

1. Keresse meg a *Linux $* kezdetű sort. A sor két példányának kell lennie, amelyek mindegyike más, *Ha... Else* záradék.
1. Fűzze hozzá a *CoreOS. autologin = ttyS0* az egyes *Linux $* -sorok végéhez.
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adja meg az egyfelhasználós üzemmódot.

## <a name="access-for-suse-sles"></a>Hozzáférés a SUSE SLES
A SLES 12 SP3 és újabb rendszerképek a soros konzolon keresztül érhetik el a hozzáférést, ha a rendszerindítás vészhelyzeti módba kerül.

### <a name="grub-access-in-suse-sles"></a>GRUB-hozzáférés a SUSE SLES
A SLES-ben a GRUB-hozzáféréshez a YaST-n keresztüli bootloader-konfiguráció szükséges. A konfiguráció létrehozásához tegye a következőket:

1. Az SSH használatával jelentkezzen be a SLES virtuális gépre, majd futtassa a parancsot `sudo yast bootloader` . Nyomja le a TAB billentyűt, nyomja le az ENTER billentyűt, majd a nyílbillentyűk segítségével navigáljon a menüben.

1. Nyissa meg a **kernel paramétereit**, majd jelölje be a **soros konzol használata** jelölőnégyzetet.
1. Adja hozzá `serial --unit=0 --speed=9600 --parity=no` a **konzol** argumentumait.
1. A beállítások mentéséhez és a kilépéshez nyomja le az F10 billentyűt.
1. A GRUB megadásához indítsa újra a virtuális gépet, és nyomja le bármelyik billentyűt a rendszerindítási folyamat során a GRUB panel megjelenítésének megtartásához.

    A GRUB alapértelmezett időtúllépése **1s**. Ezt a beállítást úgy módosíthatja, hogy megváltoztatja a `GRUB_TIMEOUT` változót a */etc/default/grub* fájlban.

![A rendszertöltő konfigurációjának inicializálása](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Egy felhasználós mód a SUSE SLES
Ha a SLES nem indítható el rendesen, a rendszer automatikusan elveti a vészhelyzeti rendszerhéjba. A vészüzemi rendszerhéj manuális megadásához tegye a következőket:

1. A GRUB-ban nyomja meg az E gombot a rendszerindítási bejegyzés (a SLES-bejegyzés) szerkesztéséhez.

1. Keresse meg a *Linux*rendszerű kernel-sort.
1. Fűzze hozzá a *systemed. Unit = Emergency. Target* címet a kernel vonalának végéhez.
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adja meg a vészhelyzeti rendszerhéjt.

   > [!NOTE]
   > Ez a művelet elviszi a vészhelyzeti rendszerhéjba egy írásvédett fájlrendszerrel. A fájlok szerkesztéséhez csatlakoztassa újra a fájlrendszert írási és olvasási jogosultságokkal. Ehhez írja be a felületet `mount -o remount,rw /` a rendszerhéjba.

## <a name="access-for-oracle-linux"></a>Hozzáférés Oracle Linux
A Red Hat Enterprise Linuxhoz hasonlóan a Oracle Linux egyfelhasználós módban a GRUB és a root felhasználó engedélyezése szükséges.

### <a name="grub-access-in-oracle-linux"></a>GRUB-hozzáférés Oracle Linux
A Oracle Linux a GRUB engedélyezve van a dobozból. A GRUB megadásához futtassa újra a virtuális gépet `sudo reboot` , majd nyomja le az ESC billentyűt. Ez a művelet a GRUB panelt jeleníti meg. Ha a GRUB panel nem jelenik meg, győződjön meg arról, hogy a `GRUB_TERMINAL` sor értéke *soros konzolt* tartalmaz (azaz `GRUB_TERMINAL="serial console"` ). Hozza létre újra a GRUB-t `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Egyfelhasználós mód a Oracle Linuxban
Az egyfelhasználós üzemmód Oracle Linuxban való engedélyezéséhez kövesse a RHEL korábbi utasításait.

## <a name="next-steps"></a>Következő lépések
A soros konzolról további információt a következő témakörben talál:
* [A Linux soros konzol dokumentációja](serial-console-linux.md)
* [A GRUB engedélyezése a soros konzol használatával különböző disztribúciókban](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Soros konzol használata NMI-és SysRq-hívásokhoz](serial-console-nmi-sysrq.md)
* [Soros konzol Windows rendszerű virtuális gépekhez](serial-console-windows.md)
* [Rendszerindítási diagnosztika](boot-diagnostics.md)
