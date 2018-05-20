---
title: Az Azure Automation szolgáltatásban, a változások követése
description: A változáskövetési megoldás könnyebb legyen azonosítani a szoftver- és Windows-szolgáltatás módosításait a környezetében bekövetkező.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b110f83274b2b42896bd18fb364c355ecc97a028
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>A változáskövetési megoldás a környezetében lévő változások követése

Ez a cikk segítséget nyújt a változáskövetési megoldás segítségével könnyedén azonosíthatók a változtatásokat a környezetben. A megoldás Windows és Linux-szoftver, a Windows és Linux-fájlok, a Windows-beállításkulcsok, a központi Windows-szolgáltatások és a Linux-démonok módosításokat követi nyomon. Konfigurációs változásokat segíthetnek a rögzítési ponthoz működési problémák.

Telepített szoftverek, Windows-szolgáltatások, Windows beállításjegyzék és a fájlok és a figyelt kiszolgálók a Linux-démonok módosításai az Naplóelemzés szolgáltatás a felhőben feldolgozásra kerülnek. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti. A változások követése irányítópulton szereplő információk segítségével a kiszolgálói infrastruktúra végrehajtott módosítások könnyen láthatja.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

A változások követése megkezdéséhez szeretne engedélyezni a változások követése és a készlet megoldás az Automation-fiókhoz.

1. Az Azure-portálon lépjen az Automation-fiók
1. Válassza ki **a változáskövetés** alatt **konfigurációs**.
1. Válasszon egy meglévő naplóelemzési munkaterület vagy **új munkaterület létrehozása** kattintson **engedélyezése**.

Ez lehetővé teszi a megoldást az automation-fiók. A megoldás engedélyezése akár 15 percet is igénybe vehet. A kék szalagcím értesíti, ha a megoldás engedélyezve van. Lépjen vissza a **változások követése** lapon kezelheti a megoldás.

## <a name="configuring-change-tracking-and-inventory"></a>Változáskövetés és a készlet konfigurálása

Megtudhatja, hogyan megoldás előkészítésére számítógépek látogassa meg: [bevezetési automatizálási megoldásokat](automation-onboard-solutions-from-automation-account.md). Ha egy gép bevezetése a változások követése és a készlet megoldás követendő elemeket is konfigurálhat. Ha engedélyezi az új fájl vagy nyomon követheti a beállításkulcsokat, engedélyezve van a változáskövetés és készlet.

A Windows és Linux-fájlokban szereplő változásainak követése, MD5 kivonatokat a fájlok használhatók. Ezek a kivonatok észleli, ha a változás nem lett végrehajtva a utolsó leltározás óta használja.

### <a name="configure-linux-files-to-track"></a>Linux-fájlok nyomon követéséhez beállítása

Az alábbi lépések segítségével nyomon követésének konfigurálása a Linux rendszerű számítógépeken:

1. Válassza ki az Automation-fiók **a változáskövetés** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (fogaskerék jel).
2. A a **változások követése** lapon jelölje be **Linux fájlok**, majd kattintson a **+ Hozzáadás** nyomon követéséhez új fájl hozzáadása.
3. Az a **Linux fájl hozzáadása a változások követése**, írja be a fájl vagy könyvtár nyomon követését, és kattintson a adatait **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás lesz alkalmazva.        |
|Elem neve     | A fájl egyszerre nyomon követendő rövid nevét.        |
|Csoport     | Fájlok logikailag csoportosítása csoport nevét.        |
|Elérési út megadása     | Ellenőrizze a fájl elérési útja. Például: "/etc/*.conf"       |
|Elérési út típusa     | Típusú elemet kell lennie a nyomon követett, a lehetséges értékek a fájlokra és könyvtárakra.        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Hagyja figyelmen kívül** - figyelmen kívül hagyja a szimbolikus hivatkozást, és nem tartalmaz a fájlok vagy könyvtárak hivatkozik.<br>**Hajtsa végre a** – a szimbolikus csatolást rekurzió során a következő, és a fájlok vagy könyvtárak hivatkozott is tartalmaz.<br>**Kezelése** - követi a szimbolikus hivatkozást, és lehetővé teszi, hogy a visszaküldött tartalom módosítása.     |

> [!NOTE]
> A „Kezelés” használata nem ajánlott, mert a fájltartalom lekérése nem támogatott.

### <a name="configure-windows-files-to-track"></a>Windows-fájlok nyomon követéséhez beállítása

Az alábbi lépésekkel konfigurálhatja a fájlok nyomon követése a Windows rendszerű számítógépeken:

