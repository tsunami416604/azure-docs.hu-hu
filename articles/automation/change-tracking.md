---
title: A Azure Automation Change Tracking és a leltár áttekintése
description: Ez a cikk ismerteti a Change Tracking és a leltár szolgáltatást, amely segít azonosítani a szoftvereket és a Microsoft szolgáltatásbeli módosításokat a környezetben.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 7a1c5d5371663f3520e76060c9c2a8df0a18449c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117529"
---
# <a name="change-tracking-and-inventory-overview"></a>A Change Tracking és a leltár áttekintése

Ez a cikk a Azure Automation Change Tracking és leltározását ismerteti. Ez a szolgáltatás nyomon követi a virtuális gépek és a kiszolgálói infrastruktúra változásait, így segít a terjesztési csomag kezelőjével kezelt szoftverekkel kapcsolatos működési és környezeti problémák azonosításában. A Change Tracking és a leltár által követett elemek a következők: 

- Windows-szoftver
- Linuxos szoftverek (csomagok)
- Windows-és Linux-fájlok
- Windows-beállításkulcsok
- Microsoft-szolgáltatások
- Linux-démonok

> [!NOTE]
> A Azure Resource Manager tulajdonságok változásainak nyomon követéséhez tekintse meg az Azure Resource Graph [változási előzményeit](../governance/resource-graph/how-to/get-resource-changes.md).

A Change Tracking és a leltár Azure Monitorból szerzi be az adatait. Log Analytics munkaterülethez csatlakozó virtuális gépek Log Analytics ügynökökkel gyűjtenek adatokat a telepített szoftverek, a Microsoft-szolgáltatások, a Windows-beállításjegyzék és-fájlok, valamint a figyelt kiszolgálókon futó Linux-démonok változásairól. Ha az adatelérési lehetőség elérhető, az ügynökök elküldik a feldolgozásra Azure Monitor. Azure Monitor alkalmazza a logikát a fogadott adatokra, rögzíti azt, és elérhetővé teszi. 

> [!NOTE]
> A Change Tracking és a leltár funkció használatához az összes virtuális gépet az Automation-fiókhoz tartozó előfizetésben és régióban kell megkeresnie.

A Change Tracking és a leltár jelenleg nem támogatja a következő elemeket:

* Rekurzió a Windows beállításjegyzékének nyomon követéséhez
* Hálózati fájlrendszerek
* Különböző telepítési módszerek
* ***. exe** -fájlok a Windows rendszerhez

Egyéb korlátozások:

* A **Maximális fájlméret** oszlop és az értékek nem használhatók az aktuális implementációban.
* Ha több mint 2500 fájlt gyűjt egy 30 perces gyűjtési ciklusban, a Change Tracking és a leltározás teljesítménye csökkenhet.
* Ha a hálózati forgalom magas, a rekordok módosítása akár hat órát is igénybe vehet.
* Ha a számítógép leállítása közben módosítja a konfigurációt, a számítógép az előző konfigurációhoz tartozó módosításokat tehet közzé.

A Change Tracking és a leltár jelenleg a következő problémákba ütközik:

