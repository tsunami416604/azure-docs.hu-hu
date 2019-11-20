---
title: Az Azure soros konzol proaktív GRUB-konfigurációja | Microsoft Docs
description: Állítsa be a GRUB-t különböző disztribúciók között, amelyek lehetővé teszik az egyszeri felhasználói és helyreállítási mód elérését az Azure Virtual Machines szolgáltatásban
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186928"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktív módon biztosíthatja, hogy hozzáférjen a GRUB-hoz, és a SYSRQ rengeteg időt takaríthat meg

A soros konzol és a GRUB hozzáférése a legtöbb esetben javítja a IaaS Linux rendszerű virtuális gépek helyreállítási idejét. A GRUB olyan helyreállítási lehetőségeket kínál, amelyek egyébként a virtuális gép helyreállításához hosszabb ideig tarthatnak. 


A virtuális gépek helyreállításának okai sok esetben a következők lehetnek:

   - Sérült fájlrendszer/kernel/MBR (fő rendszerindító rekord)
   - Sikertelen kernel-frissítések
   - Helytelen GRUB kernel-paraméterek
   - Helytelen fstab-konfigurációk
   - Tűzfal-konfigurációk
   - Elveszett jelszó
   - Összekeveredett sshd-konfigurációk fájljai
   - Hálózati konfigurációk

 Számos más forgatókönyv, amely részletesen [itt](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console) látható

Ellenőrizze, hogy elérhető-e a GRUB és a Serial console az Azure-ban üzembe helyezett virtuális gépeken. 

