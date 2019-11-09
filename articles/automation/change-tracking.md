---
title: Változások követése Azure Automation
description: A Change Tracking megoldás segítséget nyújt a környezetében előforduló szoftverek és Windows-szolgáltatások változásainak azonosításához.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fc0aeab4e9603995130392e3560325ccaba1ffc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886809"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>A környezet változásainak követése a Change Tracking megoldással

Ez a cikk segítséget nyújt a Change Tracking megoldás használatában a környezet változásainak egyszerű azonosításához. A megoldás nyomon követi a Windows-és Linux-szoftverek, a Windows-és Linux-fájlok, a Windows-beállításkulcsok, a Windows-szolgáltatások és a Linux-démonok változásait. A konfigurációs változások azonosítása segíthet a működési problémák megoldásában.

A felügyelt kiszolgálókon a telepített szoftverek, a Windows-szolgáltatások, a Windows-beállításjegyzék és a fájlok, valamint a Linux-démonok módosításai a felhőben a Azure Monitor szolgáltatásba kerülnek feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat. A Change Tracking irányítópulton található információk használatával egyszerűen megtekintheti a kiszolgálói infrastruktúrában végrehajtott módosításokat.

> [!NOTE]
> Azure Automation Change Tracking nyomon követi a virtuális gépek változásait. A Azure Resource Manager tulajdonságok változásainak nyomon követéséhez tekintse meg az Azure Resource Graph [változási előzményeit](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Támogatott Windows operációs rendszerek

A Windows-ügynök a Windows operációs rendszer következő verzióit támogatja hivatalosan:

* Windows Server 2008 R2 vagy újabb

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A következő Linux-disztribúciók hivatalosan támogatottak. A Linux-ügynök azonban más, nem felsorolt disztribúciókban is futhat. Hacsak másként nincs jelezve, az összes alverzió támogatott a felsorolt főbb verziók esetében.

### <a name="64-bit"></a>64 bites

* CentOS 6 és 7
* Amazon Linux 2017,09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux 6. és 7. kiszolgáló
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bites

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS és 16,04 LTS

## <a name="onboard"></a>Change Tracking és leltár engedélyezése

A változások követésének megkezdéséhez engedélyeznie kell a Change Tracking és a leltár megoldást. A gépek bevezetésének számos módja van Change Tracking és leltárba. A megoldás bevezetésének ajánlott és támogatott módjai a következők:

* [Virtuális gépről](automation-onboard-solutions-from-vm.md)
* [Több gép tallózása](automation-onboard-solutions-from-browse.md)
* [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>A Change Tracking és a leltár konfigurálása

A számítógépek a megoldásba való bevezetésének megismeréséhez tekintse meg az [Automation-megoldások](automation-onboard-solutions-from-automation-account.md)előkészítését ismertető témakört. Ha rendelkezik egy, a Change Tracking és a leltár megoldással rendelkező géppel, beállíthatja a nyomon követett elemeket. Ha engedélyezi egy új fájl vagy beállításkulcs nyomon követését, az Change Tracking és a leltár számára is engedélyezett.

A Windows és a Linux rendszerű fájlok változásainak nyomon követéséhez használja a fájlok MD5-kivonatait. A tézisek kivonatai ezután azt észlelik, hogy történt-e változás a legutóbbi leltár óta.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>A fájlok integritásának figyelése Azure Security Center

Azure Security Center hozzáadott egy Azure Change Tracking-ra épülő FIM-figyelőt. Míg a FIM figyeli a fájlokat és a beállításjegyzékeket, a teljes Change Tracking megoldás a következőket is tartalmazza:

- A szoftver módosításai
- Windows-szolgáltatások
- Linux-démonok

Ha már engedélyezte a FIM használatát, és szeretné kipróbálni a teljes Change Tracking megoldást, végre kell hajtania a következő lépéseket. Ez a folyamat nem távolítja el a beállításokat.

> [!NOTE]
> A teljes Change Tracking megoldás engedélyezése további díjakat eredményezhet, további tudnivalókért lásd: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

1. A figyelési megoldás eltávolításához lépjen a munkaterületre, és keresse meg a [telepített figyelési megoldások listájában](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kattintson a megoldás nevére az összefoglalás oldal megnyitásához, majd kattintson a Törlés lehetőségre a [figyelési megoldás eltávolítása](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)című rész útmutatása szerint.
3. Engedélyezze újra a megoldást úgy, hogy az Automation-fiókra navigál, és kiválasztja a **change Tracking** elemet az erőforrás menüben a **konfiguráció kezelése**területen.
4. Erősítse meg a munkaterület beállításának részleteit, és kattintson az **Engedélyezés**gombra.

### <a name="configure-linux-files-to-track"></a>A nyomon követett Linux-fájlok konfigurálása

A következő lépésekkel konfigurálhatja a fájlok követését a Linux rendszerű számítógépeken:

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A **change Tracking** lapon válassza a **Linux-fájlok**lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új fájl hozzáadásához.
3. A **change Tracking linuxos fájljának hozzáadása**elemnél adja meg a nyomon követni kívánt fájl vagy könyvtár adatait, majd kattintson a **Save (Mentés**) gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás alkalmazva van-e.        |
|Elem neve     | A nyomon követett fájl rövid neve.        |
|Csoport     | Egy csoport neve a fájlok logikai csoportosításához.        |
|Elérési út megadása     | A fájl keresésének elérési útja. Például: "/etc/*. conf"       |
|Elérési út típusa     | A nyomon követett elem típusa, a lehetséges értékek: fájl és könyvtár.        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Mellőzés** – figyelmen kívül hagyja a szimbolikus hivatkozásokat, és nem tartalmazza a hivatkozott fájlokat/címtárakat.<br>**Követés** – követi a szimbolikus hivatkozásokat a rekurzió során, és tartalmazza a hivatkozott fájlokat/címtárakat is.<br>**Kezelés** – követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom módosítását.     |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **Hamis**.|

> [!NOTE]
> A „Kezelés” használata nem ajánlott, mert a fájltartalom lekérése nem támogatott.

### <a name="configure-windows-files-to-track"></a>A nyomon követett Windows-fájlok konfigurálása

A fájlok nyomon követését a Windows rendszerű számítógépeken a következő lépésekkel konfigurálhatja:

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A **change Tracking** lapon válassza a **Windows-fájlok**lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új fájl hozzáadásához.
3. A **change Tracking Windows-fájl hozzáadása**lehetőségnél adja meg a nyomon követni kívánt fájl adatait, majd kattintson a **Mentés**gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás alkalmazva van-e.        |
|Elem neve     | A nyomon követett fájl rövid neve.        |
|Csoport     | Egy csoport neve a fájlok logikai csoportosításához.        |
|Elérési út megadása     | A fájl elérési útja, például: „c:\temp\\\*.txt”<br>Környezeti változók is használhatók, például: „%winDir%\System32\\\*.*”       |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **Hamis**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Helyettesítő karakter, rekurzió és környezeti beállítások

A rekurzió lehetővé teszi, hogy helyettesítő karakterek megadásával egyszerűsítse a címtárak közötti nyomkövetést, valamint a környezeti változókat, amelyek lehetővé teszik a fájlok különböző környezetekben történő nyomon követését több vagy dinamikus meghajtó nevével. A következő lista a rekurzió konfigurálásakor szükséges általános információkat mutatja be:

* Több fájl nyomon követéséhez helyettesítő karakterek szükségesek
* Helyettesítő karakterek használata esetén csak az elérési út utolsó szegmensében használhatók. (például `c:\folder\*file*` vagy `/etc/*.conf`)
* Ha egy környezeti változónak érvénytelen az elérési útja, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a leltár futtatásakor.
* Kerülje az általános elérési utakat, például a `c:\*.*`t az elérési út beállításakor, mivel ez túl sok mappa átjárását eredményezheti.

## <a name="configure-file-content-tracking"></a>Fájl tartalmának nyomon követésének konfigurálása

Megtekintheti a tartalmat a fájl tartalmával Change Tracking módosítás előtt és után is. Ez Windows-és Linux-fájlok esetén érhető el, a fájl minden változása esetén a fájl tartalma egy Storage-fiókban tárolódik, és a módosítás, a beágyazott vagy egymás melletti fájl előtt és után jeleníti meg a fájlt. További információt a [követett fájl tartalmának megtekintése](change-tracking-file-contents.md)című témakörben talál.

![egy fájl változásainak megtekintése](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>A Windows-beállításkulcsok nyomon követésének beállítása

A következő lépésekkel konfigurálhatja a beállításjegyzék-kulcsok nyomon követését Windows rendszerű számítógépeken:

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A **change Tracking** lapon válassza a **Windows beállításjegyzék**lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új beállításkulcs hozzáadásához.
3. A **change Tracking Windows beállításjegyzék hozzáadása**elemnél adja meg a nyomon követni kívánt kulcs adatait, majd kattintson a **Mentés**gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás alkalmazva van-e.        |
|Elem neve     | A nyomon követett beállításkulcs rövid neve.        |
|Csoport     | A beállításkulcsok logikai csoportosítására szolgáló csoportnév.        |
|Windows-beállításkulcs   | A beállításkulcs keresésének elérési útja. Például: "HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Korlátozások

Az Change Tracking-megoldás jelenleg nem támogatja a következő elemeket:

* Rekurzió a Windows beállításjegyzékének nyomon követéséhez
* Hálózati fájlrendszerek

Egyéb korlátozások:

* A **Maximális fájlméret** oszlop és az értékek nem használhatók az aktuális implementációban.
* Ha a 30 perces gyűjtési ciklusban több mint 2500 fájlt gyűjt, a megoldás teljesítménye csökkenhet.
* Ha a hálózati forgalom magas, a rekordok módosítása akár hat órát is igénybe vehet.
* Ha úgy módosítja a konfigurációt, hogy a számítógép leáll, a számítógép az előző konfigurációhoz tartozó módosításokat tehet közzé.

## <a name="known-issues"></a>Ismert problémák

A Change Tracking megoldás jelenleg a következő problémákba ütközik:

* A gyorsjavítások frissítései nem lesznek összegyűjtve a Windows Server 2016 Core RS3-gépeken.
* A Linux-démonok módosult állapotot is megjeleníthetnek, bár nem történt változás. Ennek oka az `SvcRunLevels` mező rögzítésének módja.

## <a name="change-tracking-data-collection-details"></a>Change Tracking adatgyűjtési adatok

A következő táblázat a változások típusának adatgyűjtési gyakoriságát mutatja be. Minden típushoz az aktuális állapot adatpillanatképe is legalább 24 óránként frissül:

| **Típus módosítása** | **Gyakoriság** |
| --- | --- |
| Windows beállításjegyzék | 50 perc |
| Windows-fájl | 30 perc |
| Linux-fájl | 15 perc |
| Windows-szolgáltatások | 10 másodperc és 30 perc között</br> Alapértelmezett: 30 perc |
| Linux-démonok | 5 perc |
| Windows-szoftver | 30 perc |
| Linuxos szoftverek | 5 perc |

A következő táblázat a nyomon követett elemek korlátait mutatja Change Tracking gépenként.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Registry|250||
|Windows-szoftver|250|Nem tartalmazza a szoftver gyorsjavításait|
|Linux-csomagok|1250||
|Szolgáltatások|250||
|démon|250||

A Change Tracking és a leltárt használó gépek átlagos Log Analytics adatfelhasználása körülbelül 40MB havonta. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetét, és tekintse meg a pontos használatot.

### <a name="windows-service-tracking"></a>Windows-szolgáltatás követése

A Windows-szolgáltatások alapértelmezett gyűjtési gyakorisága 30 perc. A gyakoriság konfigurálásához lépjen a **change Tracking**elemre. A **Windows-szolgáltatások** lap **beállítások szerkesztése** területén található egy csúszka, amely lehetővé teszi, hogy a Windows-szolgáltatások gyűjtési gyakoriságát akár 10 másodperctől akár 30 percig is megváltoztassa. Mozgassa a csúszkát a kívánt gyakoriságra, és automatikusan elmenti.

![Windows-szolgáltatások csúszka](./media/change-tracking/windowservices.png)

Az ügynök csak a változásokat követi nyomon, ez optimalizálja az ügynök teljesítményét. A magas küszöbérték beállítása lemaradhat, ha a szolgáltatás visszaállt az eredeti állapotába. Ha a gyakoriságot kisebb értékre állítja, az esetlegesen kihagyott módosításokat is elvégezheti.

> [!NOTE]
> Míg az ügynök 10 másodperces intervallumban nyomon követheti a változásokat, az adat még néhány percet is igénybe vehet a portálon. A portálon megjelenítendő idő változásait a rendszer továbbra is nyomon követi és naplózza.

### <a name="registry-key-change-tracking"></a>Beállításkulcs-változások követése

A beállításkulcsok változásainak figyelése az a bővíthetőségi pontok kiértékelése, amelyekben a harmadik féltől származó kód és a kártevő is aktiválható. Az alábbi lista az előre konfigurált beállításkulcsok listáját tartalmazza. Ezek a kulcsok konfigurálva vannak, de nincs engedélyezve. A beállításkulcsok nyomon követéséhez engedélyeznie kell mindegyiket.

> [!div class="mx-tdBreakAll"]
> |Beállításjegyzék kulcsa | Cél |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az Explorer. exe használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Az indításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | A leállításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Figyeli azokat a kulcsokat, amelyek betöltődik, mielőtt a felhasználó bejelentkezik a Windows-fiókjába. A kulcs a 64 bites számítógépeken futó 32 bites programokhoz használatos.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Az Alkalmazásbeállítások változásainak figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az Explorer. exe használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az Explorer. exe használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők az ikon átfedését kezelő regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők a 64 bites számítógépeken futó 32 bites programokhoz tartozó ikon átfedési kezelői regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére és a Navigálás vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére, valamint a 64 bites számítógépeken futó 32 bites programok navigálásának vezérlésére használatos.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Figyelők az új Internet Explorer-bővítményekhez, például az egyéni eszközök menüihez és az egyéni eszköztárak gombjaihoz.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például a 64 bites számítógépeken futó 32 bites programok egyéni eszköztárait és az egyéni eszköztárak gombjait figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli. Hasonló a SYSTEM [Drivers] szakaszához. INI-fájl.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli a 64 bites számítógépeken futó 32 bites programokhoz. Hasonló a SYSTEM [Drivers] szakaszához. INI-fájl.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Az ismert vagy leggyakrabban használt rendszer DLL-fájljainak listáját figyeli; Ez a rendszer megakadályozza, hogy a felhasználók a rendszer DLL-fájljainak ledobásával kihasználják a gyenge alkalmazásspecifikus címtárbeli engedélyeket.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | A Windows operációs rendszer interaktív bejelentkezési támogatási modelljét figyeli azon csomagok listáján, amelyek képesek értesítéseket fogadni a Winlogon-ból.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A következő címek megadása kifejezetten a Change Tracking. Az ezekkel a címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Nyilvános Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Change Tracking használata

A megoldás engedélyezése után megtekintheti a figyelt számítógépek változásainak összefoglalását, ha az **Automation-fiókban az** **change Tracking** lehetőségre kattint.

Megtekintheti a számítógépek módosításait, majd részletezheti az egyes események részletes adatait. A diagram tetején található legördülő lista a típus és az időtartományok alapján korlátozza a diagramot és a részletes információkat. A diagramra kattintva és húzásával egyéni időtartományt is kijelölhet. **A változás típusa** a következő értékek egyike lesz **: események**, **démonok**, **fájlok**, **beállításjegyzék**, **szoftver**, **Windows-szolgáltatások**. A kategória azt mutatja, hogy milyen típusú változást adhat **hozzá**, **módosíthat**vagy **távolíthat el**.

![Change Tracking irányítópult képe](./media/change-tracking/change-tracking-dash01.png)

Ha egy módosításra vagy eseményre kattint, megjelenik a változással kapcsolatos részletes információ. Ahogy a példában látható, a szolgáltatás indítási típusa a Manuálisról az automatikusra módosult.

![a Change Tracking részleteinek képe](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Keresési naplók

A portálon megjelenő részletek mellett a keresések a naplókon is elvégezhető. Nyissa meg a **change Tracking** lapot, és kattintson a **log Analytics**lehetőségre, amely megnyitja a **naplók** lapot.

### <a name="sample-queries"></a>Példák a lekérdezésekre

A következő táblázat a megoldás által gyűjtött változási rekordokra vonatkozó példákat tartalmaz:

|Lekérdezés  |Leírás  |
|---------|---------|
|ConfigurationData<br>&#124;where ConfigDataType = = "WindowsServices" és SvcStartupType = = "Auto"<br>&#124;ahol a SvcState = = "leállítva"<br>&#124;arg_max (TimeGenerated, *) összegzése SoftwareName, számítógép szerint         | Azon Windows-szolgáltatások legfrissebb leltározási rekordjait jeleníti meg, amelyek az automatikusra lettek beállítva, de a rendszer leállítottként jelentett.<br>Az eredmények az adott SoftwareName és számítógép legutóbbi rekordjára korlátozódnak      |
|Konfigurációváltozás<br>&#124;ahol a ConfigChangeType = = "szoftver" és a ChangeCategory = = "eltávolítva"<br>&#124;Order by TimeGenerated desc|Az eltávolított szoftverek módosítási rekordjait jeleníti meg|

## <a name="alert-on-changes"></a>Riasztás a változásokról

Az Change Tracking és a leltár kulcsfontosságú funkciója, hogy riasztást küldjön a konfigurációs állapotról, valamint a hibrid környezet konfigurációs állapotának változásairól.

A következő példában a képernyőképen látható, hogy a fájl `C:\windows\system32\drivers\etc\hosts` módosult a gépen. Ez a fájl azért fontos, mert a Windows a gazdagépek által használt IP-címek feloldására használja, és elsőbbséget élvez a DNS-sel szemben, ami csatlakozási problémákat eredményezhet, vagy a forgalom rosszindulatú vagy más veszélyes webhelyekre való átirányítását okozhatja.

![A gazdagépek fájljának módosítását bemutató diagram](./media/change-tracking/changes.png)

Ha tovább szeretné elemezni a változást, lépjen a keresés a **log Analytics**gombra kattintva. A naplóbeli keresés után keresse meg a tartalom módosításait a Hosts fájlra a `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`lekérdezéssel. Ez a lekérdezés olyan módosításokat keres, amelyekben szerepel a fájl tartalmának módosítása azon fájlok esetében, amelyek teljes elérési útja tartalmazza a "hosts" szót. Egy adott fájlt is kérhet, ha az elérési út részét a teljes űrlapra módosítja (például `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Miután a lekérdezés visszaadja a kívánt eredményeket, kattintson az **új riasztási szabály** gombra a napló keresési felületén a riasztás létrehozása lap megnyitásához. Ezt a folyamatot a Azure Portal **Azure monitor** is elérheti. A riasztás-létrehozási élményben tekintse meg újra a lekérdezést, és módosítsa a riasztási logikát. Ebben az esetben azt szeretné, hogy a riasztás akkor aktiválódik, ha a környezetben lévő összes gépen még egy változás észlelhető.

![A Hosts fájl változásainak nyomon követésére szolgáló módosítási lekérdezést bemutató kép](./media/change-tracking/change-query.png)

A feltétel logikájának beállítása után hozzon létre műveleti csoportokat, hogy végrehajtsa a riasztás indítására reagáló műveleteket. Ebben az esetben az elküldött e-maileket és egy ITSM jegyet kell létrehozni.  Számos más hasznos művelet is elvégezhető, például egy Azure-függvény, Automation runbook, webhook vagy logikai alkalmazás elindítása.

![Egy, a módosítással kapcsolatos riasztást konfiguráló rendszerképet](./media/change-tracking/action-groups.png)

Az összes paraméter és logika beállítása után alkalmazhatjuk a riasztást a környezetre.

### <a name="alert-suggestions"></a>Riasztási javaslatok

A Hosts fájl változásairól való riasztás a riasztások Change Tracking vagy leltározási adatokhoz való helyes alkalmazása, amely többek között a riasztások számára is hasznos, beleértve az alábbi szakaszban leírt példákkal együtt meghatározott eseteket is.

|Lekérdezés  |Leírás  |
|---------|---------|
|Konfigurációváltozás <br>&#124;ahol a ConfigChangeType = = "files" és a FileSystemPath tartalmazza a "c:\\Windows\\system32\\Drivers\\"|Hasznos a rendszerkritikus fájlok változásainak nyomon követéséhez|
|Konfigurációváltozás <br>&#124;ahol a FieldsChanged tartalmazza a "FileContentChecksum" és a FileSystemPath = = "c:\\Windows\\system32\\illesztőprogramok\\stb\\gazdagépek"|Hasznos a legfontosabb konfigurációs fájlok módosításainak nyomon követéséhez|
|Konfigurációváltozás <br>&#124;ahol a ConfigChangeType = = "WindowsServices" és a SvcName tartalmazza a "W3SVC" és a SvcState = = "leállítva"|Hasznos a kritikus fontosságú szolgáltatások változásainak nyomon követéséhez|
|Konfigurációváltozás <br>&#124;ahol a ConfigChangeType = = "DAEMONS" és a SvcName tartalmazza az "SSH" és a SvcState! = "Running"|Hasznos a kritikus fontosságú szolgáltatások változásainak nyomon követéséhez|
|Konfigurációváltozás <br>&#124;ahol a ConfigChangeType = = "szoftver" és a ChangeCategory = = "hozzáadva"|Olyan környezetekhez hasznos, amelyeknek szükségük van a zárolt szoftveres konfigurációkra|
|ConfigurationData <br>&#124;ahol a SoftwareName a "monitoring Agent" és a CurrentVersion! = "8.0.11081.0" kifejezést tartalmazza|Hasznos, ha azt látja, hogy mely gépeken van telepítve elavult vagy nem megfelelő szoftververzió. A legutóbbi jelentett konfigurációs állapotot jelenti, nem módosul.|
|Konfigurációváltozás <br>&#124;where RegistryKey = = @ "HKEY_LOCAL_MACHINE\\szoftver\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Hasznos a kritikus víruskeresési kulcsok változásainak követéséhez|
|Konfigurációváltozás <br>&#124;ahol a RegistryKey a következőt tartalmazza: @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\paraméterek\\FirewallPolicy"| A tűzfalbeállítások változásainak követéséhez hasznos|

## <a name="next-steps"></a>További lépések

A megoldás használatával kapcsolatos további információkért tekintse meg az Change Tracking szóló oktatóanyagot:

> [!div class="nextstepaction"]
> [A környezet változásainak megoldása](automation-tutorial-troubleshoot-changes.md)

* A [naplóbeli keresések használata Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) a részletes változások követésére szolgáló információk megtekintéséhez.
