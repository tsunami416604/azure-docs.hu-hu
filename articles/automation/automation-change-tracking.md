---
title: Az Azure Automation változásainak követése
description: A Change Tracking megoldás segítségével azonosíthatja a szoftver- és Windows-szolgáltatás módosításait a környezetében előforduló.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 10/12/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27bacb12c66ac57a0bf1aea88a447d395b6dde8c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408918"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Változások követése saját környezetében a Change Tracking megoldás

Ez a cikk segít a Change Tracking megoldás használatával könnyedén azonosíthatja a változtatásokat a környezetben. A megoldás nyomon követi a módosításokat a Windows és Linux-szoftver, a Windows és Linux-fájlok, a Windows-beállításkulcsok, a Windows-szolgáltatások és a Linux-démonok. Konfigurációs módosítások azonosítása segíthet a felmerülő működési problémákat.

Telepített szoftverek, Windows-szolgáltatások, Windows-beállításjegyzék és a fájlok és a figyelt kiszolgálókról a Linux-démonok változásokat küldi el a Log Analytics szolgáltatás a felhőben a feldolgozáshoz. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti. A Change Tracking irányítópultján található információk segítségével könnyen megtekintheti az a kiszolgáló-infrastruktúrájában elvégzett módosítások.

## <a name="supported-windows-operating-systems"></a>A támogatott Windows operációs rendszerek

A Windows-ügynök hivatalosan támogatott a Windows operációs rendszer következő verziói:

* A Windows Server 2008 R2 vagy újabb

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A következő Linux-disztribúciók hivatalosan támogatott. A Linux-ügynök azonban más disztribúció nem szerepel a listán is futhat. Ha másként nincs jelezve, az összes kisebb kiadásokban minden felsorolt főverzió támogatottak.  

### <a name="64-bit"></a>64 bites

* CentOS 6 és 7
* Amazon Linux 2017.09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux Server 6 és 7
* Debian GNU/Linux 8. és 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS és 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bites

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8. és 9
* Ubuntu Linux 14.04 LTS és 16.04 LTS

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Változások követése a kezdéshez, engedélyeznie kell a Change Tracking and Inventory megoldás az Automation-fiókhoz tartozó.

1. Az Azure Portalon lépjen az Automation-fiók
2. Válassza ki **Change Tracking** alatt **konfigurációs**.
3. Válasszon egy meglévő Log analytics-munkaterületet, vagy **új munkaterület létrehozása** kattintson **engedélyezése**.

Ez lehetővé teszi, hogy a megoldás az automation-fiók. A megoldás engedélyezése akár 15 percet is igénybe vehet. A kék szalagcím értesíti, amikor a megoldás engedélyezve lett. Lépjen vissza a **Change Tracking** lapon kezelheti a megoldást.

## <a name="configuring-change-tracking-and-inventory"></a>A Change Tracking és Inventory konfigurálása

Megtudhatja, hogyan számítógépek felvétele a megoldást, keresse fel: [Előkészítés Automation-megoldások](automation-onboard-solutions-from-automation-account.md). A Change Tracking and Inventory megoldással egy gép bevezetése után konfigurálhat az elemek nyomon követése. Ha engedélyezi az új fájlok vagy beállításkulcs nyomon követésére, a Change Tracking and Inventory engedélyezve van.

A Windows és Linux rendszereken egyaránt fájlokban változásainak követése, MD5-kivonatát a fájlok használhatók. Következő kivonatok szolgálnak majd észleli, ha a legutóbbi leltározás óta a változás nem lett végrehajtva.

### <a name="configure-linux-files-to-track"></a>Linux-fájlok nyomon konfigurálása

A következő lépések segítségével nyomon követésének konfigurálása Linux-számítógépeken:

1. Válassza ki az Automation-fiók **Change tracking** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (a fogaskerék ikonra).
2. A a **Change Tracking** lapon jelölje be **Linux-fájlok**, majd kattintson a **+ Hozzáadás** , adjon hozzá egy új fájlt, nyomon követéséhez.
3. Az a **Linux-fájl felvétele a Change Tracking**, írja be a fájl vagy könyvtár nyomon követésére, és kattintson a adatait **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás vonatkozik.        |
|Elem neve     | A követendő fájl felhasználóbarát neve.        |
|Csoport     | Fájlok logikai csoportosítására szolgáló csoportnév.        |
|Elérési út megadása     | A fájl elérési útja. Például: "/etc/*.conf"       |
|Elérési út típusa     | Lehetséges értékek a nyomon követett kívánt elem típusát a fájl- és.        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Hagyja figyelmen kívül** – mellőzi a szimbolikus hivatkozásokat, és nem tartalmazza a hivatkozott fájlokat/címtárakat.<br>**Hajtsa végre a** – követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat.<br>**Kezelése** – követi a szimbolikus hivatkozásokat, és lehetővé teszi, hogy a visszaadott tartalom módosítása.     |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **hamis**.|

