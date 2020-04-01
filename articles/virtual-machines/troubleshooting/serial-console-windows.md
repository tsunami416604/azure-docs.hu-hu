---
title: Azure Serial Console for Windows | Microsoft dokumentumok
description: Kétirányú soros konzol az Azure virtuális gépek és a virtuális gép méretezési készletek.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 68089a86b8b832638abd30aa7c36aa1c5bd84225
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410122"
---
# <a name="azure-serial-console-for-windows"></a>Azure soros konzol windowshoz

Az Azure Portalon található soros konzol hozzáférést biztosít a Windows virtuális gépek (VM-ek) és a virtuális gépek méretezési példányai szöveges alapú konzoljához. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának COM1 soros portjához csatlakozik, és a hálózattól vagy az operációs rendszer állapotától független hozzáférést biztosít hozzá. A soros konzol csak az Azure Portalon keresztül érhető el, és csak azok a felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuális gép méretezési készlethez tartozó közreműködői vagy magasabb szintű hozzáférési szerepkörrel rendelkeznek.

Soros konzol működik ugyanúgy a virtuális gépek és a virtuális gép méretezési készlet példányok. Ebben a dokumentumban a virtuális gépek minden említése implicit módon tartalmazza a virtuális gép méretezési példányait, kivéve, ha másként van feltüntetve.

A Linuxhoz tartozó soros konzoldokumentációról az [Azure Serial Console for Linux (Linux) témakörben olvashat.](serial-console-linux.md)

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban és az Azure Government nyilvános előzetes verzióban. Még nem érhető el az Azure China felhőben.


## <a name="prerequisites"></a>Előfeltételek

* A virtuális gép vagy a virtuálisgép méretezési csoport példányának az erőforrás-kezelési telepítési modellt kell használnia. Klasszikus központi telepítések nem támogatottak.

