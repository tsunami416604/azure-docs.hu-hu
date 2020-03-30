---
title: Azure soros konzol GRUB-hoz és egyfelhasználós módhoz
description: A Serial Console használata az Azure virtuális gépeiben való grub hoz.
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 87f16ec615c8b47c93745b33be12d3acd6d9177a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035043"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Serial Console használata a GRUB és az egyfelhasználós mód eléréséhez
GRUB a GRand Unified Bootloader. A GRUB-tól módosíthatja a rendszerindítási konfigurációt, hogy egyfelhasználós módba induljon, többek között.

Az egyfelhasználós mód minimális környezet, minimális funkcionalitással. Hasznos lehet a rendszerindítási, fájlrendszeri vagy hálózati problémák kivizsgálásához. Kevesebb szolgáltatás futhat a háttérben, és a futtatási szinttől függően előfordulhat, hogy a fájlrendszer nem is lesz automatikusan csatlakoztatva.

Az egyfelhasználós mód olyan helyzetekben is hasznos, amikor a virtuális gép csak úgy konfigurálható, hogy elfogadja az SSH-kulcsokat a bejelentkezéshez. Ebben az esetben előfordulhat, hogy egyetlen felhasználó módban is létrehozhat egy fiókot jelszó-hitelesítéssel.

Egyetlen felhasználói módba való belépéshez meg kell adnia a GRUB-ot, amikor a virtuális gép elindul, és módosítania kell a rendszerindítási konfigurációt a GRUB-ban. Ez a virtuális gép soros konzoljával is elvégezhető.

## <a name="general-grub-access"></a>Általános GRUB hozzáférés
A GRUB eléréséhez újra kell indítania a virtuális gépet, miközben nyitva tartja a soros konzolpanelt. Egyes distros lesz szükség billentyűzet bemenet-hoz mutat GRUB, míg mások automatikusan megmutatja GRUB néhány másodpercig, és lehetővé teszi a felhasználó billentyűzet bemenet, hogy megszünteti az időtúltöltés. 

Győződjön meg arról, hogy a GRUB engedélyezve van a virtuális gépen, hogy elérhesse az egyfelhasználós módot. A ttól függően, hogy a distro, lehet, hogy néhány beállítási munka annak biztosítására, hogy a GRUB engedélyezve van. A distro-specifikus információk az alábbiakban olvashatók.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>A virtuális gép újraindítása a GRUB eléréséhez a soros konzolon
A virtuális gép újraindítása a soros konzol panel nyitott lehet `'b'` tenni a SysRq parancsot, ha [SysRq](./serial-console-nmi-sysrq.md) engedélyezve van, vagy kattintson az Újraindítás gombra az áttekintés panelen (nyissa meg a virtuális gép egy új böngésző lapon újraindítás nélkül bezárása nélkül a soros konzol panel). Kövesse az alábbi, distro-specifikus utasításokat, hogy megtudja, mire számíthat a GRUB-tól, amikor újraindítja.

## <a name="general-single-user-mode-access"></a>Általános egyfelhasználós hozzáférés
Az egyfelhasználós módhoz való manuális hozzáférésre lehet szükség olyan helyzetekben, amikor még nem konfigurált jelszó-hitelesítéssel rendelkező fiókot. Módosítania kell a GRUB konfigurációt, hogy manuálisan adja meg az egyfelhasználós módot. Miután ezt megtette, további utasításokért olvassa el az Egyfelhasználós mód használata alaphelyzetbe állításhoz vagy jelszó hozzáadásához című témakört.

Azokban az esetekben, ahol a virtuális gép nem tudja elindítani, a distros gyakran automatikusan egyfelhasználós vagy vészhelyzeti módba dob. Mások azonban további beállításokat igényelnek, mielőtt automatikusan egyfelhasználós vagy vészhelyzeti módba kapcsolhatnának (például gyökérjelszó beállítása).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Jelszó alaphelyzetbe állítása vagy hozzáadása egyfelhasználós móddal
Ha egyfelhasználós módban van, tegye a következőket a sudo jogosultságokkal rendelkező új felhasználó hozzáadásához:
1. Futtatás `useradd <username>` felhasználó hozzáadásához
1. Futtatás `sudo usermod -a -G sudo <username>` az új felhasználói gyökérjogosultságok megadásához
1. Itt `passwd <username>` állíthatja be az új felhasználó jelszavát. Ezután új felhasználóként jelentkezhet be

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Hozzáférés a Red Hat Enterprise Linux (RHEL)
RHEL akarat csepp ön -ba egyes felhasználó mód gépiesen ha ez nem tud csomagtartó szabályosan. Ha azonban nem állított be root hozzáférést az egyfelhasználós módhoz, akkor nem lesz gyökérjelszó, és nem tud bejelentkezni. Van egy kerülő megoldás (lásd alább a "Manuális belépés egyfelhasználós módba" című részt), de a javaslat kezdetben a gyökérhozzáférés beállítása.

