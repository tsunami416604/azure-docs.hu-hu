---
title: "A Service Map megoldás használata az Operations Management Suite |} Microsoft Docs"
description: "Szolgáltatástérkép egy Operations Management Suite megoldás, amely automatikusan észleli a Windows és Linux rendszerek alkalmazás-összetevők, és leképezi a szolgáltatások közötti kommunikáció. Ez a cikk a Service Map telepítése a környezetben, és használja azt a különféle forgatókönyvekhez, amik részletesen."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: 993dff7657a73803ca21677e19b08946fb89bfa2
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="use-the-service-map-solution-in-operations-management-suite"></a>Az Operations Management Suite a Szolgáltatástérkép megoldás használja
A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Szolgáltatástérkép, használatával megtekintheti a kiszolgálók, amelyek Ön szerint egyik módja: összekapcsolt rendszerekhez, hogy a kritikus szolgáltatásokhoz. Szolgáltatástérkép jeleníti meg a kiszolgálók, a folyamatok közötti kapcsolatokat, és portok között bármely TCP-csatlakoztatott architektúra, a konfiguráció nem szükséges másik ügynököt telepíteni.

Ez a cikk ismerteti a Szolgáltatástérkép használatával részleteit. Szolgáltatástérkép és bevezetési ügynökök konfigurálásával kapcsolatos további információkért lásd: [Operations Management Suite megoldás konfigurálása a Service Map](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Eseteinek: Ellenőrizze az informatikai feldolgozza a fürttámogató függőség

### <a name="discovery"></a>Detektálás
Szolgáltatástérkép függőségek közös hivatkozás térképet automatikusan létrehozza a kiszolgálón, a folyamatok és a harmadik féltől származó szolgáltatással. Észleli, és hozzárendeli az összes TCP-függőség, jelzés nélküli kapcsolatok, a távoli külső rendszerek függ, és a hagyományos sötét területet a hálózaton, például az Active Directory függőségeket azonosító. Szolgáltatástérkép deríti fel, amely a felügyeleti rendszer létesíteni, hibás hálózati kapcsolatok gondoskodik a potenciális server hibás konfigurációja, a szolgáltatáskimaradás és a hálózati problémák azonosításához.

### <a name="incident-management"></a>Incidenskezelés
Szolgáltatástérkép segít, hogy bemutatja, hogyan kapcsolódnak a rendszerek, és egymást érintő probléma elkülönítési a munka bizonytalanságát megszüntetéséhez. Mellett azonosítása sikertelen kapcsolatok, ennek segítségével azonosíthatja a helytelenül konfigurált terheléselosztók, kritikus szolgáltatások meglepő vagy túlzott terhelése, és ügyfelek, például a fejlesztői gépek beszélgetne éles rendszerek esetén. a támadó. Integrált munkafolyamatokkal rendelkező Operations Management Suite módosítása nyomon követése, azt is láthatja, hogy a change esemény egy háttér-gépen, vagy szolgáltatás ismerteti az alapvető ok egy incidens.

### <a name="migration-assurance"></a>Áttelepítési megbízhatósági
Szolgáltatástérkép használatával hatékonyan megtervezése programot, egyre gyorsabban jelennek meg, és a segítségével győződjön meg arról, hogy semmi sem hátrahagyott és jelzés nélküli nem kiesések Azure áttelepítések ellenőrzése. Minden egymástól kölcsönösen függnek, áttelepítése együtt, mérje fel a Rendszerkonfiguráció és a kapacitás, és adja meg, hogy a futó rendszer van még kiszolgáló felhasználók, vagy egy jelölt leszerelése áttelepítési helyett a rendszer felderíthetők. A lépés befejezése után, akkor is ellenőrizhesse ügyfél terhelés és identitás ellenőrzése, hogy a vizsgálati rendszerek és az ügyfelek csatlakoznak. Ha az alhálózat tervezési és a tűzfal-meghatározások problémák vannak, a Service Map maps sikertelen kapcsolatok mutasson, a kapcsolatot igénylő rendszerek.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
Ha a használja az Azure Site Recovery, és kell meghatározása a helyreállítási folyamata az alkalmazás-környezethez, a Service Map súgó automatikusan láthatja, hogyan rendszerek támaszkodnak egymástól annak érdekében, hogy a helyreállítási terv nem megbízható. Egy kritikus fontosságú kiszolgáló vagy csoport kiválasztása, és tekintse meg az ügyfeleknek, azonosíthatja, mely előtér-rendszerek helyreállítása után a kiszolgáló visszaállítása, és elérhető. Ezzel szemben kritikus fontosságú kiszolgálók háttér-függőségek megtekintésével azonosíthatja, mely rendszerek helyreállítása előtt a rendszer visszaállítja a fókusz rendszerek.

### <a name="patch-management"></a>A javítások
Szolgáltatástérkép javítja a használatára az Operations Management Suite rendszer frissítések értékelését jelenít meg, amelyek egyéb csoportok és a kiszolgálók a szolgáltatástól függenek, így akkor is értesítést küldhet nekik előre le a rendszer a javítás végrehajtása előtt. Szolgáltatástérkép is javítja a javítás-kezelés az Operations Management Suite azáltal, hogy bemutatja, hogy a szolgáltatások elérhető és megfelelően csatlakoztatott után lett, és újraindul.


## <a name="mapping-overview"></a>Leképezési áttekintése
Szolgáltatástérkép ügynökök gyűjtse össze a TCP-kapcsolódó folyamatok információi a kiszolgálón, amelyen telepítve van és a bejövő és kimenő kapcsolatok az összes folyamat részleteit. A listában, a bal oldali ablaktáblán kiválaszthatja a gépeket vagy csoportokat, amelyeknek Szolgáltatástérkép keresztül a megadott időtartomány függősége megjelenítéséhez. Gép függőségi képezi le egy adott gép helyezi a hangsúlyt, és azok megjelenítése, amelyek közvetlen TCP-ügyfelek vagy kiszolgálók, hogy a gép összes gép.  Gép maps kiszolgálók és a Függőségek megjelenítése.

![Szolgáltatástérkép áttekintése](media/oms-service-map/service-map-overview.png)

Gépek bővíthetők a leképezés a futó megjelenítése a kijelölt időtartományban csoportok és az aktív hálózati kapcsolatokkal folyamatok feldolgozni. Ha egy távoli számítógéphez a Szolgáltatástérkép ügynök ki van bontva, a folyamat részletei láthatók, csak a kommunikációt a fókusz gép folyamatok jelennek meg. Ügynök nélküli előtér-gépek, amelyek kapcsolódnak a fókusz géppé száma fel van tüntetve a folyamatok, amelyekhez csatlakoznak bal oldalán. Ha a fókusz gépen, hogy így egy háttér-számítógépen, amelyen nincs ügynök van kapcsolat, a háttér-kiszolgálófiók szerepel a Port kiszolgálócsoport mellett egyéb kapcsolatokat ugyanazt a portszámot.

Alapértelmezés szerint a Szolgáltatástérkép maps megjelenítése a függőségi adatokat az elmúlt 30 percben. Bal felső idő vezérlők használatával lekérdezheti a maps korábbi idő porttartományok akár egy óráig hogyan függőségek kikeresi az elmúlt (például incidens vagy előtt változott meg valami) megjelenítéséhez. Szolgáltatástérkép tárolja a fizetős munkaterületek 30 napig, és a munkaterületek 7 napban.

## <a name="status-badges-and-border-coloring"></a>Állapot jelvények és szegély színezés
A térkép kiszolgálónként alján lehet a kiszolgáló állapotinformációról igényinek állapot jelvények listáját. A jelvények jelzi, hogy a kiszolgáló, az Operations Management Suite megoldás Integrációk egyikéből néhány információt. Egy jelvény kattintva viszi közvetlenül a jobb oldali állapot részleteit. A jelenleg elérhető állapot jelvények tartalmazza a riasztások, a ügyfélszolgálatához, a módosítások, a biztonsági és a frissítések.

Attól függően, hogy az állapot jelvények súlyossága gép csomópont szegélyek színes piros (kritikus), sárga (figyelmeztetés), vagy kék (tájékoztató). A szín a állapot jelvények bármelyikét legsúlyosabb károkat okozó állapotát jelöli. Szürke szegélyt megadni a csomópont, amelynek nincs Állapotjelzők jelzi.

![Állapot jelvények](media/oms-service-map/status-badges.png)

## <a name="process-groups"></a>Csoportot
Folyamat csoportok egyesítése egy folyamat csoporthoz tartozó közös termék vagy szolgáltatás folyamatokat.  Egy számítógép-csomópont kibontásakor önálló folyamatok csoportot együtt jelenik meg.  Ha a bejövő és kimenő kapcsolatokat folyamat csoporton belül a folyamatok majd nem sikerült a kapcsolat látható módon nem sikerült a teljes folyamat csoportot.

## <a name="machine-groups"></a>Számítógép-csoportok
Gép csoportok lehetővé teszik a kiszolgálók, nem csak egy hívjuk fel egy leképezést egy többrétegű alkalmazást vagy a kiszolgáló fürt összes tagja egy csoportja körül maps láthatja.

Felhasználók válassza ki, melyik kiszolgálókat együtt egy csoporthoz tartozik, és válassza ki a csoport nevét.  Ezután kiválaszthatja a csoportban található összes folyamatok és kapcsolatok megtekintéséhez, vagy csak a folyamatok és a csoport többi tagja közvetlenül kapcsolódó kapcsolatok megtekintéséhez.

![Számítógép-csoport](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Számítógép-csoport létrehozása
Hozzon létre egy csoportot, válassza ki a gép vagy gépek felsorolja a gépeket, és kattintson a kívánt **Hozzáadás csoporthoz**.

![Csoport létrehozása](media/oms-service-map/machine-groups-create.png)

Itt választhat **hozzon létre új** és nevezze el a csoportot.

![Csoport neve](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>Gép csoportok jelenleg csak 10 kiszolgálókra, de a korlát növeléséhez hamarosan tervezzük.

### <a name="viewing-a-group"></a>Egy csoport megtekintése
A létrehozását követően néhány olyan csoportot, a csoportok lapon kiválasztásával megtekintheti őket.

![Csoportok lap](media/oms-service-map/machine-groups-tab.png)

Ezután válassza ki a csoport nevét a gép csoport a térkép megtekintéséhez.
![Gép](media/oms-service-map/machine-group.png) a gépeket, a csoporthoz tartozó eljárásokat a fehér a térképen.

A csoport kibontásával felsorolja a gépeket, a gép csoportot alkotó.

![Gép gépek](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Folyamatok szűrése
A nézet összes folyamatok és kapcsolatok megjelenítő a csoport, és csak a gazdarendszerhez közvetlenül kapcsolódik a számítógép csoport között válthat.  Az alapértelmezett nézet az összes folyamat megjelenítéséhez.  A térkép felett szűrő ikonra kattintva módosíthatja a nézetet.

![Szűrő csoport](media/oms-service-map/machine-groups-filter.png)

Ha **minden folyamat** van jelölve, a térkép tartalmazza minden folyamat és kapcsolatok az egyes gépek csoportban.

![Minden számítógép csoport dolgozza fel.](media/oms-service-map/machine-groups-all.png)

Ha csak a megjelenítendő nézetet **csoport csatlakoztatva folyamatok**, a térkép fog leszűkítheti csak folyamatok és-kapcsolatok közvetlenül csatlakoztatott más számítógépekre, a csoport létrehozása egyszerűsített nézete.

![Számítógép csoport szűrve folyamatok](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Gépek felvétele egy csoportba
Egy meglévő csoporthoz hozzáadni, jelölőnégyzeteket mellett a gépeket, majd kattintson **Hozzáadás csoporthoz**.  Válassza ki a csoport hozzá szeretné adni a gépeket.
 
### <a name="removing-machines-from-a-group"></a>Gép eltávolítása egy csoportból
A csoportok listájában bontsa ki a csoport nevét a gép csoport lévő számítógépek listáját.  Ezután kattintson a három pont menü mellett látható a gép eltávolítása, és válassza ki a **eltávolítása**.

![Gép eltávolítása a csoportból](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Eltávolítása, vagy egy csoport átnevezése
Kattintson a csoport nevét, a csoport lista melletti három pont menü.

![Számítógép csoport menü](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Szerepkör ikon
Bizonyos folyamatok szolgálnak ki adott szerepkörök gépek: webalkalmazás-kiszolgálók, alkalmazás-kiszolgálókat, adatbázis, és így tovább. Szolgáltatástérkép jelzi a folyamat, illetve a számítógép mezők szerepkör ikonok azonosításához egy pillanat alatt a szerepkör egy folyamat vagy a kiszolgálón betöltött szerepe.

| Szerepkör ikon | Leírás |
|:--|:--|
| ![Webkiszolgáló](media/oms-service-map/role-web-server.png) | Webkiszolgáló |
| ![Alkalmazáskiszolgáló](media/oms-service-map/role-application-server.png) | Alkalmazáskiszolgáló |
| ![Adatbázis-kiszolgáló](media/oms-service-map/role-database.png) | Adatbázis-kiszolgáló |
| ![LDAP-kiszolgáló](media/oms-service-map/role-ldap.png) | LDAP-kiszolgáló |
| ![SMB-kiszolgálón](media/oms-service-map/role-smb.png) | SMB-kiszolgálón |

![Szerepkör ikon](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Nem sikerült kapcsolatok
Nem sikerült kapcsolatok együtt jelennek meg a Service Map maps folyamatok és a számítógépek, piros szaggatott vonal jelzi, hogy egy folyamat vagy port eléréséhez ügyfélrendszer meghiúsul. Hibás kapcsolatok minden rendszer a Service Map telepített ügynökkel küld jelentést, hogy a rendszer esetén a sikertelen kapcsolódás kísérlet azt. Szolgáltatástérkép ezt a folyamatot nem sikerült kapcsolatot létesíteni a TCP-szoftvercsatornák megfigyelésével méri. Ez a hiba eredhet tűzfal, az ügyfél vagy kiszolgáló vagy a távoli szolgáltatás nem volt elérhető helytelen beállítása.

![Nem sikerült kapcsolatok](media/oms-service-map/failed-connections.png)

Hibás kapcsolatok elősegítheti a hibaelhárítást, áttelepítési érvényesítési, biztonsági elemzés és a teljes architectural understanding ismertetése. Nem sikerült kapcsolatok néha ártalmatlan, de gyakran pontok közvetlenül a problémát, például egy feladatátvételi környezetet hirtelen kiderül, hogy nem érhető el, vagy két alkalmazásrétegek nem képes kommunikálni a felhőben az áttelepítés után.

## <a name="client-groups"></a>Az ügyfélcsoportok
Az ügyfélcsoportok a térképen jelölőnégyzetéből, amelyek megfelelnek az ügyfél gépek, amelyeken nincs függőségi ügynökök. Egyetlen ügyfél csoport jelöli az ügyfelek egy adott folyamat vagy a számítógép.

![Az ügyfélcsoportok](media/oms-service-map/client-groups.png)

Egy ügyfél csoportban lévő kiszolgálók IP-címeit, jelölje ki a csoport. A csoport tartalmát jelennek meg a **ügyfél tulajdonságai** ablaktáblán.

![Ügyfél csoport tulajdonságai](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Kiszolgálóport csoportok
Kiszolgálóport csoportok jelölőnégyzetéből, amelyek megfelelnek a kiszolgálókon, amelyeken nincs függőségi ügynökök server portok. A lista tartalmazza az portot és a kapcsolatokat ehhez a porthoz kiszolgálók számának számát. Bontsa ki a jelölőnégyzetet, hogy az egyes kiszolgálók és a kapcsolatokat. Ha a mezőben csak egy kiszolgáló, a neve vagy IP-cím szerepel.

![Kiszolgálóport csoportok](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Helyi menü
A három ponttal (…) a lap tetején kattintson jobb bármely kiszolgáló jeleníti meg, hogy a kiszolgáló a helyi menü.

![Nem sikerült kapcsolatok](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Betöltési server térkép
Kattintson a **terhelés Server térkép** új leképezés a kijelölt kiszolgálón, az új fókusz gép viszi.

### <a name="show-self-links"></a>Megjeleníti az önhivatkozások
Kattintson a **megjelenítése Self-Links** újrarajzolások számában megadva a kiszolgáló csomópontjára, így azokat az önhivatkozások, amelyek kezdődnek és zárulnak folyamatok belül a kiszolgáló TCP-kapcsolatok. Ha erre az önhivatkozások jelennek meg, a menü parancs változások **elrejtése Self-Links**, így kikapcsolhatja azokat.

## <a name="computer-summary"></a>A számítógép összefoglaló
A **gép összegzés** ablaktáblán egy kiszolgáló operációs rendszer, függőség számát és az Operations Management Suite megoldásait adatait áttekintését tartalmazza. Ezen adatok közé tartoznak a teljesítménymutatók, szolgáltatásjegyek ügyfélszolgálati, változások követését, biztonsági és frissítéseket.

![Gép összefoglalás ablaktábla](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Számítógép és a folyamat tulajdonságai
Navigálás a Szolgáltatástérkép térkép, kiválaszthatja a gépek és a folyamatok ahhoz, hogy azok tulajdonságaival kapcsolatos további környezetben. Gépek DNS-neve, IPv4 címek, CPU és memória kapacitás, virtuális gép típusa, operációs rendszer és verzió, a időt és az Operations Management Suite és a Service Map ügynökök azonosítók újraindítás utolsó kapcsolatos adatok megadása.

![Számítógép-tulajdonságok panelen](media/oms-service-map/machine-properties.png)

Folyamat részletes adatainak összegyűjtése futó folyamatok, beleértve a folyamat neve, folyamat leírása, felhasználónév és (Windowson) tartomány, vállalat neve, termék neve, termék verziója, munkakönyvtár, parancssori és folyamat metaadatainak operációs rendszer kezdési időpontja.

![Folyamat tulajdonságok panelen](media/oms-service-map/process-properties.png)

A **folyamat összegzése** ablaktábla a folyamat kapcsolatban, beleértve annak kötött portok, a bejövő és kimenő kapcsolatok további információkkal szolgál, és a kapcsolatok nem sikerült.

![Folyamat összefoglalás ablaktábla](media/oms-service-map/process-summary.png)

## <a name="operations-management-suite-alerts-integration"></a>Az Operations Management Suite riasztások integráció
Szolgáltatástérkép integrálódik az Operations Management Suite riasztások a kiválasztott időtartomány égetett riasztások a kiválasztott kiszolgálóhoz tartozó megjeleníthető. A kiszolgáló megjelenít egy ikont, ha nincsenek az aktuális riasztásokat, és a **gép riasztások** ablaktábla listázza a riasztásokat.

![Gép riasztások panelen](media/oms-service-map/machine-alerts.png)

Ahhoz, hogy a Service Map vonatkozó értesítések megjelenítése, hozzon létre egy szabályt, amely egy adott számítógép következik be. Megfelelő riasztások létrehozásához:
- Számítógép csoporthoz záradékot tartalmazni (például **számítógép időköze 1 perces**).
- Válassza ki riasztást küld, metrika mérési alapján.

![Riasztások konfigurálása](media/oms-service-map/alert-configuration.png)


## <a name="operations-management-suite-log-events-integration"></a>Operations Management Suite naplózási események integráció
Szolgáltatástérkép integrálható a naplófájl-keresési megjelenítése a kiválasztott kiszolgálóhoz tartozó összes elérhető napló események száma a kijelölt időtartományban. Esemény száma naplófájl-keresési ugorhat, és az egyéni naplózási eseményeket a listában bármely sorára kattintson.

![Gép naplóeseményeket ablaktábla](media/oms-service-map/log-events.png)

## <a name="operations-management-suite-service-desk-integration"></a>Az Operations Management Suite szolgáltatás ügyfélszolgálati integráció
Szolgáltatástérkép integráció az informatikai szolgáltatás Management-összekötő akkor automatikus, ha a két megoldás engedélyezve és konfigurálva az Operations Management Suite-munkaterülettel. Az integráció a Szolgáltatástérkép lett címkézve "Ügyfélszolgálatához." További információkért lásd: [ITSM munkaelemek IT Service Management-összekötő segítségével központilag kezelheti](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

A **gép ügyfélszolgálatához** ablaktábla listázza a kijelölt időtartományban a kiválasztott kiszolgálóhoz tartozó összes informatikai szolgáltatások kezelésében eseményt. A kiszolgáló megjelenít egy ikont, ha az aktuális elem és a gép ügyfélszolgálatához ablaktábla listázza azokat.

![Gép ügyfélszolgálatához ablaktábla](media/oms-service-map/service-desk.png)

A cikk a csatlakoztatott ITSM megoldásban megnyitásához kattintson **nézet munkaelem**.

A naplófájl-keresési elem részleteinek megtekintéséhez kattintson **jelenjen meg a keresési napló**.


## <a name="operations-management-suite-change-tracking-integration"></a>Az Operations Management Suite változások követése integráció
Szolgáltatástérkép integráció a változások követése akkor automatikus, ha a két megoldás engedélyezve és konfigurálva az Operations Management Suite-munkaterülettel.

A **gép változások követése** ablaktábla listázza az összes módosításokat, a legutóbbi első, valamint egy hivatkozást a naplóban keresse meg a további részletek lebontva.

![Gép változások követése ablaktábla](media/oms-service-map/change-tracking.png)

Az alábbi képen, amelyeket konfigurációváltozás esemény részletes nézet kijelölése után **megjelenítése a Naplóelemzési**.

![Konfigurációváltozás esemény](media/oms-service-map/configuration-change-event.png)


## <a name="operations-management-suite-performance-integration"></a>Az Operations Management Suite teljesítmény-integráció
A **gépek teljesítménye** ablaktábla megjeleníti a kiválasztott kiszolgálóhoz tartozó szabványos teljesítménymutatók. A mérőszámok közé tartozik a CPU kihasználtsága, memória-felhasználás, küldött és fogadott hálózati bájtok és a legfelső szintű folyamatok listáját által küldött és fogadott hálózati bájtok.

![Gép teljesítmény ablaktábla](media/oms-service-map/machine-performance.png)

Teljesítményadatok megtekintéséhez esetleg [engedélyezése a megfelelő Naplóelemzési teljesítményszámlálók](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  A számlálók érdemes engedélyezése:

Windows:
- Processor(*)\\kihasználtsága (%)
- Memória\\% előjegyzett memória
- A hálózati Adapter(*)\\küldött bájtok/s
- A hálózati Adapter(*)\\fogadott bájtok/s

Linux:
- Processor(*)\\kihasználtsága (%)
- Memory(*)\\% használt memória
- A hálózati Adapter(*)\\küldött bájtok/s
- A hálózati Adapter(*)\\fogadott bájtok/s

Ahhoz, hogy a hálózati teljesítményadatokat, kell is engedélyezte az Operations Management Suite átviteli adatok 2.0 megoldás.
 
## <a name="operations-management-suite-security-integration"></a>Az Operations Management Suite biztonsági integráció
Biztonsági és a naplózási szolgáltatás térkép integrációját akkor automatikus, ha a két megoldás engedélyezve és konfigurálva az Operations Management Suite-munkaterülettel.

A **gép biztonsági** ablaktábla megjeleníti azokat az adatokat az Operations Management Suite biztonsági és hitelesítési megoldás a kijelölt kiszolgálóra vonatkozóan. A panelen a kiszolgáló függőben lévő biztonsági problémák összegzését a kijelölt időtartományban sorolja fel. Kattintson bármelyik a biztonsági problémák csukja le a velük kapcsolatos részletek napló keresése.

![Számítógép biztonsági ablaktábla](media/oms-service-map/machine-security.png)


## <a name="operations-management-suite-updates-integration"></a>Az Operations Management Suite frissítések integráció
Szolgáltatástérkép integráció a frissítéskezelés akkor automatikus, ha a két megoldás engedélyezve és konfigurálva az Operations Management Suite-munkaterülettel.

A **Machine frissítések** ablaktáblán jelennek meg az Operations Management Suite frissítés felügyeleti megoldás a kiválasztott kiszolgálóhoz tartozó adatokat. A panelen a kiszolgáló a hiányzó frissítésekkel összegzését a kijelölt időtartományban sorolja fel.

![Gép változások követése ablaktábla](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Szolgáltatástérkép számítógép és a folyamat leltáradatok érhető el [keresési](../log-analytics/log-analytics-log-searches.md) a Naplóelemzési. Ezek az adatok alkalmazhat, amely tartalmazza az áttelepítés megtervezése, kapacitáselemzési, felderítési és igény szerinti teljesítmény hibaelhárítási forgatókönyveket.

Külön rekordot minden egyedi számítógép és a folyamat mellett a rekordokat, amelyek egy folyamat vagy a számítógép indításakor vagy előre telepített szolgáltatás leképezés óránként jön létre. Ezeket a rekordokat az alábbi táblázatban a jellemzőkkel rendelkezik. A mezők és értékek a ServiceMapComputer_CL események leképezés a gép erőforrás ServiceMap Azure Resource Manager API-ja mezőket. A mezők és értékek a ServiceMapProcess_CL események leképezés a mezők a folyamat erőforrás ServiceMap Azure Resource Manager API-ban. A ResourceName_s mező megegyezik a megfelelő Resource Manager szerinti erőforrás neve mezőjében. 

>[!NOTE]
>Szolgáltatástérkép szolgáltatások növekedésével a mezők kitöltése, változhat.

Nincsenek egyedi folyamatok és -számítógépek azonosításához használható belsőleg generált tulajdonságokat:

- Számítógép: Használata ResourceId vagy ResourceName_s a számítógépet az Operations Management Suite-munkaterülethez belül egyedi azonosításához.
- Folyamat: Használata ResourceId egy folyamatot, az Operations Management Suite-munkaterülethez belül egyedi azonosításához. ResourceName_s egyedi, a gép, amelyen a folyamat fut (MachineResourceName_s) keretén belül 

Több rekord is tartozhat egy adott folyamat és a megadott időtartomány számítógépe, mert a lekérdezések visszaadhatják egynél több rekordot ugyanazon a számítógépen vagy folyamat. Csak a legutóbbi rekord egészítse "|} a deduplikáció ResourceId"a lekérdezéshez.

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL rekordok
Típusú rekordok *ServiceMapComputer_CL* van a Service Map ügynökkel kiszolgálók Hardverleltár-adatait. Ezeket a rekordokat az alábbi táblázatban a jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|:--|:--|
| Típus | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterület egyik számítógépére egyedi azonosítója |
| ResourceName_s | A munkaterület egyik számítógépére egyedi azonosítója |
| ComputerName_s | A számítógép teljes Tartományneve |
| Ipv4Addresses_s | Címek a kiszolgáló IPv4 listája |
| Ipv6Addresses_s | Listáját a kiszolgáló IPv6-címek |
| DnsNames_s | DNS-nevek tömbje |
| OperatingSystemFamily_s | A Windows vagy Linux |
| OperatingSystemFullName_s | Az operációs rendszer teljes neve  |
| Bitness_s | Bitszámértékének meghatározása a gép (32 bites vagy 64 bites)  |
| PhysicalMemory_d | A fizikai memória MB-ban |
| Cpus_d | Processzorok száma |
| CpuSpeed_d | A Processzor sebessége MHz-ben|
| VirtualizationState_s | *ismeretlen*, *fizikai*, *virtuális*, *hipervizor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*, és így tovább |
| VirtualMachineNativeMachineId_g | A hipervizor által hozzárendelt, a virtuális gép azonosítója |
| VirtualMachineName_s | A virtuális gép neve |
| BootTime_t | A rendszerindítás |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL típusú rekordok
Típusú rekordok *ServiceMapProcess_CL* rendelkezik TCP kapcsolódó eljárások a Hardverleltár-adatait a Szolgáltatástérkép ügynökök kiszolgálókon. Ezeket a rekordokat az alábbi táblázatban a jellemzőkkel rendelkezik:

| Tulajdonság | Leírás |
|:--|:--|
| Típus | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | A munkaterület belül a folyamat egyedi azonosítója |
| ResourceName_s | A gépen, amelyen fut a folyamat egyedi azonosítója|
| MachineResourceName_s | A gép erőforrás neve |
| ExecutableName_s | A folyamat végrehajtható fájljának neve |
| StartTime_t | A folyamat készlet kezdete |
| FirstPid_d | Az első azonosítója (PID) folyamatkészletben |
| Description_s | A folyamat leírása |
| CompanyName_s | A vállalat neve |
| InternalName_s | A belső neve |
| ProductName_s | A termék nevét |
| ProductVersion_s | A verzió |
| FileVersion_s | A fájl verziója |
| CommandLine_s | A parancssorból |
| ExecutablePath z | A végrehajtható fájl elérési útja |
| WorkingDirectory_s | A munkakönyvtár |
| Felhasználónév | A fiók, amely alatt a folyamat végrehajtása történik |
| UserDomain | A tartományhoz, amely alatt a folyamat végrehajtása történik |


## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gép felsorolása
ServiceMapComputer_CL |} összefoglalója arg_max(TimeGenerated, *) ResourceId által

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Minden felügyelt számítógéphez fizikai memória kapacitásának listában.
ServiceMapComputer_CL |} összefoglalója arg_max(TimeGenerated, *) által ResourceId |} Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Számítógép neve, a DNS, az IP és a az operációs rendszer.
ServiceMapComputer_CL |} összefoglalója arg_max(TimeGenerated, *) által ResourceId |} a projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>A parancssorban "sql" az összes folyamat keresése
ServiceMapProcess_CL |} ahol "sql" CommandLine_s contains_cs |} összefoglalója arg_max(TimeGenerated, *) ResourceId által

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Található (legutóbbi rekord) gép neve szerint
Keresés helye (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" |} összefoglalója arg_max(TimeGenerated, *) ResourceId által

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Található (legutóbbi rekord) gép IP-cím szerint
Keresés helye (ServiceMapComputer_CL) "10.229.243.232" |} összefoglalója arg_max(TimeGenerated, *) ResourceId által

### <a name="list-all-known-processes-on-a-specified-machine"></a>A megadott számítógép összes ismert folyamat felsorolása
ServiceMapProcess_CL |} Ha MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" |} összefoglalója arg_max(TimeGenerated, *) ResourceId által

### <a name="list-all-computers-running-sql"></a>Megjeleníti az SQL futtató összes számítógépet
ServiceMapComputer_CL |} Ha a ResourceName_s (((ServiceMapProcess_CL) a keresési "\*sql\*" |} különböző MachineResourceName_s)) |} különböző ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A saját adatközpont curl összes egyedi termék verziójának felsorolása
ServiceMapProcess_CL |} Ha ExecutableName_s == "curl" |} különböző ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Hozzon létre egy számítógépcsoportot CentOS rendszerrel működő számítógépek
ServiceMapComputer_CL |} ahol "CentOS" OperatingSystemFullName_s contains_cs |} különböző ComputerName_s


