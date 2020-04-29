---
title: A Azure Automation Change Tracking és leltárának áttekintése
description: A Change Tracking és a leltár segítséget nyújt a környezetében előforduló szoftverek és Microsoft-szolgáltatások változásainak azonosításához.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 1208e08f7b85e893ba754bdbdf71a2da4f68c90a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509065"
---
# <a name="overview-of-change-tracking-and-inventory"></a>A Change Tracking és a leltár áttekintése

Ez a cikk a Azure Automation Change Tracking és leltározását ismerteti. Ez a szolgáltatás nyomon követi a virtuális gépek és a kiszolgálói infrastruktúra változásait, így segít a terjesztési csomag kezelőjével kezelt szoftverekkel kapcsolatos működési és környezeti problémák azonosításában. A Change Tracking és a leltár által követett elemek a következők: 

- Windows-szoftver
- Linuxos szoftverek (csomagok)
- Windows-és Linux-fájlok
- Windows-beállításkulcsok
- Microsoft-szolgáltatások
- Linux-démonok

A Change Tracking és a leltár beolvassa az adatait a felhőben lévő Azure Monitor szolgáltatásból. Az Azure a felügyelt kiszolgálókon a telepített szoftverekre, a Microsoft-szolgáltatásokra, a Windows-beállításjegyzékre és-fájlokra, valamint a Linux-démonokra vonatkozó módosításokat küld a Azure Monitor feldolgozásra A felhőalapú szolgáltatás a kapott adatokra alkalmazza a logikát, rögzíti azt, és elérhetővé teszi. 

> [!NOTE]
> A Azure Resource Manager tulajdonságok változásainak nyomon követéséhez tekintse meg az Azure Resource Graph [változási előzményeit](../governance/resource-graph/how-to/get-resource-changes.md).

A Change Tracking és a leltár jelenleg nem támogatja a következő elemeket:

* Rekurzió a Windows beállításjegyzékének nyomon követéséhez
* Hálózati fájlrendszerek
* Különböző telepítési módszerek
* ***. exe** -fájlok a Windows rendszerhez

Egyéb korlátozások:

* A **Maximális fájlméret** oszlop és az értékek nem használhatók az aktuális implementációban.
* Ha egy 30 perces gyűjtési ciklusban több mint 2500 fájlt gyűjt, a megoldás teljesítménye csökkenhet.
* Ha a hálózati forgalom magas, a rekordok módosítása akár hat órát is igénybe vehet.
* Ha a számítógép leállítása közben módosítja a konfigurációt, a számítógép az előző konfigurációhoz tartozó módosításokat tehet közzé.

A Change Tracking és a leltár jelenleg a következő problémákba ütközik:

* A gyorsjavítások frissítései nem lesznek összegyűjtve a Windows Server 2016 Core RS3-gépeken.
* Előfordulhat, hogy a Linux-démonok módosult állapotot mutatnak, de nem történt változás. Ez a probléma a Azure Monitor [konfigurációváltozás](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) -naplóban `SvcRunLevels` található adatrögzítési mód miatt fordul elő.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Change Tracking és a leltár, valamint a Azure Monitor Log Analytics ügynökök a Windows és a Linux operációs rendszereken egyaránt támogatottak.

### <a name="windows-operating-systems"></a>Windows operációs rendszerek

A hivatalosan támogatott Windows operációs rendszer verziója Windows Server 2008 R2 vagy újabb.

### <a name="linux-operating-systems"></a>Linux operációs rendszerek

Az alábbiakban tárgyalt Linux-disztribúciókat a Linux rendszerhez készült Log Analytics-ügynök hivatalosan támogatja. A Linux-ügynök azonban más, nem felsorolt disztribúciókban is futhat. Hacsak másként nincs jelezve, az összes alverzió támogatott a felsorolt főbb verziók esetében.

#### <a name="64-bit-linux-operating-systems"></a>64 bites Linux operációs rendszerek

