---
title: Azure soros konzol Linux rendszerhez | Microsoft Docs
description: Kétirányú soros konzol az Azure Virtual Machines és Virtual Machine Scale Setshoz.
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
ms.openlocfilehash: 1074c4bc561236039e6ee55ef2df4fc8bd8dbbfc
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772516"
---
# <a name="azure-serial-console-for-linux"></a>Azure soros konzol Linux rendszerhez

A Azure Portal soros konzolja hozzáférést biztosít a linuxos virtuális gépekhez (VM) és virtuálisgép-méretezési csoport példányaihoz. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának ttyS0 soros portjához csatlakozik, és hozzáférést biztosít a hálózattól vagy az operációs rendszer állapotától függetlenül. A soros konzol csak a Azure Portal használatával érhető el, és csak azon felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuálisgép-méretezési csoport számára a közreműködő vagy annál magasabb hozzáférési szerepkörrel rendelkeznek.

A soros konzol a virtuális gépek és a virtuálisgép-méretezési csoport példányai esetében is ugyanúgy működik. Ebben a doc-ban a virtuális gépekre vonatkozó összes említés implicit módon tartalmazza a virtuálisgép-méretezési csoport példányait, hacsak másként nincs megadva.

A Windows soros konzoljának dokumentációját lásd: [a Windows soros konzolja](../windows/serial-console.md).

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban és a nyilvános előzetes verzióban Azure Government. Az Azure China Cloud-ban még nem érhető el.


## <a name="prerequisites"></a>Előfeltételek

- A virtuális gép vagy virtuálisgép-méretezési csoport példányának a Resource Management-alapú üzemi modellt kell használnia. A klasszikus központi telepítések nem támogatottak.

- A soros konzolt használó fióknak rendelkeznie kell a [virtuálisgép-közreműködő szerepkörrel a virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) géphez és a [rendszerindítási diagnosztika](boot-diagnostics.md) Storage-fiókhoz.

