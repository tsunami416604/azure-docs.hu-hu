---
title: Azure virtuális gép soros konzol |} Microsoft Docs
description: Kétirányú soros konzol az Azure virtuális gépek.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuális gép soros konzolon (előzetes verzió) 


A virtuális gép soros konzol az Azure-on Linux és a Windows virtuális gépek egy szöveges konzol hozzáférést biztosít. A soros kapcsolat COM1 soros port a virtuális gép és a virtuális gép hozzáférést biztosít, és a virtuálisgép-hálózathoz nem kapcsolódó / operációs rendszer állapotát. A virtuális gép a soros konzoljához való hozzáférés jelenleg csak Azure-portálon történik, és csak a virtuális gép közreműködő jogosult felhasználókhoz vagy újabb operációs rendszerre a virtuális géphez való hozzáférés engedélyezett. 

> [!Note] 
> Az előzetes verziójú funkciók rendelkezésre álló, feltéve, hogy elfogadja a használati feltételeket. További információkért tekintse meg az [a Microsoft Azure kiegészítő használati és a Microsoft Azure Előzetesekre.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg ez a szolgáltatás szerepel **nyilvános előzetes** és a soros konzoljához való hozzáférés a virtuális gépek globális Azure-régiók elérhető lesz. Ezen a ponton soros konzol nincs elérhető Azure Government, a Németországi Azure és az Azure Kína felhő.

 

## <a name="prerequisites"></a>Előfeltételek 

* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 
* A soros konzol használata a fióknak rendelkeznie kell [közreműködői szerepkör](../../active-directory/role-based-access-built-in-roles.md) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiók. 

## <a name="open-the-serial-console"></a>A soros konzol megnyitása
a virtuális gépek soros konzolon keresztül csak érhető el [Azure-portálon](https://portal.azure.com). A soros konzol virtuális gépek portálon lépéseit az alábbiakban 

  1. Nyissa meg az Azure-portálon
  2. A bal oldali menüben válassza ki a virtuális gépek.
  3. Kattintson a virtuális Gépen a listában. A virtuális gép – Áttekintés lapon nyílik meg.
  4. Görgessen le a támogatási + hibaelhárítási szakaszában, és kattintson a soros konzol (előzetes verzió) opciót. Egy új panelen a soros konzol megnyitja, és indítsa el a kapcsolatot.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkcióit a virtuális gép rendszerindítási diagnosztika beállítás letiltásával inaktiválhatók adott virtuális gépekhez.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés korlátozódik, akik rendelkeznek [VM közreműködők](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) -e a virtuális géphez való hozzáférés. Ha az AAD-bérlőt többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés csak az MFA, az access keresztül [Azure-portálon](https://portal.azure.com).

### <a name="channel-security"></a>Biztonsági csatorna
Minden adat küldött vissza, és oda a keresztülhaladnak a hálózaton van titkosítva.

### <a name="audit-logs"></a>Naplók
A soros konzol hozzáférésének jelenleg bejelentkezett a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure virtuális gép rendszergazda határozza meg.  

>[!CAUTION] 
Nincs hozzáférési jelszavak, a konzol naplózza, ha futtassa a konzolon belüli parancsokat tartalmaz, vagy kimeneti jelszavak, titkos kulcsok, felhasználóneveket vagy bármilyen egyéb a személyes azonosításra alkalmas adatokat (PII), miközben a rendszer úgy tünteti fel a virtuális gép rendszerindítási diagnosztika naplózza, valamint minden más látható szöveg, a soros konzol scrollback funkció részeként. Ezek a naplók körkörös, és csak egyének olvasási engedélyt a diagnosztikai tárfiók hozzáférhetnek, azonban az ajánlott eljárást a távoli asztali kapcsolattal számára, amely titkok és/vagy PII következő ajánlott. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és egy másik felhasználó sikeresen, hogy ugyanaz a virtuális gép hozzáférést kér, az első felhasználó le lesz választva, és a második felhasználó csatlakozik oly módon, mintha az első felhasználó állandó és a fizikai konzol és egy új elhagyása a felhasználó óta várakozik.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem naplózza a rendszer! Képes érvényesíteni a kijelentkezési (keresztül SIGHUP vagy hasonló mechanizmus) kapcsolat bontása után még mindig a programba. A Windows egy automatikus időtúllépés, SAC engedélyezve van azonban Linux beállítás terminál időtúllépés. 


## <a name="accessing-serial-console-for-windows"></a>A Windows soros konzol használata 
Az Azure-on újabb Windows Server-lemezképekben lesz [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezett. SAC a Windows server-verziókban támogatott, de nincs az ügyfél-verziónkénti (pl. Windows 10, Windows 8 vagy Windows 7). Ahhoz, hogy a Windows virtuális gépek Feb2018 vagy alacsonyabb lemezképek használatával létrehozott soros konzol kérjük, az alábbi lépéseket: 

1. Csatlakozás távoli asztalon keresztül a Windows virtuális géphez
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Indítsa újra a rendszert a SAC konzol engedélyezni kell

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Ha a szükséges SAC akkor engedélyezhető, kapcsolat nélkül is, 

1. A meglévő VM adatlemezt értéke SAC kívánt windows lemez csatlakoztatásához. 
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Hogyan állapítható meg, hogy SAC engedélyezve van-e vagy sem 

Ha [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve a soros konzol nem jelenik meg a SAC parancssorába. Bizonyos esetekben egy virtuális gép állapottal kapcsolatos adatok megmutatja, vagy üres lenne.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>A soros konzol megjeleníti a rendszerindító menü engedélyezése 

Ha Windows rendszertöltő engedélyezni kell a megadását kéri, a soros konzol Windows rendszertöltő adhat hozzá a következő beállítások megjelenítése.

1. Csatlakozás távoli asztalon keresztül a Windows virtuális géphez
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Indítsa újra a rendszert a rendszerindító menü engedélyezni kell

> [!NOTE] 
> Ez a függvény pont terméktámogatási kulcsok nincs engedélyezve, ha speciális rendszerindítási beállítások használatát bcdedit/set {aktuális} onetimeadvancedoptions van szüksége, tekintse meg [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) további részletekért

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Általános példák a Windows soros konzoljához való hozzáférés 
Forgatókönyv          | A soros konzol műveletek                
:------------------|:----------------------------------------
Helytelen tűzfalszabályok | A soros konzol eléréséhez, és javítsa ki a iptables vagy a Windows tűzfal-szabályok 
Fájlrendszer sérülése/ellenőrzése | A soros konzol eléréséhez, és helyreállítja a fájlrendszer SAC CMD való bejelentkezés után
RDP konfigurációs problémák | A soros konzol eléréséhez, és jelentkezzen be cmd csatorna. Ellenőrizze a Terminálszolgáltatások, állapotát, és szükség esetén indítsa újra.
Rendszer hálózati zárolása| Hozzáférés soros konzoljához és cmd csatorna való bejelentkezéshez. A tűzfal állapotának által [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) parancssor. 

## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek kapcsolódási címet újrapróbálkozás. Táblázat alatti hibák és a megoldás listáját tartalmazza 

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztika beállításainak "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, majd próbálja megismételni a soros konzol kapcsolat. | Virtuális gép a soros konzol eléréséhez elindított állapotban kell lennie.
Nincs a virtuális gép soros konzol használatához a szükséges engedélyekkel. Ellenőrizze, hogy legalább VM közreműködői szerepkör engedélyekkel.| Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek
Nem sikerült meghatározni az erőforráscsoport a rendszerindítási diagnosztikai tárfiók "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy engedélyezve van a rendszerindítási diagnosztika a virtuális gép, és rendelkezik hozzáféréssel ehhez a tárfiókhoz. | Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek

## <a name="known-issues"></a>Ismert problémák 
Mivel azt még szakaszaiban preview a soros konzol hozzáféréshez, azt ismert problémákat keresztül dolgozik, a alatt a listája, ezen a lehetséges megoldások 

Probléma                           |   Kezelés 
:---------------------------------|:--------------------------------------------|
A virtuális gép méretezési készlet példány soros konzolon nincs lehetőség | A virtuálisgép-méretezési készlet példánya a soros konzoljához való hozzáférés előzetes időpontjában nem támogatott.
Elérte-e meg, miután a kapcsolat szalagcím nem jeleníti meg a napló a kérdés | [Elérte-e meg, nincs semmi hatása.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Csak állapotfigyelő információk jelennek meg a Windows virtuális gépek történő csatlakozás során| [Windows állapotfigyelő jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriája http://feedback.azure.com 

**Q. Hiba "meglévő konzolnak ütköző operációs rendszerének típusa"Windows"Linux operációs rendszer a kért típussal rendelkező?**

A. Egy ismert probléma elhárításához, egyszerűen nyissa meg [Azure Cloud rendszerhéj](https://docs.microsoft.com/azure/cloud-shell/overview) bash üzemmódban, majd próbálja meg újra.

**Q. Nem tudom elérni a soros konzol, ahol is fájl támogatási esetet?**

A. Az előzetes funkcióval keresztül Azure Preview feltételek vonatkozik. Ez támogatása a kezelését érdemes a fent említett csatornán keresztül. 

## <a name="next-steps"></a>További lépések
* A soros konzol érhető el is [Linux](../linux/serial-console.md) virtuális gépek
* További információ [bootdiagnostics](boot-diagnostics.md)
