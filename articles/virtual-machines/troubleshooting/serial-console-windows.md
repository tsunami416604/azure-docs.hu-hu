---
title: Windowsos Azure soros konzol | Microsoft Docs
description: Kétirányú soros konzol az Azure Virtual Machines és Virtual Machine Scale Setshoz.
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
ms.openlocfilehash: c555b058e6d0b8c110a3273525b5a8c58baaff61
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309781"
---
# <a name="azure-serial-console-for-windows"></a>Windows rendszerhez készült Azure soros konzol

A Azure Portal soros konzolja hozzáférést biztosít a Windows rendszerű virtuális gépek (VM) és a virtuálisgép-méretezési csoport példányainak szöveges alapú konzolhoz. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának COM1 soros portjához csatlakozik, és hozzáférést biztosít a hálózattól vagy az operációs rendszer állapotától függetlenül. A soros konzol csak a Azure Portal használatával érhető el, és csak azon felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuálisgép-méretezési csoport számára a közreműködő vagy annál magasabb hozzáférési szerepkörrel rendelkeznek.

A soros konzol a virtuális gépek és a virtuálisgép-méretezési csoport példányai esetében is ugyanúgy működik. Ebben a doc-ban a virtuális gépekre vonatkozó összes említés implicit módon tartalmazza a virtuálisgép-méretezési csoport példányait, hacsak másként nincs megadva.

A Linux soros konzoljának dokumentációját a [Linux rendszerhez készült Azure soros konzolon](serial-console-linux.md)találja.

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban és a nyilvános előzetes verzióban Azure Government. Az Azure China Cloud-ban még nem érhető el.


## <a name="prerequisites"></a>Előfeltételek

* A virtuális gép vagy virtuálisgép-méretezési csoport példányának a Resource Management-alapú üzemi modellt kell használnia. A klasszikus központi telepítések nem támogatottak.

- A soros konzolt használó fióknak rendelkeznie kell a [virtuálisgép-közreműködő szerepkörrel a virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) géphez és a [rendszerindítási diagnosztika](boot-diagnostics.md) Storage-fiókhoz.

