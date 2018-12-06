---
title: A Service Map megoldást használ az Azure-ban |} A Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen központi telepítése a Service Map a környezetben, és a számos célra használja.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2018
ms.author: magoedte
ms.openlocfilehash: 68c838481120dea7635ac5ea76859a8088d732fb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957350"
---
# <a name="using-service-map-solution-in-azure"></a>A Service Map megoldást használ az Azure-ban
A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. A Service Map, megtekintheti a kiszolgálók ahogyan Ön gondol rájuk: rendszerekként, amelyek kritikus fontosságú szolgáltatások biztosításához. A Service Map megmutatja a kiszolgálók, a folyamatok, a bejövő és kimenő kapcsolat késési kapcsolatokat, és portok között, bármely TCP-kapcsolattal összekötött architektúrában, semmilyen beállítást nem szükséges ügynököt telepíteni.

Ez a cikk bevezetése és használata a Service Map részleteit ismerteti. A Service Map és bevezetési ügynökök konfigurálásával kapcsolatos további információkért lásd: [konfigurálása a Service Map megoldás az Azure-ban]( service-map-configure.md).

>[!NOTE]
>Ha már telepítette a Service Map, most is megtekintheti a maps az Azure monitorban a virtuális gépek esetében kiegészítő szolgáltatást a virtuális gép állapotának és teljesítményének figyeléséhez tartalmaz. További tudnivalókért lásd: [virtuális gépek – áttekintés az Azure Monitor](../../azure-monitor/insights/vminsights-overview.md).


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="enable-service-map"></a>A Service Map engedélyezése
1. Az Azure Portalon kattintson a **+ erőforrás létrehozása**.
2. A keresősávba írja be a **Service Map** nyomja le az ENTER **Enter**.
3. A piactér keresési eredmények oldalon válassza ki a **Service Map** a listából.<br><br> ![Azure Marketplace-en keresési eredmények közül válassza ki a Service Map megoldás](./media/service-map/marketplace-search-results.png)<br>
4. Az a **Service Map** áttekintés ablaktábláján, tekintse meg a megoldás részleteit, és kattintson a **létrehozás** a Log Analytics-munkaterülethez a bevezetési folyamat megkezdéséhez.<br><br> ![Előkészítése a Service Map megoldás](./media/service-map/service-map-onboard.png).
5. Az a **megoldás konfigurálása** panelen válasszon egy meglévő, vagy hozzon létre egy új Log Analytics-munkaterületet.  Hogyan hozhat létre egy új munkaterületet kapcsolatban további információért lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../../azure-monitor/learn/quick-create-workspace.md). Miután megadta a szükséges adatokat, kattintson a **létrehozás**.  

Bár az adatokat a rendszer ellenőrzi, és a megoldást már telepítették, nyomon követheti a folyamat állapotát **értesítések** a menüből. 

