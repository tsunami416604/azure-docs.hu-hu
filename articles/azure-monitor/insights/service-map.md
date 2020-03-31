---
title: Szolgáltatástérkép-megoldás használata az Azure-ban | Microsoft dokumentumok
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk a Service Map környezetben való üzembe helyezésének és különböző forgatókönyvekben való használatának részleteit ismerteti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f2f3e84462307f43ffe432fe878476d979f489f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480912"
---
# <a name="using-service-map-solution-in-azure"></a>A Service Map megoldás használata az Azure-ban

A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. A Service Map használatával a kiszolgálókat úgy tekintheti meg, ahogyan azt el szoktuk képzelni: egymással összekapcsolt rendszereket, amelyek kritikus fontosságú szolgáltatásokat tesznek elérhetővé. A Service Map megmutatja a kiszolgálók közötti kapcsolatokat, a folyamatokat, a bejövő és a kimenő kapcsolatok késéseit, valamint minden TCP-vel csatlakoztatott architektúra portjait, és ehhez konfigurációra sincs szükség, csupán telepíteni kell az ügynököt.

Ez a cikk a bevezetés és a Szolgáltatástérkép használatának részleteit ismerteti. A megoldás előfeltételeinek konfigurálásáról [az Azure-figyelő virtuális gépekhez – áttekintés című témakörben olvashat.](vminsights-enable-overview.md#prerequisites) Összefoglalva, a következőkre van szükség:

* A Log Analytics munkaterület, hogy ezt a megoldást.

* A Windows-számítógépen vagy Linux-kiszolgálón telepített Log Analytics-ügynök úgy van beállítva, hogy ugyanazt a munkaterületet jelentse, amelyhez a megoldást engedélyezte.

* A Windows rendszerű vagy Linux-kiszolgálón telepített függőségi ügynök.

>[!NOTE]
>Ha már telepítette a Szolgáltatástérképet, most már megtekintheti a leképezéseket az Azure Monitor virtuális gépekhez, amely további funkciókat tartalmaz a virtuális gépek állapotának és teljesítményének figyeléséhez. További információ: [Azure Monitor for VMs overview](../../azure-monitor/insights/vminsights-overview.md). A Szolgáltatástérkép-megoldás és az Azure Monitor szolgáltatás leképezési szolgáltatása közötti különbségekről az alábbi gyakori kérdések című [témakörben](../faq.md#azure-monitor-for-vms)olvashat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="enable-service-map"></a>Szolgáltatásleképezés engedélyezése

1. Engedélyezze a Szolgáltatástérkép-megoldást az [Azure piactérről,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) vagy a [Megoldások galériából származó figyelési megoldások hozzáadása](solutions.md)című részben ismertetett folyamat használatával.
1. [Telepítse a függőségi ügynököt a Windows rendszerre,](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) vagy [telepítse a függőségi ügynököt Linuxra](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) minden olyan számítógépen, amelyen adatokat szeretne beszerezni. A függőségi ügynök képesek a közvetlen szomszédaikkal való kapcsolatok monitorozására, így lehetséges, hogy nem kell minden egyes számítógépre ügynököt telepíteni.

A Service Map az Azure Portalon a Log Analytics-munkaterületről érhető el, és válassza ki a **megoldások** lehetőséget a bal oldali ablaktáblából.<br><br> ![Válassza a Megoldások](./media/service-map/select-solution-from-workspace.png)lehetőséget a munkaterületen.<br> A megoldások listájában válassza a **ServiceMap (workspaceName)** elemet, és a Szolgáltatástérkép-megoldás áttekintése lapon kattintson a Szolgáltatástérkép összegző csempéjére.<br><br> ![A Szolgáltatástérkép](./media/service-map/service-map-summary-tile.png)összefoglaló csempéje .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Használati esetek: Az informatikai folyamatok függőségének tudatosítása

### <a name="discovery"></a>Felderítés

A Szolgáltatástérkép automatikusan létrehoz egy közös referenciatérképet a kiszolgálók, folyamatok és külső szolgáltatások függőségeiről. Felderíti és leképezi az összes TCP-függőséget, azonosítja a meglepetésszerű kapcsolatokat, a távoli, külső rendszereket, amelyektől függ, valamint a hálózat hagyományos sötét területeitől, például az Active Directorytól való függőségeket. A Szolgáltatástérkép felderíti a felügyelt rendszerek által létesített sikertelen hálózati kapcsolatokat, így azonosíthatja a kiszolgáló esetleges helytelen konfigurációját, a szolgáltatáskimaradást és a hálózati problémákat.

### <a name="incident-management"></a>Incidenskezelés

A Szolgáltatástérkép segít kiküszöbölni a problémaelkülönítés találgatásait azáltal, hogy megmutatja, hogyan kapcsolódnak egymáshoz a rendszerek, és hogyan befolyásolják egymást. A sikertelen kapcsolatok azonosítása mellett segít azonosítani a helytelenül konfigurált terheléselosztókat, a kritikus szolgáltatások meglepő vagy túlzott terhelését, valamint az engedélyezetlen ügyfeleket, például az éles rendszerekkel foglalkozó fejlesztői gépeket. Az integrált munkafolyamatok változáskövetéslel való használatával azt is megtekintheti, hogy egy háttér-gépen vagy szolgáltatáson lévő módosítási esemény ismerteti-e az incidens kiváltó okait.

### <a name="migration-assurance"></a>Migrációbiztosítása

A Service Map használatával hatékonyan tervezheti, felgyorsíthatja és érvényesítheti az Azure-migrálásokat, így biztosíthatja, hogy semmi ne maradson hátra, és ne történjen váratlan kimaradás. Felderítheti az összes egymástól függő rendszert, amelyeknek együtt kell áttérniük, felkell mérniük a rendszer konfigurációját és kapacitását, és meghatározhatja, hogy egy futó rendszer továbbra is szolgálja-e a felhasználókat, vagy az áttelepítés helyett leszerelésre jelölt. Az áthelyezés befejezése után ellenőrizheti az ügyfelek terhelését és identitását, és ellenőrizheti, hogy a tesztrendszerek és az ügyfelek csatlakoznak-e. Ha az alhálózati tervezés és a tűzfaldefiníciók problémákat, sikertelen kapcsolatok szolgáltatásleképezési pont, hogy a rendszerek, amelyek kapcsolatot igénylő.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

Ha az Azure Site Recovery szolgáltatást használja, és segítségre van szüksége az alkalmazáskörnyezet helyreállítási sorrendjének meghatározásához, a Service Map automatikusan megmutatja, hogy a rendszerek hogyan támaszkodnak egymásra a helyreállítási terv megbízhatóságának biztosítása érdekében. A kritikus kiszolgáló vagy csoport kiválasztásával és az ügyfelek megtekintésével azonosíthatja, hogy mely előtér-rendszereket kell helyreállítani a kiszolgáló visszaállítása és rendelkezésre állása után. Ezzel szemben a kritikus kiszolgálók háttérfüggőségeinek vizsgálatával azonosíthatja, hogy mely rendszereket kell helyreállítani a fókuszrendszerek visszaállítása előtt.

### <a name="patch-management"></a>Javítások kezelése

A Szolgáltatástérkép javítja a rendszerfrissítési felmérés használatát azáltal, hogy megmutatja, hogy mely csapatok és kiszolgálók függenek a szolgáltatástól, így előre értesítheti őket, mielőtt levenné a rendszereket a javításhoz. A Szolgáltatástérkép javítja a javítás kezelését is, és megmutatja, hogy a szolgáltatások elérhetők-e és megfelelően csatlakoztatva vannak-e a javítás és az újraindítás után.

## <a name="mapping-overview"></a>Leképezés – áttekintés

A Szolgáltatástérkép-ügynökök információkat gyűjtenek az összes TCP-hez kapcsolódó folyamatról azon a kiszolgálón, ahol telepítve vannak, valamint az egyes folyamatok bejövő és kimenő kapcsolatainak részleteit.

A bal oldali ablaktábla listájából kiválaszthatja azokat a gépeket vagy csoportokat, amelyek service map-ügynökökkel rendelkeznek a függőségek egy adott időtartományon belüli megjelenítéséhez. A gépfüggőségi leképezések egy adott gépre összpontosítanak, és az összes olyan gépet jelenítik meg, amelyek a gép közvetlen TCP-ügyfelei vagy kiszolgálói.  A gépcsoport-leképezések kiszolgálókészleteket és azok függőségeit jelenítik meg.

![Szolgáltatástérkép – áttekintés](media/service-map/service-map-overview.png)

A gépek kibonthatók a térképen, hogy a futó folyamatcsoportokat és folyamatokat aktív hálózati kapcsolatokkal jelenítsék meg a kiválasztott időtartományban. Ha egy távoli gép egy Service Map ügynök ki van bontva a folyamat részleteinek megjelenítéséhez, csak azok a folyamatok jelennek meg, amelyek kommunikálnak a fókuszgép. A fókuszgéphez csatlakozó ügynök nélküli előtér-gépek száma a folyamatok bal oldalán látható, amelyekhez csatlakoznak. Ha a fókuszgép olyan háttérgéphez létesít kapcsolatot, amelynem rendelkezik ügynökkel, a háttérkiszolgáló egy kiszolgálói portcsoport része, valamint az ugyanazzal a portszámmal való egyéb kapcsolatokkal együtt.

Alapértelmezés szerint a szolgáltatástérkép-leképezések az utolsó 30 perc függőségi adatokat jelenítik meg. A bal felső sarokban lévő idővezérlők használatával lekérdezheti a legfeljebb egy órás előzményidőtartományok leképezéseit, hogy megmutassa, hogyan néztek ki a függőségek a múltban (például egy incidens során vagy a módosítás előtt). A Szolgáltatástérkép adatait 30 napig a fizetős munkaterületeken, 7 napig pedig az ingyenes munkaterületeken tároljuk.

## <a name="status-badges-and-border-coloring"></a>Állapotjelvények és szegélyek színezése

A térkép minden kiszolgálójának alján lehet egy lista az állapotjelvényekről, amelyek a kiszolgáló állapotadatait közvetítik. A jelvények azt jelzik, hogy a megoldás-integrációk egyikéből a kiszolgálószámára van néhány releváns információ. Ha egy jelvényre kattint, közvetlenül a jobb oldali ablaktáblában található állapot részleteire kattinthat. A jelenleg elérhető állapotjelvények közé tartoznak a riasztások, az ügyfélszolgálat, a módosítások, a biztonság és a frissítések.

Az állapotjelzők súlyosságától függően a gépcsomópont szegélyei lehetnek piros (kritikus), sárga (figyelmeztetés) vagy kék (tájékoztató) színűek. A szín az állapotjelvények közül a legsúlyosabb állapotot jelöli. A szürke szegély olyan csomópontot jelöl, amelynek nincsenek állapotjelzői.

![Állapotjelvények](media/service-map/status-badges.png)

## <a name="process-groups"></a>Folyamatcsoportok

A folyamatcsoportok egy közös termékhez vagy szolgáltatáshoz társított folyamatokat egy folyamatcsoportba egyesítik.  A számítógép-csomópont kibontásakor önálló folyamatokat jelenít meg a folyamatcsoportokkal együtt.  Ha egy folyamatcsoporton belül egy folyamathoz bejövő és kimenő kapcsolatok sikertelenek, akkor a kapcsolat a teljes folyamatcsoport számára sikertelenként jelenik meg.

## <a name="machine-groups"></a>Számítógépcsoportok

A gépcsoportok lehetővé teszik, hogy a térképeket kiszolgálók köré csoportosítva láthassa, ne csak egy, így egy többrétegű alkalmazás vagy kiszolgálófürt összes tagja egy térképen látható.

A felhasználók kiválasztják, hogy mely kiszolgálók tartoznak egy csoporthoz, és kiválasztják a csoport nevét.  Ezután kiválaszthatja, hogy a csoportot az összes folyamatával és kapcsolatával együtt megtekinti, vagy csak azokkal a folyamatokkal és kapcsolatokkal tekinti meg, amelyek közvetlenül kapcsolódnak a csoport többi tagjához.

![Gépcsoport](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Gépcsoport létrehozása

Csoport létrehozásához jelölje ki a gépeket vagy gépeket a Gépek listában, és kattintson a **Hozzáadás a csoportba gombra.**

![Csoport létrehozása](media/service-map/machine-groups-create.png)

Itt **választhatja az Új létrehozása lehetőséget,** és nevet adhat a csoportnak.

![Névcsoport](media/service-map/machine-groups-name.png)

>[!NOTE]
>A gépcsoportok legfeljebb 10 kiszolgálóra korlátozódnak.

### <a name="viewing-a-group"></a>Csoport megtekintése

Miután létrehozott néhány csoportot, a Csoportok lapon tekintheti meg őket.

![Csoportok lap](media/service-map/machine-groups-tab.png)

Ezután válassza ki a csoport nevét az adott számítógépcsoport térképének megtekintéséhez.
![Gépcsoport:](media/service-map/machine-group.png) A csoporthoz tartozó gépek a térképen fehér színben vannak eltminkésszel.

A csoport kibontásával megjelennek a gépcsoportot összeget felvevő gépek.

![Gépcsoport gépek](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Szűrés folyamatok szerint

A térképnézet et a csoport összes folyamatának és kapcsolatának megjelenítése és csak a gépcsoporthoz közvetlenül kapcsolódó kapcsolatok között válthat.  Az alapértelmezett nézet az összes folyamat megjelenítése.  A nézetet a térkép feletti szűrőikonra kattintva módosíthatja.

![Szűrőcsoport](media/service-map/machine-groups-filter.png)

Ha **minden folyamat** van kiválasztva, a térkép tartalmazza az összes folyamatot és kapcsolatot a csoport minden gépén.

![Gépcsoport minden folyamat](media/service-map/machine-groups-all.png)

Ha úgy módosítja a nézetet, hogy csak **a csoporthoz kapcsolódó folyamatok jelenjenek**meg, a rendszer csak azokra a folyamatokra és kapcsolatokra szűkíti le a nézetet, amelyek közvetlenül kapcsolódnak a csoport más gépeihez, így egyszerűsített nézetjön létre.

![Gépcsoport szűrt folyamatai](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Gépek hozzáadása csoporthoz

Ha gépeket szeretne hozzáadni egy meglévő csoporthoz, jelölje be a kívánt gépek melletti jelölőnégyzeteket, majd kattintson a **Hozzáadás a csoportba gombra.**  Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni a gépeket.
 
### <a name="removing-machines-from-a-group"></a>Gépek eltávolítása egy csoportból

A Csoportok listában bontsa ki a csoport nevét a gépek gépcsoportban való listázásához.  Ezután kattintson az eltávolítani kívánt készülék melletti három pontra, és válassza az **Eltávolítás parancsot.**

![Gép eltávolítása a csoportból](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Csoport eltávolítása vagy átnevezése

Kattintson a csoportnév melletti három pontra a csoportlistában.

![Gépcsoport menü](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Szerepkör-ikonok

Bizonyos folyamatok bizonyos szerepköröket szolgálnak ki a gépeken: webkiszolgálókon, alkalmazáskiszolgálókon, adatbázison és így tovább. A Szolgáltatástérkép jegyzetekkel és a gépdobozokat szerepkör-ikonokkal együtt ismerteti, hogy egy pillantással azonosíthassa a folyamat vagy a kiszolgáló által betöltött szerepet.

| Szerepkör ikon | Leírás |
|:--|:--|
| ![Webkiszolgáló](media/service-map/role-web-server.png) | Webkiszolgáló |
| ![Alkalmazáskiszolgáló](media/service-map/role-application-server.png) | Alkalmazáskiszolgáló |
| ![Adatbázis-kiszolgáló](media/service-map/role-database.png) | Adatbázis-kiszolgáló |
| ![LDAP-kiszolgáló](media/service-map/role-ldap.png) | LDAP-kiszolgáló |
| ![SMB-kiszolgáló](media/service-map/role-smb.png) | SMB-kiszolgáló |

![Szerepkör-ikonok](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Sikertelen kapcsolatok

A sikertelen kapcsolatok a folyamatok és számítógépek szolgáltatástérkép-leképezéseiben jelennek meg, és egy szaggatott piros vonal jelzi, hogy az ügyfélrendszer nem ér el egy folyamatot vagy portot. A sikertelen kapcsolatok at bármely telepített szolgáltatástérkép-ügynökkel rendelkező rendszer jelenti, ha az a rendszer az, amely a sikertelen kapcsolatot próbálja meg. A Szolgáltatásleképezés ezt a folyamatot úgy méri, hogy megfigyeli azokat a TCP-szoftvercsatornákat, amelyek nem hoznak létre kapcsolatot. Ez a hiba a tűzfal, az ügyfél vagy a kiszolgáló helytelen konfigurációja vagy egy távoli szolgáltatás elérhetetlensége miatt következhet be.

![Sikertelen kapcsolatok](media/service-map/failed-connections.png)

A sikertelen kapcsolatok ismertetése segíthet a hibaelhárításban, az áttelepítés érvényesítésében, a biztonsági elemzésben és az általános architekturális ismeretekben. A sikertelen kapcsolatok néha ártalmatlanok, de gyakran közvetlenül egy problémára mutatnak, például egy feladatátvételi környezet hirtelen elérhetetlenné válik, vagy két alkalmazásszint nem tud beszélni a felhőbe való migrálás után.

## <a name="client-groups"></a>Ügyfélcsoportok

Az ügyfélcsoportok olyan mezők a térképen, amelyek nem függőségi ügynökökkel nem rendelkeznek ügyfélgépeket. Egyetlen ügyfélcsoport képviseli az ügyfelek et egy adott folyamathoz vagy géphez.

![Ügyfélcsoportok](media/service-map/client-groups.png)

Az ügyfélcsoport kiszolgálóinak IP-címét válassza ki a csoportot. A csoport tartalma az **Ügyfélcsoport tulajdonságai** ablaktáblában található.

![Ügyfélcsoport tulajdonságai](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Kiszolgálóportcsoportok

A kiszolgálóportcsoportok olyan mezők, amelyek olyan kiszolgálóportokat jelölnek olyan kiszolgálókon, amelyek nem rendelkeznek függőségi ügynökökkel. A mező tartalmazza a kiszolgálóportot és az adott porthoz csatlakozó kiszolgálók számát. Bontsa ki a mezőt az egyes kiszolgálók és kapcsolatok megtekintéséhez. Ha csak egy kiszolgáló van a mezőben, a név vagy az IP-cím megjelenik.

![Kiszolgálóportcsoportok](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Helyi menü

A kiszolgáló jobb felső részén található három pontra (...) kattintva megjelenik a kiszolgáló helyi menüje.

![Sikertelen kapcsolatok](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Kiszolgálótérkép betöltése

A **Kiszolgálótérkép betöltése** gombra kattintva új térképre lép, ahol a kijelölt kiszolgáló lesz az új fókuszgép.

### <a name="show-self-links"></a>Önhivatkozások megjelenítése

Az **Önhivatkozások megjelenítése gombra** kattintva újrarajzolja a kiszolgálócsomópontot, beleértve az önhivatkozásokat is, amelyek a kiszolgálón belüli folyamatokat elinduló és végződő TCP-kapcsolatok. Ha az önhivatkozások megjelennek, a menüparancs **az Önhivatkozások elrejtése (Saját hivatkozások)** jelzőre változik, így kikapcsolhatja őket.

## <a name="computer-summary"></a>Számítógép összegzése

A **Gép összegzése** ablaktábla áttekintést nyújt a kiszolgáló operációs rendszeréről, a függőségek számáról és a más megoldásokból származó adatokról. Ezek az adatok közé tartoznak a teljesítménymutatók, az ügyfélszolgálati jegyek, a változások nyomon követése, a biztonság és a frissítések.

![A gép összegzése ablaktábla](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Számítógép- és folyamattulajdonságok

Amikor egy szolgáltatástérkép-leképezésben navigál, kiválaszthatja a gépeket és folyamatokat, hogy további környezetet kapjon a tulajdonságaikról. A gépek információt nyújtanak a DNS-névről, az IPv4-címekről, a processzor- és memóriakapacitásról, a virtuális gép típusáról, az operációs rendszerről és a verzióról, az utolsó újraindítás időpontjáról, valamint az OMS- és szolgáltatástérkép-ügynökök azonosítóiról.

![A Gép tulajdonságai ablaktábla](media/service-map/machine-properties.png)

Az operációs rendszer futó folyamatainak metaadataiból gyűjtheti össze a folyamat részleteit, beleértve a folyamat nevét, a folyamat leírását, a felhasználónevet és a tartományt (Windows rendszeren), a vállalat nevét, a termék nevét, a termék verzióját, a munkakönyvtárat, a parancssort és a folyamatot kezdési időpont.

![Folyamat tulajdonságai ablaktábla](media/service-map/process-properties.png)

A **Folyamat összegzése** ablaktábla további információkat tartalmaz a folyamat kapcsolatáról, beleértve a kötött portokat, a bejövő és kimenő kapcsolatokat, valamint a sikertelen kapcsolatokat.

![Folyamat összegzése ablaktábla](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Riasztások integrációja

A Service Map integrálható az Azure Alerts szolgáltatással, hogy a kiválasztott kiszolgálóra vonatkozóan a kiválasztott időtartományban lévő kilőtt riasztások jelenjenek meg. A kiszolgáló ikont jelenít meg, ha aktuális riasztások vannak, és a **Gépriasztások** ablaktábla felsorolja a riasztásokat.

![Gépriasztások ablaktábla](media/service-map/machine-alerts.png)

Annak engedélyezéséhez, hogy a Service Map releváns riasztásokat jelenítsen meg, hozzon létre egy riasztási szabályt, amely egy adott számítógépen aktiválódik. Megfelelő riasztások létrehozása:
- Mellékeljen egy záradékot számítógép szerint csoportosítva (például **1 perces számítógép-intervallum szerint).**
- Válassza ki, hogy a metrikamérés alapján riasztást szeretne adni.

## <a name="log-events-integration"></a>Események integrációjának naplózása

A Szolgáltatástérkép integrálva van a naplókereséssel, így a kiválasztott kiszolgálón a kiválasztott időtartományban elérhető összes naplóesemény számát jeleníti meg. Az eseményszámok listájának bármelyik sorára kattintva a Naplókeresés re kattintva megtekintheti az egyes naplóeseményeket.

![Gépnapló-események ablaktábla](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Ügyfélszolgálati integráció

A Szolgáltatástérkép-integráció az IT Service Management Connector automatikus, ha mindkét megoldás engedélyezve van, és konfigurálva van a Log Analytics-munkaterületen. A Szolgáltatástérkép integrációja "Service Desk" címkével van ellátva. További információt az [ITSM-munkaelemek központi kezelése az IT Service Management Connector használatával című témakörben talál.](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview)

A **Gépszerviz előszolgáltatás** ablaktábla felsorolja a kijelölt kiszolgáló összes IT-szolgáltatáskezelési eseményét a kiválasztott időtartományban. A kiszolgáló ikont jelenít meg, ha vannak aktuális elemek, és a Gép szervizpult ablaktábla felsorolja őket.

![Gépszerviz pult ablaktáblája](media/service-map/service-desk.png)

Ha meg szeretné nyitni az elemet a csatlakoztatott ITSM-megoldásban, kattintson a **Munkaelem megtekintése gombra.**

Ha meg szeretné tekinteni az elem részleteit a Naplókeresés ben, kattintson a **Megjelenítés gombra a Naplókeresés ben**.
A kapcsolati metrikák két új táblába vannak írva a Log Analytics szolgáltatásban 

## <a name="change-tracking-integration"></a>Változáskövetés integrációja

A szolgáltatástérkép-integráció a változáskövetéslel automatikus, ha mindkét megoldás engedélyezve van és konfigurálva van a Log Analytics-munkaterületen.

A **Gépváltozáskövetés** ablaktábla felsorolja az összes módosítást, a legutóbbival együtt, valamint egy hivatkozást, amely további részletekért a Naplókeresés hez kapcsolódik.

![Gépmódosítás-követés ablaktábla](media/service-map/change-tracking.png)

Az alábbi kép egy ConfigurationChange esemény részletes nézetét mutatja be, amely a Megjelenítés a **Log Analytics szolgáltatásban**lehetőséget választva jelenhet meg.

![ConfigurationChange esemény](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Teljesítményintegráció

A **Gép teljesítménye** ablaktábla a kijelölt kiszolgáló szabványos teljesítménymutatóit jeleníti meg. A metrikák közé tartozik a CPU-kihasználtság, a memóriakihasználtság, az elküldött és fogadott hálózati bájtok, valamint a hálózati bájtok által küldött és fogadott legfelső folyamatok listája.

![A Gép teljesítménye ablaktábla](media/service-map/machine-performance.png)

A teljesítményadatok megtekintéséhez szükség lehet [a megfelelő Log Analytics teljesítményszámlálók engedélyezésére.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)  A számlálók, amelyeket engedélyezni szeretne:

Windows:
- Processzor(*)\\processzoridő %-a
- A\\használatban lévő lekötött bájtok %-a
- Hálózati adapter(*)\\küldött bájtok/mp
- Hálózati adapter(*)\\fogadott bájtok/mp

Linux:
- Processzor(*)\\processzoridő %-a
- Memória(*)\\Felhasznált memória
- Hálózati adapter(*)\\küldött bájtok/mp
- Hálózati adapter(*)\\fogadott bájtok/mp

A hálózati teljesítményadatok lefelvételéhez engedélyeznie kell a Wire Data 2.0 megoldást a munkaterületen.
 
## <a name="security-integration"></a>Biztonsági integráció

A szolgáltatástérkép-integráció a biztonsággal és a naplózással automatikus, ha mindkét megoldás engedélyezve van és konfigurálva van a Log Analytics-munkaterületen.

A **Számítógép biztonsága** ablaktábla a kijelölt kiszolgáló biztonsági és naplózási megoldásának adatait jeleníti meg. Az ablaktábla a kijelölt időtartományban a kiszolgáló valamerre fennálló biztonsági problémáinak összegzését sorolja fel. A biztonsági problémák bármelyikére kattintva a naplókeresés bekerül a rájuk vonatkozó részletekért.

![A Gép biztonsága ablaktábla](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Frissítések integrációja

A Szolgáltatástérkép-integráció az Update Management szolgáltatással automatikus, ha mindkét megoldás engedélyezve van és konfigurálva van a Log Analytics-munkaterületen.

A **Számítógép-frissítések** ablaktábla a kijelölt kiszolgáló frissítéskezelési megoldásának adatait jeleníti meg. Az ablaktábla a kijelölt időtartományban a kiszolgáló hiányzó frissítéseinek összegzését sorolja fel.

![Gépmódosítás-követés ablaktábla](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-rekordok

A Szolgáltatástérkép számítógép és a folyamat készletadatai a Log Analytics szolgáltatásban [való kereséshez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az adatokat olyan esetekre alkalmazhatja, amelyek tartalmazzák az áttelepítés tervezését, a kapacitáselemzést, a felderítést és az igény szerinti teljesítményhibaelhárítást.

Minden egyes egyedi számítógéphez és folyamathoz óránként egy rekord jön létre, a folyamat vagy számítógép indításakor vagy a Szolgáltatástérképre való bedeszkázva létrehozott rekordokon kívül. Ezek a rekordok a következő táblák ban találhatók. A ServiceMapComputer_CL események mezői és értékei a ServiceMap Azure Resource Manager API-ban a Machine erőforrás mezőihez vannak leképezve. A ServiceMapProcess_CL események mezői és értékei a ServiceMap Azure Resource Manager API Process erőforrásának mezőihez vannak leképezve. A ResourceName_s mező megegyezik a megfelelő Erőforrás-kezelő erőforrás névmezőjével. 

>[!NOTE]
>A Szolgáltatástérkép funkcióinak növekedésével ezek a mezők változhatnak.

Vannak belsőleg létrehozott tulajdonságok, amelyek segítségével azonosíthatja az egyedi folyamatokat és számítógépeket:

- Számítógép: A *ResourceId* vagy *ResourceName_s* használatával egyedileg azonosíthatja a számítógépet a Log Analytics-munkaterületen belül.
- Folyamat: A *ResourceId* használatával egyedileg azonosíthatja a folyamatot a Log Analytics-munkaterületen belül. *ResourceName_s* annak a gépnek az összefüggésében egyedi, amelyen a folyamat fut (MachineResourceName_s) 

Mivel egy adott folyamathoz és számítógéphez több rekord is létezhet egy megadott időtartományban, a lekérdezések egynél több rekordot is visszaadhatnak ugyanahhoz a számítógéphez vagy folyamathoz. Ha csak a legutóbbi rekordot szeretné felvenni, adja hozzá a "| dedup ResourceId" a lekérdezéshez.

### <a name="connections"></a>Kapcsolatok

A kapcsolati metrikák egy új táblába kerülnek a Log Analytics – VMConnection szolgáltatásban. Ez a tábla a számítógép (bejövő és kimenő) kapcsolatairól nyújt tájékoztatást. A kapcsolatmetrikák olyan API-kkal is elérhetők, amelyek egy adott metrika megszerzéséhez szükséges eszközöket biztosítják egy időablakban.  A figyelőszoftver-csatorna fogadásából eredő TCP-kapcsolatok bejövőek, míg az adott IP-hez és porthoz való csatlakozással létrehozott tcp-kapcsolatok kimenők. A kapcsolat irányát az Irány tulajdonság jelöli, amely **be-** vagy **kimenőre**állítható. 

A táblákrekordjai a függőségi ügynök által jelentett adatokból jönnek létre. Minden rekord egy egyperces megfigyelést jelöl. Az időgenerált tulajdonság az időintervallum kezdetét jelzi. Minden rekord információkat tartalmaz az adott entitás, azaz a kapcsolat vagy a port azonosításához, valamint az entitáshoz társított mutatókat. Jelenleg csak az IPv4-en keresztüli TCP használatával előforduló hálózati tevékenységet jelenti a rendszer.

A költségek és az összetettség kezelése érdekében a kapcsolatrekordok nem jelentenek egyéni fizikai hálózati kapcsolatokat. Több fizikai hálózati kapcsolat logikai kapcsolatba van csoportosítva, amely ezután megjelenik a megfelelő táblában.  Jelentés: *a VMConnection* tábla rekordjai logikai csoportosítást jelentenek, nem pedig a figyelt egyéni fizikai kapcsolatokat. A *virtuális gépkapcsolat*ban a következő attribútumokhoz azonos értékű fizikai hálózati kapcsolat egy adott egyperces időközben egyetlen logikai rekordba lesz összesítve. 

| Tulajdonság | Leírás |
|:--|:--|
| `Direction` |A kapcsolat iránya, az érték *bejövő* vagy *kimenő* |
| `Machine` |A számítógép FQDN |
| `Process` |A folyamat vagy folyamatcsoportok azonosítása a kapcsolat kezdeményezése/elfogadása |
| `SourceIp` |A forrás IP-címe |
| `DestinationIp` |A cél IP-címe |
| `DestinationPort` |A cél állomás portszáma |
| `Protocol` |A kapcsolathoz használt protokoll.  Az értékek *tcp.* |

A csoportosítás hatásának figyelembevétele érdekében a csoportosított fizikai kapcsolatok számával kapcsolatos információk a bejegyzés következő tulajdonságai között szerepelnek:

| Tulajdonság | Leírás |
|:--|:--|
| `LinksEstablished` |A jelentési időszak alatt létrehozott fizikai hálózati kapcsolatok száma |
| `LinksTerminated` |A jelentési időszak alatt leszakított fizikai hálózati kapcsolatok száma |
| `LinksFailed` |Azoknak a fizikai hálózati kapcsolatoknak a száma, amelyek a jelentési időszak alatt meghibásodtak. Ez az információ jelenleg csak a kimenő kapcsolatokesetében érhető el. |
| `LinksLive` |A jelentési időablak végén megnyitott fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

A kapcsolatszám-mérőszámokon kívül az adott logikai kapcsolaton vagy hálózati porton küldött és fogadott adatok mennyiségére vonatkozó információk a bejegyzés következő tulajdonságai ban is szerepelnek:

| Tulajdonság | Leírás |
|:--|:--|
| `BytesSent` |A jelentési időablak alatt elküldött bájtok teljes száma |
| `BytesReceived` |A jelentési időablak során fogadott bájtok teljes száma |
| `Responses` |A jelentési időszak alatt megfigyelt válaszok száma. 
| `ResponseTimeMax` |A jelentési idő ablaka során megfigyelt legnagyobb válaszidő (ezredmásodperc).  Ha nincs érték, a tulajdonság üres.|
| `ResponseTimeMin` |A jelentési idő ablaka során megfigyelt legkisebb válaszidő (ezredmásodperc).  Ha nincs érték, a tulajdonság üres.|
| `ResponseTimeSum` |A jelentési idő ablaka során megfigyelt összes válaszidő (ezredmásodperc) összege.  Ha nincs érték, a tulajdonság üres|

A jelentett adatok harmadik típusa a válaszidő – mennyi ideig várakozik a hívó a kapcsolaton keresztül küldött kérelem feldolgozására, és a távoli végpont által megválaszolandó válaszra. A jelentett válaszidő az alapul szolgáló alkalmazásprotokoll valódi válaszidejének becslése. Heurisztika alapján számítják ki a fizikai hálózati kapcsolat forrás- és célvége közötti adatáramlás megfigyelését. Fogalmilag ez a különbség a kérelem utolsó bájtja között, amikor elhagyja a feladót, és az az időpont, amikor a válasz utolsó bájtja visszaérkezik hozzá. Ez a két időbélyeg egy adott fizikai kapcsolat kérés- és válaszeseményeinek elhaolására szolgál. A köztük lévő különbség egyetlen kérelem válaszidejét jelöli. 

Ebben az első kiadásban ez a funkció, a mi algoritmus egy közelítés, amely működhet különböző mértékű siker függően a tényleges alkalmazás protokoll használt egy adott hálózati kapcsolat. A jelenlegi megközelítés például jól működik a kérelem-válasz alapú protokollok, például a HTTP(S) esetében, de nem működik egyirányú vagy üzenetsor-alapú protokollokkal.

Íme néhány fontos szempont, amelyet figyelembe kell venni:

1. Ha egy folyamat ugyanazon az IP-címen, de több hálózati csatolón keresztül fogadja a kapcsolatokat, a rendszer minden egyes kapcsolathoz külön rekordot jelent. 
2. A helyettesítő IP-című rekordok nem tartalmaznak tevékenységet. Ezek azért vannak benne, hogy képviseljék azt a tényt, hogy a számítógép egy portja nyitva áll a bejövő forgalom számára.
3. A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő IP-című rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord (ugyanahhoz a folyamathoz, porthoz és protokollhoz) van. Ha egy helyettesítő IP-rekord nincs megadva, az IsWildcardBind rekord tulajdonság a megadott IP-címmel "True" lesz állítva, jelezve, hogy a port a jelentéskészítő gép minden felületén elérhető.
4. Azok a portok, amelyek csak egy adott kapcsolaton vannak kötve, az IsWildcardBind értéke "Hamis".

#### <a name="naming-and-classification"></a>Elnevezés és besorolás

Az egyszerűség kedvéért a kapcsolat távoli végének IP-címe a RemoteIp tulajdonság része. Bejövő kapcsolatok esetén a RemoteIp megegyezik a SourceIp protokollével, míg a kimenő kapcsolatok esetében ugyanaz, mint a DestinationIp. A RemoteDnsCanonicalNames tulajdonság a számítógép által a RemoteIp számára jelentett DNS gyűjtőneveket jelöli. A RemoteDnsQuestions és remoteClassification tulajdonságok későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely

*A VMConnection* az egyes kapcsolatrekordok távoli végpontjainak földrajzi helymeghatározási adatait is tartalmazza a rekord következő tulajdonságai között: 

| Tulajdonság | Leírás |
|:--|:--|
| `RemoteCountry` |A RemoteIp-et üzemeltető ország/régió neve.  Például *az Egyesült Államok* |
| `RemoteLatitude` |A földrajzi hely szélessége.  Például *47,68* |
| `RemoteLongitude` |A helymeghatározás imázsának hosszúsága.  Például *-122.12* |

#### <a name="malicious-ip"></a>Rosszindulatú IP-cím

A *VMConnection* tábla minden RemoteIp-tulajdonságát összeveti az ismert rosszindulatú tevékenységgel rendelkező IP-címekkel. Ha a RemoteIp rosszindulatúként van azonosítva, a következő tulajdonságok lesznek feltöltve (üresek, ha az IP nem tekinthető rosszindulatúnak) a bejegyzés következő tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
| `MaliciousIp` |A RemoteIp-cím |
| `IndicatorThreadType` |Az észlelt fenyegetésjelző a következő értékek egyike: *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
| `Description` |A megfigyelt fenyegetés leírása. |
| `TLPLevel` |A Traffic Light Protocol (TLP) szint az egyik definiált érték, *fehér,* *zöld,* *borostyán,* *piros.* |
| `Confidence` |Az értékek *0 – 100*. |
| `Severity` |Az értékek *0–5*, ahol az *5* a legsúlyosabb, a *0* pedig egyáltalán nem súlyos. Az alapértelmezett érték *3*.  |
| `FirstReportedDateTime` |Az első alkalommal, amikor a szolgáltató jelentette a mutatót. |
| `LastReportedDateTime` |Az interflow utoljára látta a mutatót. |
| `IsActive` |Azt jelzi, hogy a jelzők *true* vagy *false* értékkel vannak inaktiválva. |
| `ReportReferenceLink` |Egy adott megfigyelhetővel kapcsolatos jelentésekre mutató hivatkozások. |
| `AdditionalInformation` |Adott esetben további információkat tartalmaz a megfigyelt fenyegetésről. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL rekordok

A ServiceMapComputer_CL típusú *rekordok* rendelkeznek a Service Map-ügynökökkel rendelkező kiszolgálók készletadataival. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | A munkaterületen belüli gép egyedi azonosítója |
| `ResourceName_s` | A munkaterületen belüli gép egyedi azonosítója |
| `ComputerName_s` | A számítógép FQDN |
| `Ipv4Addresses_s` | A kiszolgáló IPv4-címeinek listája |
| `Ipv6Addresses_s` | A kiszolgáló IPv6-címeinek listája |
| `DnsNames_s` | DNS-nevek tömbje |
| `OperatingSystemFamily_s` | Windows vagy Linux |
| `OperatingSystemFullName_s` | Az operációs rendszer teljes neve  |
| `Bitness_s` | A gép bitessége (32 vagy 64 bites)  |
| `PhysicalMemory_d` | A fizikai memória MB-ban |
| `Cpus_d` | A CPU-k száma |
| `CpuSpeed_d` | A CPU sebessége MHz-ben|
| `VirtualizationState_s` | *ismeretlen,* *fizikai,* *virtuális*, *hipervizor* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, és így tovább |
| `VirtualMachineNativeMachineId_g` | A hipervizor által hozzárendelt virtuálisgép-azonosító |
| `VirtualMachineName_s` | A virtuális gép neve |
| `BootTime_t` | A rendszerindítás idáig |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL Szövegrekordok

A ServiceMapProcess_CL típusú *rekordok* készletadatokkal rendelkeznek a TCP-hez kapcsolódó folyamatokhoz a Service Map ügynökökkel rendelkező kiszolgálókon. Ezek a rekordok a következő táblázatban található tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | A munkaterületen belüli folyamat egyedi azonosítója |
| `ResourceName_s` | Azon a gépen belüli folyamat egyedi azonosítója, amelyen fut|
| `MachineResourceName_s` | A számítógép erőforrásneve |
| `ExecutableName_s` | A folyamat végrehajtható fájljának neve |
| `StartTime_t` | A folyamatkészlet kezdési időpontja |
| `FirstPid_d` | A folyamatkészlet első PID-je |
| `Description_s` | A folyamat leírása |
| `CompanyName_s` | A vállalat neve |
| `InternalName_s` | A belső név |
| `ProductName_s` | A termék neve |
| `ProductVersion_s` | A termék verziója |
| `FileVersion_s` | A fájl verziója |
| `CommandLine_s` | A parancssor |
| `ExecutablePath _s` | A végrehajtható fájl elérési útja |
| `WorkingDirectory_s` | A munkakönyvtár |
| `UserName` | Az a fiók, amely nek megfelelően a folyamat végrehajtásra kerül |
| `UserDomain` | Az a tartomány, amely alatt a folyamat végrehajtásalatt áll |

## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gép felsorolása

ServiceMapComputer_CL | arg_max összegzése(TimeGenerated, *) a ResourceId szerint

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Sorolja fel az összes felügyelt számítógép fizikai memóriakapacitását.

ServiceMapComputer_CL | összegezze arg_max(TimeGenerated, *) a ResourceId | projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>A számítógép név, a DNS, az IP és az operációs rendszer listázása.

ServiceMapComputer_CL | összegezze arg_max(TimeGenerated, *) a ResourceId | ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Az összes folyamat megkeresése az "sql" paranccsal a parancssorban

ServiceMapProcess_CL | ahol CommandLine_s contains_cs "sql" | arg_max összegzése(TimeGenerated, *) a ResourceId szerint

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Gép (legutóbbi rekord) keresése erőforrásnév szerint

keresés a ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | arg_max összegzése(TimeGenerated, *) a ResourceId szerint

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Gép (legutóbbi rekord) keresése IP-cím szerint

keresés a ServiceMapComputer_CL) "10.229.243.232" | arg_max összegzése(TimeGenerated, *) a ResourceId szerint

### <a name="list-all-known-processes-on-a-specified-machine"></a>Az összes ismert folyamat listázása egy adott gépen

ServiceMapProcess_CL | ahol MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | arg_max összegzése(TimeGenerated, *) a ResourceId szerint

### <a name="list-all-computers-running-sql"></a>Az ÖSSZES SQL-t futtató számítógép listázása

ServiceMapComputer_CL | ahol ResourceName_s ((keresés a (ServiceMapProcess_CL) " sql\*\*" | distinct MachineResourceName_s)) | különböző ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A curl összes egyedi termékverziójának listázása az adatközpontomban

ServiceMapProcess_CL | ahol ExecutableName_s == "göndör" | külön ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>A CentOS rendszert futtató számítógépek számítógépcsoportjának létrehozása

ServiceMapComputer_CL | ahol OperatingSystemFullName_s "CentOS" contains_cs | különböző ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Gépek egy csoportjának kimenő kapcsolatainak összegzése

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

A Szolgáltatásleképezés ben található összes kiszolgálói, folyamat- és függőségi adat elérhető a [Service Map REST API-n](https://docs.microsoft.com/rest/api/servicemap/)keresztül.

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft automatikusan gyűjti a használati és teljesítményadatokat a Szolgáltatástérkép szolgáltatás ön általi használatával. A Microsoft ezeket az adatokat a Service Map szolgáltatás minőségének, biztonságának és integritásának biztosításához és javítására használja. A pontos és hatékony hibaelhárítási lehetőségek biztosítása érdekében az adatok információkat tartalmaznak a szoftver konfigurációjáról, például az operációs rendszerről és verzióról, az IP-címről, a DNS-névről és a munkaállomás nevéről. A Microsoft nem gyűjt neveket, címeket vagy egyéb kapcsolattartási adatokat.

Az adatgyűjtésről és -használatról a [Microsoft Online Services adatvédelmi nyilatkozatában olvashat bővebben.](https://go.microsoft.com/fwlink/?LinkId=512132)

## <a name="next-steps"></a>További lépések

További információ a Log Analytics [naplókereséseiről](../../azure-monitor/log-query/log-query-overview.md) a Szolgáltatástérkép által gyűjtött adatok lekéréséhez.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármilyen problémája van a Szolgáltatástérkép telepítésével vagy futtatásával, ez a szakasz segíthet. Ha továbbra sem tudja megoldani a problémát, forduljon a Microsoft támogatási szolgálatához.

### <a name="dependency-agent-installation-problems"></a>Függőségi ügynök telepítési problémái

#### <a name="installer-prompts-for-a-reboot"></a>A telepítő újraindításra kér
A függőségi ügynök *általában* nem igényel újraindítást a telepítés vagy eltávolítás után. Bizonyos ritka esetekben azonban a Windows Server újraindítást igényel a telepítés folytatásához. Ez akkor fordul elő, ha egy függőség, általában a Microsoft Visual C++ terjeszthető könyvtár egy zárolt fájl miatt újraindítást igényel.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>"Nem lehet telepíteni a függőségi ügynököt: A Visual Studio futásidejű kódtárait nem sikerült telepíteni (kód = [code_number])" üzenet jelenik meg

A Microsoft függőségi ügynök a Microsoft Visual Studio futásidejű kódtáraira épül. Üzenet jelenik meg, ha probléma merül fel a könyvtárak telepítése során. 

A futásidejű függvénytár telepítői naplókat hoznak létre a %LOCALAPPDATA%\temp mappában. A fájl `dd_vcredist_arch_yyyymmddhhmmss.log`, ahol `x86` `amd64` *arch* van, vagy *yyyymmddhhmmss* a dátum és idő (24 órás óra), amikor a napló jött létre. A napló a telepítést blokkoló problémával kapcsolatos részleteket tartalmaz.

Hasznos lehet először telepíteni a [legújabb futásidejű könyvtárakat.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

Az alábbi táblázat kódszámokat és javasolt felbontásokat sorol fel.

| Kód | Leírás | Megoldás: |
|:--|:--|:--|
| 0x17 | A könyvtártelepítőhöz olyan Windows-frissítés szükséges, amely nincs telepítve. | Tekintse meg a legutóbbi könyvtártelepítő naplóját.<br><br>Ha egy `Windows8.1-KB2999226-x64.msu` hivatkozást egy `Error 0x80240017: Failed to execute MSU package,` sor követ, nem rendelkezik a KB2999226. Kövesse a [Windows univerzális C-futásidejű részének](https://support.microsoft.com/kb/2999226) előfeltételei szakaszában található utasításokat. Előfordulhat, hogy az előfeltételek telepítéséhez futtatnia kell a Windows Update szolgáltatást, és többször újra kell indítania az okat.<br><br>Futtassa újra a Microsoft függőségi ügynök telepítőt. |

### <a name="post-installation-issues"></a>Telepítés utáni problémák

#### <a name="server-doesnt-appear-in-service-map"></a>A kiszolgáló nem jelenik meg a Szolgáltatásleképezésben

Ha a függőségi ügynök telepítése sikeres volt, de nem látja a gépet a Szolgáltatásleképezési megoldásban:
* Sikeresen telepítve van a függőségi ügynök? Ezt ellenőrizheti, ha ellenőrzi, hogy a szolgáltatás telepítve van-e és fut-e.<br><br>
**Windows**: Keresse meg a **Microsoft függőségi ügynök**nevű szolgáltatást.
**Linux**: Keresse meg a futó folyamat **microsoft-függőség-ügynök**.

* Ön a [Log Analytics ingyenes szinten?](https://azure.microsoft.com/pricing/details/monitor/) Az ingyenes csomag legfeljebb öt egyedi service map gépet tesz lehetővé. A további gépek nem jelennek meg a Szolgáltatástérképen, még akkor sem, ha az előző öt már nem küld adatokat.

* A kiszolgáló napló- és perf adatokat küld az Azure Monitor naplóknak? Nyissa meg az Azure Monitor\Logs webhelyet, és futtassa a következő lekérdezést a számítógéphez: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Az eredmények között számos esemény jelent meg? Az adatok frissek? Ha igen, a Log Analytics-ügynök megfelelően működik, és kommunikál a munkaterülettel. Ha nem, ellenőrizze az ügynököt a számítógépen: [Log Analytics ügynök a Windows hibaelhárításához](../platform/agent-windows-troubleshoot.md) vagy Log Analytics ügynök [Linuxhoz hibaelhárítás](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>A kiszolgáló megjelenik a Szolgáltatásleképezésben, de nincsenek folyamatai

Ha látja a gépet a Szolgáltatásleképezésben, de nem rendelkezik folyamat- vagy kapcsolatadatokkal, az azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernel illesztőprogramja nem töltődött be. 

Ellenőrizze `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` a (Windows) vagy `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). A fájl utolsó soraiban meg kell jelölnie, hogy miért nem töltődik be a kernel. Előfordulhat például, hogy a rendszermag nem támogatott Linuxon, ha frissítette a rendszermagot.

## <a name="feedback"></a>Visszajelzés

Van-e visszajelzése számunkra a Szerviztérképről vagy a dokumentációról?  Látogasson el [a User Voice oldalra,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)ahol funkciókat javasolhat, vagy szavazhat a meglévő javaslatokra.
