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
ms.openlocfilehash: 6b62bee2a6159cfd1aa1d7278f0b1ffa744f3a87
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124007"
---
# <a name="azure-serial-console-for-windows"></a>Windows rendszerhez készült Azure soros konzol

A Azure Portal soros konzolja hozzáférést biztosít a Windows rendszerű virtuális gépek (VM) és a virtuálisgép-méretezési csoport példányainak szöveges alapú konzolhoz. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának COM1 soros portjához csatlakozik, és hozzáférést biztosít a hálózattól vagy az operációs rendszer állapotától függetlenül. A soros konzol csak a Azure Portal használatával érhető el, és csak azon felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuálisgép-méretezési csoport számára a közreműködő vagy annál magasabb hozzáférési szerepkörrel rendelkeznek.

A soros konzol a virtuális gépek és a virtuálisgép-méretezési csoport példányai esetében is ugyanúgy működik. Ebben a doc-ban a virtuális gépekre vonatkozó összes említés implicit módon tartalmazza a virtuálisgép-méretezési csoport példányait, hacsak másként nincs megadva.

A Linux soros konzoljának dokumentációját a [Linux rendszerhez készült Azure soros konzolon](serial-console-linux.md)találja.

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban. Ez még nem érhető el az Azure government vagy Azure China felhőkben.


## <a name="prerequisites"></a>Előfeltételek

* A virtuális gép vagy virtuálisgép-méretezési csoport példányának a Resource Management-alapú üzemi modellt kell használnia. Klasszikus üzemi modellben nem támogatottak.

- A soros konzolt használó fióknak rendelkeznie kell a [virtuálisgép-közreműködő szerepkörrel a virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) géphez és a [rendszerindítási diagnosztika](boot-diagnostics.md) Storage-fiókhoz.

- A virtuális gép vagy virtuálisgép-méretezési csoport példányának jelszó-alapú felhasználónak kell lennie. Létrehozhat egyet a [jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) a Virtuálisgép-hozzáférési bővítmény funkcióját. Válassza ki **jelszó alaphelyzetbe állítása** származó a **támogatás + hibaelhárítás** szakaszban.

