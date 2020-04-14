---
title: Az Azure Serial Console proaktív GRUB-konfigurációja| Microsoft dokumentumok
description: Konfigurálja a GRUB-ot a különböző disztribúciók között, amely lehetővé teszi az egyfelhasználós és helyreállítási módelérését az Azure virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262893"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>A GRUB és a sysrq elérésének proaktív biztosítása sok időt takaríthat meg

A soros konzolhoz és a GRUB-hoz való hozzáférés a legtöbb esetben javítja az IaaS Linux virtuális gép helyreállítási idejét. A GRUB helyreállítási lehetőségeket kínál, amelyek egyébként hosszabb időt vesznek igénybe a virtuális gép helyreállításához. 


A virtuális gép-helyreállítás végrehajtásának okai számosak, és az alábbi forgatókönyvekhez vezethetők vissza:

   - Sérült fájlrendszerek/kernel/MBR (fő rendszertöltő rekord)
   - Sikertelen kernelfrissítések
   - Helytelen GRUB kernel paraméterek
   - Helytelen fstab konfigurációk
   - Tűzfal-konfigurációk
   - Elveszett jelszó
   - Csonka sshd konfigurációk fájlok
   - Hálózati konfigurációk

 Sok más forgatókönyvek [részletesitt](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Ellenőrizze, hogy az Azure-ban üzembe helyezett virtuális gépeken hozzáférhet-e a GRUB és a soros konzol hoz. 

Ha még nem új a Soros konzol, olvassa el [ezt a linket](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> A fájlok biztonsági mentése a módosítások végrehajtása előtt

Nézze meg ezt a videót az alábbi, hogy hogyan lehet gyorsan visszaállítani a Linux VM, ha már hozzáférhet a GRUB

[Linux VM videó helyreállítása](https://youtu.be/KevOc3d_SG4)

Számos módszer segíthet a Linux virtuális gépek helyreállításában. Felhőalapú környezetben ez a folyamat kihívást jelent.
Folyamatosan haladunk a szerszámozás és a szolgáltatások gyors helyreszolgáltatása érdekében.

Az Azure Serial Console-nal úgy kezelheti a Linux virtuális gépét, mintha egy rendszerkonzolján lenne.

Számos konfigurációs fájlt módosíthat, beleértve a rendszermag indításának módját is. 

A tapasztaltabb Linux/Unix rendszergazdák értékelni fogják az **egyfelhasználós** és **vészhelyzeti módok,** amelyek az Azure Serial Console-on keresztül érhetők el, így a lemezcsere és a virtuális gép törlése számos helyreállítási forgatókönyvek felesleges.

A helyreállítási módszer a tapasztalt problémától függ, például egy elveszett vagy tévesen elhelyezett jelszó alaphelyzetbe állítható az Azure Portal -> **Jelszó alaphelyzetbe állítása .** A **Jelszó alaphelyzetbe állítása** szolgáltatás bővítményként ismert, és kommunikál a Linux vendégügynökkel.

Más bővítmények, például az egyéni parancsfájlok azonban ezek a beállítások megkövetelik, hogy a Linux **waagent** legyen, és kifogástalan állapotban, amely nem mindig van így.

![ügynök állapota](./media/virtual-machines-serial-console/agent-status.png)


Az Azure Serial Console és a GRUB elérésének biztosítása azt jelenti, hogy a jelszómódosítása vagy a helytelen konfiguráció órák helyett percek alatt kijavítható. Még arra is kényszerítheti a virtuális gép, hogy egy másik kernel, ha több kernel van a lemezen abban a forgatókönyvben, ahol az elsődleges kernel megsérül.

![több kernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>A helyreállítási módszerek javasolt sorrendje:

- Azure soros konzol

- Lemezcsere – automatizálható a következő ekkel:

   - [Power Shell helyreállítási parancsfájlok](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash helyreállítási parancsfájlok](https://github.com/sribs/azure-support-scripts)

- Örökölt metódus

## <a name="disk-swap-video"></a>Lemezcsere videó:

Ha nem férnek hozzá a GRUB nézni [ezt a](https://youtu.be/m5t0GZ5oGAc) videót, és nézze meg, hogyan lehet könnyen automatizálni a lemezcsere eljárás, hogy visszaszerezze a VM

## <a name="challenges"></a>Kihívások:

Nem minden Linux Azure-beli virtuális gép van alapértelmezés szerint a GRUB-hozzáféréshez konfigurálva, és nem is úgy vannak beállítva, hogy a sysrq parancsokkal megszakadjanak. Néhány régebbi distózis, például az SLES 11 nincs konfigurálva a bejelentkezési parancs megjelenítéséhez az Azure soros konzolon

Ebben a cikkben áttekintjük a különböző Linux disztribúciákat és dokumentumkonfigurációkat arról, hogyan lehet a GRUB-ot elérhetővé tenni.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>A Linux virtuális gép beállítása a SysRq-kulcsok elfogadásához
A sysrq kulcs alapértelmezés szerint engedélyezve van néhány újabb Linux disztribúción, míg másokon előfordulhat, hogy csak bizonyos SysRq függvények esetében fogad el értékeket.
A régebbi distros, lehet, hogy teljesen le van tiltva.

A SysRq funkció akkor hasznos, ha egy összeomlott vagy lefagyott virtuális gépet indít közvetlenül az Azure Serial Console-ból, ami szintén hasznos a GRUB menü eléréséhez, és a virtuális gép újraindítása egy másik portálablakból vagy ssh munkamenetből eldobhatja az aktuális konzolkapcsolatot, így lejáró GRUB időtúltöltések, amelyekhez a GRUB menü megjelenítéséhez szolgál.
A virtuális gépet úgy kell konfigurálni, hogy elfogadja a kernel paraméter 1-es értékét, amely lehetővé teszi a sysrq vagy a 128 összes funkcióját, ami lehetővé teszi az újraindítást/a kikapcsolást


[Sysrq videó engedélyezése](https://youtu.be/0doqFRrHz_Mc)


Ha a virtuális gépet úgy szeretné beállítani, hogy az Azure Portalon a SysRq-parancsokon keresztül újrainduljon, 1-es értéket kell beállítania a kernel paraméter kernel.sysrq értékéhez.

Ahhoz, hogy ez a konfiguráció megóvást adjon az újraindításnak, adjon hozzá egy bejegyzést a **sysctl.conf fájlhoz.**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

A kernel paraméter dinamikus konfigurálása

`sysctl -w kernel.sysrq=1`

Ha nem rendelkezik **root** hozzáféréssel, vagy a sudo megszakad, nem lehet beállítani a sysrq-ot egy parancsértelmezőből.

Ebben a forgatókönyvben engedélyezheti a sysrq-t az Azure Portal használatával. Ez a módszer akkor lehet hasznos, ha a **sudoers.d/waagent** fájl megszakadt vagy törölték.

Az Azure Portal Operations -> Run Command -> RunShellScript szolgáltatás használatával a waagent folyamat kifogástalan állapotú, majd beadhatja ezt a parancsot a sysrq engedélyezéséhez

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Ahogy itt ![látható: engedélyezze a sysrq2-t](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Miután elkészült, akkor próbálja meg elérni **a sysrq-ot,** és látnia kell, hogy az újraindítás lehetséges.

![sysrq3 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Válassza **az Újraindítás** és a **SysRq küldés** parancs lehetőséget

![sysrq4 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

A rendszernek naplóznia kell egy olyan visszaállítási üzenetet, mint ez a

![sysrq5 engedélyezése](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB konfiguráció

Alapértelmezés szerint képesnek kell lennie arra, hogy hozzáférjen a GRUB-hoz az **Esc** kulcs lenyomva tartásával a virtuális gép indításakor, ha a GRUB menü nem jelenik meg, kényszerítheti és megtarthatja a GRUB menüt az Azure Serial Console képernyőjén az alábbi lehetőségek egyikének használatával.

**1. lehetőség** - A GRUB megjelenítésének kényszerítése a képernyőn 

Frissítse a fájlt /etc/default/grub.d/50-cloudimg-settings.cfg tartani a GRUB menüt a képernyőn a megadott TIMEOUT.
Nem kell megütnie **az Esc-et,** mivel a GRUB azonnal megjelenik

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**2. lehetőség** - Lehetővé teszi az **Esc** megnyomását a rendszerindítás előtt

Hasonló viselkedést lehet tapasztalni azáltal, hogy módosítja a fájl / etc / default / grub, és tartsa be a 3 másodperces timeout, hogy elérje **Esc**


Hozzászólás ki ezt a két sort:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
és adja hozzá ezt a sort:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 lehetővé teszi a hozzáférést a soros konzol, de nem kínál a képességét, hogy kölcsönhatásba lépnek. **Bejelentkezés:** a kérdés nem látható


A 12.04-hez a **bejelentkezéshez:** prompt:
1. Hozzon létre egy /etc/init/ttyS0.conf nevű fájlt, amely a következő szöveget tartalmazza:

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

2. Kérje upstart kezdeni a getty     
    ```
    sudo start ttyS0
    ```
 
Az Ubuntu verziók soros konzoljának konfigurálásához szükséges beállítások [itt](https://help.ubuntu.com/community/SerialConsoleHowto) találhatók

## <a name="ubuntu-recovery-mode"></a>Ubuntu helyreállítási mód

További helyreállítási és tisztítási lehetőségek állnak rendelkezésre az Ubuntu számára a GRUB-on keresztül, de ezek a beállítások csak akkor érhetők el, ha a kernel paramétereket ennek megfelelően konfigurálja.
A kernelrendszer rendszerindítási paraméterének konfigurálása esetén a helyreállítási menü t az Azure Diagnosztika, és nem az Azure Soros konzolra kell küldeni.
Az Ubuntu helyreállítási menühöz az alábbi lépések végrehajtásával férhet hozzá:

A BOOT folyamat megszakítása és a GRUB menü elérése

Válassza az Ubuntu speciális beállításait, és nyomja meg az Enter billentyűt

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Válassza ki a vonal megjelenítését *(helyreállítási mód)* ne nyomja meg az Entert, hanem nyomja meg az "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Keresse meg a rendszermagot betöltő sort, és helyettesítse az utolsó **nomodeset** paramétert **a céllal console=ttyS0 néven.**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Nyomja le a **Ctrl-x billentyűt** a rendszermag elindításához és betöltéséhez.
Ha minden jól megy, látni fogja ezeket a további beállításokat, amelyek segíthetnek más helyreállítási lehetőségek elvégzésében

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB konfiguráció

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.\+ 7 4 GRUB konfiguráció
Az alapértelmezett /etc/default/grub konfiguráció ezeken a verziókon megfelelően van konfigurálva

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

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7\.2 és 7 3 GRUB konfiguráció
A módosítandó fájl /etc/default/grub – az alapértelmezett konfiguráció így néz ki:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Az /etc/default/grub következő sorainak módosítása

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

Is hozzá ezt a sort:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub most hasonlóan kell kinéznie ehhez a példához:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
A grub konfiguráció befejezése és frissítése

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Állítsa be a SysRq kernel paramétert:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

A GRUB és a SysRq konfigurálható egyetlen sorral a rendszerhéjban vagy a Run Paranccsal. A parancs futtatása előtt készítsen biztonsági másolatot a fájlokról:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat\.6 x GRUB konfiguráció
A módosítandó fájl a /boot/grub/grub.conf. Az `timeout` érték határozza meg, hogy mennyi ideig jelenjen meg a GRUB.

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


Az utolsó *vonalterminál –-timeout=5 soros konzol* tovább növeli a **GRUB** időtúltöltését azáltal, hogy 5 másodperces figyelmeztetést ad hozzá, és **a folytatáshoz nyomja meg bármelyik billentyűt.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

A GRUB menünek meg kell jelennie a képernyőn a beállított timeout=15 számára anélkül, hogy meg kellene nyomnia az Esc billentyűt. Győződjön meg arról, hogy kattintson a konzol a böngészőben, hogy aktív a menüben, és válassza ki a szükséges kernel

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Vagy használja yast bootloader, mint egy a hivatalos [dokumentumok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Vagy add/változás /etc/default/grub a következő paramétereket:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Ellenőrizze, hogy a ttys0-t használja-e a GRUB_CMDLINE_LINUX vagy GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Újra a grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Megjelenik a Soros konzol, és rendszerindító üzeneteket jelenít meg, de nem jelenít meg **bejelentkezési adatokat:** prompt

Nyisson meg egy ssh munkamenetet a virtuális gépbe, és frissítse a **fájlt /etc/inittab** a következő sor megjegyzésének megjegyzésével:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Következő futtassa a parancsot 

`telinit q`

A GRUB engedélyezéséhez a következő módosításokat kell végrehajtani a /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Ez a konfiguráció lehetővé teszi, hogy az üzenet Nyomja meg **bármelyik gombot, hogy továbbra is** megjelenjen a konzolon 5 másodpercig 

Ezután további 5 másodpercig megjeleníti a GRUB menüt - a lefelé mutató nyíl megnyomásával megszakítja a számlálót, és kiválasztja a rendszerindító rendszermagot, vagy hozzáfűzi a **kulcsszót az** egyfelhasználós módhoz, amely root jelszót igényel.

Az **init=/bin/bash** parancs hozzáfűzése betölti a rendszermagot, de biztosítja, hogy az init programot egy bash shell váltja fel.

Jelszó megadása nélkül férhet hozzá a rendszerhéjhoz. Ezután folytassa a Linux-fiókok jelszavának frissítését, vagy egyéb konfigurációs módosításokat hajtson végre.


## <a name="force-the-kernel-to-a-bash-prompt"></a>A rendszermag kényszerítése bash-üzenetre
A GRUB-hoz való hozzáférés lehetővé teszi az inicializálási folyamat megszakítását, ez az interakció számos helyreállítási eljárás esetén hasznos.
Ha nem rendelkezik root jelszóval, és egyetlen felhasználó megköveteli, hogy root jelszóval rendelkezik, akkor a kernel programot bash-üzenetre cserélheti - ez a megszakítás úgy érhető el, hogy az init=/bin/bash-t a kernel boot vonalához fűzi

![bash1](./media/virtual-machines-serial-console/bash1.png)

Remount a / (root) fájlrendszer RW a parancs

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Most root jelszómódosítást vagy sok más Linux konfigurációs módosítást hajthat végre

![bash3](./media/virtual-machines-serial-console/bash3.png)

A virtuális gép újraindítása 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Egyfelhasználós mód

Másik lehetőségként előfordulhat, hogy egyfelhasználós vagy vészhelyzeti módban kell elérnie a virtuális gép. Jelölje ki a indítani kívánt vagy megszakítani kívánt rendszermagot nyílbillentyűkkel.
Adja meg a kívánt módot úgy, hogy az **egy-** vagy **1-es** kulcsszót hozzáfűzi a kernel rendszerindító sorához. Rhel rendszereken az **rd.break**.

Az egyfelhasználós mód eléréséről további információt [ebben a dokumentumban talál.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>További lépések
További információ az [Azure Soros konzolról]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
