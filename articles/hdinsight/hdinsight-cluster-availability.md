---
title: A fürt rendelkezésre állásának figyelése a Ambari és a Azure Monitor naplók használatával
description: Megtudhatja, hogyan használhatja a Ambari és a Azure Monitor naplókat a fürt állapotának és rendelkezésre állásának figyeléséhez.
keywords: monitorozás, ambari, monitorozás, log Analytics, riasztás, rendelkezésre állás, állapot
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 07b82f475074f5b55a2a5a93f7a59008476233c8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934267"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>A fürt rendelkezésre állásának figyelése a Ambari és a Azure Monitor naplók használatával

A HDInsight-fürtök az Apache Ambari is tartalmazzák, amely egy pillantásra és előre meghatározott riasztásokra vonatkozó információkat biztosít, valamint Azure Monitor naplózza az integrációt, amely lekérdezhető mérőszámokat és naplókat, valamint konfigurálható riasztásokat biztosít.

Ebből a dokumentumból megtudhatja, hogyan használhatja ezeket az eszközöket a fürt figyelésére, valamint néhány példát a Ambari-riasztás konfigurálására, a csomópontok rendelkezésre állási arányának figyelésére és egy olyan Azure Monitor riasztás létrehozására, amely akkor következik be, ha egy vagy több csomópontból nem érkezett szívverés. öt órán belül.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Irányítópult

