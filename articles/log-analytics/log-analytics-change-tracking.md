---
title: "Az Azure Naplóelemzés változásainak követése |} Microsoft Docs"
description: "A Naplóelemzési változáskövetési megoldás könnyebb legyen azonosítani a szoftver- és Windows-szolgáltatás módosításait a környezetében bekövetkező."
services: log-analytics
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7af2fb89f8fac9df3f784aac50c8fac7880fde43
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>A környezetben, a változáskövetési megoldás a szoftver változásainak követése

![Nyomkövetési szimbólum módosítása](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Ez a cikk segít Naplóelemzési a változáskövetési megoldás segítségével könnyedén azonosíthatók a változtatásokat a környezetben. A megoldás a Windows és Linux-szoftver, Windows-fájlok és beállításkulcsok, központi Windows-szolgáltatások és Linux-démonok módosításokat követi nyomon. Konfigurációs változásokat segíthetnek a rögzítési ponthoz működési problémák.

Milyen típusú telepített ügynök frissítéséhez megoldás telepítése. Telepített szoftverek, központi Windows-szolgáltatások és a felügyelt kiszolgálókon Linux-démonok módosításai olvasható. Ezt követően az adatok kerül feldolgozásra a felhőben Naplóelemzés szolgáltatás. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti. A változások követése irányítópulton szereplő információk segítségével a kiszolgálói infrastruktúra végrehajtott módosítások könnyen láthatja.

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása
Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

* Rendelkeznie kell egy [Windows](log-analytics-windows-agent.md), [Operations Manager](log-analytics-om-agents.md), vagy [Linux](log-analytics-linux-agents.md) ügynököt minden olyan számítógépen, ahol módosításokat figyelni kívánt.
* A változáskövetési megoldás hozzáadása az OMS-munkaterület az a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Vagy a megoldás található információk segítségével adhat hozzá [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). További beállításokra nincs is szükség.

### <a name="configure-linux-files-to-track"></a>Linux-fájlok nyomon követéséhez beállítása
A következő lépésekkel konfigurálhatja a fájlok nyomon követéséhez a Linux rendszerű számítógépeken.

1. Az OMS-portálon kattintson **beállítások** (fogaskerék jel).
2. A a **beállítások** kattintson **adatok**, és kattintson a **Linux fájl követési**.
3. A Linux fájl változások követése, írja be a teljes elérési útja, beleértve a fájlnevet a fájl, amelyet szeretne követni, és kattintson a **Hozzáadás** szimbólum. Például: "/etc/*.conf"
4. Kattintson a **Save** (Mentés) gombra.  

> [!NOTE]
> Linux fájl nyomkövetési könyvtár nyomon követése, könyvtárak, és nyomon követése helyettesítő rekurzió beleértve további képességekkel rendelkezik.

### <a name="configure-windows-files-to-track"></a>Windows-fájlok nyomon követéséhez beállítása
A következő lépésekkel konfigurálhatja a fájlok nyomon követéséhez a Windows rendszerű számítógépeken.

1. Az OMS-portálon kattintson **beállítások** (fogaskerék jel).
2. A a **beállítások** kattintson **adatok**, és kattintson a **Windows fájl követési**.
3. A Windows fájl változások követése, írja be a teljes elérési útja, beleértve a fájlnevet a fájl, amelyet szeretne követni, és kattintson a **Hozzáadás** szimbólum. Például: C:\Program Files (x86) \Internet Explorer\iexplore.exe vagy C:\Windows\System32\drivers\etc\hosts.
4. Kattintson a **Save** (Mentés) gombra.  
   ![Windows-fájl változáskövetési](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Windows-beállításkulcsok nyomon követéséhez konfigurálása
A következő lépésekkel konfigurálhatja a beállításkulcsokat, nyomon követheti a Windows rendszerű számítógépeken.

1. Az OMS-portálon kattintson **beállítások** (fogaskerék jel).
2. A a **beállítások** kattintson **adatok**, és kattintson a **Windows beállításjegyzék követési**.
3. A Windows beállításjegyzék változások követése, írja be a teljes kulcsot, amelyet szeretne követni, és kattintson a **Hozzáadás** szimbólum.
4. Kattintson a **Save** (Mentés) gombra.  
   ![A Windows beállításjegyzék változások követése](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Linux-fájl tulajdonságait ismertetése
1. **Típus**
   * **Fájl** (jelentést fájl metaadat - mérete, a módosítás dátuma, a kivonatoló, stb.)
   * **Directory** (jelentés directory metaadat - mérete, a módosítás dátuma, stb.)
2. **Hivatkozások** (symlink hivatkozások kezelése Linux egyéb fájlok és könyvtárak)
   * **Figyelmen kívül hagyása** (figyelmen kívül hagyása symlinks során nem tartalmazza a fájlok vagy könyvtárak hivatkozott rekurzió)
   * **Hajtsa végre a** (hajtsa végre a symlinks során rekurzió, így magába foglalja a hivatkozott fájlok vagy könyvtárak)
   * **Kezelése** (kövesse a symlinks, és módosítsa a visszaadott tartalom kezelése)

   > [!NOTE]   
   > A "Kezelése" hivatkozást lehetőség nem ajánlott. A fájl tartalma nem támogatott.

3. **Recurse** (Recurse keresztül mappa szintek és nyomon követheti a elérési utat értekezlet minden fájl)
4. **Sudo** (fájlok elérése vagy a sudo jogosultságot igénylő könyvtárak engedélyezése)

### <a name="limitations"></a>Korlátozások
A változáskövetési megoldás jelenleg nem támogatja a következő elemek:

* Mappák (könyvtárak) Windows fájl nyomon követése
* A rekurzió Windows fájl nyomon követése
* Helyettesítő karakterek Windows fájl nyomon követése
* Elérésiút-változók
* Hálózati fájlrendszer
* A fájl

Egyéb korlátozások is érvényesek:

* A **maximális fájlméret** oszlop és értékek nem a jelenlegi megvalósításában használt.
* Ha több mint 2500-fájlok gyűjtése a 30 perces adatgyűjtési ciklus, megoldás teljesítménye csökkent.
* Ha nagy hálózati forgalmat módosítás rekordok is tarthat legfeljebb hat órán keresztül megjelenítéséhez.
* A konfigurációs akkor módosítható, amíg a számítógép leállítása, ha a számítógép, amely a korábbi konfigurációt tartoztak fájlváltozások előfordulhat, hogy küldje.

### <a name="known-issues"></a>Ismert problémák
A változáskövetési megoldás jelenleg a következő hibákat észlelt:
* A gyorsjavítás-frissítések a Windows 10 Creators Update és Windows Server 2016 Core RS3 gépek nem történik gyűjtés.

## <a name="change-tracking-data-collection-details"></a>Nyomkövetési adatok gyűjtése adatainak módosítása
Változáskövetés gyűjti a szoftverleltár és az ügynököket, amelyeket engedélyezte a Windows-szolgáltatás metaadatok.

A következő táblázatban adatgyűjtési módszerek és egyéb hogyan adatok gyűjtése változáskövetési adatait.

| Platform | Közvetlen ügynök | Operations Manager-ügynök | Linux-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows és Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 5 perc – 50 perc, a módosítás típusától függően. További információkért tekintse meg az alábbi táblázatban. |


Az alábbi táblázat azon adatok gyűjtési gyakoriságát.

| **Változás típusa** | **frequency** | **Does****ügynök****küldése, ha eltérés?** |
| --- | --- | --- |
| A Windows beállításjegyzékben | 50 minutes | Nem |
| Windows file | 30 perc | Igen. Ha nincs változás 24 órában, pillanatképet küld el. |
| Linux-fájl | 15 perc | Igen. Ha nincs változás 24 órában, pillanatképet küld el. |
| Windows-szolgáltatások | 30 perc | Igen, ha módosítások találhatók 30 percenként. 24 óránként pillanatképet küld, függetlenül attól, módosítsa. Igen, a pillanatkép még küldött amennyiben nem módosult. |
| Linux-démonok | 5 perc | Igen. Ha nincs változás 24 órában, pillanatképet küld el. |
| A Windows szoftverek | 30 perc | Igen, ha módosítások találhatók 30 percenként. 24 óránként pillanatképet küld, függetlenül attól, módosítsa. Igen, a pillanatkép még küldött amennyiben nem módosult. |
| Linux-szoftver | 5 perc | Igen. Ha nincs változás 24 órában, pillanatképet küld el. |

### <a name="registry-key-change-tracking"></a>Beállításjegyzék-kulcs módosítás nyomon követése

A Naplóelemzési hajt végre a Windows beállításjegyzék figyelését és követését a változáskövetési megoldás. A beállításkulcsok módosításainak figyelése célja rögzítési ponthoz bővítési pontokat, ahol külső kód és a kártevők is aktiválhatja. Az alábbi listában láthatók az alapértelmezett beállításkulcsokat, hogy a megoldás követi, és miért egyes követhető nyomon.

- HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Figyelők parancsfájlok, amelyek indítási parancsot.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Figyelők futtatott parancsfájlok, leállításkor.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Mielőtt a felhasználó bejelentkezik a Windows fiókjukhoz betöltött kulcsokat figyeli. A kulcs szolgál 64 bites számítógépeken futó 32 bites program.
- HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed összetevők
    - Nyomon követi az alkalmazásbeállítások módosításait.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Figyelők közös autostart bejegyzéseit, amelyek közvetlenül a Windows Intézőt, és általában futtatási-folyamat az Explorer.exe környezet igénybe vételét.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Figyeli a ikon kezelő regisztrációs átfedő.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Figyeli a ikon átfedő 64 bites számítógépeken futó 32 bites program kezelő regisztrálása.
- HKEY\_helyi\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser segédobjektuma
    - Új böngésző segítő objektum beépülők az Internet Explorer figyeli. Használja a Document Object Model (DOM) az aktuális lap eléréséhez és a navigáció.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Új böngésző segítő objektum beépülők az Internet Explorer figyeli. A Document Object Model (DOM) az aktuális lap eléréséhez és navigáció a 64 bites számítógépeken futó 32 bites program használja.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Új Internet Explorer-bővítmények, például egyéni eszköz menük és az egyéni gombok figyeli.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Új Internet Explorer-bővítmények, például egyéni eszköz menük és 64 bites számítógépeken futó 32 bites program egyéni eszköztár gombjai figyeli.
- HKEY\_helyi\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - A 32 bites illesztőprogramok wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és vidc társított figyeli. Hasonló a rendszer [illesztőprogramok] szakasza. INI-fájl.
- HKEY\_helyi\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Figyelők a 32-bites illesztőprogramok társított wavemapper, wave1 és wave2, msacm.imaadpcm, .msadpcm, .msgsm610 és a 64 bites számítógépeken futó 32 bites program vidc. Hasonló a rendszer [illesztőprogramok] szakasza. INI-fájl.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Figyeli a lista ismert vagy a gyakran használt rendszer DLL-EK; a rendszer megakadályozza, hogy a személyek gyenge Alkalmazásengedélyek directory kihasználva ejtésével rendszer DLL-ek trójai faló verzióiban.
- HKEY\_helyi\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Figyeli a rendszeresemény-értesítéseket kaphat Winlogon, az interaktív bejelentkezési támogatási modell a Windows operációs rendszerhez a csomagok listájában.


## <a name="use-change-tracking"></a>A változáskövetés használata
A megoldás telepítése után megtekintheti a módosítások összegzése a figyelt kiszolgálók használatával a **változások követése** csempét a **áttekintése** OMS lapján.

![Változások követése csempe képe](./media/log-analytics-change-tracking/change-tracking-tile.png)

Megtekintheti a módosításai az infrastruktúrát, és a-feltárás részleteit az alábbi kategóriákban:

* Módosítások konfigurációtípus szoftver és a központi Windows-szolgáltatások szerint
* Alkalmazások és frissítések az egyes kiszolgálókon szoftver módosításai
* Szoftvermódosítások az egyes alkalmazások száma összesen
* Linux-csomagok
* Windows-szolgáltatások módosításai az egyes kiszolgálókon
* Linuxos démonok módosításai

![Változások követése irányítópult képe](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![Változások követése irányítópult képe](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Megtekintéséhez bármely módosítások típusának módosítása
1. Az a **áttekintése** lapján kattintson a **változások követése** csempére.
2. A a **módosítása követési** irányítópult, ellenőrizze az összefoglaló információkat a módosítás típus paneleken valamelyikével, és kattintson a egyet a vele kapcsolatos részletes információk megtekintése a **naplófájl-keresési** lap.
3. A naplófájl-keresési lapok egyikén tekintheti eredmények idő, illetve részletes leírást és a keresési korábbi naplók. Az eredmények szűkítéséhez értékkorlátozással is szűrhet.

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) részletes változáskövetési adatok megtekintéséhez.