1. Válassza ki az Automation-fiók **a változáskövetés** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (fogaskerék jel).
2. A a **változások követése** lapon jelölje be **Windows fájlok**, majd kattintson a **+ Hozzáadás** nyomon követéséhez új fájl hozzáadása.
3. Az a **Windows fájl hozzáadása a változások követése**, írja be a nyomon követését, és kattintson a fájl adatait **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás lesz alkalmazva.        |
|Elem neve     | A fájl egyszerre nyomon követendő rövid nevét.        |
|Csoport     | Fájlok logikailag csoportosítása csoport nevét.        |
|Elérési út megadása     | A fájl elérési útja, például: „c:\temp\myfile.txt”       |

### <a name="configure-windows-registry-keys-to-track"></a>Windows-beállításkulcsok nyomon követéséhez konfigurálása

A következő lépésekkel konfigurálhatja a beállításjegyzék-kulcs követési Windows rendszerű számítógépeken:

1. Válassza ki az Automation-fiók **a változáskövetés** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (fogaskerék jel).
2. A a **változások követése** lapon jelölje be **Windows beállításjegyzék**, majd kattintson **+ Hozzáadás** hozzáadása egy új beállításkulcs nyomon követéséhez.
3. Az a **Windows beállításjegyzék vegye fel a változások követése**, írja be a nyomon követését, és kattintson a kulcs adatait **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás lesz alkalmazva.        |
|Elem neve     | A fájl egyszerre nyomon követendő rövid nevét.        |
|Csoport     | Fájlok logikailag csoportosítása csoport nevét.        |
|Windows-beállításkulcs   | Ellenőrizze a fájl elérési útja. Például: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj Folders\Common indításkor"      |

## <a name="limitations"></a>Korlátozások

A változáskövetési megoldás jelenleg nem támogatja a következő elemek:

* Mappák (könyvtárak) Windows fájl nyomon követése
* A rekurzió Windows fájl nyomon követése
* Helyettesítő karakterek Windows fájl nyomon követése
* A Windows beállításjegyzék követési rekurzió
* Elérésiút-változók
* Hálózati fájlrendszer
* Fájl tartalma

Egyéb korlátozások is érvényesek:

* A **maximális fájlméret** oszlop és értékek nem a jelenlegi megvalósításában használt.
* Ha több mint 2500-fájlok gyűjtése a 30 perces adatgyűjtési ciklus, megoldás teljesítménye csökkent.
* Ha nagy hálózati forgalmat az módosítása rekordok megjelenítendő legfeljebb hat órát is igénybe vehet.
* A konfigurációs akkor módosítható, amíg a számítógép leállítása, ha a számítógép, amely a korábbi konfigurációt tartoztak módosítások előfordulhat, hogy küldje.

## <a name="known-issues"></a>Ismert problémák

A változáskövetési megoldás jelenleg a következő hibákat észlelt:

* A gyorsjavítás-frissítések a Windows 10 Creators Update és Windows Server 2016 Core RS3 gépek nem történik gyűjtés.

## <a name="change-tracking-data-collection-details"></a>Nyomkövetési adatok gyűjtése adatainak módosítása

Az alábbi táblázat azon adatok gyűjtési gyakoriságát. Minden az adatok pillanatképének elkészítéséhez, az aktuális állapot is frissülnek, legalább 24 óránként:

| **Változás típusa** | **Gyakoriság** |
| --- | --- |
| A Windows beállításjegyzékben | 50 perc |
| Windows-fájl | 30 perc |
| Linux-fájl | 15 perc |
| Windows-szolgáltatások | 30 perc |
| Linux-démonok | 5 perc |
| A Windows szoftverek | 30 perc |
| Linux-szoftver | 5 perc |

### <a name="registry-key-change-tracking"></a>Beállításjegyzék-kulcs módosítás nyomon követése