> [!NOTE]
> A „Kezelés” használata nem ajánlott, mert a fájltartalom lekérése nem támogatott.

### <a name="configure-windows-files-to-track"></a>Konfigurálja a Windows-fájlok nyomon követése

A következő lépéseket követve konfigurálja a fájlok nyomon követése a Windows-számítógépeken:

1. Válassza ki az Automation-fiók **Change tracking** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (a fogaskerék ikonra).
2. A a **Change Tracking** lapon jelölje be **Windows-fájlok**, majd kattintson a **+ Hozzáadás** , adjon hozzá egy új fájlt, nyomon követéséhez.
3. Az a **Windows-fájl felvétele a Change Tracking**, írja be a nyomon követésére, és kattintson a fájl adatait **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás vonatkozik.        |
|Elem neve     | A követendő fájl felhasználóbarát neve.        |
|Csoport     | Fájlok logikai csoportosítására szolgáló csoportnév.        |
|Elérési út megadása     | A fájl elérési útja, például: „c:\temp\\\*.txt”<br>Környezeti változók is használhatók, például: „%winDir%\System32\\\*.*”       |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **hamis**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Helyettesítő karakteres, rekurzió és környezeti beállítások

A rekurzió lehetővé teszi, hogy adja meg a helyettesítő karakterek egyszerűsítése érdekében a könyvtárak és a környezeti változókat, hogy nyomon követheti a fájlok között környezetekben, ahol több nyomkövetési vagy dinamikus meghajtó nevét. A következő az általános információkat, tudnia kell a rekurzió konfigurálásakor listája:

* A helyettesítő karakterek szükség több fájlok nyomon követése
* Ha helyettesítő karaktereket használ, akkor egy elérési út utolsó szegmense csak használható. (például C:\folder\\**fájl** vagy /etc/*.conf)
* Ha egy környezeti változó egy érvénytelen elérési út, érvényesítés sikeres lesz, de az elérési út sikertelen lesz, ha szoftverleltár fut le.
* Például elkerülése érdekében az általános elérési utak `c:\*.*` történő beállításakor az elérési út, ez túl sok mappák áthaladhat eredményezne.

## <a name="configure-file-content-tracking"></a>Fájl tartalma követési konfigurálása

Megtekintheti a tartalom előtt és után egy fájlt a File Content Change Tracking módosítását. Ez érhető el a Windows és Linux-fájlok minden módosítása a fájlt, a fájl tartalmát a storage-fiókban tárolt, és bemutatja a fájl előtt és után a változás, a beágyazott vagy egymás mellett. További tudnivalókért lásd: [követett fájl tartalmának megtekintése](change-tracking-file-contents.md).

![változások megtekintése egy fájlban](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Nyomon követheti a Windows-beállításkulcsok konfigurálása

A következő lépések segítségével Windows-számítógépeket a beállításjegyzék-követés konfigurálása:

1. Válassza ki az Automation-fiók **Change tracking** alatt **konfigurációkezelés**. Kattintson a **beállításainak szerkesztése** (a fogaskerék ikonra).
2. A a **Change Tracking** lapon válassza **Windows beállításjegyzék**, majd kattintson a **+ Hozzáadás** hozzáadása egy új beállításkulcs nyomon követéséhez.
3. Az a **Windows-beállításjegyzék felvétele a Change Tracking megoldásba**, adja meg az információkat nyomon követésére, és kattintson a kulcs **mentése**.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Azt határozza meg, ha a beállítás vonatkozik.        |
|Elem neve     | A követendő fájl felhasználóbarát neve.        |
|Csoport     | Fájlok logikai csoportosítására szolgáló csoportnév.        |
|Windows-beállításkulcs   | A fájl elérési útja. Példa: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj Folders\Common indítási"      |

## <a name="limitations"></a>Korlátozások

A Change Tracking megoldás jelenleg nem támogatja a következő elemek:

* A rekurzió Windows beállításjegyzék-követés
* Hálózati fájlrendszer

Egyéb korlátozások is érvényesek:

* A **maximális fájlméret** oszlop és az értékeket a fel nem használt, a jelenlegi implementációja.
* Ha több mint 2500-fájlok gyűjtése a a 30 perces adatgyűjtési ciklus, megoldás teljesítménye csökkent.
* Ha nagy hálózati forgalmat, a módosítási rekordok is a megjelenítése akár hat órát igénybe vehet.
* Miközben a számítógép leállítása a konfiguráció módosítása esetén a számítógép tartoztak a az előző konfigurációs módosítások előfordulhat, hogy közzététele.

## <a name="known-issues"></a>Ismert problémák

A Change Tracking megoldás jelenleg a következő hibásan:

* A gyorsjavítás-frissítések a Windows 10 alkotói frissítés és a Windows Server 2016 Core RS3 gépek kivételével.
* Windows-fájlok, a változások követése nem jelenleg észleli, ha egy új fájlt hozzáadni egy követett mappa elérési útja

## <a name="change-tracking-data-collection-details"></a>Követési adatok gyűjtemény adatainak módosítása

Az alábbi táblázat a módosítások típusú adatok gyűjtési gyakoriságát ismerteti. Minden az adatok pillanatképet az aktuális állapota is frissülnek, legalább 24 óránként:

| **Típus módosítása** | **Gyakoriság** |
| --- | --- |
| Windows beállításjegyzék | 50 perc |
| Windows-fájl | 30 perc |
| Linux-fájl | 15 perc |
| Windows-szolgáltatások | akár 30 percig 10 másodperc</br> Alapértelmezett: 30 perc |
| Linux-démonok | 5 perc |
| Windows szoftverek | 30 perc |
| Linux-szoftver | 5 perc |

Az alábbi táblázat bemutatja a gépek nyomon követett elemek vonatkozó korlátok a Change Tracking megoldásba.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Beállításjegyzék|250||
|Windows szoftverek|250|Nem tartalmaz szoftverfrissítéseket|
|Linux-csomagok|1250||
|Szolgáltatások|250||
|Démon|250||

### <a name="windows-service-tracking"></a>Windows-szolgáltatás nyomon követése

Windows-szolgáltatások az alapértelmezett gyűjtés gyakorisága érték 30 perc. Konfigurálhatja a gyakoriság Ugrás **Change Tracking**. Alatt **beállításainak szerkesztése** a a **Windows szolgáltatások** lapra, és van egy csúszka, amely lehetővé teszi, hogy a gyűjtemény gyakoriságának módosítása a Windows services for leggyorsabban 10 másodperc, amíg az 30 perc. A csúszkát a kívánt gyakoriságot, és azt automatikusan menti.

![Windows-szolgáltatások csúszka](./media/automation-change-tracking/windowservices.png)

Az ügynök csak nyomon követi a módosításokat, ez optimalizálja az ügynök teljesítménye. Túl magas küszöbértéket definiál beállításával módosítások előfordulhat, hogy nem észlelnének. Ha a szolgáltatás az eredeti állapotba állítja vissza a rendszer. A gyakoriság beállítás értéke kisebb, lehetővé teszi, hogy a tényleges módosítások, amelyek egyébként nem észlelnének.

> [!NOTE]
> Amíg az ügynök is egy 10 második intervallum változások nyomon követése, az adatok továbbra is megjelenik a portálon néhány percet vesz igénybe. Módosítások megjelennek a portálon az idő alatt továbbra is nyomon követi és naplózza.
  
### <a name="registry-key-change-tracking"></a>Kulcs beállításjegyzék változásainak követése

A beállításkulcsok módosításainak figyelése célja, hogy kiszűrheti a bővítési pontokat, ahol külső kód és a kártevők is aktiválhatja. Az alábbi lista az előre konfigurált beállításkulcsok listáját jeleníti meg. Ezek a kulcsok vannak konfigurálva, de nincs engedélyezve. Nyomon követheti a beállításkulcsok, engedélyeznie kell a mindegyikhez.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseket, amelyek közvetlenül a Windows Intézőben, és általában futtassa folyamaton belüli Explorer.exe a környezet igénybe vételét.    |
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők parancsfájlok, amelyek indítási parancsot.     |
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Kikapcsoláskor futó figyelők parancsprogramok.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitorozza a kulcsok, mielőtt a felhasználó bejelentkezik a Windows-fiókjára betöltött. A kulcs a 64 bites számítógépeken futó 32 bites program szolgál.    |
> |**HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed összetevők**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Nyomon követi az alkalmazásbeállítások módosításait.     |
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseket, amelyek közvetlenül a Windows Intézőben, és általában futtassa folyamaton belüli Explorer.exe a környezet igénybe vételét.|
> |**HKEY\_helyi\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők közös autostart bejegyzéseket, amelyek közvetlenül a Windows Intézőben, és általában futtassa folyamaton belüli Explorer.exe a környezet igénybe vételét.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők ikon kezelő regisztrációs átfedő.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők ikon átfedő kezelő regisztrációs 64 bites számítógépeken futó 32 bites program számára.|
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Böngészősegítő objektumok**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új böngésző segítő objektum modulokat az Internet Explorer figyeli. Használja a Document Object Model (DOM) az aktuális lap eléréséhez és a navigáció.|
> |**HKEY\_helyi\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Böngészősegítő objektumok**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új böngésző segítő objektum modulokat az Internet Explorer figyeli. A Document Object Model (DOM) az aktuális lap eléréséhez és navigáció a 64 bites számítógépeken futó 32 bites program használják.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új az Internet Explorer-bővítmény, például egyéni eszköz menük és egyéni-eszköztárgomb figyelőket.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Új az Internet Explorer-bővítmény, például egyéni eszköz menük és a 64 bites számítógépeken futó 32 bites program egyéni eszköztárgombok figyelőket.|
> |**HKEY\_helyi\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;A 32-bites illesztőprogramok wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és vidc társított figyeli. A rendszer a [illesztőprogramok] szakaszban hasonló. Az INI-fájl.|
> |**HKEY\_helyi\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyelők a 32-bites illesztőprogramok wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és társított vidc 64 bites számítógépeken futó 32 bites program számára. A rendszer a [illesztőprogramok] szakaszban hasonló. Az INI-fájl.|
> |**HKEY\_helyi\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli az ismert vagy gyakran használt rendszer DLL-ek; listája Ez a rendszer megakadályozza, hogy a személyek a gyenge directory Alkalmazásengedélyek rendszer DLL-ek trójai faló verzióiban elvetésével ártó szándékkal használja fel.|
> |**HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Figyeli a tudja fogadni az eseményértesítések Winlogon, az interaktív bejelentkezési támogatási modell, a Windows operációs rendszerhez a csomagok listájában.|

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A következő címekre szükség, kifejezetten a Change Tracking megoldásba. Ezek a címek kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.Azure-automation.us|

## <a name="use-change-tracking"></a>A Change Tracking használata

A megoldás engedélyezését követően megtekintheti a módosítások összefoglalása a figyelt számítógépek kiválasztásával **Change Tracking** alatt **konfigurációkezelés** az Automation-fiókban.

Változások a számítógép, és a-feltárás minden esemény részleteinek megtekintéséhez. Legördülő listákat a diagram és részletes információk a módosítási adattípus és időtartomány címtartományok alapján korlátozhatja a diagram tetején érhetők el. Is kattintson és húzza a diagramban válassza ki az egyéni időtartományt.

![Change Tracking irányítópult képe](./media/automation-change-tracking/change-tracking-dash01.png)

A részletes információkat, amelyek kimenetei kattint egy módosítása vagy esemény. Ahogy a példában látható, a szolgáltatás indítási típusa módosult a manuális a automatikus.

![változáskövetés részletek képe](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Keresés naplókban

Mellett a részleteit, amelyet a portálon a keresések ellen a naplók teheti meg. Az a **Change Tracking** lap meg van nyitva, kattintson **Log Analytics**, ekkor megnyílik a **naplóbeli keresés** lapot.

### <a name="sample-queries"></a>Mintalekérdezések

Az alábbi táblázatban kapcsolatos naplókeresési mintákat a megoldás által összegyűjtött rekordokkal módosítása:

|Lekérdezés  |Leírás  |
|---------|---------|
|ConfigurationData<br>&#124;ahol ConfigDataType == "WindowsServices" és a SvcStartupType == "Auto"<br>&#124;ahol SvcState == "Leállított"<br>&#124;Összegzés arg_max(TimeGenerated, *) SoftwareName, számítógép szerint         | Megjeleníti a legutóbbi Hardverleltár-rekordok automatikus értékre van beállítva, de lett jelentve le Windows-szolgáltatásokhoz<br>A program csak a legújabb bejegyzést, hogy szoftvernév és számítógép      |
|ConfigurationChange<br>&#124;ahol ConfigChangeType == "Szoftver" és a ChangeCategory == "Eltávolítva"<br>&#124;a TimeGenerated desc ORDER|A módosítási rekordok eltávolított szoftverek megjelenítése|

## <a name="next-steps"></a>További lépések

Látogasson el az oktatóanyag a Change Tracking tudhat meg többet a megoldás használata:

> [!div class="nextstepaction"]
> [A környezet változásainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)

* Használat [Log Analytics naplóbeli kereséseivel](../log-analytics/log-analytics-log-searches.md) részletes változáskövetési adatok megtekintéséhez.