### <a name="grub-access-in-rhel"></a>GRUB hozzáférés az RHEL-ben
RHEL jön grub engedélyezve a dobozból. A GRUB-ba való belépéshez indítsa újra a virtuális gépet, `sudo reboot` és nyomja meg bármelyik billentyűt. Látni fogja, hogy megjelenik a GRUB képernyő.

> Megjegyzés: A Red Hat dokumentációt is biztosít a mentési módba, a vészhelyzeti módba, a debug módba történő indításhoz és a gyökérjelszó alaphelyzetbe állításához. [Kattintson ide a eléréséhez](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Root hozzáférés beállítása egyfelhasználós módhoz RHEL-ben
Az RHEL egyfelhasználós üzemmódjában engedélyezni kell a gyökérfelhasználót, amely alapértelmezés szerint le van tiltva. Ha egyfelhasználós mód engedélyezésére van szüksége, kövesse az alábbi utasításokat:

1. Jelentkezzen be a Red Hat rendszerbe SSH-n keresztül
1. Váltás gyökérre
1. Jelszó engedélyezése a legfelső szintű felhasználó számára 
    * `passwd root`(erős gyökérjelszót állít)
1. Annak biztosítása, hogy a root felhasználó csak a ttyS0-on keresztül tud bejelentkezni
    * `edit /etc/ssh/sshd_config`és győződjön meg arról, hogy az PermitRootLogIn nincs
    * `edit /etc/securetty file`hogy csak a ttyS0-n keresztül engedélyezze a bejelentkezést 

Most, ha a rendszer elindul egyfelhasználós módban lehet bejelentkezni keresztül root jelszót.

Alternatív megoldásként RHEL 7.4 + vagy 6.9 + engedélyezheti az egyfelhasználós módban a GRUB kéri, lásd az utasításokat [itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Egyfelhasználós mód manuális megadása RHEL-ben
Ha a grub-ot és a root hozzáférést a fenti utasításokkal állította be, akkor a következő utasításokkal léphet be egyfelhasználós módba:

1. A VIRTUÁLIS GÉP újraindítása közben nyomja meg az "Esc" gombot a GRUB
1. A GRUB, nyomja meg az "e" szerkesztéséhez a kiválasztott operációs rendszer kívánt indítani a (általában az első sorban)
1. Keresse meg a kernel vonal - az Azure-ban, ez kezdődik`linux16`
1. Nyomja le a Ctrl + E billentyűkombinációt a sor végére
1. A sor végére a következőket kell hozzáadni:`systemd.unit=rescue.target`
    * Ez elindítja Önt egyfelhasználós módba. Ha vészhelyzeti üzemmódot szeretne `systemd.unit=emergency.target` használni, a sor végére`systemd.unit=rescue.target`
1. Nyomja le a Ctrl + X billentyűkombinációt a kilépéshez és az újraindításhoz az alkalmazott beállításokkal
1. A rendszer kérni fogja a rendszergazdai jelszót, mielőtt egyetlen felhasználó módba léphetne - ez ugyanaz a jelszó, amelyet a fenti utasításokban hozott létre    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Egyetlen felhasználói mód megadása gyökérfiók engedélyezése nélkül az RHEL-ben
Ha nem ment keresztül a fenti lépéseket, hogy a root felhasználó, akkor is állítsa vissza a root jelszót. Kövesse a következő utasításokat:

> Megjegyzés: Ha SELinuxot használ, kérjük, győződjön meg róla, hogy megtette a Red Hat [dokumentációjában](https://aka.ms/rhel7grubterminal) leírt további lépéseket a gyökérjelszó visszaállításakor.

1. A VIRTUÁLIS GÉP újraindítása közben nyomja meg az "Esc" gombot a GRUB
1. A GRUB, nyomja meg az "e" szerkesztéséhez a kiválasztott operációs rendszer kívánt indítani a (általában az első sorban)
1. Keresse meg a kernel vonal - az Azure-ban, ez kezdődik`linux16`
1. Add `rd.break` a sor végére, biztosítva, `rd.break` hogy van szóköz előtte (lásd az alábbi példát)
    - Ez megszakítja a rendszerindítási `initramfs` folyamatot, mielőtt az ellenőrzés átkerül a rendszerbe, `systemd`ahogy azt a Red Hat [dokumentációitt](https://aka.ms/rhel7rootpassword)leírja.
1. Nyomja le a Ctrl + X billentyűkombinációt a kilépéshez és az újraindításhoz az alkalmazott beállításokkal
1. A rendszerindítás után azonnal vészmódba kerül egy csak olvasható fájlrendszerrel. Írja `mount -o remount,rw /sysroot` be a rendszerhéjba a gyökérfájlrendszer olvasási/írási engedélyekkel való újracsatlakoztatásához
1. Miután elindítja az egyfelhasználós módot, írja be `chroot /sysroot` a váltáshoz a `sysroot` börtönbe
1. Most már gyökér vagy. Visszaállíthatja a gyökérjelszót, `passwd` majd a fenti utasításokat használva egyetlen felhasználó módba léphet. Írja `reboot -f` be az újraindítást, ha végzett.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Megjegyzés: A fenti utasítások ontja a vészhéjat, így olyan `fstab`feladatokat is végrehajthat, mint a szerkesztés . Azonban az általánosan elfogadott javaslat az, hogy állítsa vissza a root jelszót, és használja, hogy adja meg az egyfelhasználós módban. 


## <a name="access-for-centos"></a>Hozzáférés a CentOS-hoz
Hasonlóan a Red Hat Enterprise Linuxhoz, a CentOS egyfelhasználós módjához hasonlóan a GRUB és a root felhasználó is engedélyezve van. 

### <a name="grub-access-in-centos"></a>GRUB hozzáférés a CentOS-ban
CentOS jön grub engedélyezve a dobozból. A GRUB-ba való belépéshez indítsa újra a virtuális gépet, `sudo reboot` és nyomja meg bármelyik billentyűt. Látni fogja, hogy megjelenik a GRUB képernyő.

### <a name="single-user-mode-in-centos"></a>Egyfelhasználós mód a CentOS rendszerben
A CentOS egyfelhasználós módjának engedélyezéséhez kövesse a fenti RHEL-re vonatkozó utasításokat.

## <a name="access-for-ubuntu"></a>Hozzáférés az Ubuntuhoz 
Az Ubuntu képek nem igényelnek root jelszót. Ha a rendszer egyfelhasználós módba vált, további hitelesítő adatok nélkül is használhatja. 

### <a name="grub-access-in-ubuntu"></a>GRUB hozzáférés az Ubuntuban
A GRUB eléréséhez nyomja meg és tartsa lenyomva az "Esc" gombot, miközben a virtuális gép elindul. 

Alapértelmezés szerint az Ubuntu képek nem jelenítik meg automatikusan a GRUB képernyőt. Ez a következő utasításokkal módosítható:
1. Megnyitás `/etc/default/grub.d/50-cloudimg-settings.cfg` az Ön által választott szövegszerkesztőben
1. Az `GRUB_TIMEOUT` érték módosítása nem nulla értékre
1. Megnyitás `/etc/default/grub` az Ön által választott szövegszerkesztőben
1. Megjegyzés a `GRUB_HIDDEN_TIMEOUT=1` sor kihozzászólása
1. Futtassa a `sudo update-grub` parancsot.

### <a name="single-user-mode-in-ubuntu"></a>Egyfelhasználós mód az Ubuntuban
Az Ubuntu automatikusan egyfelhasználós módba vált, ha nem tud normálisan elindulni. Ha manuálisan szeretne egyfelhasználós módba lépni, kövesse az alábbi utasításokat:

1. A GRUB,nyomja meg az "e" szerkesztéséhez a boot bejegyzést (az Ubuntu bejegyzést)
1. Keresse meg a `linux`sort, hogy kezdődik, majd keresse meg`ro`
1. Add `single` `ro`után, biztosítva, hogy van egy hely előtt és után`single`
1. Nyomja le a Ctrl + X billentyűkombinációt az újraindításhoz ezekkel a beállításokkal, és lépjen be egyfelhasználós módba

## <a name="access-for-coreos"></a>Hozzáférés a CoreOS-hoz
A CoreOS egyfelhasználós üzemmódjában engedélyezni kell a GRUB-ot. 

### <a name="grub-access-in-coreos"></a>GRUB hozzáférés a CoreOS-ban
A GRUB eléréséhez nyomja meg bármelyik billentyűt, amikor a virtuális gép elindul.

### <a name="single-user-mode-in-coreos"></a>Egyfelhasználós mód a CoreOS-ban
A CoreOS automatikusan egyfelhasználós módba vált, ha nem tud normálisan elindulni. Ha manuálisan szeretne egyfelhasználós módba lépni, kövesse az alábbi utasításokat:
1. A GRUB-ból nyomja meg az "e" gombot a rendszerindító bejegyzés szerkesztéséhez
1. Keresse meg a . `linux$` 2-nek kell lennie, különböző if/else záradékokba ágyazva
1. Hozzáfűzés `coreos.autologin=ttyS0` mindkét `linux$` sor végéhez
1. Nyomja le a Ctrl + X billentyűkombinációt az újraindításhoz ezekkel a beállításokkal, és lépjen be egyfelhasználós módba

## <a name="access-for-suse-sles"></a>Hozzáférés a SUSE SLES-hez
Az SLES 12 SP3+ újabb képei hozzáférést biztosítanak a soros konzolon keresztül, ha a rendszer vészhelyzeti üzemmódba kapcsol. 

### <a name="grub-access-in-suse-sles"></a>GRUB hozzáférés a SUSE SLES-ben
Grub hozzáférés SLES igényel bootloader konfiguráció keresztül YaST. Ehhez kövesse az alábbi utasításokat:

1. ssh az SLES VM `sudo yast bootloader`és fuss . A `tab` menüben `enter` való navigáláshoz használja a billentyűt, a billentyűt és a nyílbillentyűket. 
1. Keresse `Kernel Parameters`meg a `Use serial console`t, és ellenőrizze a. 
1. Hozzáadás `serial --unit=0 --speed=9600 --parity=no` a konzol argumentumaihoz

1. Nyomja le az F10 billentyűt a beállítások mentéséhez, és lépjen ki a
1. A GRUB-ba való belépéshez indítsa újra a virtuális gépet, és nyomja meg bármelyik billentyűt a rendszerindítássorán, hogy a GRUB a képernyőn maradjon
    - A GRUB alapértelmezett időmegfizetése 1s. Ezt úgy módosíthatja, `GRUB_TIMEOUT` hogy módosítja a`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Egyfelhasználós mód a SUSE SLES-ben
Akkor automatikusan vészhéjba kerül, ha az SLES nem tud normálisan elindulni. A vészjelző rendszer manuális beviteléhez kövesse az alábbi utasításokat:

1. A GRUB-ból nyomja meg az "e" gombot a rendszerindító bejegyzés szerkesztéséhez (az SLES bejegyzés)
1. Keresse meg a kernel sort fog kezdeni`linux`
1. Hozzáfűzés `systemd.unit=emergency.target` a sor végéhez
1. Nyomja le a Ctrl + X billentyűkombinációt az újraindításhoz ezekkel a beállításokkal, és írja be a vészhelyzeti rendszerhéjat
   > Ne feledje, hogy a rendszer _írásvédett_ fájlrendszerrel vészhéjba kerül. Ha bármilyen fájlt szeretne továbbírni, újra kell csatlakoztatnia a fájlrendszert az írási és írási engedélyekkel. Ehhez írja be `mount -o remount,rw /` a héj

## <a name="access-for-oracle-linux"></a>Hozzáférés az Oracle Linuxhoz
Hasonlóan a Red Hat Enterprise Linuxhoz, az Oracle Linux egyfelhasználós módjához hasonlóan a GRUB és a root felhasználó is engedélyezve van. 

### <a name="grub-access-in-oracle-linux"></a>GRUB hozzáférés Oracle Linux-ban
Oracle Linux jön a GRUB engedélyezve ki a dobozból. A GRUB-ba való belépéshez indítsa újra a virtuális gépet, `sudo reboot` és nyomja meg az "Esc" gombot. Látni fogja, hogy megjelenik a GRUB képernyő.

### <a name="single-user-mode-in-oracle-linux"></a>Egyfelhasználós mód oracle Linux
Az Oracle Linux egyfelhasználós módjának engedélyezéséhez kövesse a fenti RHEL-re vonatkozó utasításokat.

## <a name="next-steps"></a>További lépések
* A fő soros konzol Linux dokumentációs oldal [itt](serial-console.md)található .
* A Soros konzol használata [NMI- és SysRq-hívásokhoz](serial-console-nmi-sysrq.md)
* A soros konzol [Windows](../windows/serial-console.md) virtuális gépekhez is elérhető
* További információ a [rendszerindítási diagnosztikáról](boot-diagnostics.md)