## <a name="rest-api"></a>REST API
A kiszolgáló, a folyamat és a függőségi adatokat a Service Map keresztül érhető el az összes a [szolgáltatás térkép REST API](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>diagnosztikai és használati adatok
A Microsoft automatikusan használati és teljesítményadatokat gyűjt a Szolgáltatástérkép szolgáltatás használata. A Microsoft ezeket az adatokat ellátására és fejlesztésére minőségének, biztonsági és integritását, a Service Map szolgáltatást használja. Ahhoz, hogy pontos és hatékony hibaelhárítási képességei, szerepel a szoftver, például az operációs rendszer és a verziója, a IP-cím, a DNS-nevét és a munkaállomás neve konfigurációs adatait. A Microsoft nem gyűjti, neveket, címeket és egyéb kapcsolattartási adatait.

Adatok gyűjtésével és használatával kapcsolatos további információkért lásd: a [Microsoft Online Services adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>További lépések
További információ [keresések jelentkezzen](../log-analytics/log-analytics-log-searches.md) a Log Analyticshez Szolgáltatástérkép által gyűjtött adatok lekéréséhez.


## <a name="troubleshooting"></a>Hibaelhárítás
Tekintse meg a [hibaelhárítási szakaszában a Szolgáltatástérkép konfigurálása dokumentum](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Visszajelzés
Van olyan visszajelzést az USA Szolgáltatástérkép és a jelen dokumentáció?  Látogasson el a [felhasználói véleményekkel foglalkozó weblapunkra](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol felajánlja a szolgáltatások és arra való szavazás meglévő javaslatok be.