* A virtuálisgép-méretezési csoport példányának virtuális gépnek engedélyezve kell lennie a rendszerindítási [diagnosztika](boot-diagnostics.md) szolgáltatásnak.

    ![Rendszerindítási diagnosztikai beállításokat](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>A soros konzol funkcióinak engedélyezése a Windows Serverhez

> [!NOTE]
> Ha nem lát semmit a soros konzolon, győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gépen vagy a virtuálisgép-méretezési csoporton.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>A soros konzol egyéni vagy régebbi rendszerképek engedélyezése
Az Azure-ban újabb Windows Server-rendszerképek [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezve van. SAC a Windows server-verziók esetében támogatott, de nem érhető el, az ügyfél-verziók (például a Windows 10, Windows 8 vagy Windows 7).

A régebbi Windows Server-rendszerképeket (a 2018 Februárja előtt létrehozott) a soros konzolon keresztül az Azure Portalon futtatási parancs funkciója automatikusan engedélyezheti. A Azure Portal válassza a **Futtatás parancs**elemet, majd válassza ki a **EnableEMS** nevű parancsot a listából.

![Futtassa a parancsot listája](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Azt is megteheti, hogy manuálisan engedélyezi a soros konzolt a 2018 februárjában létrehozott, Windows rendszerű virtuális gépekhez/virtuálisgép-méretezési csoportokhoz, az alábbi lépéseket követve:

1. A Windows virtuális gép kapcsolódni a távoli asztal használatával
1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Indítsa újra a rendszer a SAC konzol engedélyezni kell.

    ![SAC konzol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Szükség esetén a SAC is engedélyezése kapcsolat nélküli módban is:

1. A windows lemez legyen konfigurálva, a meglévő virtuális gép egy adatlemezt SAC csatlakoztatásához.

1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Honnan tudhatom meg, hogy engedélyezve van-e SAC?

Ha [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve, a soros konzol nem jeleníti meg a SAC használatával. Bizonyos esetekben a virtuális gép állapotára vonatkozó látható, és más esetben üres. Ha a 2018 Februárja előtt létrehozott Windows Server-rendszerképet használ, SAC valószínűleg nem engedélyezhető.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>A soros konzol a Windows rendszertöltő menü engedélyezése

Ha a Windows rendszertöltő betöltő utasításokat a soros konzolon megjelenítendő engedélyeznie kell, a következő beállítások is hozzáadhat a rendszerindítási konfigurációs adatok. További információkért lásd: [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. A Távoli asztal használatával csatlakozhat a Windows rendszerű virtuális géphez vagy a virtuálisgép-méretezési csoport példányaihoz.

1. Egy rendszergazdai parancssorból futtassa a következő parancsokat:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Indítsa újra a rendszert, engedélyezni kell a rendszerindító menü

> [!NOTE]
> Az időkorlát, a rendszerindító manager menü megjelenítéséhez beállított negatív hatással lesz az operációs rendszer rendszerindítási ideje. Ha úgy gondolja, hogy a 10 másodperces időtúllépési érték túl rövid, vagy túl hosszú, állítsa be egy másik értéket.

## <a name="use-serial-console"></a>Soros konzol használata

### <a name="use-cmd-or-powershell-in-serial-console"></a>A CMD vagy a PowerShell használata a soros konzolon

1. Csatlakozás soros konzolon. Sikeresen csatlakozott, a rendszer kéri-e **SAC >** :

    ![SAC csatlakozni](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Adja meg `cmd` hozhat létre egy csatornát, amely megtalálható a CMD példánya.

1.  Adja `ch -si 1` meg vagy `<esc>+<tab>` nyomja le a gyorsbillentyűket a cmd-példányt futtató csatornára való váltáshoz.

1.  Nyomja meg **Enter**, majd adja meg a bejelentkezési hitelesítő adatok rendszergazdai engedélyekkel.

1.  Miután megadta az érvényes hitelesítő adatokat, megnyílik a CMD-példány.

1.  Adjon meg egy PowerShell-példány indításához `PowerShell` a CMD-példányt, és nyomja le az **Enter**.

    ![Nyissa meg a PowerShell-példány](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Használja a soros konzol NMI hívások
Egy nem maszkolható (NMI) úgy tervezték, hogy hozzon létre egy olyan jelet, hogy a szoftverek virtuális gépi nem figyelmen kívül. Hagyományosan NMIs figyelje a hardverekkel kapcsolatos problémák szerepelnek, amelyek adott válaszidők szükséges rendszereken voltak használva. Napjainkban a programozók és a rendszergazdák gyakran használják a NMI-t a nem válaszoló rendszerek hibakeresésére vagy hibaelhárítására.

A soros konzol segítségével egy Azure-beli virtuálisgép-NMI küldje el a billentyűzet ikon használatával a parancssávon. Miután a NMI érkezik, a virtuális gép konfigurációja szabályozza hogyan reagál a rendszer. Windows beállítható úgy, hogy az összeomlási, és a memóriakép létrehozása egy NMI fogadásakor.

![NMI küldése](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows összeomlási memóriaképfájl létrehozásához, amikor kap egy NMI konfigurálásával kapcsolatos további információkért lásd: [egy összeomlási memóriakép létrehozása egy NMI használatával](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Függvény-kulcsok használata a soros konzol
Funkcióbillentyűk engedélyezve vannak a soros konzol a Windows virtuális gépek használatát. A soros konzol legördülő listában az F8 biztosít, egyszerűen írja be a speciális rendszerindítási beállítások menü a kényelem érdekében, de a soros konzol kompatibilis a függvény minden más kulcsok. Előfordulhat, hogy a billentyűzeten kell megnyomnia az **FN** + **F1** (vagy F2, F3 stb.) billentyűt a billentyűzeten attól függően, hogy melyik számítógépről használja a soros konzolt.

### <a name="use-wsl-in-serial-console"></a>A soros konzol WSL használata
A Windows alrendszer Linux (WSL) engedélyezve van a Windows Server 2019 vagy újabb verzió, így is lehet engedélyezni WSL soros konzolon belüli használatra, ha futtatja a Windows Server 2019 vagy újabb verziója. Egy Linux-parancsok ismeretét rendelkező felhasználók számára előnyös lehet. A Windows Server WSL engedélyezéséhez utasításokért lásd: a [telepítési útmutató](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>A Windows rendszerű virtuális gép/virtuálisgép-méretezési csoport példányának újraindítása a soros konzolon belül
A soros konzolon belül kezdeményezheti az újraindítást, ha a főkapcsoló gombra kattint, és a "virtuális gép újraindítása" lehetőségre kattint. Ez elindítja a virtuális gép újraindítását, és értesítést fog látni a Azure Portalon belül az újraindítással kapcsolatban.

Ez olyan helyzetekben lehet hasznos, amikor a soros konzol felhasználói felületének elhagyása nélkül szeretné elérni a rendszerindító menüt.

![Windows soros konzol újraindítása](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>A soros konzol letiltása
Alapértelmezés szerint minden előfizetéshez engedélyezve van a soros konzolhoz való hozzáférés. A soros konzolt letilthatja az előfizetés szintjén vagy a VM/virtuálisgép-méretezési csoport szintjén. Részletes útmutatásért látogasson el [Az Azure soros konzol engedélyezése és letiltása](./serial-console-enable-disable.md)című részhez.

## <a name="serial-console-security"></a>Soros konzol biztonsága

### <a name="access-security"></a>Hozzáférés-biztonságot
A soros konzoljához való hozzáférés hozzáférési szerepkörrel rendelkező felhasználók korlátozódik, [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) vagy újabb, a virtuális géphez. Ha az Azure Active Directory-bérlő a multi-factor authentication (MFA) szükséges, akkor a soros konzoljához való hozzáférés is kell többtényezős hitelesítés, mert a soros konzol fér hozzá a [az Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Csatorna biztonsági
Hálózati oda-vissza küldött összes adatot titkosított.

### <a name="audit-logs"></a>Naplók
A soros konzoljához való hozzáférés teljes jelenleg be van jelentkezve a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure-beli virtuálisgép-rendszergazda határozza meg.

> [!CAUTION]
> A konzol nem hozzáférést jelszavát a rendszer naplózza. Azonban a konzolon belül futó parancsok tartalmaznak, vagy a jelszavak, titkos kódok, felhasználói neveket, vagy bármely más személyes azonosításra alkalmas adatok (PII) kimeneti, ha azok lesz írva az a virtuális gép rendszerindítási diagnosztikai naplók. Ezek lesz írva az összes többi látható szöveg, valamint a soros konzolon görgessen vissza végrehajtásának részeként függvény. Ezeket a naplókat. kör alakú és egyetlen egyéni felhasználók számára a diagnosztikai tárfiók olvasási jogosultsággal rendelkező tudja elérni őket. Azonban javasoljuk, hogy kövesse az ajánlott eljárás szerint az összes adat távoli asztallal, amelyek magukban foglalhatják a titkos kódok és/vagy a személyazonosításra alkalmas adatok.

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és a egy másik felhasználó sikeresen kéri, hogy ugyanahhoz a virtuális géphez való hozzáférés, az első felhasználó megszakítja, és a második felhasználó csatlakozik-e ugyanabban a munkamenetben.

> [!CAUTION]
> Ez azt jelenti, hogy le van választva a felhasználó nem kijelentkeztetjük. Képes érvényesíteni a kijelentkezési kapcsolat bontása esetén (használatával SIGHUP vagy hasonló mechanizmus) továbbra is szerepel az ütemterv részét képezi. A Windows van egy automatikus időtúllépés, SAC; engedélyezve Linux esetén konfigurálhatja úgy a terminál időkorlátozási beállítást.

## <a name="accessibility"></a>Kisegítő lehetőségek
Kisegítő lehetőségek egy kulcsfontosságú fókuszának az Azure-soros konzolon. Ebből a célból keresztülhaladnak, hogy a soros konzolon érhető el a visual és szolgálhattunk korlátozott, valamint azokat, akik nem feltétlenül tudja az egérrel.

### <a name="keyboard-navigation"></a>Billentyűzetnavigációt
Használja a **lapon** billentyűt a billentyűzeten a soros konzol felületen az Azure Portalról nyissa meg. A hely kiemelten jelenik meg a képernyőn. Hagyja a lépéseknek az ismertetése, a soros konzol ablakából, nyomja le a **Ctrl**+**F6** a billentyűzeten.

### <a name="use-the-serial-console-with-a-screen-reader"></a>A soros konzol használata a képernyőolvasó
A soros konzol rendelkezik beépített képernyőolvasó támogatása. Kapcsolva képernyőolvasóval való navigálni lehetővé teszi a helyettesítő szöveget a jelenleg kijelölt gombra kattintva a képernyőolvasó felolvassa olvasni.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>A soros konzol eléréséhez a gyakori forgatókönyvek

Forgatókönyv          | A soros konzol műveletek
:------------------|:-----------------------------------------
Helytelen tűzfal-szabályok | Soros konzolon, és javítsa a Windows tűzfalszabályok eléréséhez.
Fájlrendszer sérülése és ellenőrzése | A soros konzol eléréséhez, és a fájlrendszer helyreállítani.
RDP-konfigurációs problémák | A soros konzol eléréséhez, és módosítsa a beállításokat. További információkért lásd: a [RDP-dokumentáció](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Rendszer hálózati zárolása | A soros konzol eléréséhez a rendszer kezelését az Azure Portalról. Egyes hálózati parancsok a Windows- [parancsok listájában találhatók: CMD és PowerShell](serial-console-cmd-ps-commands.md).
A rendszertöltő használata | Hozzáférés a BCD soros konzolon keresztül. További információ: [engedélyezése a soros konzol a Windows rendszerindítási menüjében](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Ismert problémák
A soros konzol problémák tisztában vagyunk. Ezekről a problémákról és kockázatcsökkentési lépések listáját itt látható. Ezek a problémák és enyhítések mind a virtuális gépek, mind a virtuálisgép-méretezési csoport példányai esetében érvényesek.

Probléma                             |   Kezelés
:---------------------------------|:--------------------------------------------|
Billentyű **Enter** után a kapcsolaton transzparens, nem váltják ki a bejelentkezési kérések jelenhetnek megjeleníteni. | További információkért lásd: [Hitting adja meg a hatástalan](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ez a hiba akkor fordulhat elő, ha egy egyéni virtuális Gépet, megerősített készülék vagy rendszerindítási konfiguráció, amelynek hatására a Windows a soros port megfelelően csatlakozni sikertelen futtatja. Ez a hiba akkor is előfordulhat, ha Windows 10 rendszerű virtuális gépet futtat, mert csak a Windows Server rendszerű virtuális gépekre van beállítva, hogy az EMS engedélyezve legyen.
Csak az egészségügyi információk Windows virtuális Géphez való csatlakozáskor jelenik meg| Ez a hiba akkor fordul elő, ha a speciális felügyeleti konzol nincs engedélyezve a Windows-lemezkép. Lásd: [engedélyezése a soros konzol egyéni vagy régebbi képeken](#enable-the-serial-console-in-custom-or-older-images) manuálisan engedélyezni a virtuális Gépen Windows SAC létrehozásával kapcsolatos útmutatást. További információkért lásd: [Windows egészségügyi jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Írja be a SAC kérdezzen rá Ha engedélyezve van a kernel hibakeresés nem. | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` egy rendszergazda jogú parancssorból. Ha nem használhatja az RDP, akkor ehelyett az operációsrendszer-lemez csatolása egy másik Azure virtuális géphez és módosítsa, amíg a futó adatlemezként csatlakoztatott `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, majd felcserélése vissza a lemezt.
Beillesztése PowerShell rendszerbe SAC eredményez olyan harmadik karaktert, ha az eredeti rendelkezett ismétlődő karaktert. | A probléma megoldásához futtassa `Remove-Module PSReadLine` való eltávolítása a PSReadLine modul az aktuális munkamenet. Ez a művelet nem törli vagy eltávolítja a modult.
Bizonyos billentyűzetet bemenetek furcsa SAC kimeneti előállításához (például **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) escape-karaktersorozatokat a SAC használatával által nem támogatott.
Illessze be a hosszú karakterláncok nem működik. | A soros konzol illeszthetők be a terminál 2048 karakter hosszúságú lehet, megelőzve a soros port sávszélesség sztring hossza korlátozza.
A Serial console a Azure Data Lake Storage Gen2 hierarchikus névtereket használó Storage-fiókkal nem működik. | Ez egy ismert probléma a hierarchikus névterek esetében. A megoldáshoz győződjön meg arról, hogy a virtuális gép rendszerindítási diagnosztikai tárolási fiókja nem Azure Data Lake Storage Gen2 használatával jön létre. Ez a beállítás csak a Storage-fiók létrehozásakor állítható be. Előfordulhat, hogy létre kell hoznia egy különálló rendszerindítási diagnosztikai Storage-fiókot anélkül, hogy Azure Data Lake Storage Gen2 engedélyezve lenne a probléma enyhítése érdekében.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés a githubon található létrehozásával https://aka.ms/serialconsolefeedback. Másik lehetőségként (kevésbé elsődleges), visszajelzést küldhet keresztül azserialhelp@microsoft.com vagy virtuálisgép-kategóriába https://feedback.azure.com.

**Q. Támogatja a soros konzol másolja és illessze be?**

A. Igen. Használat **Ctrl**+**Shift**+**C** és **Ctrl**+**Shift** + **V** másolja és illessze be a terminálba.

**Q. Ki engedélyezheti vagy letilthatja a soros konzol az előfizetésemet?**

A. Engedélyezi vagy letiltja a soros konzol egy előfizetési szintű szintjén, az előfizetés írási engedélyekkel rendelkeznie. Írási engedéllyel rendelkező szerepek közé tartozik a rendszergazda vagy tulajdonos szerepkörök. Egyéni szerepkörök is lehet írási engedéllyel.

**Q. Virtuális gép férhet hozzá a soros konzol?**

A. A virtuális gép közreműködő szerepkörrel kell rendelkeznie legalább egy virtuális géphez a virtuális gép soros konzol eléréséhez.

**Q. A soros konzol nem jelennek meg, mi a teendő?**

A. A rendszerkép valószínűleg hibásan konfigurált, soros hozzáféréshez. A lemezkép engedélyezése a soros konzol konfigurálásával kapcsolatos további információkért lásd: [engedélyezése a soros konzol egyéni vagy régebbi képeken](#enable-the-serial-console-in-custom-or-older-images).

**Q. A soros konzolon érhető el a virtuálisgép-méretezési csoportok?**

A. igen! Lásd [a Virtual Machine Scale sets soros konzolját](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>További lépések
* A Windows SAC [szolgáltatásban használható cmd-és PowerShell-parancsok részletes útmutatója: Windows-parancsok: CMD és PowerShell](serial-console-cmd-ps-commands.md).
* A soros konzolon érhető el is [Linux](serial-console-linux.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).
