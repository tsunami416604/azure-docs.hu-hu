---
title: Változások követése az Azure Automation segítségével
description: A Változáskövetés megoldás segít azonosítani a környezetében előforduló szoftver- és Windows-szolgáltatás-módosításokat.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682972"
---
# <a name="track-environment-changes-with-change-tracking"></a>A környezeti változások nyomon követése a változások követésével

Ez a cikk segít a változáskövetési megoldás használatával a környezet változásainak egyszerű azonosításához. A megoldás nyomon követi a következő konfigurációs módosításokat, hogy segítsen a működési problémák azonosításában:

- Windows szoftver
- Linux szoftver (csomagok)
    >[!NOTE]
    >A Változáskövetés csak a terjesztési csomagkezelővel kezelt szoftvereket követi nyomon.

- Windows és Linux fájlok
- Windows rendszerleíró kulcsok
- Windows-szolgáltatások
- Linux démonok

A megoldás engedélyezése után megtekintheti a figyelt számítógépek változásainak összegzését, ha az Automation-fiók **Konfigurációkezelés** csoportában kiválasztja a **Változáskövetés** lehetőséget.

> [!NOTE]
> Az Azure Automation változáskövetés nyomon követi a virtuális gépek változásait. Az Azure Resource Manager-szolgáltatás változásainak nyomon követéséhez olvassa el az Azure Resource Graph [módosítási előzményeit.](../governance/resource-graph/how-to/get-resource-changes.md)

Megtekintheti a számítógép módosításait, majd részletezheti az egyes események részleteit. Legördülő menük a diagram tetején érhetők el, hogy korlátozzák a diagramot és a részletes információkat a változás típusa és az időtartományok alapján. A diagramra kattintva és húzással egyéni időtartományt is kijelölhet. **A módosítás típusa** az alábbi értékek egyike **lesz: Események**, **Démonok**, **Fájlok**, **Rendszerleíró adatbázis**, **Szoftver**, **Windows-szolgáltatások.** A kategória a módosítás típusát jeleníti meg, és **hozzáadható,** **módosítható**vagy **eltávolítható.**

![A Változáskövetés irányítópult képe](./media/change-tracking/change-tracking-dash01.png)

Egy változásra vagy eseményre kattintva részletes információkat talál a változásról. Amint a példából is látható, a szolgáltatás indítási típusa Kézi ról Automatikusra változott.

![Változáskövetési részletek képe](./media/change-tracking/change-tracking-details.png)

A telepített szoftverek, a Windows-szolgáltatások, a Windows-beállításjegyzék és a fájlok, valamint a Linux-démonok módosításait a rendszer elküldi az Azure Monitor szolgáltatásnak a felhőben feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat. A Változáskövetés irányítópulton található információk használatával könnyen megtekintheti a kiszolgálói infrastruktúrában végrehajtott módosításokat.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

### <a name="windows-operating-systems"></a>Windows operációs rendszerek

A Windows operációs rendszer következő verziói hivatalosan támogatottak a Windows-ügynök számára:

* Windows Server 2008 R2 vagy újabb

### <a name="linux-operating-systems"></a>Linux operációs rendszerek

A következő Linux disztribúciók hivatalosan támogatottak. A Linux-ügynök azonban más, nem felsorolt disztribúciókon is futtathat. Eltérő rendelkezés hiányában minden kisebb kiadás támogatott minden felsorolt főverzióhoz.

#### <a name="64-bit"></a>64 bites

* CentOS 6 és 7
* Amazon Linux 2017.09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux Server 6 és 7
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS és 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32 bites

* CentOS, 6.
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14.04 LTS és 16.04 LTS

## <a name="limitations"></a>Korlátozások

A Változáskövetés megoldás jelenleg nem támogatja a következő elemeket:

* Rekurzió a Windows rendszerleíró adatbázisának nyomon követéséhez
* Hálózati fájlrendszerek
* Különböző telepítési módszerek
* ***.exe** fájlok Windows-hoz

Egyéb korlátozások:

* A **Maximális fájlméret** oszlop és értékek nincsenek használatban az aktuális implementációban.
* Ha 30 perces gyűjtési ciklusban több mint 2500 fájlt gyűjt, a megoldás teljesítménye csökkenhet.
* Ha a hálózati forgalom nagy, a változások rekordjainak megjelenítése akár hat órát is igénybe vehet.
* Ha a számítógép leállítása közben módosítja a konfigurációt, előfordulhat, hogy a számítógép az előző konfigurációhoz tartozó módosításokat tesz közzé.