Ha a soros konzol új, tekintse meg [ezt a hivatkozást](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> A módosítások végrehajtása előtt készítsen biztonsági mentést a fájlokról

Tekintse meg az alábbi videót, amelyből megtudhatja, hogyan állíthatja be gyorsan a linuxos virtuális gépet a GRUB-hozzáférés után

[Linuxos VM-videó helyreállítása](https://youtu.be/KevOc3d_SG4)

A Linux rendszerű virtuális gépek helyreállítását számos módszer segíti. A Felhőbeli környezetekben ez a folyamat kihívást jelent.
A szolgáltatások gyors helyreállításának biztosítása érdekében a rendszer folyamatosan fejleszti a folyamatban lévő eszközöket és szolgáltatásokat.

Az Azure soros konzolon a Linux rendszerű virtuális gépekkel ugyanúgy dolgozhat, mint a rendszerkonzolon.

Számos konfigurációs fájlt is kezelhet, beleértve a kernel indításának módját is. 

A tapasztaltabb Linux-/UNIX-rendszergazdák értékelik az Azure soros konzolon keresztül elérhető **egyetlen felhasználói** és **vészhelyzeti üzemmódot** , így a lemez cseréje és a virtuális gép törlése sok redundáns helyreállítási forgatókönyv esetén.

A helyreállítás módszere az észlelt problémától függ, például az elveszett vagy a rossz helyre állított jelszó visszaállítható a Azure Portal beállítások-> **új jelszó**kérése parancs használatával. A **jelszó alaphelyzetbe állítása** szolgáltatás bővítményként ismert, és a Linux vendég ügynökkel kommunikál.

Más bővítmények, például az egyéni szkriptek is elérhetők, azonban a Linux **waagent** kifogástalan állapotban kell lennie, ami nem mindig így van.

![ügynök állapota](./media/virtual-machines-serial-console/agent-status.png)


Az Azure soros konzol és a GRUB elérésének biztosítása azt jelenti, hogy a jelszó módosítása vagy a helytelen konfiguráció az óra helyett percek alatt orvosolható. Azt is megteheti, hogy a virtuális gépet egy másik kernelről történő rendszerindításra kényszeríti, ha az elsődleges kernel sérülése esetén a lemezen több kernel található.

![több kernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>A helyreállítási módszerek javasolt sorrendje:

- Azure soroz konzol

- Disk swap – automatizálható a következők bármelyikével:

   - [Rendszerhéj-helyreállítási parancsfájlok](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash helyreállítási parancsfájlok](https://github.com/sribs/azure-support-scripts)

- Örökölt metódus

## <a name="disk-swap-video"></a>Disk swap-videó:

Ha nem rendelkezik hozzáféréssel a GRUB-hoz, tekintse meg [ezt](https://youtu.be/m5t0GZ5oGAc) a videót, és nézze meg, hogyan automatizálhatja a lemezes swap-eljárást a virtuális gép helyreállításához

## <a name="challenges"></a>Kihívások

Nem minden Linux Azure-beli virtuális gép van alapértelmezés szerint beállítva a GRUB-hozzáféréshez, és egyik sincs konfigurálva sem a SYSRQ parancsainak megszakadására. Egyes régebbi disztribúciók, például a SLES 11 nem úgy vannak konfigurálva, hogy megjelenjenek a bejelentkezési kérések az Azure soros konzolon

Ebben a cikkben áttekintjük a különböző Linux-disztribúciókat és dokumentum-konfigurációkat a GRUB elérhetővé tételéhez.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Linux rendszerű virtuális gép konfigurálása SysRq-kulcsok fogadására
A SYSRQ kulcs alapértelmezés szerint engedélyezve van néhány újabb Linux-disztribúción, bár másokon úgy is konfigurálható, hogy csak bizonyos SysRq függvények esetén fogadjon értékeket.
A régebbi disztribúciókban lehet, hogy teljesen le van tiltva.

A SysRq funkció hasznos lehet egy összeomlott vagy lefagyott virtuális gép közvetlenül az Azure soros konzolról történő újraindításához, és a GRUB menühöz való hozzáféréshez is hasznos, ha a virtuális gépet egy másik portál ablakból vagy SSH-munkamenetből is eldobják, akkor az aktuális konzol kapcsolata is csökkenhet. így lejáró GRUB-időtúllépések, amelyek a GRUB menü megjelenítéséhez használatosak.
A virtuális gépet úgy kell konfigurálni, hogy fogadja az 1 értéket a kernel paraméter számára, amely lehetővé teszi a SYSRQ vagy a 128 összes funkcióját, amely lehetővé teszi az újraindítás/erő használatát


[SYSRQ-videó engedélyezése](https://youtu.be/0doqFRrHz_Mc)


Ha úgy szeretné konfigurálni a virtuális gépet, hogy SysRq-parancsokkal fogadja el az újraindítást a Azure Portalon, akkor az 1 értéket kell megadnia a kernel-paraméterhez. SYSRQ

Ahhoz, hogy ez a konfiguráció újraindítást is megmaradjon, adjon hozzá egy bejegyzést a **sysctl. conf** fájlhoz.

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

A kernel paraméter dinamikus konfigurálása

`sysctl -w kernel.sysrq=1`

Ha nem rendelkezik **rendszergazdai** hozzáféréssel, vagy a sudo megszakadt, a SYSRQ nem konfigurálható a rendszerhéj parancssorból.

Ebben a forgatókönyvben a Azure Portal használatával engedélyezheti a SYSRQ. Ez a módszer akkor lehet hasznos, ha a **sudoers. d/waagent** fájl megszakadt vagy törölve lett.

A Azure Portal Operations-> Run Command-> RunShellScript funkció használata esetén a waagent folyamatnak kifogástalan állapotban kell lennie, hogy ezt a parancsot a SYSRQ engedélyezéséhez adja.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Az itt látható módon: ![sysrq2 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Ha elkészült, próbálja meg elérni a **SYSRQ** , és látnia kell, hogy újraindításra van lehetőség.

![sysrq3 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Az **Újraindítás** és a **SYSRQ küldése** parancs kiválasztása

![sysrq4 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

A rendszernek be kell jelentkeznie egy visszaállítási üzenetet, például a következőt

![sysrq5 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB-konfiguráció

Alapértelmezés szerint a virtuális gép indításakor le kell tudnia érni a GRUB-t az **ESC** billentyű lenyomásával, ha a grub menü nem jelenik meg, az alábbi lehetőségek egyikének használatával kényszerítheti és megtarthatja a grub menüt az Azure soros konzolján.

**1. lehetőség** – a grub megjelenítésének kényszerítése a képernyőn 

Frissítse a fájl/etc/default/grub.d/50-cloudimg-Settings.cfg, hogy megtartsa a GRUB menüjét a megadott időkorláton.
Nem kell megnyomnia az **ESC billentyűt** , mivel a grub azonnal megjelenik

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**2. lehetőség** – lehetővé teszi az **ESC** lenyomását a rendszerindítás előtt

Hasonló viselkedés tapasztalható a fájl/etc/default/grub módosításával és az **ESC** -re vonatkozó 3 másodperces időkorlát betartásával


Tegye megjegyzésbe ezt a két sort:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
és adja hozzá a következő sort:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Az Ubuntu 12,04 lehetővé teszi a soros konzol elérését, de nem teszi lehetővé a kommunikációt. Egy **Bejelentkezés:** a kérdés nem látható


A 12,04 beszerzéséhez **Jelentkezzen be:** prompt:
1. Hozzon létre egy/etc/init/ttyS0.conf nevű fájlt, amely a következő szöveget tartalmazza:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. A getty indításának megkezdése     
    ```
    sudo start ttyS0
    ```
 
A soros konzol Ubuntu-verziókhoz való konfigurálásához szükséges beállítások [itt](https://help.ubuntu.com/community/SerialConsoleHowto) találhatók

## <a name="ubuntu-recovery-mode"></a>Ubuntu helyreállítási mód

A GRUB-on keresztül további helyreállítási és tisztítási lehetőségek érhetők el, azonban ezek a beállítások csak akkor érhetők el, ha ennek megfelelően konfigurálja a kernel paramétereit.
Ha nem konfigurálja ezt a kernel rendszerindítási paramétert, a rendszer a helyreállítási menüt a Azure Diagnosticsba küldi, és nem az Azure soros konzolra.
A következő lépésekkel szerezheti be a hozzáférést az Ubuntu Recovery menüjéhez:

A rendszerindítási folyamat megszakítása és a GRUB-menü elérése

Válassza az Ubuntu speciális beállításai lehetőséget, majd nyomja le az ENTER billentyűt

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Válassza ki a megjelenített sort *(helyreállítási mód)* , ne nyomja le az ENTER billentyűt, de nyomja meg az "e" gombot.

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Keresse meg azt a sort, amely betölti a kernelt, és az utolsó paramétert helyettesíti a **nomodeset** a (z) **Console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Nyomja le a **CTRL-x** billentyűkombinációt a kernel elindításához és betöltéséhez.
Ha minden jól megy, ezeket a további beállításokat fogja látni, amelyek más helyreállítási lehetőségeket is elvégezhetnek.

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB-konfiguráció

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB-konfiguráció
Ezen verziók alapértelmezett/etc/default/grub-konfigurációja megfelelően van konfigurálva

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

A SysRq kulcs engedélyezése

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 és 7\.GRUB-konfiguráció
A módosítandó fájl/etc/default/grub – az alapértelmezett konfiguráció a következő példához hasonlóan néz ki:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

A következő sorok módosítása a/etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Adja hozzá a következő sort is:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

a/etc/default/grub az alábbi példához hasonlóan kell kinéznie:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
A grub-konfiguráció befejezése és frissítése a

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Állítsa be a SysRq kernel paramétert:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Alternatív megoldásként a GRUB és a SysRq a shellben vagy a Run parancson keresztül is konfigurálható. A parancs futtatása előtt készítsen biztonsági másolatot a fájlokról:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB-konfiguráció
A módosítandó fájl/boot/grub/grub.conf. A `timeout` érték határozza meg, hogy mennyi ideig jelenjen meg a GRUB.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Az utolsó sor *terminálja – a timeout = 5 soros konzol* tovább növeli a **grub** -időtúllépést úgy, hogy 5 másodperces figyelmeztetést jelenít **meg, és a folytatáshoz nyomjon le egy billentyűt.**

![RH6 – 1](./media/virtual-machines-serial-console/rh6-1.png)

Az ESC billentyű lenyomása nélkül a GRUB menünek meg kell jelennie a képernyőn a beállított időkorláthoz = 15. Győződjön meg arról, hogy a böngészőben a konzolon az aktív menüre kattint, és kiválasztja a szükséges kernelt

![RH6 – 2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Használja a YaST bootloadert a hivatalos [dokumentumok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) szerint

Vagy a Hozzáadás/módosítás/etc/default/grub a következő paraméterekkel:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Ellenőrizze, hogy a ttyS0 használatban van-e a GRUB_CMDLINE_LINUX vagy GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Hozza létre újra a grub. cfg fájlt

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Megjelenik a soros konzol, és megjeleníti a rendszerindító üzeneteket, de nem jeleníti meg a **bejelentkezést:** prompt

Nyisson meg egy SSH-munkamenetet a virtuális gépen, és frissítse a fájlt a **/etc/inittab** :

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Ezután futtassa a parancsot 

`telinit q`

A GRUB engedélyezéséhez a következő módosításokat kell elvégezni a/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Ez a konfiguráció lehetővé teszi, **hogy az üzenet nyomjon le egy billentyűt, hogy továbbra is** megjelenjen a konzolon 5 másodpercig 

Ezután megjeleníti a GRUB menüt egy további 5 másodpercig – a lefelé mutató nyílra kattintva megszakítja a számlálót, és kiválaszthatja a rendszerindításhoz használni kívánt kernelt **, ha** az egyfelhasználós üzemmódhoz hozzáfűzi a kulcsszót, amelyhez rendszergazdai jelszót kell beállítani.

Az **init =/bin/bash** parancs hozzáfűzésével betölti a kernelt, de gondoskodik arról, hogy az init-programot egy bash-rendszerhéj váltja fel.

A rendszerhéjhoz jelszó megadása nélkül férhet hozzá. Ezután a Linux-fiókok jelszavait frissítheti, vagy más konfigurációs módosításokat is végrehajthat.


## <a name="force-the-kernel-to-a-bash-prompt"></a>A rendszermag kényszerítése bash-parancssorba
A GRUB-hozzáférés lehetővé teszi, hogy megszakítsa az inicializálási folyamatot, amely számos helyreállítási eljárás esetében hasznos lehet.
Ha nem rendelkezik rendszergazdai jelszóval, és az egyetlen felhasználóhoz rendszergazdai jelszó szükséges, akkor a rendszerindítási programot egy bash-parancssorral is elindíthatja az init program helyett – ez a megszakítás az init =/bin/bash és a kernel rendszerindítási vonalának hozzáfűzésével érhető el.

![bash1](./media/virtual-machines-serial-console/bash1.png)

Csatlakoztassa újra a/(root) fájlrendszerbeli RW-t a parancs használatával

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Most már elvégezheti a rendszergazdai jelszó módosítását vagy a Linux-konfiguráció számos más módosítását

![bash3](./media/virtual-machines-serial-console/bash3.png)

Indítsa újra a virtuális gépet a 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Egyfelhasználós mód

Előfordulhat, hogy egy felhasználói vagy vészhelyzeti módban is hozzá kell férnie a virtuális géphez. Válassza ki a indítani vagy megszakítani kívánt kernelt a nyílbillentyűk használatával.
Adja meg a kívánt módot úgy, hogy hozzáfűzi az **egy** vagy **1** kulcsszót a kernel rendszerindítási sorához. A RHEL rendszereken a **Rd. break**utótagot is használhatja.

További információ az egyfelhasználós mód eléréséről: Ez a [dokumentum](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Következő lépések
További információ az [Azure soros konzolról]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)