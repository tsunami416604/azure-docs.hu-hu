---
title: Azure Serial Console for Linux | Microsoft dokumentumok
description: Kétirányú soros konzol az Azure virtuális gépek és a virtuális gép méretezési készletek.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167019"
---
# <a name="azure-serial-console-for-linux"></a>Azure soros konzol Linuxhoz

Az Azure Portalon található soros konzol hozzáférést biztosít a Linux-alapú virtuális gépek (VM-ek) és a virtuális gépek méretezési példányai szöveges alapú konzoljához. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának ttys0 soros portjához csatlakozik, és a hálózattól vagy az operációs rendszer állapotától független hozzáférést biztosít hozzá. A soros konzol csak az Azure Portalon keresztül érhető el, és csak azok a felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuális gép méretezési készlethez tartozó közreműködői vagy magasabb szintű hozzáférési szerepkörrel rendelkeznek.

Soros konzol működik ugyanúgy a virtuális gépek és a virtuális gép méretezési készlet példányok. Ebben a dokumentumban a virtuális gépek minden említése implicit módon tartalmazza a virtuális gép méretezési példányait, kivéve, ha másként van feltüntetve.

A Soros konzol Windows-dokumentációját a [Serial Console for Windows (Soros konzol windowsos) témakörben találja.](../windows/serial-console.md)

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban és az Azure Government nyilvános előzetes verzióban. Még nem érhető el az Azure China felhőben.


## <a name="prerequisites"></a>Előfeltételek

- A virtuális gép vagy a virtuálisgép méretezési csoport példányának az erőforrás-kezelési telepítési modellt kell használnia. Klasszikus központi telepítések nem támogatottak.