## <a name="known-issues"></a>Ismert problémák

A Változáskövetés megoldás jelenleg a következő problémákat tapasztalja:

* A gyorsjavítások frissítései nem gyűjtik a rendszer a Windows Server 2016 Core RS3 rendszerű gépeken.
* A Linux démonok megmutathatják a megváltozott állapotot, még akkor is, ha nem volt változás. Ez annak köszönhető, `SvcRunLevels` hogy a mező rögzítése.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A változáskövetéshez kifejezetten a következő címekszükségesek. Az ezekhez a címekhez irányuló kommunikáció a 443-as portot használja.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Helyettesítő karakteres, rekurziós és környezeti beállítások

A Rekurzió lehetővé teszi helyettesítő karakterek megadását a könyvtárak közötti nyomon követés egyszerűsítése érdekében, valamint a környezeti változókat, hogy a fájlokat több vagy dinamikus meghajtónévvel rendelkező környezetekben is nyomon követhesse. Az alábbi lista a rekurzió konfigurálásakor ismert gyakori információkat tartalmazza:

* Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
* Helyettesítő karakterek, amelyek csak az elérési út utolsó szegmensében használhatók, például c:\folder\\file* vagy /etc/*.conf.
* Ha egy környezeti változó elérési útja érvénytelen, az érvényesítés sikeres, de ez az elérési út sikertelen lesz a Készlet futtatásakor.
* Kerülje az általános elérési utakat az elérési út beállításakor, mivel az ilyen típusú beállítás túl sok mappát eredményezhet.

## <a name="change-tracking-data-collection-details"></a>Változáskövetési adatgyűjtés részletei

Az alábbi táblázat a módosítások típusainak adatgyűjtési gyakoriságát mutatja be. Az aktuális állapot adatpillanatképe minden típusesetében legalább 24 óránként frissül:

| **Típus módosítása** | **Gyakoriság** |
| --- | --- |
| Windows rendszerleíró adatbázis | 50 perc |
| Windows-fájl | 30 perc |
| Linux fájl | 15 perc |
| Windows-szolgáltatások | 10 másodperc és 30 perc között</br> Alapértelmezett: 30 perc |
| Linux démonok | 5 perc |
| Windows szoftver | 30 perc |
| Linux szoftver | 5 perc |

Az alábbi táblázat a nyomon követett cikkkorlátokat mutatja be gépenként a változáskövetéshez.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Registry|250||
|Windows szoftver|250|Nem tartalmaz szoftvergyorsjavításokat|
|Linux csomagok|1250||
|Szolgáltatások|250||
|Daemon|250||

A Log Analytics átlagos adatforgalma a változáskövetést használó gépekesetében körülbelül 40 MB havonta. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetet, hogy pontosan milyen használattal rendelkezik.

### <a name="windows-service-tracking"></a>A Windows szolgáltatás nyomon követése

A Windows-szolgáltatások alapértelmezett gyűjtési gyakorisága 30 perc. A gyakoriság konfigurálásához nyissa meg a **Változások követése című.** A **Windows-szolgáltatások** lap **Beállítások szerkesztése** csoportjában van egy csúszka, amely lehetővé teszi a Windows-szolgáltatások gyűjtési gyakoriságának módosítását 10 másodpercről akár 30 percre. Mozgassa a csúszkát a kívánt frekvenciára, és automatikusan menti azt.

![A Windows-szolgáltatások csúszkája](./media/change-tracking/windowservices.png)

Az ügynök csak a változásokat követi nyomon, ez optimalizálja az ügynök teljesítményét. Ha a szolgáltatás visszaáll az eredeti állapotába, a magas küszöbérték beállítása elmulaszthatja a módosításokat. Ha a frekvenciát kisebb értékre állítja, akkor olyan módosításokat kaphat, amelyek egyébként kimaradhatnak.

> [!NOTE]
> Míg az ügynök nyomon követheti a változásokat egy 10 másodperces időközre, az adatok továbbra is néhány percet vesz igénybe a portálon való megjelenítéshez. A portálon való megjelenítéshez vezető idő alatt végrehajtott módosításokat a rendszer továbbra is nyomon követi és naplózza.

### <a name="registry-key-change-tracking"></a>Rendszerleíró kulcs változásának nyomon követése

A beállításkulcsok módosításainak figyelése a külső kódok és rosszindulatú programok aktiválását szolgáló bővíthetőségi pontok pontos meghatározásához használható. Az alábbi lista az előre konfigurált beállításkulcsok listáját tartalmazza. Ezek a kulcsok konfigurálva vannak, de nincsenek engedélyezve. A beállításkulcsok nyomon követéséhez engedélyeznie kell mindegyiket.

> [!div class="mx-tdBreakAll"]
> |Beállításjegyzék kulcsa | Cél |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Azokat a gyakori automatikus indítási bejegyzéseket figyeli, amelyek közvetlenül a Windows Intézőbe csatlakoztatnak, és általában az Explorer.exe programmal futnak.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Az indításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | A leállításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Figyeli azokat a kulcsokat, amelyek betöltődnek, mielőtt a felhasználó bejelentkezne a Windows-fiókjába. A kulcs 64 bites számítógépeken futó 32 bites programokhoz használatos.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Figyeli az alkalmazásbeállítások változásait.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Azokat a gyakori automatikus indítási bejegyzéseket figyeli, amelyek közvetlenül a Windows Intézőbe csatlakoztatnak, és általában az Explorer.exe programmal futnak.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Azokat a gyakori automatikus indítási bejegyzéseket figyeli, amelyek közvetlenül a Windows Intézőbe csatlakoztatnak, és általában az Explorer.exe programmal futnak.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Az ikonátfedés-kezelő regisztrációjának figyelői.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | A 64 bites számítógépeken futó 32 bites programok ikonátfedés-kezelőjének regisztrációját figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Az Internet Explorer új böngészősegítő objektumbővítményeit figyeli. Az aktuális oldal DOKUMENTUMobjektum-modelljének (DOM) elérésére és a navigáció vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Az Internet Explorer új böngészősegítő objektumbővítményeit figyeli. Az aktuális lap dokumentumobjektum-modelljének (DOM) elérésére és a 64 bites számítógépeken futó 32 bites programok navigációjának vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például az egyéni eszközmenük és az egyéni eszköztárgombok figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például az egyéni eszközmenük és a 64 bites számítógépeken futó 32 bites programok egyéni eszköztárgombjainak figyelői.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | A wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és vidc társított 32 bites illesztőprogramokat figyeli. Hasonló a RENDSZER [illesztőprogramok] szakaszához. INI fájlt.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | A wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és vidc 32 bites, 64 bites számítógépeken futó programokhoz kapcsolódó 32 bites illesztőprogramokat figyeli. Hasonló a RENDSZER [illesztőprogramok] szakaszához. INI fájlt.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Az ismert vagy általánosan használt rendszerDL-ek listáját figyeli; ez a rendszer megakadályozza, hogy az emberek kihasználják a gyenge alkalmazás könyvtár engedélyek csepegés trójai faló változatai rendszer DLS.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | A Winlogon, a Windows operációs rendszer interaktív bejelentkezési támogatási modelljétől érkező eseményértesítések fogadására képes csomagok listáját figyeli.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Változáskövetés engedélyezése

A változások nyomon követésének megkezdéséhez engedélyeznie kell a Változáskövetési megoldást. A gépek et számos módon lehet berakni a követés módosításához. A megoldás ajánlott és támogatott módjai az alábbiakban találhatók.

* [Virtuális gépről](automation-onboard-solutions-from-vm.md)
* [Több gép böngészéséből](automation-onboard-solutions-from-browse.md)
* [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Egy Azure Automation runbook](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Változáskövetés konfigurálása

A számítógépek megoldásba való csatlakoztatásáról a [Bevezetési automatizálási megoldások című](automation-onboard-solutions-from-automation-account.md)témakörben olvashat. Ha már rendelkezik a változáskövetési megoldással rendelkező géppel, beállíthatja az elemek nyomon követését. Ha engedélyezi egy új fájl vagy beállításkulcs nyomon követését, akkor az mindkét változáskövetéshez engedélyezve van.

A windowsos és linuxos fájlok változásainak nyomon követéséhez a fájlok MD5-kimondottait használjuk. A tézisek kivéit ezután annak észlelésére használják, hogy történt-e változás az utolsó készlet óta.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Fájlintegritás-figyelés engedélyezése az Azure Security Centerben

Az Azure Security Center hozzáadta a fájlintegritás figyelését (FIM), amely az Azure change tracking-re épül. Míg a FIM csak a fájlokat és a nyilvántartásokat figyeli, a teljes változáskövetési megoldás a következőket is tartalmazza:

- Szoftverváltozások
- Windows-szolgáltatások
- Linux démonok

Ha már engedélyezte a FIM-et, és ki szeretné próbálni a teljes változáskövetési megoldást, akkor a következő lépéseket kell végrehajtania. Ez a folyamat nem távolítja el a beállításokat.

> [!NOTE]
> A teljes változáskövetési megoldás engedélyezése további díjakat okozhat, további információkért lásd: [Automatizálási díjszabás](https://azure.microsoft.com/pricing/details/automation/).

1. Távolítsa el a figyelési megoldást úgy, hogy a munkaterületre navigál, és megtalálja a [telepített figyelési megoldások listájában.](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)
2. Kattintson a megoldás nevére az összefoglaló oldal megnyitásához, majd kattintson a Törlés gombra, amint azt [a Figyelési megoldás eltávolítása című](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)részben részletezi.
3. Engedélyezze újra a megoldást úgy, hogy az Automation-fiókra navigál, és a Konfigurációkezelés csoportban a **Változáskövetés** lehetőséget **választja.**
4. Erősítse meg a munkaterület-beállítás részleteit, és kattintson **az Engedélyezés gombra.**

## <a name="configure-file-content-change-tracking"></a>Fájltartalom-módosítás-követés konfigurálása

Megtekintheti a fájlmódosítás előtti és utáni tartalmat a fájltartalom-változások követésével. Ez a funkció Windows és Linux fájlokhoz érhető el. A fájl minden egyes módosítása esetén a fájl tartalma egy tárfiókban tárolódik. A fájl a módosítás előtt és után jelenik meg, szövegközi vagy egymás melletti. További információ: [A nyomon követett fájlok tartalmának megtekintése.](change-tracking-file-contents.md)

![fájl módosításának megtekintése](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>A Windows beállításkulcsainak konfigurálása a nyomon követéshez

A rendszerleíró kulcs követésének windowsos számítógépeken történő konfigurálásához kövesse az alábbi lépéseket:

1. Az Automation-fiókban válassza a **Változáskövetés lehetőséget** a **Konfigurációkezelés csoportban.** Kattintson **a Beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A Változáskövetés lapon válassza a **Windows rendszerleíró adatbázis**lehetőséget, majd kattintson a + **Hozzáadás** gombra a nyomon követő új beállításkulcs hozzáadásához.
3. A **Windows beállításjegyzékében**adja meg a nyomon követő kulcs adatait, majd kattintson a **Mentés gombra.**

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, hogy a beállítás alkalmazva van-e.        |
|Elem neve     | A nyomon követendő rendszerleíró kulcs rövid neve.        |
|Csoport     | A beállításkulcsok logikai csoportosításának csoportneve.        |
|Windows-beállításkulcs   | A beállításkulcs ellenőrzésének elérési útja. Például: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Fájlkövetés konfigurálása Windows rendszeren

A fájlkövetés windowsos számítógépeken történő konfigurálásához kövesse az alábbi lépéseket:

1. Az Automation-fiókban válassza a **Változáskövetés lehetőséget** a **Konfigurációkezelés csoportban.** Kattintson **a Beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A Változáskövetés lapon válassza a **Windows-fájlok**lehetőséget, majd kattintson **a + Hozzáadás** gombra a nyomon követő új fájl hozzáadásához.
3. A **Windows-fájl hozzáadása a változások követéséhez**mezőbe írja be a nyomon követni és a **Mentés**gombra kattintó fájl adatait.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Igaz, ha a beállítás van alkalmazva, és hamis egyébként.        |
|Elem neve     | A követendő fájl rövid neve.        |
|Csoport     | A fájlok logikai csoportosításának csoportneve.        |
|Elérési út megadása     | A fájl ellenőrzésének elérési útja, például **c:\temp\\\*.txt**<br>Használhat környezeti változókat is, `%winDir%\System32\\\*.*`például .       |
|Rekurzió     | Igaz, ha rekurziót használ, amikor a nyomon követendő elemet keresi, és a Hamis egyéb.        |
|Fájltartalom feltöltése minden beállításhoz| Igaz a fájltartalom feltöltéséhez a nyomon követett módosításokon, és a Hamis egyéb.|

## <a name="configure-file-tracking-on-linux"></a>Fájlkövetés konfigurálása Linuxon

A következő lépésekkel konfigurálhatja a fájlkövetést Linux rendszerű számítógépeken:

1. Az Automation-fiókban válassza a **Változáskövetés lehetőséget** a **Konfigurációkezelés csoportban.** Kattintson **a Beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
2. A Változáskövetés lapon válassza a **Linux-fájlok**lehetőséget, majd kattintson **a + Hozzáadás** gombra a nyomon követő új fájl hozzáadásához.
3. A **Linux-fájl hozzáadása a változáskövetéshez**mezőbe írja be a nyomon követni és a **Mentés**gombra kattintó fájl vagy könyvtár adatait.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, hogy a beállítás alkalmazva van-e.        |
|Elem neve     | A követendő fájl rövid neve.        |
|Csoport     | A fájlok logikai csoportosításának csoportneve.        |
|Elérési út megadása     | A fájl ellenőrzésének elérési útja. Például: "/etc/*.conf"       |
|Elérési út típusa     | A követendő elem típusa, a lehetséges értékek a Fájl és a Könyvtár.        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Figyelmen kívül hagyása** – Figyelmen kívül hagyja a szimbolikus hivatkozásokat, és nem tartalmazza a hivatkozott fájlokat/könyvtárakat.<br>**Follow** - Követi a szimbolikus hivatkozásokat a rekurzió során, és tartalmazza a hivatkozott fájlokat/könyvtárakat is.<br>**Kezelés** - Követi a szimbolikus linkeket, és lehetővé teszi a visszaadott tartalom módosítását.     |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **Hamis**.|

> [!NOTE]
> A „Kezelés” használata nem ajánlott, mert a fájltartalom lekérése nem támogatott.

## <a name="search-logs"></a>Keresési naplók

Különböző kereséseket tehet a naplók alapján a változásrekordokért. Ha a Változáskövetés lapon meg van nyitva, kattintson a **Log Analytics gombra,** ez megnyitja a Naplók lapot. Az alábbi táblázat mintanapló-kereséseket tartalmaz a megoldás által gyűjtött módosítási rekordokra:

|Lekérdezés  |Leírás  |
|---------|---------|
|Konfigurációs adatok<br>&#124; ahol configDataType == "WindowsServices" és SvcStartupType == "Auto"<br>&#124; ahol SvcState == "Megállt"<br>&#124; összegezheti arg_max(TimeGenerated, *) a SoftwareName, Computer         | A Windows-szolgáltatások legutóbbi készletrekordjait jeleníti meg, amelyek automatikusra voltak állítva, de leállítva jelentették őket<br>Az eredmények az adott SoftwareName és Computer legutóbbi rekordjára korlátozódnak.      |
|ConfigurationChange (Konfigurációmódosítása)<br>&#124; ahol configChangeType == "Szoftver" és ChangeCategory == "Removed"<br>&#124; sorrendet a TimeGenerated desc|Az eltávolított szoftverek módosítási rekordjainak megjelenítése|

## <a name="alert-on-changes"></a>Értesítés a változásokról

A változáskövetés egyik fő képessége a konfigurációs állapot ra és a hibrid környezet konfigurációs állapotának bármilyen módosítására vonatkozó riasztás. A következő példa azt mutatja, hogy a **C:\windows\system32\drivers\etc\hosts** fájl módosult a számítógépen. Ez a fájl azért fontos, mert a Windows az állomásnevek IP-címekre való feloldására használja. Ez a művelet elsőbbséget élvez a DNS-sel szemben, és kapcsolódási problémákat vagy a forgalom rosszindulatú vagy más módon veszélyes webhelyekre való átirányítását eredményezheti.

![A gazdagépfájl módosítását bemutató diagram](./media/change-tracking/changes.png)

A módosítás további elemzéséhez nyissa meg a Naplókeresés a **Log Analytics**elemre kattintva című naplót. A Naplókeresés után keresse meg a Hosts fájl `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`tartalomváltozásait a lekérdezéssel. Ez a lekérdezés olyan módosításokat keres, amelyek a fájltartalom módosítását tartalmazzák azon fájlok esetében, amelyek teljesen minősített elérési útja tartalmazza a "gazdagépek" szót. Egy adott fájlt úgy is kérhet, hogy az elérési út `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`részt teljesen minősített űrlapra (például ) módosítja.

Miután a lekérdezés visszaadja a kívánt eredményt, kattintson az **Új riasztási szabály** a naplókeresésben a riasztás létrehozási lapjának megnyitásához. Ezt a felületet az **Azure Monitoron** keresztül is megnyithatja az Azure Portalon. 

Ellenőrizze újra a lekérdezést, és módosítsa a riasztási logikát. Ebben az esetben azt szeretné, hogy a riasztás aktiválódik, ha a környezetben lévő összes gépen csak egy változás észlelhető.

![A gazdagépfájl módosítási lekérdezését ábrázoló kép](./media/change-tracking/change-query.png)

A feltétellogika beállítása után rendeljen hozzá műveletcsoportokat az aktivált riasztásra adott válaszként végrehajtandó műveletekhez. Ebben az esetben beállítottam az elküldendő e-maileket és egy ITSM jegyet.  Számos más hasznos művelet is eltehető, például egy Azure-függvény, automation runbook, webhook vagy logikai alkalmazás aktiválása.

![Egy műveletcsoportot konfiguráló kép, amely riasztást ad a változásról](./media/change-tracking/action-groups.png)

Miután az összes paraméter és logika be van állítva, alkalmazhatjuk a riasztást a környezetre.

### <a name="alert-suggestions"></a>Riasztási javaslatok

Bár a gazdagépfájl módosításainak riasztása a változáskövetés vagy a készletadatok riasztási vagy készletadatainak megfelelő alkalmazása, sokkal több riasztási forgatókönyv van, beleértve a példalekérdezésekkel együtt definiált eseteket az alábbi szakaszban.

|Lekérdezés  |Leírás  |
|---------|---------|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol a ConfigChangeType == "Files" és\\\\a FileSystemPath tartalmazza a " c: windows system32\\illesztőprogramok\\"|Hasznos a rendszerkritikus fájlok változásainak nyomon követéséhez|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol FieldsChanged tartalmaz "FileContentChecksum" és FileSystemPath\\==\\\\"c:\\\\windows system32 illesztőprogramok stb állomások"|Hasznos a kulcsfontosságú konfigurációs fájlok módosításának nyomon követéséhez|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol a ConfigChangeType == "WindowsServices" és az SvcName tartalmazza a "w3svc" és az SvcState == "Stopped"|Hasznos a rendszerkritikus szolgáltatások változásainak nyomon követéséhez|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol a ConfigChangeType == "Démonok" és az SvcName "ssh" és SvcState != "Running"|Hasznos a rendszerkritikus szolgáltatások változásainak nyomon követéséhez|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol configChangeType == "Szoftver" és ChangeCategory == "Added"|Hasznos olyan környezetekben, ahol le kell zárni a szoftverkonfigurációkat|
|Konfigurációs adatok <br>&#124; ahol a SoftwareName tartalmazza a "Monitoring Agent" és a CurrentVersion != "8.0.11081.0"|Hasznos annak megtekintéséhez, hogy mely gépeken van elavult vagy nem megfelelő szoftververzió. Az utoljára jelentett konfigurációs állapotot jelenti, nem a módosításokat.|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124; ahol RegistryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Hasznos a kritikus vírusbillentyűk változásainak nyomon követéséhez|
|ConfigurationChange (Konfigurációmódosítása) <br>&#124;, ahol a RegistryKey\\tartalmazza\\a\\\\@"HKEY_LOCAL_MACHINE\\\\SYSTEM CurrentControlSet Services SharedAccess Parameters FirewallPolicy"| Hasznos a tűzfalbeállításainak változásainak nyomon követéséhez|

## <a name="next-steps"></a>További lépések

Látogasson el a változáskövetés ről szóló oktatóanyagba, ha többet szeretne megtudni a megoldás használatáról:

> [!div class="nextstepaction"]
> [A környezet változásainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)

* Az [Azure Monitor naplóiban végzett naplókeresések](../log-analytics/log-analytics-log-searches.md) használatával részletes változáskövetési adatokat tekinthet meg.
