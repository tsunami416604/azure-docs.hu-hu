---
title: Azure soros konzol a GRUB-hoz és egy egyszeri felhasználói üzemmódhoz
description: A grub soros konzoljának használata az Azure Virtual Machines szolgáltatásban.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035043"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>A soros konzol használata a GRUB és az single user mód eléréséhez
A GRUB a GRand Unified bootloader. A GRUB-ból a rendszerindítási konfigurációt úgy módosíthatja, hogy az egy felhasználói módba induljon, egyebek között.

Az egyfelhasználós mód a minimális funkcionalitással rendelkező minimális környezet. Hasznos lehet a rendszerindítási problémák, a fájlrendszeri problémák vagy a hálózati problémák kivizsgálásához. Előfordulhat, hogy kevesebb szolgáltatás fut a háttérben, és a runlevel függően előfordulhat, hogy a fájlrendszer még nem lesz automatikusan csatlakoztatva.

Az egyfelhasználós mód olyan helyzetekben is hasznos, ahol a virtuális gép csak úgy konfigurálható, hogy fogadja az SSH-kulcsokat a bejelentkezéshez. Ebben az esetben előfordulhat, hogy az egyfelhasználós módot fogja használni a jelszó-hitelesítéssel rendelkező fiók létrehozásához.

Az egyfelhasználós mód megadásához meg kell adnia a GRUB-t a virtuális gép indításakor, és módosítania kell a rendszerindítási konfigurációt a GRUB-ban. Ezt a virtuális gép soros konzolján teheti meg.

## <a name="general-grub-access"></a>Általános GRUB-hozzáférés
A GRUB eléréséhez újra kell indítania a virtuális gépet, miközben nyitva tartja a soros konzol paneljét. Egyes disztribúciók billentyűzet-bevitelt igényelnek a GRUB megjelenítéséhez, míg mások néhány másodpercig automatikusan megjelenítik a GRUB-t, és lehetővé teszik a felhasználói billentyűzet bemenetének megszakítani az időkorlátot. 

Biztosítania kell, hogy a GRUB engedélyezve legyen a virtuális gépen, hogy az egyetlen felhasználói üzemmódhoz tudjon hozzáférni. A disztribúciótól függően előfordulhat, hogy a GRUB engedélyezve van. A disztribúcióra vonatkozó információk az alábbiakban olvashatók.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>A virtuális gép újraindítása a GRUB a soros konzolon való eléréséhez
Ha az [SYSRQ](./serial-console-nmi-sysrq.md) engedélyezve van, vagy ha az Áttekintés panelen az Újraindítás gombra kattint, a virtuális gép újraindítása a soros konzol paneljén megnyitható egy SYSRQ `'b'` parancs használatával, vagy az áttekintő panel újraindítási gombjára kattintva (Nyissa meg a virtuális gépet egy új böngésző lapon a soros konzol bezárása nélkül történő újraindításhoz). Kövesse az alábbi disztribúció-specifikus utasításokat, hogy megtudja, mi várható a GRUB-ban az újraindításkor.

## <a name="general-single-user-mode-access"></a>Általános egyszeri felhasználói módú hozzáférés
Az egyfelhasználós mód manuális elérése olyan helyzetekben lehet szükséges, amikor még nem konfigurált jelszó-hitelesítéssel rendelkező fiókot. A GRUB-konfiguráció módosításával manuálisan kell megadnia egy felhasználói üzemmódot. Ha ezt elvégezte, további útmutatásért lásd: az egyfelhasználós mód használata a visszaállításhoz vagy a jelszó hozzáadásához.

Azokban az esetekben, amikor a virtuális gép nem tud elindulni, a disztribúciók gyakran automatikusan egyszeri felhasználói módba vagy vészhelyzeti módba kerülnek. Másoknak azonban további beállításra van szükségük ahhoz, hogy a rendszer automatikusan egy felhasználói vagy vészhelyzeti üzemmódba vonja őket (például a gyökér jelszavának beállítása).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Jelszó alaphelyzetbe állítása vagy hozzáadása egyetlen felhasználói módban
Ha egyfelhasználós módban van, az alábbi lépéseket követve adhat hozzá új felhasználót sudo jogosultságokkal:
1. `useradd <username>` futtatása felhasználó hozzáadásához
1. `sudo usermod -a -G sudo <username>` futtatása az új felhasználói gyökérszintű jogosultságok megadásához
1. Az új felhasználó jelszavának beállításához használja a `passwd <username>`. Ezután bejelentkezhet az új felhasználóként

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Hozzáférés Red Hat Enterprise Linux (RHEL)
A RHEL automatikusan egy felhasználói módba kerül, ha a szokásos módon nem indítható el. Ha azonban nem állított be rendszergazdai jogosultságot az egyfelhasználós üzemmódhoz, nem lesz rendszergazdai jelszava, és nem fog tudni bejelentkezni. Megkerülő megoldás (lásd az "egyfelhasználós üzemmód manuális megadása" szakaszt alább), de a javaslat a root Access kezdeti beállítására szolgál.

