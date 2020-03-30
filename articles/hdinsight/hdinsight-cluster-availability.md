---
title: 'Figyelés: Apache Ambari & Azure Monitor-naplók – Azure HDInsight'
description: Ismerje meg, hogyan használhatja az Ambari és az Azure Monitor naplók a fürt állapotának és elérhetőségének figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060174"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Fürtelérhetőség figyelése az Apache Ambari és az Azure Monitor naplóival

A HDInsight-fürtök tartalmazzák az Apache Ambari-t, amely egy pillantással és előre definiált riasztásokkal biztosítja az állapotinformációkat, valamint az Azure Monitor naplóinak integrációját, amely lekérdezhető metrikákat és naplókat, valamint konfigurálható riasztásokat biztosít.

Ez a dokumentum bemutatja, hogyan használhatja ezeket az eszközöket a fürt figyelésére, és néhány példát mutat be az Ambari-riasztások konfigurálására, a csomópontok rendelkezésre állási arányának figyelésére és az Azure Monitor riasztásának létrehozására, amely akkor aktiválódik, ha egy vagy több csomópont szívverése nem érkezett meg. Öt óra múlva.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Irányítópult

Az Ambari irányítópultja az **Ambari otthoni** hivatkozásának kiválasztásával érhető el az Azure Portal HDInsight-áttekintése **fürtirányítópultok** szakaszában az alábbiak szerint. Másik lehetőségként úgy érhető el, `https://CLUSTERNAME.azurehdinsight.net` hogy egy olyan böngészőben navigál, ahol a CLUSTERNAME a fürt neve.