- A soros konzolt használó fióknak rendelkeznie kell a virtuális gép virtuális gép hez és a [rendszerindító diagnosztikai](boot-diagnostics.md) tárfiókhoz használt [Virtuálisgép közreműködői szerepkörével.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

- A virtuális gép vagy a virtuálisgép méretezési csoport példányának jelszóalapú felhasználóval kell rendelkeznie. Létrehozhat egyet a virtuális gép hozzáférési bővítmény [jelszó-visszaállítási](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkciójával. Válassza a **Jelszó alaphelyzetbe állítása** lehetőséget a **Támogatás + hibaelhárítás** szakaszban.

* A virtuális gép méretezési csoport példányának rendelkeznie kell [a rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezésével.

    ![Rendszerindítási diagnosztikai beállítások](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>A Soros konzol funkcióinak engedélyezése a Windows Server rendszerben

> [!NOTE]
> Ha nem lát semmit a soros konzolon, győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép vagy a virtuális gép méretezési csoport.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>A soros konzol engedélyezése egyéni vagy régebbi képeken
Az Azure-ban az újabb Windows Server-lemezképek alapértelmezés szerint engedélyezve vannak a [Speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC). A SAC a Windows kiszolgálói verzióiban támogatott, de nem érhető el az ügyfélverziókban (például Windows 10, Windows 8 vagy Windows 7).

Régebbi Windows Server-lemezképek (2018 februárja előtt létrehozott) esetén automatikusan engedélyezheti a soros konzolt az Azure Portal futtatási parancsszolgáltatásán keresztül. Az Azure Portalon válassza a **Futtatás parancsot,** majd válassza ki az **EnableEMS** nevű parancsot a listából.

![Parancslista futtatása](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Másik lehetőségként a 2018 februárja előtt létrehozott Windows virtuális gépek/virtuálisgép-méretezési csoport soros konzoljának manuális engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Csatlakozás a Windows virtuális géphez a Távoli asztal használatával
1. A felügyeleti parancssorból futtassa a következő parancsokat:
    - `bcdedit /ems {current} on`, `bcdedit /ems '{current}' on` vagy ha PowerShellt használ
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Indítsa újra a SAC-konzol engedélyezéséhez szükséges rendszert.

    ![SAC konzol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Szükség esetén a SAC offline módban is engedélyezhető:

1. Csatolja azt az windows lemezt, amelyhez a SAC-ot adatlemezként konfigurálni szeretné a meglévő virtuális géphez.

1. A felügyeleti parancssorból futtassa a következő parancsokat:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Honnan tudhatom, hogy a SAC engedélyezve van-e?

Ha a [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve, a soros konzol nem jeleníti meg a SAC-kérdést. Bizonyos esetekben a virtuális gép állapotának információjelenik meg, és más esetekben üres. Ha 2018 februárja előtt létrehozott Windows Server-lemezképet használ, a SAC valószínűleg nem lesz engedélyezve.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>A Windows rendszerindítási menüengedélyezése a soros konzolon

Ha engedélyeznie kell a Windows rendszertöltő üzenetének megjelenítését a soros konzolon, a következő további beállításokat adhatja hozzá a rendszerindítási konfigurációs adatokhoz. További információ: [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Csatlakozzon a Windows virtuális géphez vagy a virtuálisgép-méretezési példányhoz a Távoli asztal használatával.

1. A felügyeleti parancssorból futtassa a következő parancsokat:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Indítsa újra a rendszer indítását a rendszerindítási menü engedélyezéséhez

> [!NOTE]
> A rendszerindító kezelő menü megjelenítéséhez beállított időmeghosszabbítás hatással lesz az operációs rendszer rendszerindítási idejére. Ha úgy gondolja, hogy a 10 másodperces időtúlérték túl rövid vagy túl hosszú, állítsa be egy másik értékre.

## <a name="use-serial-console"></a>Soros konzol használata

### <a name="use-cmd-or-powershell-in-serial-console"></a>CMD vagy PowerShell használata a soros konzolon

1. Csatlakozzon a soros konzolhoz. Ha sikeresen csatlakozik, a kérdés **SAC>: **

    ![Csatlakozás a SAC-hoz](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.    Adja `cmd` meg, hogy hozzon létre egy csatornát, amely egy CMD-példány.

1.    A `ch -si 1` CMD-példányt futtató csatornára való váltáshoz írja be vagy nyomja `<esc>+<tab>` le a billentyűparancsokat.

1.    Nyomja le az **Enter**billentyűt, és írja be a rendszergazdai engedélyekkel rendelkező bejelentkezési hitelesítő adatokat.

1.    Miután megadta az érvényes hitelesítő adatokat, megnyílik a CMD-példány.

1.    PowerShell-példány indításához `PowerShell` írja be a CMD-példányba, majd nyomja le az **Enter billentyűt.**

    ![PowerShell-példány megnyitása](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>A soros konzol használata NMI-hívásokhoz
A nem maszkolható megszakítás (NMI) olyan jelet hoz létre, amelyet a virtuális gépen lévő szoftverek nem hagynak figyelmen kívül. Korábban az NM-eket arra használták, hogy figyeljék a hardverproblémákat a konkrét válaszidőt megkövetelő rendszereken. Napjainkban a programozók és a rendszergazdák gyakran használják az NMI-t a nem válaszoló rendszerek hibakeresésére vagy hibaelhárítására.

A soros konzol segítségével nmi-t küldhet egy Azure virtuális gépnek a parancssávon lévő billentyűzet ikon használatával. Az NMI kézbesítése után a virtuális gép konfigurációja szabályozza a rendszer válaszát. A Windows beállítható úgy, hogy összeomlik, és nmi-t kap, és memóriaképfájlt hozzon létre.

![NMI küldése](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

A Windows összeomlási memóriaképfájl nmi-betöltése esetén történő létrehozásáról a [ , Hogyan hozhat létre összeomlási memóriaképfájlt NMI használatával.](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

### <a name="use-function-keys-in-serial-console"></a>Funkcióbillentyűk használata soros konzolban
A funkcióbillentyűk engedélyezve vannak a windowsos virtuális gépek soros konzoljához. A soros konzol legördülő menüjében az F8 biztosítja a speciális rendszerindítási beállítások menübe való egyszerű belépés kényelmét, de a soros konzol kompatibilis az összes többi funkcióbillentyűvel. Előfordulhat, hogy meg kell nyomnia **az Fn** + **F1** (vagy F2, F3 stb.) billentyűt a billentyűzeten attól függően, hogy a számítógépről használja-e a soros konzolt.

### <a name="use-wsl-in-serial-console"></a>A WSL használata soros konzolon
A Windows Alsystem for Linux (WSL) engedélyezve van a Windows Server 2019 vagy újabb rendszerhez, így a WSL-t is engedélyezheti a soros konzolon való használatra, ha Windows Server 2019-es vagy újabb rendszert használ. Ez hasznos lehet azoknak a felhasználóknak, akik ismerik a Linux parancsokat. A WsL for Windows Server engedélyezéséről a telepítési útmutatóban talál [útmutatást.](https://docs.microsoft.com/windows/wsl/install-on-server)

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>A Windows VM/virtuálisgép méretezési példányának újraindítása a Soros konzolon
A soros konzolon belüli újraindítást a bekapcsológombra való navigálással és a "Virtuális gép újraindítása" gombra kattintva kezdeményezheti. Ez elindítja a virtuális gép újraindítását, és megjelenik egy értesítés az Azure Portalon az újraindításról.

Ez olyan helyzetekben hasznos, amikor a soros konzol élményének elhagyása nélkül szeretné elérni a rendszerindítási menüt.

![Windows soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

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
> A rendszer nem naplózza a konzol hozzáférési jelszavait. Ha azonban a konzolon futó parancsok jelszavakat, titkos kulcsokat, felhasználóneveket vagy bármilyen más, személyazonosításra alkalmas adatokat (PII) tartalmaznak, akkor azok a virtuális gép rendszerindítási diagnosztikai naplóiba kerülnek. Ezek a soros konzol visszagörgetési funkciójának megvalósításának részeként az összes többi látható szöveggel együtt lesznek megírva. Ezek a naplók körkörösek, és csak a diagnosztikai tárfiókolvasási jogosultsággal rendelkező személyek férhetnek hozzá. Javasoljuk azonban, hogy kövesse a távoli asztal használatának ajánlott gyakorlatát minden olyan funkcióhoz, amely titkokat és/vagy személyazonosításra alkalmas adatokat tartalmazhat.

### <a name="concurrent-usage"></a>Egyidejű használat
Ha egy felhasználó csatlakozik a soros konzolhoz, és egy másik felhasználó sikeresen hozzáférést kér ugyanahhoz a virtuális géphez, az első felhasználó leválik, a második felhasználó pedig ugyanahhoz a munkamenethez csatlakozik.

> [!CAUTION]
> Ez azt jelenti, hogy a kapcsolatbontásban szereplő felhasználó nem lesz kijelentkezve. A kijelentkezés kényszerítése a kapcsolat bontásakor (SIGHUP vagy hasonló mechanizmus használatával) még mindig szerepel az ütemtervben. A Windows rendszerben a SAC automatikus időtúltöltést engedélyez; Linux esetén konfigurálhatja a terminál időkiszolgáló beállítását.

## <a name="accessibility"></a>Kisegítő lehetőségek
A kisegítő lehetőségek az Azure soros konzoljának egyik fő fókusza. Ennek érdekében biztosítottuk, hogy a soros konzol elérhető legyen a látás- és halláskárosultak, valamint az egér használatára esetleg nem képes személyek számára.

### <a name="keyboard-navigation"></a>Billentyűzettel való navigáció
A **billentyűzet Tab** billentyűjével navigálhat az Azure Portalsoros konzolfelületén. A tartózkodási helye ki lesz emelve a képernyőn. Ha meg szeretné hagyni a soros konzolablak fókuszát, nyomja le a Ctrl**F6** **billentyűt**+a billentyűzeten.

### <a name="use-the-serial-console-with-a-screen-reader"></a>A soros konzol használata képernyőolvasóval
A soros konzol beépített képernyőolvasó-támogatással rendelkezik. Ha bekapcsolt képernyőolvasóval navigál, a képernyőolvasó felolvashatja az aktuálisan kijelölt gomb helyettesítő szövegét.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Gyakori forgatókönyvek a soros konzol eléréséhez

Forgatókönyv          | Műveletek a soros konzolon
:------------------|:-----------------------------------------
Helytelen tűzfalszabályok | A soros konzol elérése és a Windows tűzfalszabályainak javítása.
Fájlrendszer sérülése/ellenőrzése | Hozzáférés a soros konzolhoz és a fájlrendszer helyreállítása.
RDP-konfigurációs problémák | Hozzáférés a soros konzolhoz, és módosíthatja a beállításokat. További információt az [RDP dokumentációjában](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)talál.
Hálózati zárolási rendszer | A rendszer kezeléséhez a soros konzol az Azure Portalon. Egyes hálózati parancsok a Windows parancsaiban [találhatók: CMD és PowerShell](serial-console-cmd-ps-commands.md).
Interakció a rendszertöltővel | A BCD elérése a soros konzolon keresztül. További információt [a Windows rendszerindítási menüengedélyezése a soros konzolon című témakörben talál.](#enable-the-windows-boot-menu-in-the-serial-console)

## <a name="known-issues"></a>Ismert problémák
Tisztában vagyunk a soros konzollal és a virtuális gép operációs rendszerével kapcsolatos problémákkal. Az alábbiakban felsorolják ezeket a problémákat és a Windows virtuális gépek enyhülésének lépéseit. Ezek a problémák és a megoldási vonatkoznak mind a virtuális gépek és a virtuális gép méretezési csoport példányai. Ha ezek nem egyeznek meg a látott hibával, tekintse meg a gyakori soros konzolszolgáltatási hibákat a [Common Serial Console hibáiban.](./serial-console-errors.md)

Probléma                             |   Kezelés
:---------------------------------|:--------------------------------------------|
Ha az **Enter** billentyűt lenyomja a kapcsolatszalag után, a bejelentkezési kérdés nem jelenik meg. | További információ: [Ütő meg nem csinál semmit](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a hiba akkor fordulhat elő, ha egyéni virtuális gép, megerősített készülék vagy rendszerindítási konfigurációt futtat, amely miatt a Windows nem tud megfelelően csatlakozni a soros porthoz. Ez a hiba akkor is előfordul, ha Windows 10 virtuális gépet futtat, mert csak a Windows Server virtuális gépek vannak beállítva az EMS engedélyezésére.
Csak az állapotadatok jelennek meg, amikor Windows virtuális géphez csatlakozik| Ez a hiba akkor fordul elő, ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezképhez. A [SOROS-konzol engedélyezése egyéni vagy régebbi lemezképeken](#enable-the-serial-console-in-custom-or-older-images) című témakörben ismerteti, hogyan engedélyezheti manuálisan a SAC-ot a Windows virtuális gépen. További információt a [Windows állapotjelzései című témakörben talál.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
A SAC nem veszi fel a teljes soros konzol területet a böngészőben | Ez egy ismert probléma a Windows és a terminál emulátor. Mindkét csapattal nyomon követjük ezt a problémát, de egyelőre nincs enyhítés.
Nem lehet beírni a SAC parancssorba, ha a kernel hibakeresése engedélyezve van. | RDP a virtuális `bcdedit /debug {current} off` gép, és futtassa egy rendszergazda jogú parancssorból. Ha nem rdp, ehelyett csatolja az operációs rendszer lemezegy másik Azure-beli virtuális gép, és módosítsa, miközben kapcsolódik az adatlemez futtatásával, `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`majd a lemez cseréje vissza.
A PowerShell-be való beillesztés sac-ban egy harmadik karaktert eredményez, ha az eredeti tartalom ismétlődő karakterrel volt. | Kerülő megoldáshoz `Remove-Module PSReadLine` futtassa a PSReadLine modul kiürítéséhez az aktuális munkamenetből. Ez a művelet nem törli vagy távolítja el a modult.
Egyes billentyűzetbemenetek furcsa SAC kimenetet eredményeznek (például **[A**, **[3~**). | [A VT100](https://aka.ms/vtsequences) escape-szekvenciákat a SAC parancssor nem támogatja.
A hosszú húrok beillesztése nem működik. | A soros konzol a terminálba beillesztett karakterláncok hosszát 2048 karakterre korlátozza, hogy megakadályozza a soros port sávszélességének túlterhelését.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése a GitHub-probléma létrehozásával a következő helyen: https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé előnyös), azserialhelp@microsoft.com visszajelzést küldhet a https://feedback.azure.comvirtuális gép kategóriáján keresztül vagy a virtuális gép kategóriájában.

**K. Támogatja a soros konzol a másolást/beillesztést?**

A. Igen. A Ctrl**Shift**+**C** és a Ctrl**Shift**+**V** **billentyűkombinációval**+ **Ctrl**+másolhat és illesztődjön be a terminálba.

**K. Ki engedélyezheti vagy tilthatja le a soros konzolt az előfizetésemhez?**

A. A soros konzol előfizetési szintű engedélyezéséhez vagy letiltásához írási engedéllyel kell rendelkeznie az előfizetéshez. Az írási engedéllyel rendelkező szerepkörök közé tartoznak a rendszergazdai vagy tulajdonosi szerepkörök. Az egyéni szerepkörök írási engedéllyel is rendelkezhetnek.

**K. Ki férhet hozzá a virtuális gépem soros konzoljához?**

A. A virtuális gép soros konzoljának eléréséhez a virtuális gép szerepkörrel vagy magasabb szerepkörrel kell rendelkeznie.

**K. A soros konzolnem jelenít meg semmit, mit tegyek?**

A. A rendszerkép valószínűleg helytelenül van konfigurálva a soros konzolhoz való hozzáféréshez. A lemezkép soros konzol engedélyezésére való konfigurálásáról [a Soros konzol engedélyezése egyéni vagy régebbi képeken című témakörben](#enable-the-serial-console-in-custom-or-older-images)talál.

**K. Elérhető a soros konzol a virtuálisgép-méretezési csoportokhoz?**

A. igen! A [virtuálisgép-méretezési készletek soros konzolja](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>További lépések
* A Windows SAC-ban használható CMD- és PowerShell-parancsok részletes útmutatóját a [Windows cmd és powershell parancsai ban talál.](serial-console-cmd-ps-commands.md)
* A soros konzol [linuxos](serial-console-linux.md) virtuális gépekhez is elérhető.
* További információ a [rendszerindítási diagnosztikáról](boot-diagnostics.md).