### <a name="grub-access-in-rhel"></a>GRUB-hozzáférés a RHEL-ben
A RHEL a GRUB engedélyezve van a dobozból. A GRUB beírásához indítsa újra a virtuális gépet `sudo reboot` és nyomja le bármelyik billentyűt. Ekkor megjelenik a GRUB képernyő.

> Megjegyzés: a Red Hat dokumentációt is biztosít a mentési módba való rendszerindítás, a vészhelyzeti mód, a hibakeresési mód és a gyökér jelszavának alaphelyzetbe állításához. Ide [kattintva érheti el](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Rendszergazdai hozzáférés beállítása egyetlen felhasználói üzemmódhoz a RHEL-ben
A RHEL egyfelhasználós üzemmódjában engedélyezni kell a legfelső szintű felhasználót, amely alapértelmezés szerint le van tiltva. Ha engedélyezni szeretné az egyfelhasználós üzemmódot, kövesse az alábbi utasításokat:

1. Bejelentkezés a Red Hat rendszerbe SSH-n keresztül
1. Váltás a gyökérre
1. Jelszó engedélyezése a legfelső szintű felhasználó számára 
    * `passwd root` (erős legfelső szintű jelszó beállítása)
1. Győződjön meg arról, hogy a root felhasználó csak a ttyS0-on keresztül tud bejelentkezni
    * `edit /etc/ssh/sshd_config`, és győződjön meg arról, hogy a PermitRootLogIn értéke nem
    * `edit /etc/securetty file`, hogy csak a ttyS0-on keresztül engedélyezze a bejelentkezést 

Most, ha a rendszer egyfelhasználós módba indul, bejelentkezhet a root password használatával.

Alternatívaként a RHEL 7.4 + vagy 6.9 + esetében engedélyezheti az egyfelhasználós üzemmódot a GRUB-kérésekben, [itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single) talál útmutatást.

### <a name="manually-enter-single-user-mode-in-rhel"></a>Egyfelhasználós mód manuális megadása a RHEL
Ha a fenti utasításokkal beállította a GRUB és a root elérést, akkor a következő utasításokkal adhatja meg az egyfelhasználós üzemmódot:

1. Nyomja meg az ESC billentyűt a virtuális gép újraindításakor a GRUB beírásához
1. A GRUB-ban az "e" gomb megnyomásával szerkessze a kiválasztott operációs rendszert, amelyet be szeretne indítani (általában az első sor)
1. Az Azure-ban található kernel-vonal megkeresése, ez a következővel fog kezdődni: `linux16`
1. Nyomja le a CTRL + E billentyűkombinációt a sor végére való ugráshoz
1. Adja hozzá a következőt a sor végéhez: `systemd.unit=rescue.target`
    * Ez egy felhasználói módba fog indulni. Ha vészhelyzeti módot szeretne használni, vegyen fel `systemd.unit=emergency.target`t a sor végére `systemd.unit=rescue.target` helyett
1. Nyomja le a CTRL + X billentyűkombinációt a kilépéshez, majd indítsa újra az alkalmazott beállításokkal
1. A rendszer kérni fogja a rendszergazdai jelszót, mielőtt beírja az egyfelhasználós módot – ez ugyanaz a jelszó, amelyet a fenti utasításokban hozott létre.    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Adja meg az egyfelhasználós üzemmódot anélkül, hogy a gyökérszintű fiók engedélyezve van a RHEL
Ha nem hajtja végre a fenti lépéseket a root felhasználó engedélyezéséhez, akkor továbbra is alaphelyzetbe állíthatja a legfelső szintű jelszót. Kövesse az alábbi utasításokat:

> Megjegyzés: Ha a SELinux-t használja, gondoskodjon arról, hogy a legfelső szintű jelszó alaphelyzetbe [állításakor](https://aka.ms/rhel7grubterminal) a Red Hat dokumentációjában leírt további lépéseket is megtegye.

1. Nyomja meg az ESC billentyűt a virtuális gép újraindításakor a GRUB beírásához
1. A GRUB-ban az "e" gomb megnyomásával szerkessze a kiválasztott operációs rendszert, amelyet be szeretne indítani (általában az első sor)
1. Az Azure-ban található kernel-vonal megkeresése, ez a következővel fog kezdődni: `linux16`
1. Vegyen fel `rd.break` a sor végére, és győződjön meg arról, hogy van-e szóköz a `rd.break` előtt (lásd az alábbi példát)
    - Ezzel a művelettel megszakítja a rendszerindítási folyamatot, mielőtt a rendszer átadja a vezérlőt `initramfs`ról `systemd`ra, a Red [hat dokumentációjában](https://aka.ms/rhel7rootpassword)leírtaknak megfelelően.
1. Nyomja le a CTRL + X billentyűkombinációt a kilépéshez, majd indítsa újra az alkalmazott beállításokkal
1. A rendszerindítás után a rendszer a csak olvasható fájlrendszerrel rendelkező vészhelyzeti módban fogja eldobni. Írja be `mount -o remount,rw /sysroot` a rendszerhéjba, hogy újracsatlakoztassa a rendszerindító fájlrendszert olvasási/írási engedélyekkel
1. Az egyfelhasználós módban történő rendszerindítás után a `chroot /sysroot` beírásával válthat a `sysroot` jailre
1. Most már a root. Alaphelyzetbe állíthatja a legfelső szintű jelszót `passwd`, majd a fenti utasítások segítségével megadhatja az egyfelhasználós üzemmódot. Ha elkészült, írja be `reboot -f` az újraindításhoz.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Megjegyzés: a fenti utasítások segítségével a rendszer elvégzi a vészhelyzeti rendszerhéj működését, így olyan feladatokat is végrehajthat, mint például a `fstab`szerkesztése. Az általánosan elfogadott javaslat azonban a gyökér jelszavának alaphelyzetbe állítása, és az egyfelhasználós mód megadására használható. 


## <a name="access-for-centos"></a>CentOS-hozzáférés
A Red Hat Enterprise Linuxhoz hasonlóan a CentOS-ben az egyfelhasználós mód a GRUB és a root felhasználó engedélyezését igényli. 

### <a name="grub-access-in-centos"></a>GRUB-hozzáférés a CentOS-ben
A CentOS a GRUB-ban engedélyezve van a dobozból. A GRUB beírásához indítsa újra a virtuális gépet `sudo reboot` és nyomja le bármelyik billentyűt. Ekkor megjelenik a GRUB képernyő.

### <a name="single-user-mode-in-centos"></a>Egy felhasználói mód a CentOS-ben
Az egyfelhasználós mód a CentOS-ben való engedélyezéséhez kövesse a fenti RHEL utasításait.

## <a name="access-for-ubuntu"></a>Hozzáférés Ubuntu rendszerhez 
Az Ubuntu-lemezképekhez nem szükséges a gyökér jelszava. Ha a rendszer egyfelhasználós módba indul, további hitelesítő adatok nélkül használhatja. 

### <a name="grub-access-in-ubuntu"></a>GRUB-hozzáférés Ubuntuban
A GRUB eléréséhez nyomja le és tartsa nyomva a "ESC" billentyűt, amíg a virtuális gép elindult. 

Alapértelmezés szerint az Ubuntu-lemezképek nem fogják automatikusan megjeleníteni a GRUB-képernyőt. Ez a következő utasításokkal módosítható:
1. `/etc/default/grub.d/50-cloudimg-settings.cfg` megnyitása tetszőleges szövegszerkesztőben
1. `GRUB_TIMEOUT` értékének módosítása nullától eltérő értékre
1. `/etc/default/grub` megnyitása tetszőleges szövegszerkesztőben
1. Megjegyzés a `GRUB_HIDDEN_TIMEOUT=1` sorban
1. Futtassa a `sudo update-grub` parancsot.

### <a name="single-user-mode-in-ubuntu"></a>Egyszeri felhasználói üzemmód az Ubuntuban
Ha a szokásos módon nem tud elindulni, az Ubuntu automatikusan egy felhasználói módba kerül. Az egyfelhasználós mód manuális megadásához kövesse az alábbi utasításokat:

1. A GRUB-ból kattintson az "e" gombra a rendszerindítási bejegyzés szerkesztéséhez (Ubuntu-bejegyzés)
1. Keresse meg a `linux`kezdetű vonalat, majd keresse meg a `ro`
1. `single` hozzáadása `ro`után, gondoskodjon arról, hogy a `single` előtt és után legyen szóköz.
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adjon meg egy felhasználói módot

## <a name="access-for-coreos"></a>Hozzáférés a CoreOS
A CoreOS egyetlen felhasználói üzemmódjában engedélyezni kell a GRUB használatát. 

### <a name="grub-access-in-coreos"></a>GRUB-hozzáférés a CoreOS-ben
A GRUB eléréséhez nyomja le bármelyik billentyűt a virtuális gép indításakor.

### <a name="single-user-mode-in-coreos"></a>Egyetlen felhasználói mód a CoreOS
A CoreOS automatikusan egy felhasználói módba kerül, ha a szokásos módon nem indítható el. Az egyfelhasználós mód manuális megadásához kövesse az alábbi utasításokat:
1. A GRUB-ból kattintson az "e" gombra a rendszerindítási bejegyzés szerkesztéséhez.
1. Keresse meg a `linux$`kezdetű sort. 2, különböző if/Else záradékban ágyazva
1. `coreos.autologin=ttyS0` hozzáfűzése a `linux$` sorok végéhez
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adjon meg egy felhasználói módot

## <a name="access-for-suse-sles"></a>Hozzáférés a SUSE SLES
A SLES 12 SP3 és újabb rendszerképek a soros konzolon keresztül érhetik el a hozzáférést, ha a rendszerindítást vészhelyzeti módban indítja el. 

### <a name="grub-access-in-suse-sles"></a>GRUB-hozzáférés a SUSE SLES
A SLES-ben a GRUB-hozzáféréshez a YaST-n keresztül szükséges a bootloader konfigurálása Ehhez kövesse az alábbi utasításokat:

1. SSH-t a SLES virtuális gépre, és futtassa `sudo yast bootloader`. A menüben navigáljon a `tab` kulcs, a `enter` kulcs és a nyílbillentyűk használatával. 
1. Keresse meg `Kernel Parameters`, és jelölje be a `Use serial console`. 
1. `serial --unit=0 --speed=9600 --parity=no` hozzáadása a konzol argumentumokhoz

1. A beállítások mentéséhez és a kilépéshez nyomja meg az F10 billentyűt
1. A GRUB megadásához indítsa újra a virtuális gépet, és nyomja le bármelyik billentyűt a rendszerindítási folyamat során, hogy a GRUB a képernyőn maradjon
    - A GRUB alapértelmezett időtúllépése 1s. Ezt úgy módosíthatja, ha módosítja a `GRUB_TIMEOUT` változót a `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Egyetlen felhasználói mód a SUSE SLES
Ha a SLES normál esetben nem indítható el, a rendszer automatikusan eltávolítja a vészhelyzeti rendszerhéjba. A vészhelyzeti rendszerhéj manuális megadásához kövesse az alábbi utasításokat:

1. A GRUB-ból kattintson az "e" gombra a rendszerindítási bejegyzés szerkesztéséhez (az SLES-bejegyzés)
1. Keresse meg a kernelt, amelyet a `linux` fog kezdeni
1. `systemd.unit=emergency.target` hozzáfűzése a sor végéhez
1. Nyomja le a CTRL + X billentyűkombinációt a beállítások újraindításához, és adja meg a vészhelyzeti rendszerhéjt
   > Vegye figyelembe, hogy a vészhelyzeti rendszerhéj egy _írásvédett_ fájlrendszerrel fog eldobni. Ha bármilyen fájlhoz szeretne módosításokat végezni, akkor újra kell csatlakoztatnia a fájlrendszert írási és olvasási engedélyekkel. Ehhez írja be `mount -o remount,rw /` a rendszerhéjba.

## <a name="access-for-oracle-linux"></a>Hozzáférés Oracle Linux
A Red Hat Enterprise Linuxhoz hasonlóan a Oracle Linux egy felhasználói módja a GRUB és a root felhasználó engedélyezését igényli. 

### <a name="grub-access-in-oracle-linux"></a>GRUB-hozzáférés Oracle Linux
A Oracle Linux a GRUB engedélyezve van a dobozból. A GRUB beírásához indítsa újra a virtuális gépet `sudo reboot` és nyomja meg az ESC billentyűt. Ekkor megjelenik a GRUB képernyő.

### <a name="single-user-mode-in-oracle-linux"></a>Egy felhasználói mód a Oracle Linuxban
A fenti RHEL utasításait követve engedélyezze az egyfelhasználós üzemmódot a Oracle Linuxban.

## <a name="next-steps"></a>Következő lépések
* [Itt](serial-console.md)található a soros konzolhoz tartozó fő Linux Dokumentációs oldal.
* Soros konzol használata [NMI-és SYSRQ-hívásokhoz](serial-console-nmi-sysrq.md)
* A soros konzol a [Windows](../windows/serial-console.md) rendszerű virtuális gépekhez is elérhető
* További információ a [rendszerindítási diagnosztika](boot-diagnostics.md) szolgáltatásról
