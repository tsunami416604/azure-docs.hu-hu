---
title: Az Azure virtuális gépek soros konzolja |} A Microsoft Docs
description: Azure-beli virtuális gépek soros konzol és a kétirányú.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867861"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuális gépek soros konzolja (előzetes verzió) 


A virtuális gépek soros konzolja az Azure-ban a Linux és Windows virtuális gépek egy szöveges alapú konzol hozzáférést biztosít. A soros kapcsolat COM1 soros porton, a virtuális gép és a virtuális géphez való hozzáférés biztosít, és amelyek nem kapcsolódnak a virtuális gép hálózati / operációs rendszer állapotát. Virtuális gép a soros konzoljához való hozzáférés is jelenleg csak az Azure Portalon keresztül történik, és csak azok számára, akik rendelkeznek a virtuális gép közreműködő vagy újabb a virtuális géphez való hozzáférés engedélyezett. 

> [!Note] 
> Előzetes verziók által elérhető, a feltétellel, hogy elfogadja a használati feltételeket. További információkért lásd: [Microsoft Azure kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a szolgáltatás jelenleg a **nyilvános előzetes verzióban** és a soros konzoljához való hozzáférés a virtuális gépek globális Azure-régióban érhető el. Ezen a ponton soros konzol nem érhető el az Azure Government, Azure Germany és Azure China cloud.


## <a name="prerequisites"></a>Előfeltételek 

* A resource management üzemi modellhez kell használnia. Klasszikus üzemi modellben nem támogatottak. 
* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 
* A soros konzol használatával a fióknak rendelkeznie kell [közreműködői szerepkört](../../role-based-access-control/built-in-roles.md) virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiókot. 
* Linux-disztribúció jellemző beállításait, lásd: [a soros konzol eléréséhez a Linux rendszerre](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>A soros konzol megnyitása
a virtuális gépek soros konzolon keresztül csak érhető el [az Azure portal](https://portal.azure.com). A portálon keresztül a virtuális gépek soros konzol eléréséhez a lépéseket az alábbiakban 

  1. Nyissa meg az Azure Portalon
  2. A bal oldali menüben válassza ki a virtuális gépeket.
  3. Kattintson a listában a virtuális gépen. A virtuális gép áttekintő oldala nyílik meg.
  4. Görgessen le a támogatás és hibaelhárítás szakaszhoz, majd kattintson a soros konzol (előzetes verzió) lehetőséget. A soros konzol segítségével egy új panel megnyitja és indítsa el a kapcsolatot.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Soros konzol beállított jelszóval rendelkező helyi felhasználó szükséges. Jelenleg csak a nyilvános SSH-kulcs konfigurált virtuális gépek nem fog a soros konzoljához való hozzáférés. Hozzon létre egy helyi felhasználót felhasználónévvel és jelszóval, hajtsa végre a [Virtuálisgép-hozzáférési bővítmény](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) , és hozzon létre helyi felhasználói jelszót.

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkció a virtuális gép rendszerindítási diagnosztikai beállítás letiltásával inaktiválhatók adott virtuális gépek.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés a felhasználók, akik rendelkeznek korlátozódik [virtuális gép közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy a virtuális géphez való hozzáférés felett. Ha az AAD-bérlő többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés is szükség van egy MFA-n keresztül van a hozzáférés [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Az összes adat küldött vissza, és oda-titkosításra kerül az átvitel.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.  

>[!CAUTION] 
A konzol hozzáférés jelszavakat nem jelentkezik, ha a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket vagy bármely más, személyazonosításra alkalmas adatok (PII) kimeneti, amíg azok lesz írva a virtuális gép rendszerindítási diagnosztikája naplózza, és minden egyéb látható szöveg, a soros konzol scrollback funkció részeként. Ezeket a naplókat. kör alakú, és csak egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező hozzáférése, de javasoljuk, hogy kövesse az ajánlott eljárás az összes adat az SSH-konzol használatával, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó le lesz választva, és a második felhasználó csatlakoztatott oly módon, mintha az első felhasználó, hogy mobilplatformonként és a fizikai konzol és a egy új elhagyása a felhasználó úgy.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem kijelentkeztetésével! Képes érvényesíteni a kijelentkezési (SIGHUP vagy hasonló mechanizmus) keresztül kapcsolat bontása esetén továbbra is szerepel az ütemterv részét képezi. A Windows-automatikus időtúllépés, SAC engedélyezve van azonban Linux konfigurálhatja a Terminálszolgáltatások időkorlátozási beállítást. Ez egyszerűen adja hozzá `export TMOUT=600` a .bash_profile fájlt vagy a felhasználó .profile bejelentkezési időkorlátja 10 perc után a munkamenet-, a konzolon.

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkció a virtuális gép rendszerindítási diagnosztikai beállítás letiltásával inaktiválhatók adott virtuális gépek.

## <a name="common-scenarios-for-accessing-serial-console"></a>Általános forgatókönyvek a soros konzol eléréséhez 
Forgatókönyv          | A soros konzol műveletek                |  Az operációs rendszer alkalmazási terület 
:------------------|:-----------------------------------------|:------------------
Megszakadt az FSTAB fájl | Adja meg a kulcsot a folytatáshoz javítsa ki az fstab fájlt egy szövegszerkesztőben. Lásd: [fstab-hibák megoldása](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Helytelen tűzfal-szabályok | Soros konzol eléréséhez, és engedélyezze az iptables vagy a Windows tűzfal-szabályok javítása | Linux/Windows 
Fájlrendszer sérülése és ellenőrzése | Soros konzol eléréséhez, és helyreállítja a fájlrendszer | Linux/Windows 
Az RDP/SSH konfigurációs problémák | Soros konzol eléréséhez és a beállítások módosítása | Linux/Windows 
Rendszer hálózati zárolása| Hozzáférés a soros konzol a rendszer kezelése a portálon keresztül | Linux/Windows 
A rendszertöltő használata | Hozzáférés a grub-HIBÁT/BCD soros konzolon keresztül | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Soros konzol eléréséhez a linuxhoz
Ahhoz, hogy megfelelően működnek a soros konzol a vendég operációs rendszer olvasása és írása az üzenetek konzol a soros port kell állítani. A legtöbb [támogatott az Azure Linux-Disztribúció](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) a soros konzol alapértelmezés szerint konfigurálva van. Csak kattintva a portálon a soros konzol szakasz hozzáférést biztosít a konzolon. 

### <a name="access-for-red-hat"></a>Hozzáférés a Red Hat 
Elérhető az Azure-ban Red Hat-lemezképeit hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. A Red Hat egyfelhasználós üzemmódban szükséges gyökér szintű felhasználó engedélyezni kell, amely alapértelmezés szerint le van tiltva. Ha nincs szüksége egyfelhasználós mód engedélyezéséhez, kövesse az alábbi utasításokat:

1. Jelentkezzen be SSH-n keresztül a Red Hat rendszer
2. Gyökér szintű felhasználó számára jelszó engedélyezése 
 * `passwd root` (erős legfelső szintű jelszó beállítása)
3. Győződjön meg, hogy a gyökér szintű felhasználó csak bejelentkezhet ttyS0 keresztül
 * `edit /etc/ssh/sshd_config` és ellenőrizze, hogy a PermitRootLog van beállítva, nem
 * `edit /etc/securetty file` a kizárólag a naplót a ttyS0 keresztül 

Mostantól Ha a rendszer betölti a egyfelhasználós módban bejelentkezhet gyökér szintű jelszó keresztül.

Másik lehetőségként az RHEL 7.4 + vagy 6.9 + engedélyezheti egyetlen felhasználói mód a grub-HIBÁT a kérni fogja, tudnivalókat [Itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Ubuntu hozzáférés 
Ubuntu-rendszerképek az Azure-ban elérhető hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Ha a rendszer betölti az egyfelhasználós módot további hitelesítő adatok nélkül elérheti. 

### <a name="access-for-coreos"></a>Hozzáférés a CoreOS
CoreOS lemezkép érhető el az Azure-ban hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Ha a szükséges rendszer rendszerindításra alkalmas az egyfelhasználós módot GRUB paraméterek módosítása és hozzáadása `coreos.autologin=ttyS0` lehetővé tenné a core felhasználói naplóba is és a soros konzolon érhetők el. 

### <a name="access-for-suse"></a>SUSE hozzáférés
SLES lemezkép érhető el az Azure-ban hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Ha régebbi verzióit SLES használ az Azure-ban, hajtsa végre a [tudásbáziscikk](https://www.novell.com/support/kb/doc.php?id=3456486) engedélyezése a soros konzol. Újabb képek, SLES 12 SP3 + is lehetővé teszi, hogy a soros konzolon keresztül hozzáférést abban az esetben a rendszer vészhelyzeti módban indul.

### <a name="access-for-centos"></a>Hozzáférés a CentOS
CentOS lemezkép érhető el az Azure-ban hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Az egyfelhasználós módot kövesse a fenti Red Hat-lemezképeit az alábbiakhoz hasonló utasításokat. 

### <a name="access-for-oracle-linux"></a>Oracle Linux-hozzáférés
Az Azure-ban elérhető Oracle Linux-rendszerképeket hozzáféréséhez a konzolhoz alapértelmezés szerint engedélyezve van. Az egyfelhasználós módot kövesse a fenti Red Hat-lemezképeit az alábbiakhoz hasonló utasításokat.

### <a name="access-for-custom-linux-image"></a>Hozzáférés az egyéni Linux-rendszerképek
A Linux rendszerű virtuális gép egyéni rendszerkép soros konzol engedélyezéséhez a parancsot egy terminálban a futtathatók ttyS0 /etc/inittab hozzáféréséhez a konzolhoz. Alább egy példa hozzáadja a a inittab fájlban van 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek újrapróbálkozás a kapcsolódási IP-címet a rendszer. Táblázat alatti hibák és kockázatcsökkentési listája látható. 

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. | Virtuális gépnek kell lennie a soros konzol eléréséhez a elindított állapotú
Nem rendelkezik a szükséges engedélyekkel a virtuális gép a soros konzol használata. Győződjön meg arról, hogy Virtuálisgép-közreműködői szerepkör engedélyei.| Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Soros konzol eléréséhez az egyes hozzáférési engedélyt kell. Lásd: [a hozzáférési követelmények](#prerequisites) részletekért

## <a name="known-issues"></a>Ismert problémák 
Sok továbbra is szakaszaiban az előzetes verzió a soros hozzáféréshez, hogy bizonyos ismert problémák keresztül dolgozik, alább látható ezen a lehetséges megoldások listája 

Probléma                           |   Kezelés 
:---------------------------------|:--------------------------------------------|
A virtual machine scale set példány soros konzol nem választható |  Előzetes verzió idején a virtuális gép méretezési csoport példányaihoz a soros konzoljához való hozzáférés nem támogatott.
Szerezze meg, miután a kapcsolaton transzparens nem jeleníti meg a napló-parancssorban | [Szerezze meg a adja meg, nincs semmi hatása.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése egy problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése a keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriáját http://feedback.azure.com

**Q.I hibaüzenet "meglévő konzolnak ütköző"Windows"a kért operációsrendszer-típus Linux operációsrendszer-típust?**

A. Ez a probléma megoldásához egy ismert probléma az egyszerűen nyissa meg [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) a bash módot, majd próbálkozzon újra.

**Q. Nem tudom elérni a soros konzol, ahol is fájlt egy támogatási kérést?**

A. Ezt az előzetes funkciót a hatálya alá tartozó keresztül az Azure előzetes verziójú szolgáltatás feltételeit. Támogatás a legjobban a fent említett csatornákon keresztül kezeli. 

## <a name="next-steps"></a>További lépések
* A soros konzolon érhető el is [Windows](../windows/serial-console.md) virtuális gépek
* Tudjon meg többet [bootdiagnostics](boot-diagnostics.md)