* A gyorsjavítások frissítései nem gyűjthetők a Windows Server 2016 Core RS3-gépeken.
* Előfordulhat, hogy a Linux-démonok módosult állapotot mutatnak, de nem történt változás. Ez a probléma a `SvcRunLevels` Azure monitor [konfigurációváltozás](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) -naplóban található adatrögzítési mód miatt fordul elő.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Change Tracking és a leltár minden olyan operációs rendszeren támogatott, amely megfelel Log Analytics ügynök követelményeinek. A hivatalos operációsrendszer-verziók a Windows Server 2008 SP1 vagy újabb, illetve Windows 7 SP1 vagy újabb verziójúak. A szolgáltatás számos Linux operációs rendszeren is támogatott. Az Log Analyticst támogató operációs rendszerek esetében lásd: [log Analytics ügynök áttekintése](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A Change Tracking és a leltárnak kifejezetten a következő táblázatban felsorolt hálózati címeket kell megadnia. A címekkel való kommunikáció az 443-es portot használja.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>A Change Tracking és a leltár felhasználói felülete

A Azure Portal Change Tracking és leltár használatával megtekintheti a figyelt számítógépek változásainak összefoglalását. A funkció az Automation-fiók **konfiguráció kezelése** területén a virtuális gépek hozzáadása lehetőségre kattintva, a **change Tracking** vagy a **Inventory** elemhez választható.  

![Change Tracking irányítópult](./media/change-tracking/change-tracking-dash01.png)

A legördülő lista az irányítópult tetején található, hogy korlátozza a Change Tracking diagramot és a részletes információkat a változás típusa és az időtartományok alapján. A diagramra kattintva és húzásával egyéni időtartományt is kijelölhet. 

A módosításra vagy eseményre kattintva megtekintheti annak részleteit. Az elérhető változási típusok a következők:

* Események
* Démonok
* Files
* Registry
* Szoftverek
* Microsoft-szolgáltatások

Hozzáadhat, módosíthat vagy eltávolíthat minden módosítást. Az alábbi példa egy szolgáltatás indítási típusának módosítását mutatja be Manuálisról automatikusra.

![Change Tracking és leltár részletei](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>FIM-támogatás a Azure Security Centerban

A Change Tracking és a leltár a [Azure Security Center file Integrity monitoring (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)használatát teszi lehetővé. Míg a FIM figyeli a fájlokat és a beállításjegyzékeket, a teljes Change Tracking és a leltár funkció a következőket is tartalmazza:

- A szoftver módosításai
- Microsoft-szolgáltatások
- Linux-démonok

> [!NOTE]
> A teljes Change Tracking és a leltár funkció engedélyezése további díjakat eredményezhet. Lásd: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/). A FIM törölhető a Azure Portal elérhető [telepített megfigyelési megoldások listájáról](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) . Lásd: [figyelési megoldás eltávolítása](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>A fájlok változásainak követése

A Windows és a Linux rendszerű fájlok változásainak nyomon követéséhez a Change Tracking és a leltár a fájlok MD5 kivonatait használja. A szolgáltatás a kivonatok használatával észleli, hogy történtek-e változások az utolsó leltár óta.

## <a name="tracking-of-file-content-changes"></a>A fájl tartalmának változásai nyomon követése

A Change Tracking és a leltár lehetővé teszi egy Windows-vagy Linux-fájl tartalmának megtekintését. Egy fájl minden módosítása esetén a Change Tracking és a leltár egy [Azure Storage-fiókban](../storage/common/storage-create-storage-account.md)tárolja a fájl tartalmát. Egy fájl nyomon követése után megtekintheti annak tartalmát a módosítás előtt vagy után. A fájl tartalma beágyazott vagy egymás mellett is megtekinthető. 

![Egy fájl változásainak megtekintése](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Beállításkulcsok nyomon követése

A Change Tracking és a leltár lehetővé teszi a Windows-beállításkulcsok változásainak figyelését. A figyeléssel megadhatja azokat a bővíthetőségi pontokat, amelyekben a harmadik féltől származó kód és a kártevő is aktiválható. Az alábbi táblázat az előre konfigurált (de nem engedélyezett) beállításkulcsokat sorolja fel. A kulcsok nyomon követéséhez engedélyeznie kell mindegyiket.

> [!div class="mx-tdBreakAll"]
> |Beállításjegyzék kulcsa | Szerep |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Az indításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | A leállításkor futó parancsfájlokat figyeli.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Figyeli azokat a kulcsokat, amelyek betöltődik, mielőtt a felhasználó bejelentkezik a Windows-fiókba. A kulcs a 64 bites számítógépeken futó 32 bites alkalmazásokhoz használatos.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Az Alkalmazásbeállítások változásainak figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | A közvetlenül a Windows Intézőben csatlakoztató helyi menüparancsokat figyeli, és általában az **Explorer. exe**használatával futtatja a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Figyeli a közvetlenül a Windows Intézőben csatlakoztatható Hook-kezelőket, és általában az **Explorer. exe**használatával futtatja a folyamatot.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők az ikon átfedését kezelő regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Figyelők a 64 bites számítógépeken futó 32 bites alkalmazásokhoz tartozó ikon átfedési kezelői regisztrációja.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére és a Navigálás vezérlésére szolgál.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Figyelők az Internet Explorer új böngésző Helper Object beépülő moduljaihoz. Az aktuális oldal Document Object Model (DOM) elérésére, valamint a 64 bites számítógépeken futó 32 bites alkalmazások navigálásának vezérlésére használatos.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Figyelők az új Internet Explorer-bővítményekhez, például az egyéni eszközök menüihez és az egyéni eszköztárak gombjaihoz.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Az új Internet Explorer-bővítmények, például a 64 bites számítógépeken futó 32 bites alkalmazásokhoz készült egyéni eszköztárak és egyéni eszköztárgombok figyelése.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli. Hasonló a **System. ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | A Wavemapper, a wave1 és a Wave2, a MSACM. imaadpcm, a. msadpcm, a. msgsm610 és a vidc társított 32 bites illesztőprogramokat figyeli a 64 bites számítógépeken futó 32 bites alkalmazásokhoz. Hasonló a **System. ini** fájl [Drivers] szakaszához.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Az ismert vagy leggyakrabban használt rendszerdll-fájlok listáját figyeli. A figyelés megakadályozza, hogy a felhasználók a rendszer DLL-fájljainak eldobásával kihasználják a gyenge alkalmazásspecifikus címtárbeli engedélyeket.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | A Windows rendszerhez készült interaktív bejelentkezési támogatási modellben figyeli azon csomagok listáját, amelyek értesítéseket kaphatnak a **Winlogon. exe**fájlból.

## <a name="recursion-support"></a>Rekurzió támogatása

A Change Tracking és a leltár támogatja a rekurziót, ami lehetővé teszi helyettesítő karakterek megadását a címtárak közötti nyomkövetés egyszerűsítése érdekében. A rekurzió olyan környezeti változókat is biztosít, amelyek lehetővé teszik a fájlok különböző környezetekben történő nyomon követését több vagy dinamikus meghajtó nevével. A következő lista a rekurzió konfigurálásakor szükséges általános tudnivalókat tartalmazza:

* Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
* A helyettesítő karaktereket csak a fájl elérési útjának utolsó szegmensében használhatja, például **c:\mappa \\ fájl*** vagy **/etc/*. conf**.
* Ha egy környezeti változónak érvénytelen az elérési útja, az érvényesítés sikeres lesz, de az elérési út meghiúsul.
* Az elérési út beállításakor el kell kerülnie az általános elérési utak nevét, mivel az ilyen típusú beállítás túl sok mappa átjárását okozhatja.

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

A Change Tracking és a leltárt használó gépek átlagos Log Analytics adatfelhasználása körülbelül 40 MB/hó, a környezettől függően. A Log Analytics munkaterület használati és becsült költségek funkciójával megtekintheti a Change Tracking és a leltár által betöltött adatokat egy használati diagramon. Ezzel az adatnézettel kiértékelheti az adathasználatot, és meghatározhatja, hogyan befolyásolja a számláját. Lásd: [a használat és a becsült költségek megismerése](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs). 

### <a name="microsoft-service-data"></a>Microsoft-szolgáltatásokra vonatkozó adatkezelés

A Microsoft-szolgáltatások alapértelmezett gyűjtési gyakorisága 30 perc. A gyakoriságot a **beállítások szerkesztése**elemre a **Microsoft-szolgáltatások** lapon található csúszka használatával konfigurálhatja.

![Microsoft-szolgáltatások csúszka](./media/change-tracking/windowservices.png)

A teljesítmény optimalizálása érdekében a Log Analytics ügynök csak a változásokat követi nyomon. Ha a szolgáltatás visszaáll az eredeti állapotába, a magas küszöbérték beállítása nem fog módosulni. Ha a gyakoriságot kisebb értékre állítja, az esetlegesen kihagyott módosításokat is elvégezheti.

> [!NOTE]
> Míg az ügynök 10 másodperces intervallumban nyomon követheti a változásokat, az adat továbbra is néhány percet vesz igénybe, hogy megjelenjen a Azure Portal. A portálon megjelenő időpontokban végrehajtott módosítások továbbra is nyomon követhetők és naplózhatók.

## <a name="support-for-alerts-on-configuration-state"></a>Riasztások támogatása a konfiguráció állapotában

Az Change Tracking és a leltár kulcsfontosságú funkciója, hogy a hibrid környezet konfigurációs állapotának változásairól riasztást küld. Számos hasznos művelet aktiválható a riasztásokra adott válaszokban, például az Azure functions, az Automation runbookok, a webhookok és a hasonló műveletek esetén. A **c:\Windows\System32\drivers\etc\hosts** -fájl változásainak riasztása egy adott gépen a riasztások helyes alkalmazása Change Tracking és leltározási adatként. Számos további forgatókönyv is létezik a riasztásokhoz, beleértve a következő táblázatban definiált lekérdezési forgatókönyveket is. 

|Lekérdezés  |Leírás  |
|---------|---------|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "files" és a FileSystemPath a "c: \\ Windows \\ System32 \\ Drivers \\ " kifejezést tartalmazza|A rendszerkritikus fájlok változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, hogy a FieldsChanged tartalmazza-e a "FileContentChecksum" és a FileSystemPath = = "c: \\ Windows system32 illesztőprogramok, illetve a \\ \\ \\ \\ gazdagépek"|Hasznos a legfontosabb konfigurációs fájlok módosításainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "Microsoft Services" és a SvcName a következőt tartalmazza: "W3SVC" és SvcState = = "leállítva"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "DAEMONS" és a SvcName tartalmazza az "SSH" és a SvcState! = "Running"|A rendszerkritikus szolgáltatások változásainak nyomon követésére használható.|
|Konfigurációváltozás <br>&#124;, ahol a ConfigChangeType = = "szoftver" és a ChangeCategory = = "hozzáadva"|Olyan környezetekhez hasznos, amelyeknek szükségük van a zárolt szoftveres konfigurációkra.|
|ConfigurationData <br>&#124;, ahol a SoftwareName tartalmazza a "figyelési ügynököt" és a CurrentVersion! = "8.0.11081.0"|Hasznos, ha azt látja, hogy mely gépeken van telepítve elavult vagy nem megfelelő szoftververzió. Ez a lekérdezés a legutóbbi jelentett konfigurációs állapotot jelenti, de nem jelenti a módosításokat.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ Software \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Hasznos a kritikus vírusvédelmi kulcsok változásainak nyomon követéséhez.|
|Konfigurációváltozás <br>&#124;, ahol a RegistryKey a következőt tartalmazza: @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ Parameters \\ FirewallPolicy"| Hasznos a tűzfalbeállítások változásainak nyomon követéséhez.|

## <a name="next-steps"></a>További lépések

* A szolgáltatás Automation-fiókból való engedélyezéséhez lásd: [change Tracking és leltár engedélyezése Automation-fiókból](automation-enable-changes-from-auto-acct.md).
* Ha engedélyezni szeretné a szolgáltatást a Azure Portal tallózásával, tekintse meg a [change Tracking és a leltár engedélyezése a Azure Portalből](automation-onboard-solutions-from-browse.md)című témakört.
* Ha engedélyezni szeretné a szolgáltatást egy runbook, tekintse meg a következőt: [change Tracking és leltár engedélyezése runbook](automation-enable-changes-from-runbook.md).
* Ha engedélyezni szeretné a szolgáltatást egy Azure-beli virtuális gépről, tekintse meg az Azure-beli [virtuális gép Change Tracking és leltározásának engedélyezése](automation-enable-changes-from-vm.md)című témakört.