A beállításkulcsok módosításainak figyelése célja rögzítési ponthoz bővítési pontokat, ahol külső kód és a kártevők is aktiválhatja. Az alábbi lista előre konfigurált beállításkulcsok listáját jeleníti meg. Ezek a kulcsok vannak konfigurálva, de nincs engedélyezve. Ezek a kulcsok követheti nyomon, engedélyeznie kell minden egyes.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.    |
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők parancsfájlok, amelyek indítási parancsot.     |
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők futtatott parancsfájlok, leállításkor.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Mielőtt a felhasználó bejelentkezik a Windows fiókjukhoz betöltött kulcsokat figyeli. A kulcs szolgál 64 bites számítógépeken futó 32 bites program.    |
> |**HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed összetevők**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Nyomon követi az alkalmazásbeállítások módosításait.     |
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.|
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli a ikon kezelő regisztrációs átfedő.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli a ikon átfedő 64 bites számítógépeken futó 32 bites program kezelő regisztrálása.|
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser segédobjektuma**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új böngésző segítő objektum beépülők az Internet Explorer figyeli. Használja a Document Object Model (DOM) az aktuális lap eléréséhez és a navigáció.|
> |**HKEY\_helyi\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser segédobjektuma**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új böngésző segítő objektum beépülők az Internet Explorer figyeli. A Document Object Model (DOM) az aktuális lap eléréséhez és navigáció a 64 bites számítógépeken futó 32 bites program használja.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új Internet Explorer-bővítmények, például egyéni eszköz menük és az egyéni gombok figyeli.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új Internet Explorer-bővítmények, például egyéni eszköz menük és 64 bites számítógépeken futó 32 bites program egyéni eszköztár gombjai figyeli.|
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;A 32 bites illesztőprogramok wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és vidc társított figyeli. Hasonló a rendszer [illesztőprogramok] szakasza. INI-fájl.|
> |**HKEY\_helyi\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők a 32-bites illesztőprogramok társított wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és a 64 bites számítógépeken futó 32 bites program vidc. Hasonló a rendszer [illesztőprogramok] szakasza. INI-fájl.|
> |**HKEY\_helyi\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli a lista ismert vagy a gyakran használt rendszer DLL-EK; a rendszer megakadályozza, hogy a személyek gyenge Alkalmazásengedélyek directory kihasználva ejtésével rendszer DLL-ek trójai faló verzióiban.|
> |**HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli a rendszeresemény-értesítéseket kaphat Winlogon, az interaktív bejelentkezési támogatási modell a Windows operációs rendszerhez a csomagok listájában.|

## <a name="use-change-tracking"></a>A változáskövetés használata

A megoldás engedélyezése után megtekintheti a módosítások összegzése a figyelt számítógépek kiválasztásával **változások követése** alatt **konfigurációkezelés** az Automation-fiókban.

A számítógép, és a-feltárás minden esemény részletes módosításokat megtekintheti. Közvetlen időszakosan megszakadó tetején található a diagram és részletes információk a módosítás típus- és időponttartományok alapján korlátozni a diagram érhetők el. Is kattintson és húzza a jelölje be egy egyéni időtartományt a diagram.

![Változások követése irányítópult képe](./media/automation-change-tracking/change-tracking-dash01.png)

Ez a változás kapcsolatos részletes információk a módosítása vagy esemény kattintva megjelenik. A példában látható, mert a szolgáltatás indítási típusa automatikus manuális értékűre változott.

![a változáskövetés részletek képe](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Keresési naplókat

A részleteket a portál által biztosított, mellett keresések szemben a naplók végezheti el. Az a **változások követése** megnyitva, kattintson **Naplóelemzési**, ekkor megnyílik a **naplófájl-keresési** lap.

### <a name="sample-queries"></a>Mintalekérdezések

A következő táblázat minta napló keres a megoldás által gyűjtött rekordok módosítása:

|Lekérdezés  |Leírás  |
|---------|---------|
|ConfigurationData<br>&#124;Ha ConfigDataType == "WindowsServices" és a SvcStartupType == "Auto"<br>&#124;Ha SvcState == "Leállt"<br>&#124;összefoglalója arg_max(TimeGenerated, *) SoftwareName, a számítógép által         | Megjeleníti a legutóbbi Hardverleltár-rekordok volt automatikus értékre van beállítva, de volt jelentett leállására Windows-szolgáltatások<br>A program csak a legutóbbi rekord SoftwareName és a számítógép      |
|Konfigurációváltozás<br>&#124;Ha ConfigChangeType == "Szoftver" és a ChangeCategory == "Eltávolítva"<br>&#124;TimeGenerated desc rendezési|Megjeleníti a módosítás bejegyzéseket eltávolított szoftver|

## <a name="next-steps"></a>További lépések

Az oktatóanyag keresse fel a változások követése tudhat meg többet a megoldással:

> [!div class="nextstepaction"]
> [A környezet változásai hibaelhárítása](automation-tutorial-troubleshoot-changes.md)

* Használjon [Log Analytics-e jelentkezni a keresések](../log-analytics/log-analytics-log-searches.md) részletes változáskövetési adatok megtekintéséhez.