A Ambari irányítópultja a **Ambari Kezdőlap** hivatkozására kattintva érhető el az alábbi ábrán látható HDInsight Azure Portal áttekintés paneljének **fürt irányítópultok** szakaszában. Azt is megteheti, hogy a következő URL-címet írja be a [böngésző\<https://\>clustername. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight erőforrás-portál nézet](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Ekkor a rendszer kérni fogja a fürt bejelentkezési felhasználónevét és jelszavát. Adja meg a fürt létrehozásakor kiválasztott hitelesítő adatokat.

Ekkor megnyílik a Ambari-irányítópult, amely a HDInsight-fürt állapotának gyors áttekintését biztosító widgeteket tartalmaz. Ezek a minialkalmazások olyan metrikákat mutatnak, mint például az élő Adatcsomópontok (feldolgozó csomópontok) és a Naplócsomópontok (Zookeeper csomópont), a NameNodes (csomópontok), valamint bizonyos típusú fürtökhöz tartozó mérőszámok, például a Spark-és a erőforráskezelő-fürtök esetében a fonal Hadoop.

![Ambari-irányítópult](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Gazdagépek – az egyes csomópontok állapotának megtekintése

Az egyes csomópontokra vonatkozó állapotinformációkat is megtekintheti. A **gazdagépek** lapra kattintva megtekintheti a fürt összes csomópontjának listáját, és megtekintheti az egyes csomópontok alapszintű információit. Az egyes csomópontok neve mezőtől balra található zöld pipa jelzi, hogy az összes összetevő fel van-e állítva a csomóponton. Ha egy összetevő leállt egy csomóponton, a zöld pipa helyett piros riasztási háromszög jelenik meg.

![Ambari-gazdagépek nézet](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Ezután a csomópont **nevére** kattintva megtekintheti az adott csomópont részletes gazdagép-metrikáit. Ez a nézet az egyes összetevők állapotát és rendelkezésre állását jeleníti meg.

![A Ambari egyetlen csomópontos nézetet üzemeltet](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-riasztások

A Ambari számos konfigurálható riasztást is kínál, amelyek bizonyos események bejelentését teszik lehetővé. A riasztások aktiválásakor a rendszer a Ambari bal felső sarkában jeleníti meg a riasztások számát tartalmazó piros jelvényt. A jelvényre kattintva az aktuális riasztások listája látható.

![Ambari-riasztások száma](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

A riasztási definíciók és állapotukk listájának megtekintéséhez kattintson a **riasztások** fülre az alábbi ábrán látható módon.

![Ambari-riasztások definíciói nézete](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

A Ambari számos előre meghatározott riasztást kínál a rendelkezésre állással kapcsolatban, beleértve a következőket:

| Riasztás megnevezése                        | Leírás                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode állapotának összegzése           | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha nem kifogástalan állapotú Adatcsomópontok van                                                                                                                |
| NameNode magas rendelkezésre állási állapota | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha az aktív NameNode vagy a készenléti NameNode nem fut.                                                                              |
| Rendelkezésre álló Naplócsomópontok százalékos aránya    | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Naplócsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a JournalNode folyamat-ellenőrzésének eredményeit. |
| Rendelkezésre álló Adatcsomópontok százalékos aránya       | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Adatcsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamat-ellenőrzésének eredményeit.       |

A Ambari-riasztások teljes listája [itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)található, amelyek segítenek a fürt rendelkezésre állásának figyelésében.

A riasztások részleteinek megtekintéséhez vagy a feltételek módosításához kattintson a riasztás **nevére** . A **DataNode állapotának összegzése** példaként. Megtekintheti a riasztás leírását, valamint a "figyelmeztetés" vagy "kritikus" riasztást kiváltó konkrét feltételeket, valamint a feltételek ellenőrzési intervallumát is. A konfiguráció szerkesztéséhez kattintson a konfigurációs mező jobb felső sarkában található **Szerkesztés** gombra.

![Ambari riasztás konfigurálása](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Itt szerkesztheti a leírást, és – ami még fontosabb – a figyelmeztetési vagy kritikus riasztások ellenőrzési időközét és küszöbértékeit.

![Ambari-riasztás konfigurációjának szerkesztési nézete](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Ebben a példában két nem kifogástalan állapotú Adatcsomópontok aktiválhat kritikus riasztást, és 1 nem kifogástalan állapotú DataNode csak figyelmeztetést indíthat. A Szerkesztés befejezése után kattintson a **Mentés** gombra.

### <a name="email-notifications"></a>E-mail értesítés

A Ambari-riasztásokhoz e-mail-értesítéseket is konfigurálhat. Ehhez a **riasztások** lapon kattintson a bal felső sarokban található **műveletek** gombra, majd az **értesítések kezelése** lehetőségre.

![Ambari – értesítések kezelése művelet](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Ekkor megnyílik a riasztási értesítések kezelésére szolgáló párbeszédpanel. **+** Kattintson a párbeszédpanel alján található elemre, és töltse ki a kötelező mezőket, hogy biztosítsa a Ambari az e-mail-kiszolgáló adataival, amelyekből e-maileket szeretne küldeni.

> [!TIP]
> A Ambari e-mail-értesítéseinek beállítása jó módszer lehet a riasztások egy helyen való fogadására sok HDInsight-fürt kezelésekor.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor naplók integrációja

Azure Monitor naplók lehetővé teszik több erőforrás, például a HDInsight-fürtök által generált adatok összegyűjtését és összesítését egy helyen az egységes monitorozási élmény eléréséhez.

Előfeltételként szükség lesz egy Log Analytics munkaterületre az összegyűjtött adatok tárolásához. Ha még nem hozott létre ilyet, kövesse az alábbi utasításokat: [Log Analytics munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight-Azure Monitor naplók integrációjának engedélyezése

A portál HDInsight-fürterőforrás lapján kattintson az **Operations Management Suite** panelre. Ezután kattintson az **Engedélyezés** elemre, és válassza ki a log Analytics munkaterületet a legördülő listából.

![HDInsight Operations Management Suite panel](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>A naplók paneljének lekérdezési metrikái és naplói

Azure Monitor naplózási integráció engedélyezése után (ez eltarthat néhány percig), navigáljon a **log Analytics munkaterület** -erőforráshoz, és kattintson a **naplók** panelre.

![Log Analytics munkaterület naplók panelje](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

A **naplók** panelen számos példa szerepel a lekérdezésekben, például:

| Lekérdezés neve                      | Leírás                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| A számítógépek rendelkezésre állása ma    | A naplókat küldő számítógépek számának diagramja óránként                     |
| Szívverések listázása                 | Az összes számítógép szívverésének listázása az elmúlt órában                           |
| Az egyes számítógépek utolsó szívverése | Az egyes számítógépek által eljuttatott utolsó szívverés megjelenítése                             |
| Nem elérhető számítógépek           | Az összes olyan ismert számítógép listázása, amely nem küldött szívverést az elmúlt 5 órában |
| Rendelkezésre állási arány               | Az egyes csatlakoztatott számítógépek rendelkezésre állási arányának kiszámítása                |

Futtassa például a **rendelkezésre állási arány** mintájának lekérdezését úgy, hogy a lekérdezés **futtatása** elemre kattint, ahogy az a fenti képernyőképen is látható. Ez a fürt egyes csomópontjainak rendelkezésre állási arányát fogja megjeleníteni százalékban. Ha több HDInsight-fürtön is engedélyezte a metrikák küldését ugyanarra a Log Analytics munkaterületre, akkor a fürt összes csomópontjának rendelkezésre állási aránya megjelenik.

![Log Analytics munkaterület naplóinak "rendelkezésre állási arány" mintájának lekérdezése](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> A rendelkezésre állási sebességet 24 órás időszakra mérjük, így a fürtnek legalább 24 órán át futnia kell, mielőtt a pontos rendelkezésre állási díjakat látni fogja.

Ezt a táblázatot a jobb felső sarokban található **rögzítés** gombra kattintva rögzítheti egy megosztott irányítópulton. Ha nem rendelkezik írható megosztott irányítópultokkal, Itt láthatja, hogyan hozhat létre egyet itt: [Irányítópultok létrehozása és megosztása a Azure Portalban](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Riasztások Azure Monitor

Beállíthat Azure Monitor riasztásokat is, amelyek akkor aktiválódnak, ha egy metrika értéke vagy egy lekérdezés eredményei megfelelnek bizonyos feltételeknek. Például hozzon létre egy riasztást, amely e-mailt küld, ha egy vagy több csomópont 5 órán belül nem küldött szívverést (azaz nem érhető el).

A **naplók** panelen futtassa a nem **elérhető számítógépek** minta lekérdezést úgy, hogy a lekérdezés **futtatása** elemre kattint az alább látható módon.

![Log Analytics munkaterület naplóinak "nem elérhető számítógépek" mintájának lekérdezése](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Ha az összes csomópont elérhető, a lekérdezésnek most 0 eredményt kell visszaadnia. Kattintson az **új riasztási szabály** elemre a lekérdezéshez tartozó riasztás konfigurálásának megkezdéséhez.

![Log Analytics munkaterület új riasztási szabálya](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

A riasztásnak három összetevője van: az *erőforrás* , amelyhez létre kívánja hozni a szabályt (ebben az esetben a log Analytics munkaterület), a riasztás aktiválásának *feltételét* , valamint azokat a *műveleti csoportokat* , amelyek meghatározzák, hogy mi történjen a riasztás esetén. kiváltott.

Az alább látható **feltétel címére**kattintva fejezze be a jel logikájának konfigurálását.

![Riasztási szabály feltétele](media/hdinsight-cluster-availability/portal-condition-title.png)

Ekkor megnyílik a **jel logikai beállítása** panel.

A **riasztási logika** szakasz a következőképpen állítható be:

*Alapja: Eredmények száma, feltétel: Nagyobb, mint, küszöbérték: 0.*

Mivel ez a lekérdezés csak a nem elérhető csomópontokat adja vissza eredményként, ha az eredmények száma egyre nagyobb nullánál, a riasztásnak tüzet kell mutatnia.

A **kiértékelt szakasz alapján** állítsa be az **időszakot** és a **gyakoriságot** attól függően, hogy milyen gyakran kívánja a nem elérhető csomópontok keresését.

Vegye figyelembe, hogy ennek a riasztásnak a esetében meg kell győződnie arról, hogy az **időtartam = gyakoriság.** Az időtartammal, gyakorisággal és egyéb riasztási paraméterekkel kapcsolatos további információkat [itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)talál.

Ha befejezte a jel logikájának konfigurálását, kattintson a **kész** gombra.

![Riasztási szabály a jel logikájának konfigurálása](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Ha még nem rendelkezik meglévő műveleti csoporttal, kattintson az **új létrehozása** elemre a **műveleti csoportok** szakaszban.

![Riasztási szabály – új műveleti csoport](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Ekkor megnyílik a **műveleti csoport hozzáadása** panel. Válassza ki a **műveleti csoport nevét**, a **rövid nevet**, az **előfizetést**és az **erőforráscsoportot.** A **műveletek** szakaszban válassza ki a **művelet nevét** , és válassza az **E-mail/SMS/leküldés/hang** lehetőséget a **művelet típusaként.**

> [!NOTE]
> Több más művelet is elindítható egy e-mail/SMS/push/hang mellett, például egy Azure-függvény, a LogicApp, a webhook, a ITSM és az Automation Runbook mellett. [tudj meg többet.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Ekkor megnyílik az **e-mail/SMS/leküldés/hang** panel. Válassza ki a címzett **nevét** , **jelölje be** az **e-mail** szövegmezőt, és írjon be egy e-mail-címet, amelyre a riasztást el szeretné juttatni. Kattintson az **OK gombra** az **e-mail/SMS/leküldés/hang** panelen, majd a **műveleti csoport hozzáadása** panelen a műveleti csoport konfigurálásának befejezéséhez.

![Riasztási szabály hozzáadása műveleti csoport](media/hdinsight-cluster-availability/portal-add-action-group.png)

Miután ezek a pengék bezárultak, a műveleti **csoportok** szakaszban szereplő műveleti csoportnak kell megjelennie. Végül fejezze be a **riasztás részletei** szakaszt a **riasztási szabály nevének** és **leírásának** beírásával és a **Súlyosság**kiválasztásával.
A befejezéshez kattintson a **riasztási szabály létrehozása** elemre.

![Riasztási szabály létrehozása Befejezés](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A **Súlyosság** meghatározásának lehetősége egy hatékony eszköz, amely több riasztás létrehozásakor is használható. Létrehozhat például egy riasztást egy figyelmeztetés (1. pont) létrehozásához, ha egy fő csomópont leáll, és egy másik riasztás, amely kritikus (a 0. szint) állapotot eredményez abban a valószínűtlen eseményben, amelyet a főcsomópontok leállnak.

Ha a riasztás feltétele teljesül, a riasztás tüzet fog kapni, és e-mailben megkapja a riasztás részleteit, például:

![Riasztási e-mail Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

A kilőtt összes riasztást súlyosság szerint csoportosítva is megtekintheti a **log Analytics munkaterület** **riasztások** paneljén.

![Log Analytics munkaterület riasztásai](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Ha egy súlyossági csoportra (például az **1.,** a fenti kiemeltre) kattint, az adott súlyosságú riasztások rekordjait jeleníti meg az alábbi módon:

![1\. Log Analytics munkaterület-riasztások](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>További lépések
- [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben](hdinsight-high-availability-linux.md)