![HDInsight erőforrásportál nézet](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Ezután a rendszer kérni fogja a fürt bejelentkezési felhasználónevét és jelszavát. Adja meg a fürt létrehozásakor kiválasztott hitelesítő adatokat.

Ezután az Ambari irányítópultra kerül, amely néhány mérőszámot megjelenítő widgeteket tartalmaz, amelyek gyors áttekintést nyújtanak a HDInsight-fürt állapotáról. Ezek a widgetek olyan mérőszámokat mutatnak, mint például az élő DataNodes (feldolgozócsomópontok) és a JournalNodes (zookeeper csomópont), a NameNodes (head csomópontok) üzemidő, valamint az egyes fürttípusokra jellemző metrikák száma, például a YarN ResourceManager spark- és Hadoop-fürtök üzemideje.

![Az Apache Ambari műszerfal-kijelzőt használ](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – egyéni csomópontállapot megtekintése

Megtekintheti az egyes csomópontok állapotadatait is. A **Hosts** fülre választva megtekintheti a fürt összes csomópontjának listáját, és megtekintheti az egyes csomópontokra vonatkozó alapvető információkat. Az egyes csomópontnevektől balra lévő zöld pipa azt jelzi, hogy az összes összetevő a csomóponton van. Ha egy összetevő nem érhető el egy csomóponton, a zöld pipa helyett egy piros figyelmeztető háromszög jelenik meg.

![HDInsight Apache Ambari hosts nézet](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Ezután egy csomópont **nevében** kiválaszthat egy csomópont nevét az adott csomópont részletesebb gazdametrikák megtekintéséhez. Ez a nézet az egyes összetevők állapotát/rendelkezésre állását mutatja.

![Az Apache Ambari egyetlen csomópontnézetnek ad otthont](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari riasztások

Ambari is kínál több konfigurálható riasztások, amelyek értesítést bizonyos események. Amikor riasztások aktiválódnak, az Ambari bal felső sarkában jelennek meg egy piros jelvényben, amely a riasztások számát tartalmazza. A jelvény kiválasztásakor megjelenik az aktuális riasztások listája.

![Az Apache Ambari aktuális riasztásai száma](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

A riasztásdefiníciók és állapotuk listájának megtekintéséhez válassza a **Riasztások** lapot, az alábbiak szerint.

![Ambari riasztásdefiníciók nézet](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Az Ambari számos előre meghatározott riasztást kínál a rendelkezésre állással kapcsolatban, többek között:

| Riasztás neve                        | Leírás   |
|---|---|
| DataNode állapot összefoglalása           | Ez a szolgáltatásszintű riasztás akkor aktiválódik, ha nem megfelelő datanodes|
| NameNode magas rendelkezésre állási állapot | Ez a szolgáltatásszintű riasztás akkor aktiválódik, ha az Aktív NameNode vagy a Standby NameNode nem fut.|
| Rendelkezésre álló százaléknaplónodes    | Ez a riasztás akkor aktiválódik, ha a journalnodes a fürtben nagyobb, mint a beállított kritikus küszöbértéket. Összesíti a JournalNode folyamatellenőrzések eredményeit. |
| Rendelkezésre álló adatcsomópontok százalékos száma       | Ez a riasztás akkor aktiválódik, ha a fürtben lévő datanodes-ok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamatellenőrzések eredményeit.|

A teljes listát ambari riasztások, amelyek segítenek nyomon követni a rendelkezésre álló klaszter [megtalálható itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

A riasztás részleteinek megtekintéséhez vagy a feltételek módosításához válassza ki a riasztás **nevét.** Vegyük például **a DataNode Health Summary-ot.** Láthatja a riasztás leírását, valamint azokat a konkrét feltételeket, amelyek "figyelmeztetési" vagy "kritikus" riasztást váltanak ki, valamint a feltételek ellenőrzési időközét. A konfiguráció szerkesztéséhez kattintson **a** Szerkesztés gombra a Konfiguráció párbeszédpanel jobb felső sarkában.

![Apache Ambari riasztási konfiguráció](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Itt szerkesztheti a leírást, és ami még fontosabb, az ellenőrzési időközt és a figyelmeztetési vagy kritikus riasztások küszöbértékeit.

![Ambari riasztási konfigurációk szerkesztése nézet](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Ebben a példában 2 nem megfelelő datanodes kiváltó kritikus riasztást, és 1 nem megfelelő datanode csak egy figyelmeztetést. Ha végzett a szerkesztéssel, válassza a **Mentés** lehetőséget.

### <a name="email-notifications"></a>E-mail-értesítések

Az Ambari-riasztásokhoz e-mail értesítéseket is konfigurálhat. Ehhez a **Riasztások** lapon kattintson a bal felső sarokban lévő **Műveletek** gombra, majd az **Értesítések kezelése parancsra.**

![Ambari értesítéskezelési művelet](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Megnyílik a riasztási értesítések kezelésére szolgáló párbeszédpanel. Válassza **+** ki a párbeszéd alján lévő mezőt, és töltse ki a szükséges mezőket, hogy az Ambari tartalmazza az e-mail kiszolgáló adatait, ahonnan e-maileket küldhet.

> [!TIP]
> Az Ambari e-mail értesítések beállítása jó módja lehet a riasztások fogadásának egy helyen, ha sok HDInsight-fürtöt kezel.

## <a name="azure-monitor-logs-integration"></a>Az Azure Monitor naplóinak integrációja

Az Azure Monitor naplói lehetővé teszik, hogy a több erőforrás, például a HDInsight-fürtök által generált adatok egy helyen legyenek összegyűjtve és összesítve az egységes figyelési élmény elérése érdekében.

Előfeltételként az összegyűjtött adatok tárolásához szüksége lesz egy Log Analytics-munkaterületre. Ha még nem hozott létre egyet, kövesse az alábbi utasításokat: [Log Analytics-munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HdInsight Azure Figyelő naplóinak integrációjának engedélyezése

A portál HDInsight-fürterőforrás-lapján válassza az **Azure Monitor**lehetőséget. Ezután válassza **az engedélyezés t,** és válassza ki a Log Analytics munkaterületet a legördülő menüből.

![HDInsight operations management csomag](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Lekérdezési mutatók és naplótáblák

Ha az Azure Monitor naplóintegrációja engedélyezve van (ez eltarthat néhány percig), keresse meg a **Log Analytics-munkaterületi** erőforrást, és válassza **a Naplók**lehetőséget.

![Log Analytics-munkaterületi naplók](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

A naplók számos mintalekérdezést sorolnak fel, például:

| Lekérdezés neve                      | Leírás                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Számítógépek elérhetősége ma    | A naplókat küldő számítógépek számának diagramja óránként                     |
| Szívverések listázása                 | Az összes számítógép-szívverés listázása az elmúlt órából                           |
| Az egyes számítógépek utolsó szívverése | Az egyes számítógépek által küldött utolsó szívverés megjelenítése                             |
| Nem elérhető számítógépek           | Az összes olyan ismert számítógép listázása, amely az elmúlt 5 órában nem küldött szívverést |
| Rendelkezésre állási arány               | Az egyes csatlakoztatott számítógépek elérhetőségi sebességének kiszámítása                |

Például futtassa a **rendelkezésre állási arány** minta lekérdezést a **Futtatás** az adott lekérdezésen lehetőség kiválasztásával, ahogy az a fenti képernyőképen látható. Ez a fürt egyes csomópontainak rendelkezésre állási arányát jeleníti meg százalékban. Ha engedélyezte több HDInsight-fürt számára, hogy metrikákat küldjenek ugyanarra a Log Analytics-munkaterületre, akkor a fürtösszes csomópontjának rendelkezésre állási aránya jelenik meg.

![A Log Analytics munkaterületnaplója naplózza a "rendelkezésre állási arány" mintalekérdezést](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> A rendelkezésre állási arány t 24 órás időszakban mérik, így a fürtnek legalább 24 órán át futnia kell, mielőtt pontos rendelkezésre állási díjakat látna.

Ezt a táblázatot a jobb felső sarokban lévő **Pin** gombra kattintva rögzítheti egy megosztott irányítópulton. Ha nem rendelkezik írható megosztott irányítópultokkal, itt láthatja, hogyan hozhat létre egyet: [Irányítópultok létrehozása és megosztása az Azure Portalon.](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)

### <a name="azure-monitor-alerts"></a>Az Azure Monitor riasztásai

Azure Monitor riasztásokat is beállíthat, amelyek akkor aktiválódnak, ha egy metrika értéke vagy egy lekérdezés eredményei megfelelnek bizonyos feltételeknek. Például hozzon létre egy riasztást, hogy küldjön egy e-mailt, ha egy vagy több csomópont nem küldött szívverést 5 órán belül (azaz feltételezhető, hogy nem érhető el).

A **Naplók lapon futtassa**a **Nem elérhető számítógépek** mintalekérdezést a **Futtatás** az adott lekérdezésen lehetőséget választva, az alábbiak szerint.

![A Log Analytics munkaterületnaplója naplózza az "nem elérhető számítógépek" mintáját](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Ha az összes csomópont elérhető, ez a lekérdezés egyelőre nulla eredményt ad vissza. Kattintson **az Új riasztási szabály** gombra a lekérdezés riasztásának konfigurálásához.

![Log Analytics munkaterület új riasztási szabály](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

A riasztásnak három összetevője van: az az *erőforrás,* amelyhez létre kell hoznia a szabályt (ebben az esetben a Log Analytics-munkaterület), a riasztás aktiválásának *feltétele,* és a *műveletcsoportok,* amelyek meghatározzák, hogy mi fog történni a riasztás aktiválásakor.
Kattintson a **feltétel címére**, az alábbiak szerint a jellogika beállításának befejezéséhez.

![Portálriasztás létrehozási szabályfeltétele](media/hdinsight-cluster-availability/portal-condition-title.png)

Ekkor megnyílik **A jellogika konfigurálása**.

Állítsa be a **Riasztáslogika** szakaszt az alábbiak szerint:

*A következők alapján: Eredmények száma, Feltétel: Nagyobb, mint, Küszöbérték: 0.*

Mivel ez a lekérdezés csak nem érhető el csomópontok eredményként, ha az eredmények száma valaha is nagyobb, mint 0, a riasztást kell a tűz.

A **Kiértékelés szakaszban** állítsa be az **időszakot** és a **gyakoriságot** annak alapján, hogy milyen gyakran szeretné ellenőrizni a nem elérhető csomópontokat.

A riasztás céljából győződjön meg arról, **period=gyakoriság.** Az időszakról, a gyakoriságról és más riasztási paraméterekről [itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)olvashat bővebben.

Válassza a **Kész** lehetőséget, ha befejezte a jellogika konfigurálását.

![A riasztási szabály konfigurálja a jellogikát](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Ha még nem rendelkezik meglévő műveletcsoporttal, kattintson az **Új létrehozása gombra** a **Műveletcsoportok** szakaszban.

![A riasztási szabály új műveletcsoportot hoz létre](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Ekkor megnyílik **a Művelethozzáadása csoport**. Válasszon **egy műveletcsoport nevét,** **rövid nevet**, **előfizetést**és **erőforráscsoportot.** A **Műveletek csoportban** válasszon egy **műveletnevet,** és válassza **az E-mail/SMS/Push/Voice** lehetőséget **művelettípusként.**

> [!NOTE]
> Számos más műveletek egy riasztást is aktiválhat mellett egy e-mail/SMS/Push/Voice, például egy Azure-függvény, LogicApp, Webhook, ITSM és Automation Runbook. [tudj meg többet.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Ez megnyitja **e-mail / SMS / Push / Voice**. Válassza ki a címzett **nevét,** **jelölje be** az **E-mail jelölőnégyzetet,** és írja be azt az e-mail címet, amelyre a riasztást el szeretné küldeni. Válassza az **OK gombot** **az E-mailben/SMS/Push/Voice-ban,** majd a **Művelethozzáadása csoportban** a műveletcsoport konfigurálásának befejezéséhez.

![A riasztási szabály hozzáadási műveletcsoportot hoz létre](media/hdinsight-cluster-availability/portal-add-action-group.png)

Miután ezek a panelek bezárultak, a műveletcsoportnak a Műveletcsoportok szakaszban kell **szerepelnie.** Végül töltse ki a **Riasztás részletei** szakaszt a **riasztási szabály nevének** és **leírásának** beírásával, valamint a **súlyosság kiválasztásával.** A befejezéshez kattintson a **Figyelmeztetési szabály létrehozása gombra.**

![A portál riasztási szabály befejezést hoz létre](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A **súlyosság i. súlyossági szint** megadása hatékony eszköz, amely több riasztás létrehozásakor használható. Létrehozhat például egy riasztást egy figyelmeztetés (Sev 1) kiemeléséhez, ha egy fejcsomópont leáll, és egy másik riasztást, amely kritikus (Sev 0) riasztást ad ki abban a valószínűtlen esetben, ha mindkét főcsomópont leáll.

Ha a riasztás feltétele teljesül, a riasztás kigyullad, és e-mailt kap a riasztás részleteivel, mint ez:

![Példa az Azure Monitor riasztási e-mailre](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

A **naplóelemzési munkaterületen**a **Riasztások** elemre bontva is megtekintheti az összes riasztást, súlyosság szerint csoportosítva.

![Log Analytics-munkaterületi riasztások](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

A súlyossági csoportosítás (azaz a fent kiemelt **Sev 1)** kiválasztása az adott súlyosságú riasztások rekordjait jeleníti meg, amelyek az alábbihoz hasonlóan aktiválódtak:

![A Log Analytics munkaterülete egy riasztást küld](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>További lépések

- [Az Apache Hadoop-fürtök elérhetősége és megbízhatósága a HDInsightban](hdinsight-high-availability-linux.md)