* CentOS 6 és 7
* Amazon Linux 2017,09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux 6. és 7. kiszolgáló
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit-linux-operating-systems"></a>32 bites Linux operációs rendszerek

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS és 16,04 LTS

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A Change Tracking és a leltárnak kifejezetten a következő táblázatban felsorolt hálózati címeket kell megadnia. A címekkel való kommunikáció az 443-es portot használja.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>A Change Tracking és a leltár felhasználói felülete

A Azure Portal Change Tracking és leltár használatával megtekintheti a figyelt számítógépek változásainak összefoglalását. A funkció a **change Tracking (változások követése** ) lehetőség kiválasztásával érhető **el az Automation-** fiókban. 

![Change Tracking irányítópult](./media/change-tracking/change-tracking-dash01.png)

A legördülő lista az irányítópult tetején található, hogy korlátozza a Change Tracking diagramot és a részletes információkat a változás típusa és az időtartományok alapján. A diagramra kattintva és húzásával egyéni időtartományt is kijelölhet. 

A módosításra vagy eseményre kattintva megtekintheti annak részleteit. Az elérhető változási típusok a következők:

* Események
* Démonok
* Fájlok
* Registry
* Szoftverek
* Microsoft-szolgáltatások

Lehetőség van az egyes módosítások hozzáadására, módosítására vagy eltávolítására. Az alábbi példában megtekintheti a szolgáltatás indítási típusának módosítását Manuálisról automatikusra.

