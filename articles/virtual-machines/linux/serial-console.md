---
title: Azure virtuális gép soros konzol |} Microsoft Docs
description: Kétirányú soros konzol az Azure virtuális gépek.
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
ms.openlocfilehash: 69f5e29be77f25d649ce357dae6e3905ab2bf6b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuális gép soros konzolon (előzetes verzió) 


A virtuális gép soros konzol az Azure-on Linux és a Windows virtuális gépek egy szöveges konzol hozzáférést biztosít. A soros kapcsolat COM1 soros port a virtuális gép és a virtuális gép hozzáférést biztosít, és a virtuálisgép-hálózathoz nem kapcsolódó / operációs rendszer állapotát. A virtuális gép a soros konzoljához való hozzáférés jelenleg csak Azure-portálon történik, és csak a virtuális gép közreműködő jogosult felhasználókhoz vagy újabb operációs rendszerre a virtuális géphez való hozzáférés engedélyezett. 

> [!Note] 
> Az előzetes verziójú funkciók rendelkezésre álló, feltéve, hogy elfogadja a használati feltételeket. További információkért tekintse meg az [a Microsoft Azure kiegészítő használati és a Microsoft Azure Előzetesekre.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg ez a szolgáltatás szerepel **nyilvános előzetes** és a soros konzoljához való hozzáférés a virtuális gépek globális Azure-régiók elérhető lesz. Ezen a ponton soros konzol nincs elérhető Azure Government, a Németországi Azure és az Azure Kína felhő.


## <a name="prerequisites"></a>Előfeltételek 

* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 
* A soros konzol használata a fióknak rendelkeznie kell [közreműködői szerepkör](../../role-based-access-control/built-in-roles.md) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiók. 
* Linux distro jellemző beállításait, lásd: [Linux a soros konzol használata](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>A soros konzol megnyitása
a virtuális gépek soros konzolon keresztül csak érhető el [Azure-portálon](https://portal.azure.com). A virtuális gépek portálon soros konzol eléréséhez a lépéseket az alábbiakban 

  1. Nyissa meg az Azure-portálon
  2. A bal oldali menüben válassza ki a virtuális gépek.
  3. Kattintson a virtuális Gépen a listában. A virtuális gép – Áttekintés lapon nyílik meg.
  4. Görgessen le a támogatási + hibaelhárítási szakaszában, majd kattintson a soros konzol (előzetes verzió) lehetőséget. Egy új panelen a soros konzol megnyitja, és indítsa el a kapcsolatot.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Soros konzol egy helyi felhasználói beállított jelszóval van szükség. Jelenleg csak a nyilvános SSH-kulcs konfigurált virtuális géppel nem lesz a soros konzoljához való hozzáférés. Hozzon létre egy helyi felhasználói jelszót, kövesse az [VM hozzáférési bővítmény](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) , és hozzon létre helyi felhasználói jelszavát.

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkcióit a virtuális gép rendszerindítási diagnosztika beállítás letiltásával inaktiválhatók adott virtuális gépekhez.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés korlátozódik, akik rendelkeznek [VM közreműködők](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) -e a virtuális géphez való hozzáférés. Ha az AAD-bérlőt többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés csak az MFA, az access keresztül [Azure-portálon](https://portal.azure.com).

### <a name="channel-security"></a>Biztonsági csatorna
Minden adat küldött vissza, és oda a keresztülhaladnak a hálózaton van titkosítva.

### <a name="audit-logs"></a>Naplók
A soros konzol hozzáférésének jelenleg bejelentkezett a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure virtuális gép rendszergazda határozza meg.  

>[!CAUTION] 
Nincs hozzáférési jelszavak, a konzol naplózza, ha futtassa a konzolon belüli parancsokat tartalmaz, vagy kimeneti jelszavak, titkos kulcsok, felhasználóneveket vagy bármilyen egyéb a személyes azonosításra alkalmas adatokat (PII), miközben a rendszer úgy tünteti fel a virtuális gép rendszerindítási diagnosztika naplózza, valamint minden más látható szöveg, a soros konzol scrollback funkció részeként. Ezek a naplók körkörös, és csak egyének olvasási engedélyt a diagnosztikai tárfiók hozzáférhetnek, azonban az ajánlott eljárást az SSH-konzollal számára, amely titkok és/vagy PII következő ajánlott. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és egy másik felhasználó sikeresen, hogy ugyanaz a virtuális gép hozzáférést kér, az első felhasználó le lesz választva, és a második felhasználó csatlakozik oly módon, mintha az első felhasználó állandó és a fizikai konzol és egy új elhagyása a felhasználó óta várakozik.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem naplózza a rendszer! Képes érvényesíteni a kijelentkezési (keresztül SIGHUP vagy hasonló mechanizmus) kapcsolat bontása után még mindig a programba. A Windows egy automatikus időtúllépés, SAC engedélyezve van azonban Linux beállítás terminál időtúllépés. Ez egyszerűen adja hozzá `export TMOUT=600` a .bash_profile vagy a felhasználó .profile bejelentkezési 10 perc után a munkamenet időtúllépési, a konzolon.

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkcióit a virtuális gép rendszerindítási diagnosztika beállítás letiltásával inaktiválhatók adott virtuális gépekhez.

## <a name="common-scenarios-for-accessing-serial-console"></a>Általános példák a soros konzol használata 
Forgatókönyv          | A soros konzol műveletek                |  Az operációs rendszer alkalmazhatósági 
:------------------|:-----------------------------------------|:------------------
Hibás FSTAB fájl | Adja meg a kulcsot a folytatáshoz, majd javítsa ki fstab fájlt egy szövegszerkesztő segítségével. Lásd: [fstab problémák megoldásával](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Helytelen tűzfalszabályok | Soros konzol eléréséhez, és javítsa ki a iptables vagy a Windows tűzfal-szabályok | Linux és Windows 
Fájlrendszer sérülése/ellenőrzése | Soros konzol eléréséhez és fájlrendszer helyreállítása | Linux és Windows 
Az RDP/SSH konfigurációs problémák | Soros konzol eléréséhez és beállításainak módosítása | Linux és Windows 
Rendszer hálózati zárolása| Hozzáférés soros konzolon keresztül rendszer kezeléséhez | Linux és Windows 
A rendszertöltő való interakció | Hozzáférés LÁRVAJÁRAT/BCD soros konzolon keresztül | Linux és Windows 

## <a name="accessing-serial-console-for-linux"></a>Soros konzol használata Linux rendszeren
Ahhoz, hogy a soros konzol működését a vendég operációs rendszer és a soros portjára konzol üzenetek olvasási kell állítani. A legtöbb [Azure Linux támogatott Disztribúciókkal](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) a soros konzol alapértelmezés szerint konfigurálva van. Csak kattintva a portálon a soros konzol szakasz hozzáférést biztosít a konzolon. 

### <a name="access-for-redhat"></a>RedHat hozzáférés 
RedHat képek elérhető Azure hozzáférhetnek konzol alapértelmezés szerint engedélyezett. Red Hat egyfelhasználós módban van szükség a gyökér szintű felhasználó engedélyezni kell, amely alapértelmezés szerint le van tiltva. Ha engedélyezi az egyfelhasználós módot kell, kövesse az alábbi utasításokat:

1. Jelentkezzen be a Red Hat rendszer SSH-kapcsolaton keresztül
2. Gyökér szintű felhasználó számára jelszó engedélyezése 
 * `passwd root` (gyökér erős jelszó beállítása)
3. Győződjön meg arról, gyökér szintű felhasználó csak bejelentkezhet ttyS0 keresztül
 * `edit /etc/ssh/sshd_config` a PermitRootLog van állítva a nem biztosítására és
 * `edit /etc/securetty file` csak a ttyS0 keresztül bejelentkezés engedélyezéséhez 

Most már a rendszer a egyfelhasználós módban indul el, ha bejelentkezhet gyökér szintű jelszó használatával.

Másik lehetőségként 7.4 + vagy 6.9 + engedélyezheti az RHEL egyfelhasználós módban a LÁRVAJÁRAT kérni fogja, lásd: utasításokat [Itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Ubuntu hozzáférés 
Ubuntu képek elérhető Azure hozzáférhetnek konzol alapértelmezés szerint engedélyezett. Ha a rendszer az egyfelhasználós módot a indul el, további hitelesítő adatok nélkül hozzáférhet. 

### <a name="access-for-coreos"></a>Hozzáférés a CoreOS
CoreOS képek elérhető Azure hozzáférhetnek konzol alapértelmezés szerint engedélyezett. Ha a szükséges rendszer rendszerindításra alkalmas az egyfelhasználós módot LÁRVAJÁRAT paraméterek módosítása és hozzáadása `coreos.autologin=ttyS0` core felhasználó számára elérhető soros konzolon és a napló lehetővé tenné. 

### <a name="access-for-suse"></a>Hozzáférés a SUSE
SLES képek elérhető Azure hozzáférhetnek konzol alapértelmezés szerint engedélyezett. Ha régebbi verziói a SLES használ az Azure-on, kövesse a [tudásbáziscikkben](https://www.novell.com/support/kb/doc.php?id=3456486) soros konzol engedélyezéséhez. Újabb képek a SLES 12 SP3 + is lehetővé teszi a hozzáférést a soros konzolon keresztül abban az esetben a rendszer a vészhelyzeti módban indul el.

### <a name="access-for-centos"></a>Hozzáférés a CentOS
CentOS képek elérhető Azure hozzáférhetnek konzol alapértelmezés szerint engedélyezett. Az egyfelhasználós módot kövesse a fenti Red Hat képeket hasonló utasításokat. 

### <a name="access-for-oracle-linux"></a>Hozzáférés az Oracle Linux
Oracle Linux képek, amely az Azure konzolhozzáférés alapértelmezés szerint engedélyezve van. Az egyfelhasználós módot kövesse a fenti Red Hat képeket hasonló utasításokat.

### <a name="access-for-custom-linux-image"></a>Kép: egyéni Linux hozzáférés
Az egyéni Linux Virtuálisgép-lemezkép a soros konzol engedélyezéséhez /etc/inittab ttyS0 futtatni egy terminált a konzol eléréséhez. Alább adja hozzá ezt a inittab fájlban példa van 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek kapcsolódási címet újrapróbálkozás. Táblázat alatti hibák és a megoldás listáját tartalmazza 

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztika beállításainak "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, majd próbálja megismételni a soros konzol kapcsolat. | Virtuális gép a soros konzol eléréséhez elindított állapotban kell lennie.
Nincs a virtuális gép a soros konzol használatához a szükséges engedélyekkel. Ellenőrizze, hogy legalább VM közreműködői szerepkör engedélyekkel.| Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek
Nem sikerült meghatározni az erőforráscsoport a rendszerindítási diagnosztikai tárfiók "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy engedélyezve van a rendszerindítási diagnosztika a virtuális gép, és rendelkezik hozzáféréssel ehhez a tárfiókhoz. | Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek

## <a name="known-issues"></a>Ismert problémák 
Mivel azt még szakaszaiban preview a soros konzol hozzáféréshez, azt ismert problémákat keresztül dolgozik, a alatt a listája, ezen a lehetséges megoldások 

Probléma                           |   Kezelés 
:---------------------------------|:--------------------------------------------|
A virtuális gép méretezési készlet példány soros konzolon nincs lehetőség |  A virtuálisgép-méretezési készlet példánya a soros konzoljához való hozzáférés előzetes időpontjában nem támogatott.
Elérte-e meg, miután a kapcsolat szalagcím nem jeleníti meg a napló a kérdés | [Elérte-e meg, nincs semmi hatása.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriája http://feedback.azure.com

**Q.I hibaüzenet "meglévő konzolnak ütköző operációs rendszerének típusa"Windows"Linux operációs rendszer a kért típussal rendelkező?**

A. Egy ismert probléma elhárításához, egyszerűen nyissa meg [Azure Cloud rendszerhéj](https://docs.microsoft.com/azure/cloud-shell/overview) bash üzemmódban, majd próbálja meg újra.

**Q. Nem tudom elérni a soros konzol, ahol is fájl támogatási esetet?**

A. Az előzetes funkcióval keresztül Azure Preview feltételek vonatkozik. Ez támogatása a kezelését érdemes a fent említett csatornán keresztül. 

## <a name="next-steps"></a>További lépések
* A soros konzolon érhető el is [Windows](../windows/serial-console.md) virtuális gépek
* További információ [bootdiagnostics](boot-diagnostics.md)