- A virtuális gép vagy virtuálisgép-méretezési csoport példányának jelszó-alapú felhasználónak kell lennie. Létrehozhat egyet a virtuálisgép-hozzáférési bővítmény [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkciójával. Válassza a **jelszó alaphelyzetbe állítása** lehetőséget a **támogatás + hibaelhárítás** szakaszban.

- A virtuális gép vagy virtuálisgép-méretezési csoport példányának engedélyezve kell lennie a [rendszerindítási diagnosztika](boot-diagnostics.md) szolgáltatásnak.

    ![Rendszerindítási diagnosztika beállításai](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- A Linux-disztribúciókkal kapcsolatos beállításokért lásd: [Serial Console Linux-disztribúció rendelkezésre állása](#serial-console-linux-distribution-availability).

- A virtuális gép vagy virtuálisgép-méretezési csoport példányát a `ttys0`soros kimenetére kell konfigurálni. Ez az alapértelmezett Azure-lemezképek esetében, de ezt az egyéni rendszerképeken érdemes megtekinteni. Részletek [alább](#custom-linux-images).


> [!NOTE]
> A soros konzolon helyi felhasználónak kell lennie egy konfigurált jelszóval. A csak SSH nyilvános kulccsal konfigurált virtuális gépek vagy virtuálisgép-méretezési csoportok nem tudnak bejelentkezni a soros konzolra. Ha jelszót használó helyi felhasználót szeretne létrehozni, használja a [VMAccess bővítményt](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), amely a portálon elérhető Azure Portal **jelszó alaphelyzetbe állítása** lehetőség kiválasztásával, valamint a jelszóval rendelkező helyi felhasználó létrehozásával érhető el.
> A fiók rendszergazdai jelszavát a GRUB használatával is alaphelyzetbe állíthatja, ha az [egyfelhasználós módba szeretné indítani a rendszerindítást](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Soros konzol Linux-disztribúció rendelkezésre állása
Ahhoz, hogy a soros konzol megfelelően működjön, a vendég operációs rendszert úgy kell konfigurálni, hogy a konzol üzeneteinek olvasására és írására a soros portra legyen konfigurálva. A legtöbb [támogatott Azure Linux-disztribúcióhoz](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) alapértelmezés szerint a soros konzol van konfigurálva. A Azure Portal támogatás és **Hibaelhárítás** területén a **Serial Console** kiválasztása lehetőséget biztosít a soros konzolhoz való hozzáféréshez.

> [!NOTE]
> Ha nem lát semmit a soros konzolon, győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gépen. Az **ENTER billentyű** lenyomásával gyakran problémák merülnek fel, ha a soros konzolon semmi sem jelenik meg.

Terjesztés      | Csatlakozás a soros konzolhoz
:-----------|:---------------------
Red Hat Enterprise Linux    | Serial console hozzáférés alapértelmezés szerint engedélyezve van.
CentOS      | Serial console hozzáférés alapértelmezés szerint engedélyezve van.
Debian      | Serial console hozzáférés alapértelmezés szerint engedélyezve van.
Ubuntu      | Serial console hozzáférés alapértelmezés szerint engedélyezve van.
CoreOS      | Serial console hozzáférés alapértelmezés szerint engedélyezve van.
SUSE        | Az Azure-ban elérhető újabb SLES-lemezképek alapértelmezés szerint engedélyezve vannak a soros konzolon. Ha az Azure-ban a SLES régebbi verzióit (10 vagy korábbi verziót) használja, akkor a soros konzol engedélyezéséhez tekintse meg a [Tudásbázis cikkét](https://www.novell.com/support/kb/doc.php?id=3456486) .
Oracle Linux        | Serial console hozzáférés alapértelmezés szerint engedélyezve van.

### <a name="custom-linux-images"></a>Egyéni Linux-rendszerképek
Ha engedélyezni szeretné a soros konzolt az egyéni linuxos virtuálisgép-rendszerkép számára, engedélyezze a konzol hozzáférését a */etc/inittab* , hogy `ttyS0`-terminált futtasson. Például: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Előfordulhat, hogy egy getty-t is el kell indítania a ttyS0-on. Ez a `systemctl start serial-getty@ttyS0.service`használatával végezhető el.

A ttyS0 a soros kimenethez célként is hozzá kell adni. Ha szeretne többet megtudni arról, hogyan konfigurálhat egyéni rendszerképeket a soros konzollal, tekintse meg az általános rendszerkövetelményeket a [linuxos virtuális merevlemez létrehozása és feltöltése az Azure-ban](https://aka.ms/createuploadvhd#general-linux-system-requirements)című témakörben.

Ha egyéni kernelt hoz létre, érdemes lehet engedélyezni a következő kernel-jelzőket: `CONFIG_SERIAL_8250=y` és `CONFIG_MAGIC_SYSRQ_SERIAL=y`. A konfigurációs fájl általában a */boot/* útvonalon található.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>A soros konzol elérésének gyakori forgatókönyvei

Alkalmazási helyzet          | Műveletek a soros konzolon
:------------------|:-----------------------------------------
Törött *FSTAB* -fájl | Nyomja le az **ENTER** billentyűt a folytatáshoz, majd egy szövegszerkesztővel javítsa ki az *FSTAB* fájlt. Előfordulhat, hogy egyfelhasználós módban kell lennie. További információért lásd a soros konzol című szakaszt, amely [bemutatja, hogyan lehet az fstab-problémákat kijavítani](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) és a [soros konzol használatával hozzáférni a grub és az egyfelhasználós üzemmódhoz](serial-console-grub-single-user-mode.md).
Helytelen tűzfalszabályok |  Ha úgy konfigurálta az iptables-t, hogy letiltsa az SSH-kapcsolatot, akkor a soros konzol használatával az SSH használata nélkül is dolgozhat a virtuális géppel. További részletek az [iptables man oldalon](https://linux.die.net/man/8/iptables)találhatók.<br>Hasonlóképpen, ha a tűzfal blokkolja az SSH-hozzáférést, a virtuális gépet a soros konzolon keresztül érheti el, és újrakonfigurálhatja a tűzfalat. További részletek a tűzfalon keresztül elérhető [dokumentációban](https://firewalld.org/documentation/)találhatók.
Fájlrendszer-sérülés/-ellenőrzési | Az Azure Linux rendszerű virtuális gép soros konzol szakasza nem indítható el, mert a soros konzol használatával a sérült fájlrendszerek hibaelhárításával kapcsolatos további részletek a [fájlrendszer hibája miatt](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) megtalálhatók.
SSH-konfigurációs problémák | Nyissa meg a soros konzolt, és módosítsa a beállításokat. A Serial console a virtuális gép SSH-konfigurációjától függetlenül is használható, mivel nincs szükség hálózati kapcsolatra a virtuális géppel. A hibaelhárítási útmutató az [olyan Azure Linux rendszerű virtuális gépek SSH-kapcsolatainak hibaelhárítását ismerteti, amelyek meghibásodnak, hibákkal vagy visszautasítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)rendelkeznek. További részletek a Linux rendszerű [virtuális gépek Azure-beli csatlakoztatásával kapcsolatos problémák részletes SSH-hibaelhárítási lépéseiben](./detailed-troubleshoot-ssh-connection.md) olvashatók.
A rendszerbetöltővel való interakció | Indítsa újra a virtuális gépet a soros konzol paneljén a GRUB eléréséhez a Linux rendszerű virtuális gépen. További részletek és a disztribúcióra vonatkozó információk: [a soros konzol használata a grub és az single user mód eléréséhez](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>A soros konzol letiltása

Alapértelmezés szerint minden előfizetéshez engedélyezve van a soros konzolhoz való hozzáférés. A soros konzolt letilthatja az előfizetés szintjén vagy a VM/virtuálisgép-méretezési csoport szintjén. Részletes útmutatásért látogasson el [Az Azure soros konzol engedélyezése és letiltása](./serial-console-enable-disable.md)című részhez.

## <a name="serial-console-security"></a>Serial console biztonság

### <a name="access-security"></a>Hozzáférés biztonsága
A soros konzolhoz való hozzáférés csak azokra a felhasználókra korlátozódik, akik a virtuális gép [közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy magasabb hozzáférési szerepkörével rendelkeznek. Ha a Azure Active Directory bérlő a többtényezős hitelesítés (MFA) használatát igényli, akkor a soros konzolhoz való hozzáféréshez is szüksége lesz az MFA-ra, mivel a soros konzol hozzáférése a [Azure Portalon](https://portal.azure.com)keresztül történik.

### <a name="channel-security"></a>Csatorna biztonsága
A visszaadott és oda továbbított összes adatforgalom titkosítva van a huzalon.

### <a name="audit-logs"></a>Naplók
A soros konzolhoz való összes hozzáférés jelenleg a virtuális gép [rendszerindítási diagnosztikai](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplóiban van naplózva. A naplókhoz való hozzáférést az Azure-beli virtuális gép rendszergazdája birtokolja és felügyeli.

> [!CAUTION]
> Nincsenek naplózva a konzol hozzáférési jelszava. Ha azonban a konzolon belül futnak a jelszavak, a titkos kódok, a felhasználónevek vagy a személyazonosításra alkalmas adatok bármilyen más formája, akkor azokat a rendszer a virtuális gép rendszerindítási diagnosztikai naplóiba írja. A soros konzol görgetőgomb-visszagörgetési funkciója megvalósításának részeként minden más látható szöveggel együtt lesznek írva. Ezek a naplók körkörösek, és csak azok a személyek férhetnek hozzájuk, akik olvasási engedéllyel rendelkeznek a diagnosztikai Storage-fiókhoz. Ha olyan adatkezelői parancsokat helyez üzembe, amelyek titkokat vagy személyes adatokat tartalmaznak, javasoljuk, hogy használja az SSH-t, hacsak a soros konzol nem feltétlenül szükséges.

### <a name="concurrent-usage"></a>Egyidejű használat
Ha egy felhasználó csatlakozik a soros konzolhoz, és egy másik felhasználó sikeresen kér hozzáférést ugyanahhoz a virtuális géphez, akkor az első felhasználó le lesz választva, és a második felhasználó ugyanahhoz a munkamenethez csatlakozik.

> [!CAUTION]
> Ez azt jelenti, hogy a leválasztott felhasználó nem lesz kijelentkezve. A Kilépés (SIGHUP vagy hasonló mechanizmus használatával) leválasztásának lehetősége továbbra is az ütemterven látható. A Windows rendszerben automatikus időtúllépés van engedélyezve a speciális felügyeleti konzolon (SAC); a Linux esetében azonban beállíthatja a terminál időtúllépési beállítását. Ehhez vegye fel `export TMOUT=600` a *. bash_profile* vagy a *. profil* fájlban a konzolra való bejelentkezéshez használt felhasználóhoz. Ez a beállítás 10 perc elteltével időtúllépést eredményez a munkamenetben.

## <a name="accessibility"></a>Kisegítő lehetőségek
A kisegítő lehetőségek az Azure soros konzol egyik fő témája. Ennek érdekében biztosítjuk, hogy a soros konzol teljes mértékben elérhető legyen.

### <a name="keyboard-navigation"></a>Navigálás billentyűzettel
A billentyűzet **Tab** billentyűjét használva navigáljon a soros konzol felületén a Azure Portal. A hely ki lesz emelve a képernyőn. A soros konzol ablakának fókuszának elhagyásához nyomja le a **Ctrl**+**F6** billentyűt a billentyűzeten.

### <a name="use-serial-console-with-a-screen-reader"></a>Soros konzol használata képernyőolvasóval
A soros konzol beépített képernyőolvasó-támogatással rendelkezik. A bekapcsolt képernyőolvasóval való navigálás lehetővé teszi, hogy az aktuálisan kijelölt gombhoz tartozó ALT szöveg hangosan felolvassa a képernyőolvasót.

## <a name="known-issues"></a>Ismert problémák
Tisztában vagyunk a soros konzollal és a virtuális gép operációs rendszerével kapcsolatos problémákkal. Ezen problémák listája és a Linux rendszerű virtuális gépek enyhítésének lépései. Ezek a problémák és enyhítések mind a virtuális gépek, mind a virtuálisgép-méretezési csoport példányai esetében érvényesek. Ha ezek nem egyeznek a megjelenő hibával, tekintse meg a soros konzol gyakori [hibáival](./serial-console-errors.md)kapcsolatos általános hibákat.

Probléma                           |   Kezelés
:---------------------------------|:--------------------------------------------|
Az **ENTER** billentyű lenyomása után a kapcsolódási szalagcím nem eredményezi a bejelentkezési üzenet megjelenítését. | További információ: [ütő ENTER do Nothing](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a probléma akkor fordulhat elő, ha egy egyéni virtuális gépet, megerősített készüléket vagy GRUB-konfigurációt futtat, amely miatt a Linux nem tud csatlakozni a soros porthoz.
Serial console a szöveg csak a képernyőméret egy részét veszi fel (általában egy szövegszerkesztő használata után). | A soros konzolok nem támogatják az ablakméret ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)) egyeztetését, ami azt jelenti, hogy a rendszer nem küld SIGWINCH-jelet a frissítési képernyő méretétől, és a virtuális gép nem ismeri a terminál méretét. Telepítse a xterm-t vagy egy hasonló segédprogramot a `resize` parancs megadásához, majd futtassa `resize`.
A hosszú karakterláncok beillesztése nem működik. | A soros konzol korlátozza a terminálba beillesztett sztringek hosszát 2048 karakterre, hogy megakadályozza a soros port sávszélességének túlterhelését.
Kiszámíthatatlan billentyűzet-bevitel a SLES BYOS-lemezképekben. A billentyűzet bemenete csak szórványosan ismerhető fel. | Ez a Plymouth-csomaggal kapcsolatos probléma. A Plymouth nem futtatható az Azure-ban, mert nincs szükség a splash-képernyőre, és az nem zavarja a platformot a soros konzol használatának. Távolítsa el a Plymouth-t `sudo zypper remove plymouth`, majd indítsa újra. Azt is megteheti, hogy a GRUB-konfiguráció kernel-vonalát úgy módosítja, hogy hozzáfűzi `plymouth.enable=0` a sor végéhez. Ezt úgy teheti meg, hogy a rendszerindítási [bejegyzést a rendszerindítás során szerkeszti](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), vagy a `/etc/default/grub`GRUB_CMDLINE_LINUX sorát szerkeszti, újraépíti a grub-t a `grub2-mkconfig -o /boot/grub2/grub.cfg`használatával, majd újraindul.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K. Hogyan küldhetek visszajelzést?**

A. Küldjön visszajelzést a GitHub-probléma létrehozásához https://aka.ms/serialconsolefeedback címen. Másik lehetőségként (kevésbé előnyben részesített) küldhet visszajelzést azserialhelp@microsoft.com vagy https://feedback.azure.com virtuális gép kategóriájában.

**K. támogatja a soros konzol a másolást és beillesztést?**

A. Igen. A **CTRL** billentyűt lenyomva tartva a+**SHIFT**+**C** és **CTRL**+**SHIFT**+**V** billentyűkombinációt a terminálba való másoláshoz és beillesztéshez.

**K. használhatok soros konzolt SSH-kapcsolat helyett?**

A. Habár ez a használat technikailag lehetségesnek tűnhet, a soros konzol elsődlegesen hibaelhárítási eszközként használható olyan helyzetekben, amikor az SSH-n keresztüli kapcsolat nem lehetséges. Javasoljuk, hogy a soros konzolt SSH-helyettesítéssel használja a következő okok miatt:

- A soros konzol nem rendelkezik annyi sávszélességgel, mint az SSH. Mivel ez egy csak szöveges kapcsolat, a grafikus felhasználói felület több nehéz interakciót okoz.
- Serial console hozzáférés jelenleg csak Felhasználónév és jelszó használatával lehetséges. Mivel az SSH-kulcsok sokkal biztonságosabbak, mint a Felhasználónév/jelszó kombinációk, a bejelentkezési biztonsági szempontból javasoljuk, hogy az SSH-t a soros konzolon keresztül.

**K. ki engedélyezheti vagy letilthatja az előfizetéshez tartozó soros konzolt?**

A. A soros konzol előfizetés szintű engedélyezéséhez vagy letiltásához írási engedéllyel kell rendelkeznie az előfizetéshez. Az írási engedéllyel rendelkező szerepkörök rendszergazdai vagy tulajdonosi szerepköröket is tartalmaznak. Az egyéni szerepkörök rendelkezhetnek írási engedélyekkel is.

**K. ki férhet hozzá a virtuális gép/virtuálisgép-méretezési csoport soros konzolján?**

A. A soros konzol eléréséhez a virtuális gép közreműködői szerepköre vagy a virtuális gép vagy virtuálisgép-méretezési csoport számára is magasabbnak kell lennie.

**K. a soros konzol nem jelenít meg semmit, mit tegyek?**

A. A rendszerkép valószínűleg helytelenül van konfigurálva a soros konzolhoz való hozzáféréshez. További információ a rendszerképek a soros konzol engedélyezéséről való konfigurálásáról: [Serial Console Linux-disztribúció rendelkezésre állása](#serial-console-linux-distribution-availability).

**K. elérhető a soros konzol a virtuálisgép-méretezési csoportokhoz?**

A. igen! Lásd [a Virtual Machine Scale sets soros konzolját](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**K. Ha a virtuális gép vagy virtuálisgép-méretezési csoport csak SSH-kulcsos hitelesítéssel van beállítva, akkor is használhatom a soros konzolt a virtuális gép/virtuálisgép-méretezési csoport példányaihoz való kapcsolódáshoz?**

A. Igen. Mivel a soros konzol nem igényel SSH-kulcsokat, csak Felhasználónév/jelszó kombinációt kell beállítania. Ehhez válassza a **jelszó alaphelyzetbe állítása** lehetőséget a Azure Portalban, és használja ezeket a hitelesítő adatokat a soros konzolra való bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések
* A soros konzollal [elérheti a grub és az egyfelhasználós üzemmódot](serial-console-grub-single-user-mode.md).
* A soros konzolon [NMI és SYSRQ hívásokat](serial-console-nmi-sysrq.md)használhat.
* Megtudhatja, hogyan [engedélyezheti a grub-t különböző disztribúciókban](serial-console-grub-proactive-configuration.md) a soros konzol használatával
* A soros konzol a Windows rendszerű [virtuális gépekhez](../windows/serial-console.md)is elérhető.
* További információ a [rendszerindítási diagnosztika](boot-diagnostics.md)szolgáltatásról.