![Change Tracking részletei](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>A fájlok változásainak követése

A Windows és a Linux rendszerű fájlok változásainak nyomon követéséhez a Change Tracking és a leltár a fájlok MD5 kivonatait használja. A szolgáltatás a kivonatok használatával észleli, hogy történtek-e változások az utolsó leltár óta.

## <a name="tracking-of-file-content-changes"></a>A fájl tartalmának változásai nyomon követése

A Change Tracking és a leltár lehetővé teszi egy Windows-vagy Linux-fájl tartalmának megtekintését a fájl módosítása előtt és után. Egy fájl minden módosítása esetén a Change Tracking és a leltár egy [Azure Storage-fiókban](../storage/common/storage-create-storage-account.md)tárolja a fájl tartalmát. A fájl nyomon követése után megtekintheti annak tartalmát a módosítás előtt vagy után. A tartalmakat soronként vagy egymás mellett is megtekintheti. 

![Egy fájl változásainak megtekintése](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Beállításkulcsok nyomon követése

A Change Tracking és a leltár lehetővé teszi a beállításkulcsok változásainak figyelését. A figyeléssel megadhatja azokat a bővíthetőségi pontokat, amelyekben a harmadik féltől származó kód és a kártevő is aktiválható. Az alábbi táblázat az előre konfigurált (de nem engedélyezett) beállításkulcsokat sorolja fel. A kulcsok nyomon követéséhez engedélyeznie kell mindegyiket.

> [!div class="mx-tdBreakAll"]
> |Beállításjegyzék kulcsa | Cél |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az **Explorer. exe**használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Az indításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | A leállításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Figyeli azokat a kulcsokat, amelyek betöltődik, mielőtt a felhasználó bejelentkezik a Windows-fiókba. A kulcs a 64 bites számítógépeken futó 32 bites alkalmazásokhoz használatos.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Az Alkalmazásbeállítások változásainak figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az **Explorer. exe**használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Figyeli az olyan általános indítási bejegyzéseket, amelyek közvetlenül a Windows Intézőben kapcsolódnak, és általában az **Explorer. exe**használatával futtatják a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők az ikon átfedését kezelő regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők a 64 bites számítógépeken futó 32 bites alkalmazásokhoz tartozó ikon átfedési kezelői regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére és a Navigálás vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére, valamint a 64 bites számítógépeken futó 32 bites alkalmazások navigálásának vezérlésére használatos.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Figyelők az új Internet Explorer-bővítményekhez, például az egyéni eszközök menüihez és az egyéni eszköztárak gombjaihoz.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például a 64 bites számítógépeken futó 32 bites alkalmazásokhoz készült egyéni eszköztárak és egyéni eszköztárgombok figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli. Hasonló a **System. ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli a 64 bites számítógépeken futó 32 bites alkalmazásokhoz. Hasonló a **System. ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Az ismert vagy leggyakrabban használt rendszerdll-fájlok listáját figyeli. Ez a rendszer megakadályozza, hogy a felhasználók a rendszer DLL-fájljainak ledobásával kihasználják a gyenge alkalmazásspecifikus címtárbeli engedélyeket.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | A Windows rendszerhez készült interaktív bejelentkezési támogatási modellben figyeli azon csomagok listáját, amelyek értesítéseket kaphatnak a **Winlogon. exe**fájlból.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>A fájlok integritás-figyelésének támogatása Azure Security Center

A Change Tracking és a leltár a [Azure Security Center file Integrity monitoring (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)használatát teszi lehetővé. Míg a FIM figyeli a fájlokat és a beállításjegyzékeket, a teljes Change Tracking és a leltár funkció a következőket is tartalmazza:

- A szoftver módosításai
- Microsoft-szolgáltatások
- Linux-démonok

## <a name="recursion-support"></a>Rekurzió támogatása

A Change Tracking és a leltár támogatja a rekurziót, ami lehetővé teszi helyettesítő karakterek megadását a címtárak közötti nyomkövetés egyszerűsítése érdekében. A rekurzió olyan környezeti változókat is biztosít, amelyek lehetővé teszik a fájlok különböző környezetekben történő nyomon követését több vagy dinamikus meghajtó nevével. A következő lista a rekurzió konfigurálásakor szükséges általános tudnivalókat tartalmazza:

* Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
* A helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például **\\c:\mappa fájl*** vagy **/etc/*. conf**.
* Ha egy környezeti változónak érvénytelen az elérési útja, az érvényesítés sikeres lesz, de az elérési út meghiúsul.
* Az elérési út beállításakor Kerülje az általános elérési utak nevét, mivel az ilyen típusú beállítás túl sok mappa átjárását okozhatja.

## <a name="change-tracking-and-inventory-data-collection"></a>Change Tracking és leltározási adatgyűjtés

A következő táblázat a Change Tracking és a leltár által támogatott változások típusának adatgyűjtési gyakoriságát mutatja. Minden típus esetében az aktuális állapot adatpillanatképe legalább 24 óránként frissül.

| **Típus módosítása** | **Gyakoriság** |
| --- | --- |
| Windows beállításjegyzék | 50 perc |
| Windows-fájl | 30 perc |
| Linux-fájl | 15 perc |
| Microsoft-szolgáltatások | 10 másodperc és 30 perc között</br> Alapértelmezett: 30 perc |
| Linux-démonok | 5 perc |
| Windows-szoftver | 30 perc |
| Linuxos szoftverek | 5 perc |

A következő táblázat a nyomon követett elemek korlátozásait mutatja gépenként a Change Tracking és a leltár esetében.

| **Erőforrás** | **Korlát** |
|---|---|---|
|Fájl|500|
|Registry|250|
|Windows-szoftver (nem tartalmazza a gyorsjavításokat) |250|
|Linux-csomagok|1250|
|Szolgáltatások|250|
|Démonok|250|

A Change Tracking és a leltárt használó gépek Log Analytics átlagos adatfelhasználása körülbelül 40 MB/hó. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetét, és tekintse meg a pontos használatot.

### <a name="microsoft-service-data"></a>Microsoft-szolgáltatásokra vonatkozó adatkezelés

A Microsoft-szolgáltatások alapértelmezett gyűjtési gyakorisága 30 perc. A gyakoriságot a **beállítások szerkesztése**elemre a **Microsoft-szolgáltatások** lapon található csúszka használatával konfigurálhatja. 

![Microsoft-szolgáltatások csúszka](./media/change-tracking/windowservices.png)

A teljesítmény optimalizálása érdekében a Log Analytics ügynök csak a változásokat követi nyomon. Ha a szolgáltatás visszaáll az eredeti állapotába, a magas küszöbérték beállítása nem fog módosulni. Ha a gyakoriságot kisebb értékre állítja, az esetlegesen kihagyott módosításokat is elvégezheti.

> [!NOTE]
> Míg az ügynök 10 másodperces intervallumban nyomon követheti a változásokat, az adat továbbra is néhány percet vesz igénybe, hogy megjelenjen a Azure Portal. A portálon megjelenő időpontokban végrehajtott módosítások továbbra is nyomon követhetők és naplózhatók.

## <a name="support-for-alerts-on-configuration-state"></a>Riasztások támogatása a konfiguráció állapotában

Az Change Tracking és a leltár kulcsfontosságú funkciója, hogy a hibrid környezet konfigurációs állapotának változásairól riasztást küld. Számos hasznos művelet aktiválható a riasztásokra adott válaszokban, például az Azure functions, az Automation runbookok, a webhookok és a hasonló műveletek esetén. A **C:\Windows\System32\drivers\etc\hosts** -fájl változásainak riasztása egy adott gépen a riasztások helyes alkalmazása Change Tracking és leltározási adatként. Számos további forgatókönyv is létezik a riasztásokhoz, beleértve a következő táblázatban definiált lekérdezési forgatókönyveket is. 

|Lekérdezés  |Leírás  |
|---------|---------|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "files" és a FileSystemPath a\\"\\c\\:\\Windows system32 drivers" kifejezést tartalmazza|A rendszerkritikus fájlok változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, hogy a FieldsChanged tartalmazza-e a "FileContentChecksum" és a\\FileSystemPath\\=\\=\\"\\c: Windows system32 illesztőprogramok, illetve a gazdagépek"|Hasznos a legfontosabb konfigurációs fájlok módosításainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "Microsoft Services" és a SvcName a következőt tartalmazza: "W3SVC" és SvcState = = "leállítva"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "DAEMONS" és a SvcName tartalmazza az "SSH" és a SvcState! = "Running"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "szoftver" és a ChangeCategory = = "hozzáadva"|Olyan környezetekhez hasznos, amelyeknek szükségük van a zárolt szoftveres konfigurációkra.|
|ConfigurationData <br>&#124;, ahol a SoftwareName tartalmazza a "figyelési ügynököt" és a CurrentVersion! = "8.0.11081.0"|Hasznos, ha azt látja, hogy mely gépeken van telepítve elavult vagy nem megfelelő szoftververzió. Ez a lekérdezés a legutóbbi jelentett konfigurációs állapotot jelenti, de nem jelenti a módosításokat.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey = = @\\"\\HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows CurrentVersion QualityCompat"| Hasznos a kritikus vírusvédelmi kulcsok változásainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey a következőt tartalmazza\\:\\@\\"\\HKEY_LOCAL_MACHINE\\System\\CurrentControlSet Services SharedAccess Parameters FirewallPolicy"| Hasznos a tűzfalbeállítások változásainak nyomon követéséhez.|

## <a name="next-steps"></a>További lépések

* A runbookok Change Tracking és Leltározásával kapcsolatban lásd: a [change Tracking és a leltár kezelése](change-tracking-file-contents.md).
* Change Tracking és leltárral kapcsolatos hibák elhárításához tekintse meg a [change Tracking és a leltár hibaelhárítása](automation-tutorial-troubleshoot-changes.md)című témakört.
* A [naplóbeli keresések használata Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) a részletes változások követésére szolgáló információk megtekintéséhez.