A Service Map elérése a Log Analytics-munkaterület az Azure Portalon, és válassza a **megoldások** a bal oldali ablaktáblán.<br><br> ![Válassza ki a munkaterület megoldások beállítást](./media/service-map/select-solution-from-workspace.png).<br> Megoldások listájából válassza ki a **ServiceMap(workspaceName)** és a Service Map megoldás áttekintése lapon kattintson az összefoglalás csempére a Service Map.<br><br> ![A Service Map-összefoglaló csempe](./media/service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Használati esetek: Győződjön meg arról, az informatikai folyamatok függőségi figyelembe

### <a name="discovery"></a>Detektálás
A Service Map automatikusan felépít egy közös függőségek térképe referencia a kiszolgálók, folyamatok és a külső szolgáltatások. Felderíti és a térképek összes TCP függőséget, meglepő kapcsolatok, a távoli külső rendszerek függenek, és a hagyományos sötét területeihez, a hálózaton, például az Active Directory függőségek azonosításához. A Service Map felderíti a sikertelen hálózati kapcsolatokat, a felügyelt rendszerekről létesíteni, segítve a potenciális server Virtual Network szolgáltatás hibás, a szolgáltatás-kimaradás és a hálózati problémák azonosítását.

### <a name="incident-management"></a>incidenskezelés
A Service Map segítségével bemutatja, hogyan rendszerekhez kapcsolódó és egymással érintő esetlegességét a probléma kiküszöbölése. Mellett azonosítása sikertelen kapcsolatok, és biztonságosabbá teszi a helytelenül konfigurált terheléselosztók, kritikus szolgáltatások knál vagy túlzott terhelés azonosítása és a támadó ügyfelek, például a fejlesztői gépek legtöbbet beszélgető éles rendszereket. Integrált munkafolyamatok a Change Tracking segítségével is megjelenik-e egy esemény egy háttér-gépen, vagy szolgáltatás egy incidens gyökérokának ismerteti.

### <a name="migration-assurance"></a>Áttelepítési garancia
A Service Map használatával hatékony tervezéséhez programot, gyorsabban, és a ellenőrzése Azure áttelepítések, ami segít, győződjön meg arról, hogy semmi sem marad, és nem fordulhat elő, meglepő valamilyen okból kimaradás lép. Felfedezheti, hogy az összes saját rendszerek esetében, amelyek együtt áttelepítése, felmérheti a Rendszerkonfiguráció és a kapacitás és adja meg, hogy a futó rendszerek továbbra is szolgálja ki felhasználók, illetve egy jelölt helyett áttelepítési leszerelése. Az Áthelyezés befejezése után ellenőrizheti az ügyfél által generált terhelést, és az identitás ellenőrzése, hogy tesztelési rendszerek és az ügyfelek csatlakoznak. Ha az alhálózat tervezési és a tűzfal-meghatározások problémákba ütközik, a Service Map maps sikertelen kapcsolatok mutat, a rendszerek esetében, amelyek kapcsolat.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
Ha használja az Azure Site Recovery és kell a helyreállítás során alkalmazás környezetében, a Service Map meghatározása segítséget automatikusan jeleníti meg, hogyan használja a rendszerek egymástól annak érdekében, hogy a helyreállítási terv megbízható. Egy kritikus fontosságú kiszolgáló vagy csoport kiválasztása, és az ügyfelek megtekintése, mely előtér-rendszerek helyreállítása után a kiszolgáló visszaállítása, és elérhető azonosíthatja. Ezzel szemben kritikus fontosságú kiszolgálók háttér-függőségek megnézzük, azonosíthatja mely rendszerek, hogy helyreálljon, mielőtt a rendszer visszaállítja a fókusz rendszerek.

### <a name="patch-management"></a>Javítások kezelése
A Service Map fokozza a rendszerfrissítési felmérés használatát megjelenítésével, attól függően más csapatok és a kiszolgálók a szolgáltatáshoz, így képes értesíteni őket előre Pro opravy rendszereit lenyomva végrehajtása előtt. A Service Map is fokozza a javítások kezelését ismerheti meg, hogy a szolgáltatás elérhető, és megfelelően csatlakoztatott után legyenek-e javítani, és újraindul.

## <a name="mapping-overview"></a>Leképezés – áttekintés
A Service Map ügynökök gyűjtse össze a kiszolgálón, amelyen telepítve van az összes TCP-kapcsolattal rendelkező-folyamatokkal kapcsolatos információkat és a bejövő és kimenő kapcsolatok számára az egyes folyamatok részleteit.

A bal oldali ablaktáblán a listából kiválaszthatja a gépek vagy csoportokat, amelyeknek a Service Map függősége megjelenítése a megadott időtartomány keresztül. Gép függőségi leképezi egy adott gép koncentrálhat, és azok megjelenítése, amelyek közvetlen TCP-ügyfelek vagy kiszolgálók, hogy a gép az összes gép.  Számítógépcsoport maps kiszolgálók és a Függőségek megjelenítése.

![A Service Map áttekintése](media/service-map/service-map-overview.png)

Gépek a leképezés megjelenítéséhez a futó bővíthetők ki csoportokat és az aktív hálózati kapcsolatokkal rendelkező folyamatokat feldolgozni a kijelölt időtartományban. Ha egy távoli gépen a Service Map ügynökkel folyamat részleteinek megjelenítéséhez ki van bontva, csak a fókusz gép kommunikáló folyamatokat jelennek meg. Ügynök nélküli kivételfigyelés előtér-, a fókusz géphez csatlakozó gépek száma a bal oldalon, a folyamatokat, amelyekhez csatlakoznak jelzi. A fókusz gépet, hogy így olyan háttér-gépen, amelyen nincs ügynök kapcsolatot, ha a háttérkiszolgáló-szerepel egy Port kiszolgálócsoportot, és egyéb kapcsolatok ugyanazt a portszámot.

Alapértelmezés szerint a Service Map maps megjelenítése a függőségi adatokat az elmúlt 30 percben. Bal felső idő vezérlők használatával lekérdezheti a maps múltbéli porttartományok akár egy óráig bemutatják, hogyan függőségek kikeresi az elmúlt (például az incidens alatt vagy előtt történt változás). Fizetős munkaterületek 30 napig, és a munkaterületek 7 napig a Service Map adatokat tárolja.

## <a name="status-badges-and-border-coloring"></a>Állapot jelvények és szegély színezés
A térképen minden kiszolgáló alján lehet a kiszolgáló állapotinformációról igényinek állapot jelvények listáját. A jelvény jelzi, hogy a kiszolgáló a megoldáshoz való integrációt közül néhány vonatkozó adatot. Egy jelvény kattintva megnyílik a közvetlenül a jobb oldali panelen az állapot részleteit. A jelenleg rendelkezésre álló állapot jelvények közé tartozik a riasztások, Service Desk, módosítások, biztonsági és frissítéseket.

Az állapot jelvények súlyosságát, attól függően gép csomópont szegélyek, színes piros (kritikus), sárga (figyelmeztetés) vagy kék (tájékoztató). A szín bármilyen jelvények állapot legsúlyosabb állapotát jelöli. Szürke szegélyt megadni egy csomópont, amelynek nincs Állapotjelzők jelzi.

![Jelvények állapota](media/service-map/status-badges.png)

## <a name="process-groups"></a>Folyamat csoportok
Csoportosítja kombinálásával egyetlen folyamat csoportba tartozó közös termék vagy szolgáltatás folyamatokat.  Ha egy gép csomópont önálló folyamatok csoportosítja együtt jelenik meg.  Ha egy folyamat egy folyamat csoportban bejövő és kimenő kapcsolatok majd a kapcsolat nem sikerült akkor jelenik meg, mert nem sikerült a teljes folyamat csoportot.

## <a name="machine-groups"></a>Számítógépcsoportok
Gépcsoportok lehetővé teszi több kiszolgáló, ne csak egyet láthatja az összes tagját egy többrétegű alkalmazást vagy kiszolgálófürt egy térképen Eszközkezelőre maps.

Felhasználók válassza ki, mely kiszolgálók együtt egy csoporthoz tartozik, és válassza ki a csoport nevét.  Ezután kiválaszthatja a csoport összes kapcsolatok és folyamatok megtekintése, illetve megtekinteni azt csak a folyamatok és a kapcsolatok közvetlenül kapcsolódik az a csoport más tagjai.

![Számítógépcsoport](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Egy számítógépcsoport létrehozása
Hozzon létre egy csoportot, válassza ki a gépet, vagy a gépek a listában, és kattintson a kívánt gépekre **Hozzáadás csoporthoz**.

![Csoport létrehozása](media/service-map/machine-groups-create.png)

Itt választhat **új létrehozása** , és adja meg a csoport nevét.

![Csoport neve](media/service-map/machine-groups-name.png)

>[!NOTE]
>Gépcsoportok 10 kiszolgálók korlátozódnak.

### <a name="viewing-a-group"></a>Egy csoport megtekintése
Miután létrehozott néhány csoportot, válassza ki a csoportok lapon megtekintheti őket.

![Csoportok lapon](media/service-map/machine-groups-tab.png)

Ezután válassza ki a csoport nevét, a térkép megtekintéséhez az adott gép csoporthoz.
![Számítógépcsoport](media/service-map/machine-group.png) a gépeket a csoporthoz tartozó a fehér a térképen foglaltaknak.

A csoport kiterjesztése a gépeken a gép csoportot alkotó listázza.

![Gépek csoportosítása a gépek](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Szűrés folyamatok szerint
Itt válthat a térképnézet folyamatok és a kapcsolatok megjelenítése a csoport és csak a közvetlenül kapcsolódik az a gép csoport között.  Az alapértelmezett nézet az összes folyamat megjelenítéséhez.  A térkép felett a szűrő ikonjára kattintva módosíthatja a nézetet.

![Szűrőcsoport](media/service-map/machine-groups-filter.png)

Amikor **összes folyamat** van kijelölve, a térkép tartalmazza az összes olyan folyamatokat és kapcsolatok az egyes gépek a csoportban.

![Minden számítógépcsoport dolgozza fel.](media/service-map/machine-groups-all.png)

Ha módosítja a nézet csak **csoporthoz kapcsolódó folyamatok**, a térkép fog leszűkül, hogy csak ezen folyamatok és a kapcsolatok közvetlenül csatlakoztatott más számítógépekre, a csoport létrehozása egy egyszerűsített nézetet.

![A szűrt folyamatok számítógépcsoport](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Gépek felvétele egy csoportba
Egy meglévő csoporthoz hozzáadni, jelölje be azoknak a gépeket, majd kattintson a **Hozzáadás csoporthoz**.  Ezután válassza ki a gépeket hozzáadni kívánt csoport.
 
### <a name="removing-machines-from-a-group"></a>Gépek eltávolítása egy csoportból
A csoportok listájában bontsa ki a csoport nevét, az a gép csoportban található gépek listáját.  Kattintson a három pont menü mellett a távolítsa el, és válassza a kívánt gépet **eltávolítása**.

![Gép eltávolítása a csoportból](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Távolítsa el, vagy egy csoport átnevezése
Kattintson a három pont menü mellett a csoport nevét, a csoport listában.

![Gép csoport menü](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Szerepkör ikon
Bizonyos folyamatok szolgál adott szerepkörök gépeken: webalkalmazás-kiszolgálók, az alkalmazáskiszolgálók, adatbázis és így tovább. A Service Map jelzi a folyamatok és a gépek négyzeteken szerepkör ikonok a szerepkör egy folyamatot, vagy a kiszolgáló fontos szerepet játszik az egyetlen pillantással azonosításának megkönnyítése érdekében.

| Szerepkör ikon | Leírás |
|:--|:--|
| ![Webkiszolgáló](media/service-map/role-web-server.png) | Webkiszolgáló |
| ![Alkalmazáskiszolgáló](media/service-map/role-application-server.png) | Alkalmazáskiszolgáló |
| ![Adatbázis-kiszolgáló](media/service-map/role-database.png) | Adatbázis-kiszolgáló |
| ![LDAP-kiszolgáló](media/service-map/role-ldap.png) | LDAP-kiszolgáló |
| ![SMB-kiszolgálón](media/service-map/role-smb.png) | SMB-kiszolgálón |

![Szerepkör ikon](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Sikertelen kapcsolatok
Sikertelen kapcsolatok együtt jelennek meg a folyamatok és a számítógépek, a Service Map maps piros szaggatott vonal jelzi, hogy egy ügyfél rendszer érhetők el egy folyamat és a port nem működik. Ha a rendszer a sikertelen kapcsolódás kísérlet egy sikertelen kapcsolatok a Service Map telepített ügynökkel bármely rendszerből jelenti. A Service Map ezt a folyamatot nem sikerült kapcsolatot létesíteni a TCP-szoftvercsatornák megfigyelésével méri. Ez a hiba hatására a tűzfal, melyek a helytelen konfiguráció az ügyfél vagy kiszolgáló vagy egy távoli szolgáltatás nem volt elérhető.

![Sikertelen kapcsolatok](media/service-map/failed-connections.png)

Sikertelen kapcsolatok segítségére lehetnek a hibakeresésben, migrálásának ellenőrzése, biztonsági elemzés és a teljes architekturális ismertetése ismertetése. Sikertelen kapcsolatok néha érintetlen, de gyakran közvetlenül a problémát, például egy feladatátvételi környezetet hirtelen váljon nem érhető el, vagy éppen nem lehet felvenni a kapcsolatot a felhőbe történő migrálás után két alkalmazásrétegek mutassanak.

## <a name="client-groups"></a>Ügyfélcsoportok
Ügyfél csoportjai mezőkbe a térképen, amelyek az ügyfélgépek, amelyek nem rendelkeznek a függőségi ügynököt. Egyetlen ügyfél csoport jelöli az ügyfelek egy egyedi folyamat vagy a gépen.

![Ügyfélcsoportok](media/service-map/client-groups.png)

Tekintse meg a kiszolgálók egy ügyfélcsoportba tartozó IP-címét, válassza ki a csoportot. A csoport tartalmát szerepelnek a **ügyfél tulajdonságai** ablaktáblán.

![Ügyfél tulajdonságai](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Kiszolgálóport csoportok
Kiszolgálóport csoportok olyan kiszolgálók, amelyek nem rendelkeznek a függőségi ügynökök kiszolgáló portjait képviselő be. A mezőbe a kiszolgálóport és a kiszolgálókat erre a portra kapcsolatokkal darabszámát tartalmazza. Bontsa ki az egyes kiszolgálók és a kapcsolatok a mezőbe. Ha a mezőben csak egy kiszolgálót, a nevét vagy IP-cím szerepel.

![Kiszolgálóport csoportok](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Helyi menü
A felül látható a három pontra (...) kattint jobb oldalán található bármely olyan kiszolgálóra a helyi menüben a kiszolgálóhoz tartozó jeleníti meg.

![Sikertelen kapcsolatok](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Kiszolgálótérkép betöltése
Kattintson a **Kiszolgálótérkép betöltése** megnyílik egy új térkép a kiválasztott kiszolgálón, az új fókusz gép.

### <a name="show-self-links"></a>Önmagukra mutató hivatkozások megjelenítése
Kattintson a **megjelenítése Self-Links** újrarajzolja a kiszolgáló-csomópontot, így azokat is önmagukra mutató hivatkozások, amelyek, amely a kezdő és záró folyamatok belül a kiszolgáló TCP-kapcsolatokat. Ha önmagukra mutató hivatkozások jelennek meg, a menü módosításokat **elrejtése Self-Links**, így kikapcsolhatja azokat.

## <a name="computer-summary"></a>A számítógép összefoglaló
A **gép összefoglalás** ablaktáblán egy kiszolgáló operációs rendszer, a függőség számát és a többi megoldásaiból származó adatok áttekintését tartalmazza. Az adatok közé tartozik a teljesítmény-mérőszámok, ügyfélszolgálati szolgáltatásjegyek, változáskövetés, biztonsági és frissítéseket.

![Gép összefoglalás panel](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Számítógép és a folyamat tulajdonságai
Navigálás a Service Map térképet, kiválaszthatja a gépek és azok tulajdonságaival kapcsolatos további környezet próbál a jeggyel folyamatokat. Gépek DNS neve, IPv4 címeket, a Processzor és memória-kapacitás, virtuális gép típusa, operációs rendszer és verzió, legutóbbi újraindítás időt, és a szolgáltatás és az OMS-ügynökök az azonosítók adatainak megadása.

![Gép Tulajdonságok ablaktábla](media/service-map/machine-properties.png)

Folyamat részletei gyűjthet az operációs rendszer metaadatokat, eljárások, beleértve a folyamat neve, folyamat leírása, felhasználónév és a tartomány (a Windows), Cégnév, terméknév, termékverzió, munkakönyvtár, parancssor és folyamat fut kezdő időpontja.

![Tulajdonságok ablaktábla feldolgozása](media/service-map/process-properties.png)

A **kapcsolatos eljárás összefoglalása** panel további információkat találhat a folyamat kapcsolatot, beleértve a kötött portok, a bejövő és kimenő kapcsolatokat, és a kapcsolatok meghiúsultak.

![Folyamat összefoglalás panel](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Riasztások-integráció
A Service Map integrálható az Azure-riasztások a kiválasztott kiszolgálóhoz tartozó aktivált riasztások megjelenítéséhez a kijelölt időtartományban található. A kiszolgáló ikont jelenít meg, ha vannak az aktuális riasztásokat, és a **gép riasztások** panel felsorolja a riasztásokat.

![Számítógép-riasztások panel](media/service-map/machine-alerts.png)

Ahhoz, hogy a kapcsolódó riasztások megjelenítése a Service Map, hozzon létre egy riasztási szabályt, amely akkor aktiválódik, egy adott számítógépen. Megfelelő riasztások létrehozásához:
- Számítógép csoporthoz záradékot tartalmazni (például **számítógép időköze 1 perces**).
- Válasszon riasztást metrikus egység alapján.

## <a name="log-events-integration"></a>Események naplóintegráció
A Service Map integrálható a naplóbeli keresés megjelenítése a kiválasztott kiszolgálóhoz tartozó összes elérhető napló események száma a kijelölt időtartományban. Kattintson bármelyik sorra a listában a naplóbeli keresés ugorhat, és az egyes alkalmazásnapló-események események számát.

![Gép alkalmazásnapló-események ablak](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Ügyfélszolgálat-integráció
Az IT Service Management-összekötő a Service Map integráció akkor automatikus, ha mindkét megoldás engedélyezve van, és a Log Analytics-munkaterületen konfigurált. Az integráció a Service Map feliratú "Ügyfélszolgálat". További információkért lásd: [központilag kezelheti az IT Service Management-összekötő használatával ITSM munkadarabok](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

A **gép ügyfélszolgálat** panel felsorolja a kijelölt időtartományban található a kiválasztott kiszolgálóhoz tartozó összes IT Service Management-eseményt. A kiszolgáló ikont jelenít meg, ha nincsenek aktuális elemek, és a gép ügyfélszolgálat ablaktábla sorolja fel őket.

![Gép ügyfélszolgálat panel](media/service-map/service-desk.png)

A cikk a csatlakoztatott ITSM-megoldás megnyitásához kattintson a **munkaelem megtekintése**.

Az elem részleteinek megtekintése a Naplókeresésben, kattintson a **megjelenítés a Naplókeresésben**.
A Log Analytics két új táblázat írt kapcsolati metrika 

## <a name="change-tracking-integration"></a>Módosítsa a Híváskövetés-integráció
A Service Map-integráció a Change Tracking akkor automatikus, ha mindkét megoldás engedélyezve van, és a Log Analytics-munkaterületen konfigurált.

A **gép Change Tracking** panel felsorolja az összes módosítást, és a legutóbbi van legelöl, valamint lefúrhat a naplóbeli keresés további részleteket mutató hivatkozást.

![Gép Change Tracking panel](media/service-map/change-tracking.png)

Az alábbi képen, amelyeket láthat ConfigurationChange esemény részletes nézet kiválasztása után **megjelenítése a Log Analytics**.

![Konfigurációváltozás esemény](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Teljesítmény-integráció
A **gép teljesítményét** ablaktábla megjeleníti a kiválasztott kiszolgálóhoz tartozó szabványos teljesítmény-mérőszámok. A metrikák által küldött és fogadott hálózati bájtok CPU kihasználtságát, a memóriahasználat, küldött és fogadott hálózati bájtok és a felső folyamatok listáját tartalmazza.

![Számítógép teljesítménye paneljén](media/service-map/machine-performance.png)

Teljesítményadatok megjelenítéséhez szükség lehet [engedélyezése a Log Analytics megfelelő teljesítményszámlálók](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Az engedélyezni kívánt számlálókat:

Windows:
- Processor(*)\\processzoridő
- Memória\\előjegyzett kihasználtsága (%)
- Hálózati Adapter(*)\\küldött bájtok/s
- Hálózati Adapter(*)\\fogadott bájtok/mp

Linux:
- Processor(*)\\processzoridő
- Memory(*)\\foglalt memória %
- Hálózati Adapter(*)\\küldött bájtok/s
- Hálózati Adapter(*)\\fogadott bájtok/mp

A hálózati teljesítményadatok lekéréséhez kell is engedélyezve van a Wire Data 2.0 megoldás a munkaterület.
 
## <a name="security-integration"></a>Biztonsági integrálása
Biztonsági és auditálási megoldás a Service Map integráció akkor automatikus, ha mindkét megoldás engedélyezve van, és a Log Analytics-munkaterületen konfigurált.

A **gépi biztonsági** panelen a kiválasztott kiszolgálóhoz tartozó a biztonsági és auditálási megoldás adatait jeleníti meg. A panelen a kiválasztott időtartományban sorolja fel a kiszolgáló szálankénti függőben lévő biztonsági problémák összefoglalása. A biztonsági problémák gyakorlatokat bármelyikét le tárolóiba kattintva egy Naplókeresés velük kapcsolatos részleteket.

![Gép biztonsági panel](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Frissítések integráció
Az Update Management Service Map integrációs akkor automatikus, ha mindkét megoldás engedélyezve van, és a napló Anlaytics munkaterületen konfigurált.

A **Machine-frissítések** ablaktábla a kijelölt kiszolgálón az Update Management megoldás adatait jeleníti meg. A panelen a kiválasztott időtartományban sorolja fel a kiszolgáló hiányzó frissítésekkel összegzését.

![Gép Change Tracking panel](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Service Map-számítógép és a folyamat leltáradatok érhető el [keresési](../../azure-monitor/log-query/log-query-overview.md) a Log Analyticsben. Ezeket az adatokat, beleértve az áttelepítés megtervezése, kapacitáselemzési, felderítési és igény szerinti teljesítménnyel kapcsolatos hibaelhárítás forgatókönyveket is alkalmazhat.

Egy rekord minden egyedi számítógép és a folyamat a rekordokat, amelyek akkor jönnek létre egy folyamatot, vagy a számítógép indításakor vagy bevezetve a Service Map mellett óránként jön létre. Ezek a rekordok tulajdonságait az alábbi táblázatban rendelkezik. A mezők és értékek a mezők a ServiceMap Azure Resource Manager API-ban a gép erőforrás ServiceMapComputer_CL események leképezés. A mezők és értékek a ServiceMapProcess_CL események leképezés a mezőket a folyamat erőforrás a ServiceMap Azure Resource Manager API-ban. A ResourceName_s mezője megegyezik a megfelelő Resource Manager-erőforrás neve mezőjében. 

>[!NOTE]
>A Service Map funkciók növekedésével ezek a mezők változhatnak.

Belsőleg generált tulajdonságaiként segítségével egyedi folyamatokat és a számítógépek azonosításához:

- Számítógép: Használata *ResourceId* vagy *ResourceName_s* egy számítógépen belül a Log Analytics-munkaterület egyedi azonosításához.
- A folyamat: Használja *ResourceId* egy folyamatot egy Log Analytics-munkaterületen belül egyedi azonosításához. *ResourceName_s* egyedi, a gép, amelyen a folyamatot futtató (MachineResourceName_s) környezetében 

Több rekord megadott folyamat és a egy adott időtartományban számítógép is létezik, mivel a lekérdezések adhat vissza ugyanazon a számítógépen vagy folyamat egynél több rekordot. Adja meg a legújabb bejegyzést, adja hozzá a "|} a deduplikáció erőforrás-azonosító"a lekérdezéshez.

### <a name="connections"></a>Kapcsolatok
A Log Analytics - VMConnection új tábla kapcsolati metrika kerüljenek. Ez a táblázat ismerteti a kapcsolatokat a gép (bejövő és kimenő). Kapcsolati metrika is ki vannak téve az API-kkal, amelyek biztosítják az eszközöket egy adott metrika beszerzése egy adott időszakban.  TCP-kapcsolatok eredő "*fogadja el*- ing figyel-e szoftvercsatorna bejövő, míg a által létrehozott *csatlakozás*- ing, hogy egy adott IP-cím és port kimenő. A kapcsolat irányát képviseli a iránya tulajdonság, amely lehet megadni **bejövő** vagy **kimenő**. 

Ezeknek a tábláknak rögzíti a függőségi ügynök által küldött adatokból jönnek létre. Minden rekord egy megfigyelési egy egy perces időszakra jelöli. A TimeGenerated tulajdonság az időintervallum kezdetét jelzi. Minden rekord tartalmazza a megfelelő entitás, amely azonosító adatokat, kapcsolat vagy a port, valamint az adott entitás kapcsolódó metrikák. Jelenleg csak akkor fordul elő, a TCP-val IPv4 hálózati aktivitás jelentett.

Kezelheti a költségeket és összetettséget, csatlakozási rekordjainak nem felelnek meg az egyes fizikai hálózati kapcsolatokat. Több fizikai hálózati kapcsolatot, amely megjelenik majd a megfelelő tábla logikai kapcsolatot vannak csoportosítva.  Ez azt jelenti, rögzíti a *VMConnection* tábla felel meg a logikai csoportosítása és nem az egyes fizikai kapcsolatokat betartását. Egyetlen logikai rekord összesíti ugyanazt az értéket a következő attribútumok megosztása során egy adott egyperces időszakban, a fizikai hálózati kapcsolatot a rendszer *VMConnection*. 

| Tulajdonság | Leírás |
|:--|:--|
|Irány |A kapcsolat irányát, értéke *bejövő* vagy *kimenő* |
|Gép |A számítógép teljes Tartományneve |
|Feldolgozás |Identitáskezelési folyamat vagy a csoportok a folyamatok, a kapcsolat kezdeményezése és elfogadása |
|SourceIp |A forrás IP-címe |
|DestinationIp |A cél-IP-cím |
|DestinationPort |A cél-port száma |
|Protokoll |A kapcsolathoz használt protokoll.  Értékek *tcp*. |

A fiókra a csoportosítás a hatását, csoportosított fizikai kapcsolatok számával kapcsolatos információkat a rekord a következő tulajdonságok:

| Tulajdonság | Leírás |
|:--|:--|
|LinksEstablished |A jelentéskészítési időszakban létesített fizikai hálózati kapcsolatok száma |
|LinksTerminated |A jelentéskészítési időszakban le lett állítva fizikai hálózati kapcsolatok száma |
|LinksFailed |A jelentéskészítési időszakban sikertelenül fizikai hálózati kapcsolatok száma. Ez az információ érhető el jelenleg csak a kimenő kapcsolatok számára. |
|LinksLive |Volt megnyitva a jelentéskészítési időtartomány végén található fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

Mellett száma kapcsolati metrika adatmennyiség kapcsolatos információkat küldött, és a egy adott logikai kapcsolat fogadott, vagy hálózati port is szerepelnek a rekord a következő tulajdonságokat:

| Tulajdonság | Leírás |
|:--|:--|
|BytesSent |A jelentéskészítési időszakban elküldött bájtok száma |
|BytesReceived |A jelentéskészítési időszakban fogadott bájtok teljes száma |
|Válaszok |A jelentéskészítési időszakban megfigyelt válaszok száma. 
|ResponseTimeMax |A legnagyobb válaszideje (ezredmásodperc) észlelt a jelentéskészítési időszakban.  Ha nincs érték a tulajdonság értéke üres.|
|ResponseTimeMin |A legkisebb válaszideje (ezredmásodperc) észlelt a jelentéskészítési időszakban.  Ha nincs érték a tulajdonság értéke üres.|
|ResponseTimeSum |Az összes válaszidők összege a jelentéskészítési időszakban megfigyelt (ezredmásodperc).  Ha nincs érték a tulajdonság üres|

A jelentett adatokat harmadik típus a válaszidő - mennyi ideig does egy hívó költségek feldolgozni és a távoli végpont célzottól kapcsolaton keresztül küldött kérés vár. A válaszidő jelentett igaz válaszideje, az alapul szolgáló protokoll becslése. Számított heurisztika alapján a megfigyelési adatok között a forrás és cél végén egy fizikai hálózati kapcsolatot a flow használatával. Elméleti szinten a kérelem utolsó bájtját elhagyja a küldő, és az idő a válasz utolsó bájtját érkezésekor vissza közötti különbség. E két időbélyegek ábrázolni a kérések és válaszok események egy adott fizikai kapcsolathoz szolgálnak. A különbség a kettő válaszideje, egyetlen kérelem jelöli. 

Ez a szolgáltatás első verziójában az algoritmus sikeres, a tényleges protokoll egy adott hálózati kapcsolathoz használt függően különböző fokú feltétlenül közelítés. Például az aktuális módszer jól működik a kérés-válasz alapú protokollokat, például a HTTP (S), de nem dolgozhat egyirányú, illetve üzenetet az üzenetsor-alapú protokollokat.

Az alábbiakban néhány fontos tudnivalók:

1. Ha egy folyamat ugyanazon IP-címen, de több hálózati adapterrel kapcsolatokat fogad, minden egyes kapcsolathoz külön rekord lesz jelentve. 
2. Helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordokat tartalmazni fogja nincs tevékenység. Nem foglalja azokat, amelyek arra, hogy a gépen port nyitott a bejövő forgalmat.
3. Részletességi és adatmennyiség csökkentése érdekében helyettesítő karaktert tartalmazó IP-Címmel rendelkező rekordok kimarad egy egyező rekordot (az ugyanabban a folyamatban, port és protokoll) esetén egy adott IP-címmel. Amikor egy helyettesítő karaktert tartalmazó IP-címrekordot, a rendszer a IsWildcardBind rekord tulajdonságot az adott IP-címmel rendelkező fog beállítani "True" jelzi, hogy a port van-e téve a jelentéskészítési gép minden felületen keresztül történik.
4. Csak egy adott illesztő a kötött portok beállítása "False" IsWildcardBind rendelkezik.

#### <a name="naming-and-classification"></a>Elnevezési és besorolása
Az egyszerűség kedvéért egy kapcsolat a távoli vég IP-címét a RemoteIp tulajdonság szerepel. A bejövő kapcsolatok esetében RemoteIp megegyezik SourceIp, mint a kimenő kapcsolatokhoz megegyezik a DestinationIp. A RemoteDnsCanonicalNames tulajdonság jelöli a canonical DNS-nevek, a gép által jelentett RemoteIp. A RemoteDnsQuestions és RemoteClassification tulajdonság későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely
*VMConnection* is tartalmaz földrajzi adatokat a távoli kapcsolat rekordokban végén a rekord a következő tulajdonságai: 

| Tulajdonság | Leírás |
|:--|:--|
|RemoteCountry |Az üzemeltető RemoteIp ország neve.  Ha például *Egyesült Államok* |
|RemoteLatitude |A földrajzi szélesség.  Ha például *47.68* |
|RemoteLongitude |A földrajzi hosszúság.  Ha például *-122.12* |

#### <a name="malicious-ip"></a>Kártékony IP-cím
Minden RemoteIp tulajdonság *VMConnection* tábla be van jelölve IP-címek összevetéssel az ismert kártékony tevékenységek. Ha a RemoteIp kártevőként azonosított a következő tulajdonságok lesz kitöltve (azok üres, ha a IP-cím nem számít rosszindulatú) a rekord a következő tulajdonságai:

| Tulajdonság | Leírás |
|:--|:--|
|MaliciousIp |A RemoteIp címe |
|IndicatorThreadType |Észlelt fenyegetés mutató a következő értékek egyike *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *kártevő*, *adathalász*, *Proxy*, *elleni*, *Megnézendők*.   |
|Leírás |Az észlelt fenyegetés leírása. |
|TLPLevel |Közlekedési lámpa protokoll (TLP) szint egyike a meghatározott értékeknek *fehér*, *zöld*, *sárga*, *Red*. |
|Megbízhatóság |Értékek a következők *0 – 100*. |
|Severity |Értékek a következők *0 – 5*, ahol *5* van a legsúlyosabb és *0* nem súlyos egyáltalán. Alapértelmezett érték *3*.  |
|FirstReportedDateTime |Először a szolgáltató jelenteni a kijelző. |
|LastReportedDateTime |A kijelző Interflow által látott utolsó időpontját. |
|IsActive |Azt jelzi, hogy a mutatók vannak inaktiválása az *igaz* vagy *hamis* értéket. |
|ReportReferenceLink |Egy adott rendszernek megfigyelhetőnek kapcsolatos jelentéseket mutató hivatkozásokat tartalmaz. |
|AdditionalInformation |További információkat biztosít, ha van ilyen, az észlelt fenyegetés kapcsolatban. |

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL records
Típussal rendelkező rekordok *ServiceMapComputer_CL* rendelkezik a kiszolgálókat a Szolgáltatástérkép-ügynökökkel Hardverleltár-adatait. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Típus | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belül a gépek egyedi azonosítója |
| ResourceName_s | A munkaterületen belül a gépek egyedi azonosítója |
| ComputerName_s | A számítógép teljes Tartományneve |
| Ipv4Addresses_s | Listáját a kiszolgáló az IPv4-címek |
| Ipv6Addresses_s | Listáját a kiszolgáló az IPv6-címek |
| DnsNames_s | DNS-nevek tömbje |
| OperatingSystemFamily_s | Windows vagy Linux rendszeren |
| OperatingSystemFullName_s | Az operációs rendszer teljes neve  |
| Bitness_s | Bitszámértékének a gép (32 bites vagy 64 bites)  |
| PhysicalMemory_d | A fizikai memória (MB) |
| Cpus_d | Processzorok száma |
| CpuSpeed_d | A CPU-sebesség MHz-ben|
| VirtualizationState_s | *ismeretlen*, *fizikai*, *virtuális*, *hipervizor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*, és így tovább |
| VirtualMachineNativeMachineId_g | A hipervizor által hozzárendelt virtuális gép azonosítója |
| VirtualMachineName_s | A virtuális gép neve |
| BootTime_t | A rendszerindítás ideje |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL Type records
Típussal rendelkező rekordok *ServiceMapProcess_CL* rendelkezik TCP-kapcsolattal rendelkező folyamatokat a Hardverleltár-adatait a Service Map-ügynökökkel kiszolgálókon. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| Típus | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterületen belül a folyamat egyedi azonosítója |
| ResourceName_s | A gépen, amelyen fut a folyamat egyedi azonosítója|
| MachineResourceName_s | Az erőforrás nevét a gép |
| ExecutableName_s | A folyamat végrehajtható fájl neve |
| StartTime_t | A folyamat készlet kezdő időpont |
| FirstPid_d | Az első PID folyamatkészletben |
| Description_s | A folyamat leírása |
| CompanyName_s | A vállalat neve |
| InternalName_s | A belső név |
| ProductName_s | A termék nevét |
| ProductVersion_s | A termék verziója |
| FileVersion_s | A fájl verziója |
| CommandLine_s | A parancssorból |
| ExecutablePath z | A végrehajtható fájl elérési útja |
| WorkingDirectory_s | A munkakönyvtárban |
| Felhasználónév | A fiók, amely alatt a folyamat végrehajtása |
| UserDomain | A tartományhoz, amelyben a folyamat végrehajtása |

## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gépek listája
ServiceMapComputer_CL |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>A fizikai memória-kapacitás az összes felügyelt számítógép listája.
ServiceMapComputer_CL |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint |} Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Számítógép neve, DNS, IP és az operációs rendszer.
ServiceMapComputer_CL |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint |} a projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>A parancssorban keresse meg az "sql" összes folyamat
ServiceMapProcess_CL |} ahol CommandLine_s contains_cs "sql" |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Keresse meg a gép (legutóbbi rekord) erőforrás szerint
Keresés a (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Keresse meg a gép (legutóbbi rekord) IP-cím alapján
Keresés a (ServiceMapComputer_CL) "10.229.243.232" |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint

### <a name="list-all-known-processes-on-a-specified-machine"></a>Egy megadott számítógép összes ismert folyamat listázása
ServiceMapProcess_CL |} ahol MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" |} Összegzés arg_max(TimeGenerated, *) erőforrás-azonosító szerint

### <a name="list-all-computers-running-sql"></a>Az SQL futtató számítógépek listája
ServiceMapComputer_CL |} ahol a ResourceName_s (((ServiceMapProcess_CL) lévő keresése "\*sql\*" |} különböző MachineResourceName_s)) |} különböző ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Saját adatközpontban curl összes egyedi termék verziója listázása
ServiceMapProcess_CL |} ahol ExecutableName_s == "curl" |} különböző ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Az összes számítógép CentOS rendszerű számítógépcsoport létrehozása
ServiceMapComputer_CL |} ahol OperatingSystemFullName_s contains_cs "CentOS" |} különböző ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>A kimenő kapcsolatok a gépek csoportból összefoglalója
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API
A kiszolgáló, a folyamat és a függőségi adatokat a Szolgáltatástérkép keresztül érhető el az összes a [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok
A Microsoft automatikusan gyűjt használatának és teljesítményének adatokat a Szolgáltatástérkép szolgáltatás használata. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a Szolgáltatástérkép szolgáltatás javítására használja. Adja meg a pontos és hatékony hibaelhárítási képességeket kínál, az adatok például az operációs rendszer és verzió, IP-cím, DNS-nevet és munkaállomás-neve, a szoftver konfigurációjára vonatkozó információkat tartalmaz. A Microsoft nem gyűjti a neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>További lépések
Tudjon meg többet [naplókereséseket](../../azure-monitor/log-query/log-query-overview.md) a Log Analytics által a Service Map összegyűjtött adatok lekéréséhez.


## <a name="troubleshooting"></a>Hibaelhárítás
Tekintse meg a [hibaelhárítás szakaszhoz a konfigurálása a Service Map dokumentum]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Visszajelzés
Van bármilyen visszajelzése szolgáltatást, mert a Service Map vagy ez a dokumentáció?  Látogasson el a [User Voice lap](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol javaslattétel szolgáltatásokra, illetve szavazhat fel meglévő javaslatokat.
