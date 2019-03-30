---
title: Fürt rendelkezésre állása és az Ambari és az Azure Monitor naplók figyelése
description: Ismerje meg, hogyan használhatja az Ambari, és az Azure Monitor naplózza a fürt állapotának és rendelkezésre állás monitorozása.
keywords: figyelés, az ambari, figyelő, a log analytics, a riasztás, rendelkezésre állást és egészségügyi
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 9bf0c0f45007f81312ac08230d571b727220bcd9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671331"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Fürt rendelkezésre állása és az Ambari és az Azure Monitor naplók figyelése

HDInsight-fürtök tartalmazzák az Apache Ambari, amely gyors és előre meghatározott riasztások állapotinformációkat biztosít, és is lekérdezhető metrikákat és naplókat, valamint a konfigurálható riasztásokat biztosít az Azure Monitor naplók integrációja.

Ez a dokumentum bemutatja, hogyan használhatja ezeket az eszközöket a fürt monitorozásához és Ambari riasztás konfigurálása, a Figyelés csomópont rendelkezésre állási aránya és hoz létre az Azure Monitor-riasztást, amely akkor aktiválódik, ha nem érkezett szívverés egy vagy több csomópont néhány példán keresztül mutatja be az öt órában.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Irányítópult

Az Ambari irányítópult kattintva érhetők el a **otthoni Ambari** hivatkozásra a **fürt irányítópultjai** a HDInsight áttekintés panelen látható az Azure portal, az alábbi szakasza. Másik megoldásként is elérhető a böngészőben a következő URL-cím megadásával [https://\<clustername\>. azurehdinsight.NET formátumban van](https://clustername.azurehdinsight.net/)

![HDInsight portál erőforrásnézetében](media/hdinsight-cluster-availability/portal-overview.png)

Ezután kéri a fürt bejelentkezési felhasználóneve és jelszava. A hitelesítő adatokat adja meg a fürt létrehozásakor választott.

Majd megnyílik az Ambari irányítópultot, amely néhány olyan metrikákkal, adjon meg egy rövid áttekintést nyújt a HDInsight-fürt megjelenítő widgeteket tartalmazza. Az ezeken a widgeteken élő Adatcsomópontok (munkavégző csomópontok) és JournalNodes (zookeeper-csomópont), például metrikák megjelenítése (fő csomópontok) NameNodes hasznos üzemidőt garantál, bizonyos fürt riasztástípusokra jellemző jól mérőszámokként, például YARN ResourceManager-os üzemidő a Spark és Hadoop-fürtöket.

![Ambari-irányítópult](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Gazdagépek – az egyes csomópontok állapotának megtekintése

Megtekintheti az egyes csomópontok állapotinformációit. Kattintson a **gazdagépek** fülre a fürt összes csomópontja listájának megtekintéséhez és alapvető információkat szeretne az egyes csomópontok látni. Bal oldalán található minden egyes csomópont nevét a zöld pipa jelzi, hogy az összes összetevő fel a csomóponton. Ha egy összetevő nem működik egy csomóponton, látni fogja a zöld pipa helyett egy piros háromszöggel.

![Az Ambari gazdagépek megtekintése](media/hdinsight-cluster-availability/ambari-hosts.png)

Ezután kattintson a a **neve** gazdagép metrikái, hogy az adott csomópont részletesebb megtekintéséhez egy csomópont. Ez a nézet állapotának és rendelkezésre állását jeleníti meg minden egyes összetevő.

![Az Ambari gazdagépek egyetlen csomópont nézet](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-riasztások

Az Ambari számos konfigurálható riasztások által biztosított egyes események szóló értesítés is kínál. A figyelmeztetéseket, ha azok egy piros jelvény, amely tartalmazza a riasztások számát az Ambari bal felső sarokban látható. Ez a jelvény kattintva aktuális riasztások listáját tartalmazza.

![Ambari-riasztások száma](media/hdinsight-cluster-availability/ambari-alerts.png)

Riasztásdefiníciók és azok állapotát listájának megtekintéséhez kattintson a **riasztások** lapon, a lent látható módon.

![Az Ambari riasztások definícióit megtekintése](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Az Ambari kínál számos előre meghatározott riasztások rendelkezésre állásával, többek között:

| Riasztás megnevezése                        | Leírás                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode állapot összegzése           | A szolgáltatásiszint-riasztás akkor aktiválódik, ha nem megfelelő állapotú DataNodes                                                                                                                |
| NameNode magas rendelkezésre állás állapota | A szolgáltatásiszint-riasztás akkor aktiválódik, ha a NameNode aktív vagy készenléti NameNode nem futnak.                                                                              |
| Százalékos JournalNodes érhető el    | Ez a riasztás akkor aktiválódik, ha száma JournalNodes le a fürtben nem nagyobb a beállított kritikus küszöbértéknél. Gyűjti JournalNode folyamat ellenőrzések eredményeit. |
| Százalékos DataNodes érhető el       | Ez a riasztás akkor aktiválódik, ha száma DataNodes le a fürtben meghaladja a beállított kritikus küszöbértéket. Gyűjti DataNode folyamat ellenőrzések eredményeit.       |

Az Ambari teljes listáját, riasztást küld, hogy egy fürt rendelkezésre állásának található Súgó figyelő [Itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Adott riasztás részleteinek megtekintéséhez, vagy módosítani a feltételeket, kattintson a **neve** a riasztás. Igénybe **DataNode állapot összegzése** példaként. Láthatja, hogy a riasztást, valamint az adott feltételeknek, amelyek kiváltják a "figyelmeztetés" vagy "kritikus" riasztás és az ellenőrzési időköz feltétel leírását. A konfiguráció szerkesztéséhez kattintson a **szerkesztése** gombot a konfigurációs képernyő jobb felső sarkában.

![Az Ambari riasztási konfigurációja](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Itt módosíthatja a leírást, és ami még fontosabb, az ellenőrzés gyakorisága és a figyelmeztetési vagy kritikus riasztások küszöbértékeit.

![Az Ambari riasztási konfigurációs szerkesztési nézete](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Ebben a példában a kritikus riasztást és 1 nem megfelelő állapotú DataNode egyetlen eseményindító figyelmeztetés 2 nem megfelelő állapotú DataNodes sikerült. Kattintson a **mentése** Ha elkészült szerkesztését.

### <a name="email-notifications"></a>E-mail-értesítések

Az Ambari riasztásokról szóló e-mail értesítések is igény szerint konfigurálható. Ehhez, mikor a a **riasztások** lapra, majd a **műveletek** gombra a bal felső sarokban, majd a **értesítések kezelésére.**

![Az Ambari értesítések művelet kezelése](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Riasztási értesítések kezelésére szolgáló párbeszédpanel nyílik meg. Kattintson a **+** alján, a párbeszédpanelen, és töltse ki a kötelező mezőket az Ambari az e-mailben adja meg a kiszolgáló adatait, amelyről e-mailek küldése.

> [!TIP]
> Az Ambari beállítása e-mail-értesítések segítségével egy helyen értesítést kapni, amikor több HDInsight-fürtök kezelése is lehet.

## <a name="azure-monitor-logs-integration"></a>Az Azure Monitor-naplók integrációja

Az Azure Monitor több erőforrások, például a HDInsight-fürtökben, így gyűjtött és a egy helyen való elérése egy egységes felügyeleti élmény összesített által létrehozott lehetővé teszi, hogy adatokat naplózza.

Egy előfeltétel szüksége lesz a Log Analytics-munkaterület a gyűjtött adatok tárolásához. Ha Ön még nem hozott létre egyet, követheti az itt leírt utasításokat: [Hozzon létre egy Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight az Azure Monitor-naplók integrációja

A HDInsight fürt erőforrás oldalon a portálon kattintson a **Operations Management Suite** panelen. Kattintson a **engedélyezése** , és válassza ki a Log Analytics-munkaterületet a legördülő listából.

![HDInsight Operations Management Suite panel](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>A naplók panelen metrikák és naplók táblák lekérdezése

Az Azure Monitor log-integráció engedélyezése után (Ez eltarthat néhány percig), keresse meg a **Log Analytics-munkaterület** erőforrást, majd kattintson a a **naplók** panel

![Log Analytics munkaterület naplók panel](media/hdinsight-cluster-availability/portal-logs.png)

A **naplók** panel felsorolja egy minta-lekérdezések száma, például:

| A lekérdezés nevét                      | Leírás                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Számítógépek rendelkezésre állás ma    | Diagram küldeni a naplókat, minden egyes óráért számítógépek száma                     |
| A szívverések listája                 | Az összes számítógép szívverés az elmúlt órában a listában                           |
| Mindegyik számítógép a legutóbbi szívverés | Az utolsó szívverés minden számítógép által küldött megjelenítése                             |
| A számítógép nem érhető el           | Nem küld szívverést az elmúlt 5 órában ismert számítógépek listája |
| Rendelkezésre állás               | A rendelkezésre állási aránya minden csatlakoztatott számítógép kiszámítása                |

Tegyük fel, futtassa a **elérhetőségű** mintalekérdezés kattintva **futtatása** a lekérdezés a fenti képernyőképen látható módon. Ez jelennek meg a rendelkezésre állási aránya az egyes csomópontok a fürtben százalékos értékként. Ha engedélyezte a több HDInsight-fürtöket mérőszámok küldése a Log Analytics munkaterületén, látni fogja a rendelkezésre állási arány jelenik meg ezen fürtök összes csomópontján.

![Log Analytics munkaterület naplók panel "rendelkezésre állási árfolyam" mintalekérdezés](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Rendelkezésre állás, a fürt legalább 24 órán keresztül, még nem jelenik meg a pontos rendelkezésre állási díjak futtatnia kell egy 24 órás időszakban mérjük.

Ez a táblázat egy megosztott irányítópulton is rögzíthet kattintva **PIN-kód** jobb felső sarokban. Ha nem rendelkezik írható megosztott irányítópulttal, hogyan hozhat létre egyet látható itt: [Az Azure Portalon irányítópultok létrehozása és megosztása](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Az Azure Monitor riasztások

Is állíthat be az Azure Monitor riasztások, amelyek indítási feltétel metrika értékét, vagy a lekérdezési eredmények megfelelnek bizonyos feltételeknek. Tegyük fel, hozzunk létre egy riasztást küldjön egy e-mailt, ha egy vagy több csomópontja nem küldtek szívverést 5 óra (azaz feltételezzük, hogy nem érhető el).

Az a **naplók** panelen, futtassa a **számítógépek elérhetetlenné** mintalekérdezés kattintva **futtatása** , ahogy az alábbi lekérdezés a.

![Log Analytics munkaterület naplók panel "számítógép nem érhető el" mintalekérdezés](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Ha minden csomópont elérhető, a lekérdezés adja vissza 0 eredmény most. Kattintson a **Új riasztási szabály** a riasztást ehhez a lekérdezéshez konfigurálásának megkezdéséhez.

![Log Analytics munkaterület új riasztási szabály](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Riasztás három összetevőből áll: a *erőforrás* , amelyek esetében szeretne létrehozni a szabályt (a Log Analytics-munkaterületet ebben az esetben), a *feltétel* a riasztást kiváltó és a *Műveletcsoportok*  amelyek meghatározzák, hogy mi történik, ha a riasztás akkor aktiválódik.

Kattintson a **cím feltétel**, ahogy az alábbi, a jel logikájának konfigurálásának befejezéséhez.

![Riasztási szabály a feltétel](media/hdinsight-cluster-availability/portal-condition-title.png)

Ekkor megnyílik a **jellogika konfigurálása** panelen.

Állítsa be a **riasztási logika** szakasz az alábbiak szerint:

*Alapján: Az eredmények, a feltétel száma: Meghaladja a küszöbértéket: 0.*

Mivel ez a lekérdezés csak nem érhető el csomópontokat, eredményként, ha adja vissza eredmények száma minden eddiginél nagyobb, mint 0, a riasztás kell elindítani.

Az a **alapján Evaluated** szakaszában a **időszak** és **gyakorisága** alapján milyen gyakran szeretné ellenőrizze a csomópontok nem érhető el.

Megjegyzés: Ez a riasztás céljából kívánja győződjön meg arról, hogy **időtartam = gyakorisága.** Időszak, gyakoriságát és más riasztási paraméterekkel kapcsolatos további információ található [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Kattintson a **kész** Ha befejezte a jellogika konfigurálása.

![Riasztási szabály jellogika konfigurálása](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Ha Ön még nem rendelkezik meglévő műveletcsoport, kattintson a **hozzon létre új** alatt a **Műveletcsoportok** szakaszban.

![Riasztási szabály új műveletcsoport](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Ekkor megnyílik a **műveleti csoport hozzáadása** panelen. Válasszon egy **műveletcsoport neve**, **rövid, nevet**, **előfizetés**, és **erőforráscsoportot.** Alatt a **műveletek** válassza egy **műveletnév** válassza **e-mailek és SMS és leküldéses/Hangvétel** , a **művelet típusa.**

> [!NOTE]
> Van néhány egyéb művelet, riasztást egy e-mailek és SMS és leküldéses/Hangvétel, például egy Azure-függvény, LogicApp, Webhookot, ITSM és Automation-Runbook mellett is aktiválhatja. [tudj meg többet.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Ekkor megnyílik a **e-mailek és SMS és leküldéses/Hangvétel** panelen. Válasszon egy **neve** a címzett **ellenőrizze** a **E-mail** mezőbe, majd írjon be egy e-mail címet, amely a riasztást küldje kívánja. Kattintson a **OK** a a **e-mailek és SMS és leküldéses/Hangvétel** panelen ezt a a **műveleti csoport hozzáadása** panelen a műveleti csoport konfigurálásának befejezéséhez.

![Riasztási szabály műveletcsoport hozzáadása](media/hdinsight-cluster-availability/portal-add-action-group.png)

Után zárja be a paneleket, megjelenik a műveletcsoport felsorolva a **Műveletcsoportok** szakaszban. Végül, végezze el a **riasztás részleteinek** szakaszban írja be egy **riasztási szabály neve** és **leírása** kiválasztása, és egy **súlyossági**.
Kattintson a **riasztási szabály létrehozása** befejezéséhez.

![A Befejezés riasztási szabály létrehozása](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Meghatározhatja, hogy **súlyossági** egy hatékony eszköz, amely több riasztás létrehozásakor használható. Például létrehozhat egy riasztás emelése figyelmeztetés (Sev 1), ha csak egy átjárócsomóponttal megfelelően le, és a egy új riasztás, amely kiváltja a kritikus (Sev 0) az eseményben nem valószínű, hogy mind az átjárócsomópontokhoz leáll.

A riasztás feltétele teljesült, ha a riasztás akkor aktiválódik, és egy e-mailt az ehhez hasonló a riasztás részleteit:

![Az Azure Monitor riasztási e-mail](media/hdinsight-cluster-availability/alert-email.png)

Minden riasztás kiváltó rendelkezik, a súlyosság szerint csoportosítva megtekintheti a **riasztások** paneljén a **Log Analytics-munkaterület**.

![Log Analytics-munkaterület riasztások](media/hdinsight-cluster-availability/portal-alerts.png)

Súlyosság csoportosítása kattintva (azaz **Sev 1,** , a fent kiemelt) jelennek meg az összes riasztáshoz adott súlyossággal rendelkezik, amely alatt történt például rekordok:

![Log Analytics-munkaterület sev 1 riasztások](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>További lépések
- [Rendelkezésre állás és megbízhatóság, a HDInsight Apache Hadoop-fürtök](hdinsight-high-availability-linux.md)