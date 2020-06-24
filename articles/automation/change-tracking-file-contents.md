---
title: A Change Tracking és a leltár kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használható a Change Tracking és a leltár a szoftverek és a Microsoft-szolgáltatások változásainak nyomon követésére a környezetben.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: 0eebd626013614bb6240fc0e6530a358a2b86d1c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781191"
---
# <a name="manage-change-tracking-and-inventory"></a>A Change Tracking és az Inventory kezelése

Új fájl vagy beállításkulcs a nyomon követéséhez való hozzáadásakor Azure Automation engedélyezi a [change Tracking és a leltár](change-tracking.md)számára. Ez a cikk ismerteti a nyomon követés konfigurálását, a nyomon követési eredmények áttekintését és a riasztások kezelését a változások észlelésekor.

A cikkben ismertetett eljárások használata előtt győződjön meg arról, hogy engedélyezte a Change Tracking és a leltárt a virtuális gépeken a következő módszerek egyikével:

* [A Change Tracking és az Inventory engedélyezése Automation-fiókból](automation-enable-changes-from-auto-acct.md)
* [Change Tracking és leltár engedélyezése a Azure Portal tallózásával](automation-enable-changes-from-browse.md)
* [A Change Tracking és az Inventory engedélyezése runbookból](automation-enable-changes-from-runbook.md)
* [A Change Tracking és az Inventory engedélyezése Azure-beli virtuális gépről](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>A központi telepítés hatókörének korlátozása

A Change Tracking és a leltár a munkaterületen belüli hatókör-konfigurációval célozza meg a számítógépeket, hogy fogadják a módosításokat. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Fájlok nyomon követése

A fájlok és mappák/könyvtárak változásainak nyomon követéséhez használhatja a Change Tracking és a leltárt. Ez a szakasz azt ismerteti, hogyan konfigurálhatja a Windows és Linux rendszereken a fájlok nyomon követését.

### <a name="configure-file-tracking-on-windows"></a>A fájlok nyomon követésének konfigurálása Windows rendszeren

A következő lépésekkel konfigurálhatja a Windows rendszerű számítógépeken a fájlok nyomon követését:

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. 
2. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
3. A munkaterület konfigurációja lapon válassza a **Windows-fájlok**lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új fájl hozzáadásához.
4. A Windows-fájl hozzáadása Change Tracking ablaktáblán adja meg a nyomon követni kívánt fájl vagy mappa adatait, majd kattintson a **Save (Mentés**) gombra. A következő táblázat az információhoz használható tulajdonságokat határozza meg.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követett fájl rövid neve.        |
    |Csoport     | Egy csoport neve a fájlok logikai csoportosításához.        |
    |Elérési út megadása     | A fájl keresésének elérési útja, például **c:\Temp \\ \* . txt**. Használhat környezeti változókat is, például: `%winDir%\System32\\\*.*` .       |
    |Elérési út típusa     | Az elérési út típusa A lehetséges értékek: fájl és mappa.        |    
    |Rekurzió     | True (igaz), ha a rendszer rekurziót használ a nyomon követett elem keresésekor, máskülönben hamis értéket ad.        |    
    |Fájl tartalmának feltöltése | True (igaz) – a fájl tartalmának feltöltése a nyomon követett változásokon, ellenkező esetben hamis.|

5. Győződjön meg arról, hogy a **fájl tartalmának feltöltéséhez**igaz értéket ad meg. Ezzel a beállítással engedélyezhető a fájl tartalmának követése a megadott elérési úthoz.

### <a name="configure-file-tracking-on-linux"></a>A fájlok nyomon követésének konfigurálása Linuxon

A következő lépésekkel konfigurálhatja a fájlok követését a Linux rendszerű számítógépeken:

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. 
2. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
3. A munkaterület konfigurációja lapon válassza a **Linux-fájlok**lehetőséget, majd kattintson a **+ Hozzáadás** gombra a nyomon követett új fájl hozzáadásához.
4. A Change Tracking Linux-fájl hozzáadása ablaktáblán adja meg a nyomon követni kívánt fájl vagy könyvtár adatait, majd kattintson a **Mentés**gombra. A következő táblázat az információhoz használható tulajdonságokat határozza meg.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Igaz, ha a beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követett fájl rövid neve.        |
    |Csoport     | Egy csoport neve a fájlok logikai csoportosításához.        |
    |Elérési út megadása     | A fájl keresésének elérési útja, például **/etc/*. conf**.       |
    |Elérési út típusa     | Az elérési út típusa A lehetséges értékek a fájl és a könyvtár.        |
    |Rekurzió     | True (igaz), ha a rendszer rekurziót használ a nyomon követett elem keresésekor, máskülönben hamis értéket ad.        |
    |Sudo használata     | Igaz értéke a sudo használata az elem keresésekor, ellenkező esetben hamis.         |
    |Hivatkozások     | Ez a beállítás határozza meg, hogyan kezelje a szimbolikus hivatkozásokat a címtárak bejárásakor. Lehetséges értékek:<br> Mellőzés – figyelmen kívül hagyja a szimbolikus hivatkozásokat, és nem tartalmazza a hivatkozott fájlokat/címtárakat.<br>Követés – követi a szimbolikus hivatkozásokat a rekurzió során, és tartalmazza a hivatkozott fájlokat/címtárakat is.<br>Kezelés – követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom módosítását.<br>**Megjegyzés:** A kezelés lehetőség nem ajánlott, mert nem támogatja a fájl tartalmának lekérését.    |
    |Fájl tartalmának feltöltése | True (igaz) – a fájl tartalmának feltöltése a nyomon követett változásokon, ellenkező esetben hamis. |

5. Győződjön meg arról, hogy a **fájl tartalmának feltöltéséhez**igaz értéket ad meg. Ezzel a beállítással engedélyezhető a fájl tartalmának követése a megadott elérési úthoz.

   ![Linux-fájl hozzáadása](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Fájl tartalmának követése

A fájl tartalmának nyomon követése lehetővé teszi egy fájl tartalmának megtekintését a követett változások előtt és után. A szolgáltatás minden változás után menti a fájl tartalmát egy [Storage-fiókba](https://docs.microsoft.com/azure/storage/common/storage-account-overview) . Íme néhány, a fájl tartalmának követésére szolgáló szabály:

* A fájl tartalmának tárolásához a Resource Manager-alapú üzemi modellt használó standard Storage-fiók szükséges. 
* Ne használja a prémium és a klasszikus telepítési modell Storage-fiókokat. Lásd: [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md).
* A Storage-fiókot csak egy Automation-fiókhoz lehet kapcsolni.
* Az Automation-fiókban engedélyezni kell a [change Tracking és a leltárt](change-tracking.md) .

### <a name="enable-tracking-for-file-content-changes"></a>A fájl tartalmának változásai követésének engedélyezése

A következő lépésekkel engedélyezheti a fájlok tartalmának módosítását:

1. A Azure Portal nyissa meg Automation-fiókját, majd válassza a **change Tracking** elemet a **konfiguráció kezelése**elemnél.
2. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
3. Válassza a **fájl tartalma** lehetőséget, majd kattintson a **hivatkozás**gombra. Ez a beállítás megnyitja a tartalom helyének hozzáadása Change Tracking ablaktáblán.

   ![Tartalom helyének hozzáadása](./media/change-tracking-file-contents/enable.png)

4. Válassza ki a fájl tartalmának tárolásához használni kívánt előfizetést és Storage-fiókot. 

5. Ha engedélyezni szeretné a fájl tartalmának nyomon követését az összes meglévő követett fájlnál, válassza **a be** lehetőséget a **fájl tartalmának feltöltéséhez az összes beállításnál**. Ezt a beállítást később is megváltoztathatja az egyes fájlok elérési útjára.

   ![Storage-fiók beállítása](./media/change-tracking-file-contents/storage-account.png)

6. A Change Tracking és a leltár megjeleníti a Storage-fiók és a közös hozzáférésű aláírás (SAS) URI-azonosítóját, amikor engedélyezi a fájl tartalmának módosítását. Az aláírások 365 nap után lejárnak, és **az újbóli Létrehozás gombra kattintva**újra létrehozhatók.

   ![Fiók kulcsainak listázása](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Követett fájl tartalmának megtekintése

Miután Change Tracking és a leltár észleli a nyomon követett fájl módosítását, megtekintheti a fájl tartalmát a változás részletei panelen.  

![Változások listázása](./media/change-tracking-file-contents/change-list.png)

1. A Azure Portal nyissa meg Automation-fiókját, majd válassza a **change Tracking** elemet a **konfiguráció kezelése**elemnél.

2. Válasszon ki egy fájlt a módosítások listájában, és válassza a **fájl tartalmának módosítása** lehetőséget a fájl tartalmának megtekintéséhez. A változás részletei panelen az egyes tulajdonságokhoz tartozó Fájlinformációk előtt és után megjelenő szabvány látható.

   ![Változás részletei](./media/change-tracking-file-contents/change-details.png)

3. Egymás melletti nézetben tekinti meg a fájl tartalmát. A **beágyazott** nézetet kiválasztva megjelenítheti a módosításokat.

## <a name="track-registry-keys"></a>Beállításkulcsok nyomon követése

A következő lépésekkel konfigurálhatja a beállításjegyzék-kulcsok nyomon követését Windows rendszerű számítógépeken:

1. A Azure Portal nyissa meg Automation-fiókját, majd válassza a **change Tracking** elemet a **konfiguráció kezelése**elemnél. 
2. Kattintson a **beállítások szerkesztése** (a fogaskerék szimbólum) elemre.
3. A munkaterület konfigurációja lapon válassza a **Windows beállításjegyzék**lehetőséget.
4. Kattintson a **+ Hozzáadás** gombra a nyomon követett új beállításkulcs hozzáadásához.
5. A Change Tracking Windows beállításjegyzék hozzáadása ablaktáblán adja meg a nyomon követett kulcs adatait, majd kattintson a Save ( **Mentés**) gombra. A következő táblázat az információhoz használható tulajdonságokat határozza meg.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Értéke TRUE (igaz), ha egy beállítás alkalmazva van, ellenkező esetben hamis.        |
    |Elem neve     | A nyomon követni kívánt beállításkulcs rövid neve.        |
    |Csoport     | Csoport neve a beállításkulcsok logikai csoportosításához.        |
    |Windows-beállításkulcs   | Az elérési úttal rendelkező kulcsnév, például: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Naplók keresése a változási rekordokhoz

A változási rekordok esetében különböző kereséseket végezhet a Azure Monitor naplókon. Ha megnyitotta a Change Tracking (változások nyomon követése) lapot, kattintson **log Analytics** a naplók lap megnyitásához. A következő táblázat a változási rekordokra vonatkozó példákat tartalmaz.

|Lekérdezés  |Leírás  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Megjeleníti az automatikusra beállított és a leállítottként jelentett Microsoft-szolgáltatások legújabb leltározási rekordjait. Az eredmények a megadott számítógépnév és számítógép legutóbbi rekordjára korlátozódnak.    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|Megjeleníti a törölt szoftverek módosítási rekordjait.|

## <a name="create-alerts-on-changes"></a>Riasztások létrehozása a változásokról

A következő példa azt mutatja, hogy a fájl **c:\Windows\System32\drivers\etc\hosts** módosult a gépen. Ez a fájl azért fontos, mert a Windows azt használja az IP-címekhez tartozó állomásnevek feloldására. Ez a művelet elsőbbséget élvez a DNS-sel szemben, és kapcsolódási problémákhoz vezethet. Emellett a rosszindulatú vagy más veszélyes webhelyekre irányuló forgalom átirányításához vezethet.

![A gazdagépek fájljának módosítását bemutató diagram](./media/change-tracking-file-contents/changes.png)

Ezt a példát követve megtudhatja, hogyan hozhat létre riasztásokat a változásokon.

1. Az Automation-fiókban válassza a **változások követése** a **konfiguráció**felügyelete alatt lehetőséget, majd válassza a **log Analytics**lehetőséget. 
2. A naplók keresése területen keresse meg a tartalom módosításait a **hosts** fájlban a lekérdezéssel `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Ez a lekérdezés a szót tartalmazó teljes elérési úttal rendelkező fájlok tartalmának módosítását keresi `hosts` . Egy adott fájlt is megadhat, ha az elérési út részét a teljes űrlapra módosítja, például a használatával `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Miután a lekérdezés visszaadja az eredményeit, kattintson az **új riasztási szabály** elemre a naplóbeli keresésben a riasztás létrehozása lap megnyitásához. Ezen a lapon a Azure Portal **Azure monitor** is megnyithatja. 

4. Vizsgálja meg újra a lekérdezést, és módosítsa a riasztási logikát. Ebben az esetben azt szeretné, hogy a riasztás akkor aktiválódik, ha a környezetben lévő összes gépen még egy változás észlelhető.

    ![Váltás a Hosts fájl változásainak nyomon követése lekérdezésre](./media/change-tracking-file-contents/change-query.png)

5. Miután beállította a riasztási logikát, rendeljen műveleti csoportokat a riasztás aktiválására válaszként végrehajtandó műveletek végrehajtásához. Ebben az esetben az elküldött e-maileket és az IT Service Management-(ITSM-) jegyet kell létrehozni. 

    ![A műveleti csoport beállítása a riasztásra a módosításkor](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>További lépések

* A hatókör-konfigurációkkal kapcsolatos további információkért lásd: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).
* Ha a Log Analytics munkaterületen tárolt naplókra van szüksége, tekintse meg a következő témakört: [keresések Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md).
* Ha a telepítésekkel fejeződött be, tekintse [meg az Automation-fiók összekapcsolása a Change Tracking és a leltárhoz](automation-unlink-workspace-change-tracking.md)című témakört.
* A virtuális gépek Change Tracking és leltárból való törléséhez lásd: [virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md).
* A szolgáltatási hibák elhárításáról lásd: [change Tracking és leltározási problémák elhárítása](troubleshoot/change-tracking.md).