- A virtuális gép vagy virtuálisgép-méretezési csoport példányának jelszó-alapú felhasználónak kell lennie. Létrehozhat egyet a virtuálisgép-hozzáférési bővítmény [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkciójával. Válassza a **jelszó alaphelyzetbe állítása** lehetőséget a **támogatás + hibaelhárítás** szakaszban.

* A virtuálisgép-méretezési csoport példányának virtuális gépnek engedélyezve kell lennie a [rendszerindítási diagnosztika](boot-diagnostics.md) szolgáltatásnak.

    ![Rendszerindítási diagnosztika beállításai](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>A soros konzol funkcióinak engedélyezése a Windows Serverhez

> [!NOTE]
> Ha nem lát semmit a soros konzolon, győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gépen vagy a virtuálisgép-méretezési csoporton.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>A soros konzol engedélyezése egyéni vagy régebbi lemezképekben
Az Azure-ban újabb Windows Server-rendszerképekhez a [Speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. A SAC támogatott a Windows Server-verzióiban, de nem érhető el az ügyfél verzióiban (például Windows 10, Windows 8 vagy Windows 7).

A régebbi Windows Server-rendszerképekhez (2018 februárjában létrehozott) a soros konzol automatikusan engedélyezhető a Azure Portal Futtatás parancsával. A Azure Portal válassza a **Futtatás parancs**elemet, majd válassza ki a **EnableEMS** nevű parancsot a listából.

![Parancsok futtatása](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Azt is megteheti, hogy manuálisan engedélyezi a soros konzolt a 2018 februárjában létrehozott, Windows rendszerű virtuális gépekhez/virtuálisgép-méretezési csoportokhoz, az alábbi lépéseket követve:

1. Kapcsolódás a Windows rendszerű virtuális géphez Távoli asztal használatával
1. A rendszergazdai parancssorból futtassa a következő parancsokat:
    - `bcdedit /ems {current} on`, vagy `bcdedit /ems '{current}' on` Ha a PowerShellt használja
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Engedélyezze újra a SAC-konzol rendszerét.

    ![SAC-konzol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Ha szükséges, a SAC offline is engedélyezhető:

1. Csatlakoztassa azt a Windows-lemezt, amelyhez a SAC-t adatlemezként kell konfigurálni a meglévő virtuális géphez.

1. A rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hogyan tudni, hogy a SAC engedélyezve van-e?

Ha a [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve, a soros konzol nem jeleníti meg a SAC-kérést. Bizonyos esetekben a virtuális gép állapotával kapcsolatos információk jelennek meg, más esetekben pedig üresek. Ha a február 2018. előtt létrehozott Windows Server-rendszerképet használ, a SAC valószínűleg nem lesz engedélyezve.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>A Windows rendszerindító menüjének engedélyezése a soros konzolon

Ha engedélyezni szeretné a Windows rendszerindítási kérések megjelenítését a soros konzolon, a következő további beállításokat adhatja hozzá a rendszerindítási konfigurációs adataihoz. További információ: [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. A Távoli asztal használatával csatlakozhat a Windows rendszerű virtuális géphez vagy a virtuálisgép-méretezési csoport példányaihoz.

1. A rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. A rendszer újraindítása a rendszerindító menü engedélyezéséhez

> [!NOTE]
> A rendszerindítási kezelő menüjéhez megadott időkorlát hatással lesz az operációs rendszer rendszerindítási idejére. Ha úgy gondolja, hogy a 10 másodperces időkorlát túl rövid vagy túl hosszú, állítsa egy másik értékre.

## <a name="use-serial-console"></a>Soros konzol használata

### <a name="use-cmd-or-powershell-in-serial-console"></a>A CMD vagy a PowerShell használata a soros konzolon

1. Kapcsolódjon a soros konzolhoz. Ha sikeresen kapcsolódott, a kérdés **SAC>**:

    ![Kapcsolódás SAC-hez](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.    Adja meg a `cmd` parancsot egy olyan csatorna létrehozásához, amely rendelkezik cmd-példánnyal.

1.    Adja meg `ch -si 1` vagy nyomja le a `<esc>+<tab>` gyorsbillentyűket a cmd-példányt futtató csatornára való váltáshoz.

1.    Nyomja le az **ENTER**billentyűt, majd adja meg a bejelentkezési hitelesítő adatokat rendszergazdai engedélyekkel.

1.    Az érvényes hitelesítő adatok megadása után megnyílik a CMD-példány.

1.    PowerShell-példány elindításához írja be `PowerShell` a parancsot a cmd-példányba, majd nyomja le az **ENTER**billentyűt.

    ![PowerShell-példány megnyitása](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>A soros konzol használata NMI-hívásokhoz
A nem maszkolt megszakítás (NMI) úgy van kialakítva, hogy egy olyan jelet hozzon létre, amelyet a virtuális gépen lévő szoftver nem hagy figyelmen kívül. A NMIs korábban a hardveres problémák figyelésére használták az adott válaszidőt igénylő rendszereken. Napjainkban a programozók és a rendszergazdák gyakran használják a NMI-t a nem válaszoló rendszerek hibakeresésére vagy hibaelhárítására.

A soros konzol segítségével NMI küldhet egy Azure-beli virtuális gépre a parancssáv billentyűzet ikonjának használatával. A NMI elszállítása után a virtuális gép konfigurációja vezérli, hogy a rendszer hogyan reagáljon. A Windows beállítható úgy, hogy összeomlik, és hozzon létre egy memóriakép-fájlt a NMI fogadásakor.

![NMI küldése](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Arról, hogy a Windows hogyan hozható létre összeomlási memóriaképet tartalmazó fájl létrehozásához, ha NMI kap, lásd: [összeomlási memóriakép fájljának létrehozása NMI használatával](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Funkcióbillentyűk használata a soros konzolon
A funkcióbillentyűk engedélyezve vannak a soros konzol használatához a Windows rendszerű virtuális gépeken. A soros konzolon a legördülő menüben található F8 egyszerűen beírhatja a speciális rendszerindítási beállítások menüt, de a soros konzol kompatibilis az összes többi funkcióbillentyű használatával. Előfordulhat, hogy a billentyűzeten kell megnyomnia az **FN**  +  **F1** (vagy F2, F3 stb.) billentyűt a billentyűzeten attól függően, hogy melyik számítógépről használja a soros konzolt.

### <a name="use-wsl-in-serial-console"></a>A WSL használata a soros konzolon
A Linux rendszerhez készült Windows alrendszer (WSL) engedélyezve van a Windows Server 2019-es vagy újabb verziójára, ezért a soros konzolon is engedélyezhető a WSL, ha Windows Server 2019 vagy újabb rendszert futtat. Ez hasznos lehet olyan felhasználók számára, akik is ismerik a Linux-parancsokat. A Windows Server WSL engedélyezésével kapcsolatos utasításokért tekintse meg a [telepítési útmutatót](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>A Windows rendszerű virtuális gép/virtuálisgép-méretezési csoport példányának újraindítása a soros konzolon belül
A soros konzolon belül kezdeményezheti az újraindítást, ha a főkapcsoló gombra kattint, és a "virtuális gép újraindítása" lehetőségre kattint. Ez elindítja a virtuális gép újraindítását, és értesítést fog látni a Azure Portalon belül az újraindítással kapcsolatban.

Ez olyan helyzetekben lehet hasznos, amikor a soros konzol felhasználói felületének elhagyása nélkül szeretné elérni a rendszerindító menüt.

![Windows soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>A soros konzol letiltása
Alapértelmezés szerint minden előfizetéshez engedélyezve van a soros konzolhoz való hozzáférés. A soros konzolt letilthatja az előfizetés szintjén vagy a VM/virtuálisgép-méretezési csoport szintjén. Részletes útmutatásért látogasson el [Az Azure soros konzol engedélyezése és letiltása](./serial-console-enable-disable.md)című részhez.

## <a name="serial-console-security"></a>Serial console biztonság

### <a name="access-security"></a>Hozzáférés biztonsága
A soros konzolhoz való hozzáférés csak azokra a felhasználókra korlátozódik, akik a virtuális gép [közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy magasabb hozzáférési szerepkörével rendelkeznek. Ha a Azure Active Directory bérlő a többtényezős hitelesítés (MFA) használatát igényli, akkor a soros konzolhoz való hozzáféréshez is szüksége lesz az MFA-ra, mivel a soros konzol hozzáférése a [Azure Portalon](https://portal.azure.com)keresztül történik.

### <a name="channel-security"></a>Csatorna biztonsága
A visszaadott és oda továbbított összes adatforgalom titkosítva van a huzalon.

### <a name="audit-logs"></a>Auditnaplók
A soros konzolhoz való összes hozzáférés jelenleg a virtuális gép [rendszerindítási diagnosztikai](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplóiban van naplózva. A naplókhoz való hozzáférést az Azure-beli virtuális gép rendszergazdája birtokolja és felügyeli.

> [!CAUTION]
> Nincsenek naplózva a konzol hozzáférési jelszava. Ha azonban a konzolon belül futnak a jelszavak, a titkos kódok, a felhasználónevek vagy a személyazonosításra alkalmas adatok bármilyen más formája, akkor azokat a rendszer a virtuális gép rendszerindítási diagnosztikai naplóiba írja. A soros konzol görgetőgomb-visszagörgetési funkciója megvalósításának részeként minden más látható szöveggel együtt lesznek írva. Ezek a naplók körkörösek, és csak azok a személyek férhetnek hozzájuk, akik olvasási engedéllyel rendelkeznek a diagnosztikai Storage-fiókhoz. Javasoljuk azonban, hogy a Távoli asztal használatának ajánlott eljárását a titkokat és/vagy személyes adatokat is magában foglaló bármit is felhasználjon.

### <a name="concurrent-usage"></a>Egyidejű használat
Ha egy felhasználó csatlakozik a soros konzolhoz, és egy másik felhasználó sikeresen kér hozzáférést ugyanahhoz a virtuális géphez, akkor az első felhasználó le lesz választva, és a második felhasználó ugyanahhoz a munkamenethez csatlakozik.

> [!CAUTION]
> Ez azt jelenti, hogy a leválasztott felhasználó nem lesz kijelentkezve. A Kilépés (SIGHUP vagy hasonló mechanizmus használatával) leválasztásának lehetősége továbbra is az ütemtervben van. Windows esetén a SAC szolgáltatásban automatikus időtúllépés van engedélyezve. a Linux esetében beállíthatja a terminál időtúllépési beállítását.

## <a name="accessibility"></a>Kisegítő lehetőségek
A kisegítő lehetőségek az Azure soros konzol egyik fő témája. Ebből a célból biztosítjuk, hogy a soros konzol elérhető legyen a látássérültek számára, vagy akik nagyothallók, illetve akik esetleg nem tudják használni az egeret.

### <a name="keyboard-navigation"></a>Billentyűzettel való navigáció
A billentyűzet **Tab** billentyűjét használva navigáljon a soros konzol felületén a Azure Portal. A hely ki lesz emelve a képernyőn. A soros konzol ablakának fókuszának elhagyásához nyomja le a **CTRL F6 billentyűt**a + **F6** billentyűzeten.

### <a name="use-the-serial-console-with-a-screen-reader"></a>A soros konzol használata képernyőolvasóval
A soros konzol beépített képernyőolvasó-támogatással rendelkezik. A bekapcsolt képernyőolvasóval való navigálás lehetővé teszi, hogy az aktuálisan kijelölt gombhoz tartozó ALT szöveg hangosan felolvassa a képernyőolvasót.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>A soros konzol elérésének gyakori forgatókönyvei

Forgatókönyv          | Műveletek a soros konzolon
:------------------|:-----------------------------------------
Helytelen tűzfalszabályok | Nyissa meg a soros konzolt, és javítsa a Windows tűzfal szabályait.
Fájlrendszer-sérülés/-ellenőrzési | Nyissa meg a soros konzolt, és állítsa helyre a fájlrendszert.
RDP-konfigurációs problémák | Nyissa meg a soros konzolt, és módosítsa a beállításokat. További információkért tekintse meg az [RDP-dokumentációt](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Hálózati zárolási rendszerek | A rendszer kezeléséhez nyissa meg a soros konzolt a Azure Portal. Egyes hálózati parancsok a Windows- [parancsok: cmd és PowerShell](serial-console-cmd-ps-commands.md)című részben vannak felsorolva.
A rendszerbetöltővel való interakció | A BCD elérése a soros konzolon keresztül. További információ: [a Windows rendszerindítási menüjének engedélyezése a soros konzolon](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Ismert problémák
Tisztában vagyunk a soros konzollal és a virtuális gép operációs rendszerével kapcsolatos problémákkal. Ezen problémák listája és a Windows rendszerű virtuális gépek enyhítésének lépései. Ezek a problémák és enyhítések mind a virtuális gépek, mind a virtuálisgép-méretezési csoport példányai esetében érvényesek. Ha ezek nem egyeznek a megjelenő hibával, tekintse meg a soros konzol gyakori [hibáival](./serial-console-errors.md)kapcsolatos általános hibákat.

Probléma                             |   Kezelés
:---------------------------------|:--------------------------------------------|
Az **ENTER** billentyű lenyomása után a kapcsolódási szalagcím nem eredményezi a bejelentkezési üzenet megjelenítését. | További információ: [ütő ENTER do Nothing](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a hiba akkor fordulhat elő, ha egy egyéni virtuális gépet, megerősített készüléket vagy rendszerindítási konfigurációt futtat, amelynek hatására a Windows nem tud megfelelően csatlakozni a soros porthoz. Ez a hiba akkor is előfordulhat, ha Windows 10 rendszerű virtuális gépet futtat, mert csak a Windows Server rendszerű virtuális gépekre van beállítva, hogy az EMS engedélyezve legyen.
Windows rendszerű virtuális géphez való csatlakozáskor csak állapotadatok jelennek meg| Ez a hiba akkor fordul elő, ha a speciális felügyeleti konzol nincs engedélyezve a Windows-rendszerképhez. Lásd: [a soros konzol engedélyezése egyéni vagy régebbi lemezképekben](#enable-the-serial-console-in-custom-or-older-images) a Windows rendszerű virtuális GÉPEN a SAC manuális engedélyezésével kapcsolatos utasításokért. További információ: [Windows Health Signals](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
A SAC nem veszi át a teljes soros konzolt a böngészőben | Ez egy ismert probléma a Windowsban és a terminál-emulátorban. Ezt a problémát mindkét csapatnál nyomon követjük, de jelenleg nincs megoldás.
Nem lehet beírni a SAC-parancssorba, ha a kernel hibakeresése engedélyezve van. | RDP-t a virtuális gépre, és futtassa `bcdedit /debug {current} off` a parancsot egy rendszergazda jogú parancssorból. Ha nem tud RDP-t, az operációsrendszer-lemezt egy másik Azure-beli virtuális géphez is csatlakoztathatja, és a futtatásával adatlemezként is módosíthatja `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` , majd visszaállíthatja a lemezt.
Ha az eredeti tartalom ismétlődő karaktert tartalmaz, a PowerShellbe való beillesztés egy harmadik karaktert eredményez. | Megkerülő megoldásként futtassa a parancsot a `Remove-Module PSReadLine` PSReadLine modul az aktuális munkamenetből való eltávolításához. Ez a művelet nem törli vagy eltávolítja a modult.
A billentyűzet egyes bemenetei furcsa SAC-kimenetet hoznak létre (például **[a**, **[3 ~**). | A SAC-parancssor nem támogatja a [VT100](https://aka.ms/vtsequences) escape-sorozatot.
A hosszú karakterláncok beillesztése nem működik. | A soros konzol korlátozza a terminálba beillesztett sztringek hosszát 2048 karakterre, hogy megakadályozza a soros port sávszélességének túlterhelését.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K. Hogyan küldhetek visszajelzést?**

A. Visszajelzés létrehozásához hozzon létre egy GitHub-problémát a következő címen: https://aka.ms/serialconsolefeedback . Másik lehetőségként (kevésbé előnyben részesített) küldhet visszajelzést azserialhelp@microsoft.com a virtuális gép kategóriájában vagy a használatával https://feedback.azure.com .

**K. támogatja a soros konzol a másolást és beillesztést?**

A. Igen. **Ctrl** + **Shift** + **C** **Ctrl** + **Shift** + A terminálba másoláshoz és beillesztéshez használja a CTRL SHIFT C és a CTRL SHIFT**V** billentyűkombinációt.

**K. ki engedélyezheti vagy letilthatja az előfizetéshez tartozó soros konzolt?**

A. A soros konzol előfizetés szintű engedélyezéséhez vagy letiltásához írási engedéllyel kell rendelkeznie az előfizetéshez. Az írási engedéllyel rendelkező szerepkörök rendszergazdai vagy tulajdonosi szerepköröket is tartalmaznak. Az egyéni szerepkörök rendelkezhetnek írási engedélyekkel is.

**K. ki férhet hozzá a virtuális géphez tartozó soros konzolhoz?**

A. A virtuális gép soros konzoljának eléréséhez rendelkeznie kell a virtuálisgép-közreműködő szerepkörrel vagy magasabb jogosultsággal.

**K. a soros konzol nem jelenít meg semmit, mit tegyek?**

A. A rendszerkép valószínűleg helytelenül van konfigurálva a soros konzolhoz való hozzáféréshez. További információ a lemezképnek a soros konzol engedélyezéséhez való konfigurálásáról: [a soros konzol engedélyezése egyéni vagy régebbi lemezképekben](#enable-the-serial-console-in-custom-or-older-images).

**K. elérhető a soros konzol a virtuálisgép-méretezési csoportokhoz?**

A. igen! Lásd [a Virtual Machine Scale sets soros konzolját](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>További lépések
* A Windows SAC szolgáltatásban használható CMD-és PowerShell-parancsok részletes útmutatója [: Windows-parancsok: cmd és PowerShell](serial-console-cmd-ps-commands.md).
* A soros konzol [Linux](serial-console-linux.md) rendszerű virtuális gépekhez is elérhető.
* További információ a [rendszerindítási diagnosztika](boot-diagnostics.md)szolgáltatásról.