- A soros konzolt használó fióknak rendelkeznie kell a virtuális gép virtuális gép hez és a [rendszerindító diagnosztikai](boot-diagnostics.md) tárfiókhoz használt [Virtuálisgép közreműködői szerepkörével.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

- A virtuális gép vagy a virtuálisgép méretezési csoport példányának jelszóalapú felhasználóval kell rendelkeznie. Létrehozhat egyet a virtuális gép hozzáférési bővítmény [jelszó-visszaállítási](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkciójával. Válassza a **Jelszó alaphelyzetbe állítása** lehetőséget a **Támogatás + hibaelhárítás** szakaszban.

- A virtuális gép vagy a virtuálisgép méretezési csoport példányának engedélyeznie kell [a rendszerindítási diagnosztikát.](boot-diagnostics.md)

    ![Rendszerindítási diagnosztikai beállítások](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- A Linux-disztribúciókra vonatkozó beállításokról a [Serial console Linux disztribúció elérhetősége .](#serial-console-linux-distribution-availability)

- A virtuális gép vagy a virtuálisgép méretezési `ttys0`csoport példányát konfigurálni kell a soros kimenethez. Ez az alapértelmezett Azure-lemezképek, de érdemes ellenőrizni ezt az egyéni lemezképek. Részletek [az alábbiakban](#custom-linux-images).


> [!NOTE]
> A soros konzolhoz egy helyi felhasználóra van szükség, aki rendelkezik egy konfigurált jelszóval. A csak SSH nyilvános kulccsal konfigurált virtuális gépek vagy virtuálisgép-méretezési készletek nem tudnak bejelentkezni a soros konzolra. Helyi felhasználó jelszóval történő létrehozásához használja a [VMAccess-bővítményt,](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)amely elérhető a portálon az Azure Portalon a **Jelszó alaphelyzetbe állítása** lehetőség kiválasztásával, és hozzon létre egy helyi felhasználót jelszóval.
> A fiókrendszergazdai jelszót is visszaállíthatja a [GRUB használatával az egyfelhasználós módba való rendszerindításhoz.](./serial-console-grub-single-user-mode.md)

## <a name="serial-console-linux-distribution-availability"></a>Soros konzol Linux disztribúció elérhetősége
A soros konzol megfelelő működéséhez a vendég operációs rendszert úgy kell beállítani, hogy a konzolüzeneteket a soros portra olvassa és írja. A legtöbb [támogatott Azure Linux-disztribúció](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) alapértelmezés szerint a soros konzol konfigurálva van. Ha a **Soros konzolt** az Azure Portal **Support + troubleshooting** szakaszában választja ki, a soros konzol hoz való hozzáférést.

> [!NOTE]
> Ha nem lát semmit a soros konzolon, győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gépen. Ütő **Belép** akarat gyakran erősít kérdés hol semmi van bemutatás megjelöl -ban sorozat vigasztal.

Disztribúció      | Csatlakozás a soros konzolhoz
:-----------|:---------------------
Red Hat Enterprise Linux    | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.
CentOS      | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.
Debian      | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.
Ubuntu      | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.
CoreOS      | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.
SUSE        | Az Azure-ban elérhető újabb SLES-lemezképek alapértelmezés szerint engedélyezve vannak a soros konzolhozzáférés. Ha az SLES régebbi (10-es vagy korábbi) verzióit használja az Azure-ban, tekintse meg a [tudásbáziscikket](https://www.novell.com/support/kb/doc.php?id=3456486) a soros konzol engedélyezéséhez.
Oracle Linux        | A soros konzolhozzáférés alapértelmezés szerint engedélyezve van.

### <a name="custom-linux-images"></a>Egyéni Linux-képek
Ha engedélyezni szeretné a soros konzolt az egyéni Linux Virtuálisgép-lemezképhez, engedélyezze `ttyS0`a konzolhozzáférést a *fájlban /etc/inittab* terminál futtatásához. Például: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Előfordulhat, hogy a ttyS0-n getty-t kell létrehoznia. Ezt meg lehet `systemctl start serial-getty@ttyS0.service`tenni a .

Azt is szeretné, hogy adjunk ttys0, mint a cél a soros kimenet. Az egyéni lemezkép soros konzollal való együttműködésre történő konfigurálásáról a [Linux-virtuális merevlemez létrehozása és feltöltése az Azure-ban](https://aka.ms/createuploadvhd#general-linux-system-requirements)című témakörben talál további információt.

Ha egyéni rendszermagot hozunk létre, engedélyezd a kerneljelzők: `CONFIG_SERIAL_8250=y` és `CONFIG_MAGIC_SYSRQ_SERIAL=y`a . A konfigurációs fájl általában a */boot/* elérési úton található.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Gyakori forgatókönyvek a soros konzol eléréséhez

Forgatókönyv          | Műveletek a soros konzolon
:------------------|:-----------------------------------------
Hibás *FSTAB* fájl | Az *FSTAB-fájl* javításához nyomja le az **Enter** billentyűt, és használjon szövegszerkesztőt. Erre szükség lehet egyfelhasználós módban. További információt az [Fstab problémák kijavítása](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) és a Soros konzol használata a [GRUB és az egyfelhasználós mód eléréséhez](serial-console-grub-single-user-mode.md)című sorozatos konzolszakaszban talál.
Helytelen tűzfalszabályok |  Ha úgy állította be az iptables-t, hogy blokkolja az SSH-kapcsolatot, a soros konzol segítségével ssh nélkül kommunikálhat a virtuális géppel. További részletek találhatók az [iptables man oldalon](https://linux.die.net/man/8/iptables).<br>Hasonlóképpen, ha a firewalld blokkolja az SSH-hozzáférést, a virtuális gép a soros konzolon keresztül, és újrakonfigurálja a firewalld. További részletek a [tűzfallal kapcsolatos dokumentációban](https://firewalld.org/documentation/)találhatók.
Fájlrendszer sérülése/ellenőrzése | Tekintse meg az Azure Linux virtuális gép soros konzolszakaszát, [amely fájlrendszeri hibák miatt nem indítható](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) el a sérült fájlrendszerek soros konzolt használó hibaelhárításával kapcsolatos további részletekért.
SSH konfigurációs problémák | Hozzáférés a soros konzolhoz, és módosíthatja a beállításokat. Soros konzol a virtuális gép SSH-konfigurációjától függetlenül használható, mivel nem szükséges, hogy a virtuális gép hálózati kapcsolattal működjön. Hibaelhárítási útmutató érhető el az [SSH-kapcsolatok hibaelhárítása egy Azure Linux virtuális géphez, amely meghibásodik, hibákat elhárít, vagy elutasításra kerül.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) További részletek az [SSH részletes hibaelhárítási lépései az Azure-beli Linux virtuális gépekhez való csatlakozással kapcsolatos problémákról](./detailed-troubleshoot-ssh-connection.md)
Interakció a rendszertöltővel | Indítsa újra a virtuális gép a soros konzol panelen a GRUB eléréséhez a Linux virtuális gép. További részletekért és a distro-specifikus információkért [lásd: Soros konzol használata a GRUB és az egyfelhasználós mód eléréséhez.](serial-console-grub-single-user-mode.md)

## <a name="disable-the-serial-console"></a>A soros konzol letiltása

Alapértelmezés szerint minden előfizetéshez engedélyezve van a soros konzolhozzáférés. Letilthatja a soros konzolt az előfizetés i vagy a virtuális gép/virtuálisgép méretezési szintjén. Részletes útmutatásért látogasson el [az Azure serial console engedélyezése és letiltása](./serial-console-enable-disable.md)című ellátogat elemre.

## <a name="serial-console-security"></a>Soros konzol biztonsága

### <a name="access-security"></a>Hozzáférés biztonsága
A soros konzolhoz való hozzáférés azokra a felhasználókra korlátozódik, akik a [virtuális gép közreműködőjének](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy a virtuális géphez való hozzáférési szerepkörrel rendelkeznek. Ha az Azure Active Directory-bérlő többtényezős hitelesítést (MFA) igényel, akkor a soros konzolhoz való hozzáférésnek is szüksége lesz az MFA-ra, mivel a soros konzol hozzáférése az [Azure Portalon](https://portal.azure.com)keresztül történik.

### <a name="channel-security"></a>Csatorna biztonsága
Minden oda-vissza küldött adat titkosítva van a hálózaton.

### <a name="audit-logs"></a>Naplók
A soros konzolhoz való összes hozzáférés jelenleg a virtuális gép [rendszerindító diagnosztikai](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplóiban van bejelentkezve. Ezekhez a naplókhoz való hozzáférés tulajdonosa és ellenőrzése az Azure virtuális gép rendszergazdája.

> [!CAUTION]
> A rendszer nem naplózza a konzol hozzáférési jelszavait. Ha azonban a konzolon futó parancsok jelszavakat, titkos kulcsokat, felhasználóneveket vagy bármilyen más, személyazonosításra alkalmas adatokat (PII) tartalmaznak, akkor azok a virtuális gép rendszerindítási diagnosztikai naplóiba kerülnek. Ezek a soros konzol visszagörgetési funkciójának megvalósításának részeként az összes többi látható szöveggel együtt lesznek megírva. Ezek a naplók körkörösek, és csak a diagnosztikai tárfiókolvasási jogosultsággal rendelkező személyek férhetnek hozzá. Ha titkos vagy személyazonosításra alkalmas adatokat vagy személyazonosításra alkalmas adatokat tartalmazó parancsokat használ, javasoljuk az SSH használatát, kivéve, ha a soros konzol feltétlenül szükséges.

### <a name="concurrent-usage"></a>Egyidejű használat
Ha egy felhasználó csatlakozik a soros konzolhoz, és egy másik felhasználó sikeresen hozzáférést kér ugyanahhoz a virtuális géphez, az első felhasználó leválik, a második felhasználó pedig ugyanahhoz a munkamenethez csatlakozik.

> [!CAUTION]
> Ez azt jelenti, hogy a kapcsolatbontásban szereplő felhasználó nem lesz kijelentkezve. A kijelentkezés kényszerítése a kapcsolat bontásakor (SIGHUP vagy hasonló mechanizmus használatával) még mindig az ütemtervben van. Windows esetén a Speciális felügyeleti konzol (SAC) automatikus időtúltöltést engedélyez; Linux esetén azonban konfigurálhatja a terminál időkiszolgáló beállítását. Ehhez adja hozzá `export TMOUT=600` a *.bash_profile* vagy *.profile* fájlba a konzolra való bejelentkezéshez használt felhasználó számára. Ez a beállítás 10 perc elteltével időzíti a munkamenetet.

## <a name="accessibility"></a>Kisegítő lehetőségek
A kisegítő lehetőségek az Azure Serial Console egyik fő fókusza. Ennek érdekében biztosítottuk, hogy a soros konzol teljes mértékben elérhető legyen.

### <a name="keyboard-navigation"></a>Billentyűzettel való navigáció
A **billentyűzet Tab** billentyűjével navigálhat az Azure Portalsoros konzolfelületén. A tartózkodási helye ki lesz emelve a képernyőn. Ha meg szeretné hagyni a soros konzolablak fókuszát, nyomja le a Ctrl**F6** **billentyűt**+a billentyűzeten.

### <a name="use-serial-console-with-a-screen-reader"></a>A Soros konzol használata képernyőolvasóval
A soros konzol beépített képernyőolvasó-támogatással rendelkezik. Ha bekapcsolt képernyőolvasóval navigál, a képernyőolvasó felolvashatja az aktuálisan kijelölt gomb helyettesítő szövegét.

## <a name="known-issues"></a>Ismert problémák
Tisztában vagyunk a soros konzollal és a virtuális gép operációs rendszerével kapcsolatos problémákkal. Íme egy lista ezekről a problémákról és a Linux virtuális gépek kockázatcsökkentési lépéseiről. Ezek a problémák és a megoldási vonatkoznak mind a virtuális gépek és a virtuális gép méretezési csoport példányai. Ha ezek nem egyeznek meg a látott hibával, tekintse meg a gyakori soros konzolszolgáltatási hibákat a [Common Serial Console hibáiban.](./serial-console-errors.md)

Probléma                           |   Kezelés
:---------------------------------|:--------------------------------------------|
Ha az **Enter** billentyűt lenyomja a kapcsolatszalag után, a bejelentkezési kérdés nem jelenik meg. | Előfordulhat, hogy a GRUB nincs megfelelően konfigurálva. Futtassa a `grub2-mkconfig -o /etc/grub2-efi.cfg` következő `grub2-mkconfig -o /etc/grub2.cfg`parancsokat: és/vagy . További információ: [Ütő meg nem csinál semmit](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a probléma akkor fordulhat elő, ha egyéni virtuális gép, megerősített készülék vagy GRUB konfigurációt futtat, amely miatt a Linux nem csatlakozik a soros porthoz.
A soros konzol szövege csak a képernyő méretének egy részét foglalja el (gyakran szövegszerkesztő használata után). | A soros konzolok nem támogatják az ablakméretéről szóló egyeztetést ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), ami azt jelenti, hogy nem lesz SIGWINCH jel a képernyőméret frissítéséhez, és a virtuális gép nem lesz tudomása a terminál méretéről. Telepítse az xterm vagy hasonló segédprogramot a `resize` parancs `resize`biztosításához, majd futtassa a parancsot.
A hosszú húrok beillesztése nem működik. | A soros konzol a terminálba beillesztett karakterláncok hosszát 2048 karakterre korlátozza, hogy megakadályozza a soros port sávszélességének túlterhelését.
Kiszámíthatatlan billentyűzetbevitel SLES BYOS-képeken. A billentyűzet bevitelét csak szórványosan ismeri fel a program. | Ez a probléma a Plymouth csomaggal van. Plymouth nem kell futtatni az Azure-ban, mert nincs szükség kezdőképre, és Plymouth zavarja a platform használatát soros konzol. Távolítsa el `sudo zypper remove plymouth` Plymouth-ot, majd indítsa újra. Másik lehetőségként módosítsa a GRUB konfiguráció kernelvonalát a sor végére fűzve. `plymouth.enable=0` Ezt úgy teheti meg, hogy [a rendszerindításkor szerkeszti a rendszerindítási bejegyzést,](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)vagy szerkeszti a GRUB_CMDLINE_LINUX `/etc/default/grub`sort, újraépíti a GRUB-ot a segítségével, `grub2-mkconfig -o /boot/grub2/grub.cfg`majd újraindítja.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése a GitHub-probléma létrehozásával a következő helyen: https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé előnyös), azserialhelp@microsoft.com visszajelzést küldhet a https://feedback.azure.comvirtuális gép kategóriáján keresztül vagy a virtuális gép kategóriájában.

**K. Támogatja a soros konzol a másolást/beillesztést?**

A. Igen. A Ctrl**Shift**+**C** és a Ctrl**Shift**+**V** **billentyűkombinációval**+ **Ctrl**+másolhat és illesztődjön be a terminálba.

**K. Használhatok soros konzolt SSH-kapcsolat helyett?**

A. Bár ez a használat technikailag lehetségesnek tűnhet, a soros konzolelsősorban hibaelhárító eszközként használható olyan helyzetekben, amikor az SSH-n keresztüli kapcsolat nem lehetséges. Javasoljuk, hogy ne használja a soros konzolt SSH csereként a következő okok miatt:

- A soros konzolnak nincs akkora sávszélessége, mint az SSH.The serial console doesn't have as much bandwidth as SSH. Mivel ez egy csak szöveges kapcsolat, több GUI-nehéz kölcsönhatások nehéz.
- A soros konzolok hoz való hozzáférés jelenleg csak felhasználónév vel és jelszóval lehetséges. Mivel az SSH-kulcsok sokkal biztonságosabbak, mint a felhasználónév/jelszó kombinációk, a bejelentkezés biztonsági szempontból az SSH-t javasoljuk soros konzolon keresztül.

**K. Ki engedélyezheti vagy tilthatja le a soros konzolt az előfizetésemhez?**

A. A soros konzol előfizetési szintű engedélyezéséhez vagy letiltásához írási engedéllyel kell rendelkeznie az előfizetéshez. Az írási engedéllyel rendelkező szerepkörök közé tartoznak a rendszergazdai vagy tulajdonosi szerepkörök. Az egyéni szerepkörök írási engedéllyel is rendelkezhetnek.

**K. Ki férhet hozzá a virtuális gép/virtuálisgép-méretezési csoport soros konzoljához?**

A. A virtuális gép vagy a virtuális gép méretezési beállítása a virtuális gép vagy a virtuális gép méretezési csoport hoz létre a virtuális gép közreműködői szerepkörrel vagy magasabb szintű.

**K. A soros konzolnem jelenít meg semmit, mit tegyek?**

A. A rendszerkép valószínűleg helytelenül van konfigurálva a soros konzolhoz való hozzáféréshez. A lemezkép soros konzol engedélyezésére való konfigurálásáról a [Serial console Linux disztribúció elérhetősége című](#serial-console-linux-distribution-availability)témakörben talál további információt.

**K. Elérhető a soros konzol a virtuálisgép-méretezési csoportokhoz?**

A. igen! A [virtuálisgép-méretezési készletek soros konzolja](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**K. Ha csak SSH-kulcshitelesítéssel állítom be a virtuális gép vagy a virtuális gép méretezési készletét, továbbra is használhatom a soros konzolt a virtuális gép/virtuálisgép méretezési példányához való csatlakozáshoz?**

A. Igen. Mivel a soros konzolhoz nincs szükség SSH-kulcsokra, csak egy felhasználónév/jelszó kombinációt kell beállítania. Ehhez válassza a **Jelszó alaphelyzetbe állítása** az Azure Portalon, és használja ezeket a hitelesítő adatokat a soros konzolra való bejelentkezéshez.

## <a name="next-steps"></a>További lépések
* Használja a soros konzolt a [GRUB és az egyfelhasználós mód eléréséhez.](serial-console-grub-single-user-mode.md)
* Használja a soros konzolt [NMI és SysRq hívásokhoz.](serial-console-nmi-sysrq.md)
* Megtanulják, hogyan kell használni a soros konzol, hogy [grub különböző distros](serial-console-grub-proactive-configuration.md)
* A soros konzol [windowsos virtuális gépekhez](../windows/serial-console.md)is elérhető.
* További információ a [rendszerindítási diagnosztikáról](boot-diagnostics.md).

