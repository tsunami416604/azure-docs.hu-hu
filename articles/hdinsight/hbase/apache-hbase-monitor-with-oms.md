---
title: Az Azure Naplóelemzés – az Azure HDInsight HBase figyelése |} Microsoft Docs
description: Azure Log Analytics segítségével figyelheti a HDInsight HBase-fürtökkel.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-hbase-with-log-analytics"></a>A Naplóelemzési figyelő HBase

HDInsight HBase figyelési használja az Azure Naplóelemzés HDInsight HBase teljesítményadatok gyűjtéséhez a HDInsight-fürt csomópontjának. A HBase-specifikus képi megjelenítések és irányítópultokat, a metrikákat, és képes létrehozni az egyéni ellenőrzési szabályok és értesítések eszközök biztosít. Hatékonyan nyomon követheti a metrikákat több HDInsight-HBase-fürtök több Azure-előfizetések között.

A Naplóelemzési rendszer szolgáltatása [Azure](../../operations-management-suite/operations-management-suite-overview.md) , amely figyeli a felhőben és a helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A Naplóelemzési elő az erőforrások a felhőalapú és helyszíni környezetben és egyéb eszközök, így elemzési biztosít több forrásból származó adatokat gyűjt.

[Elemzési megoldásokat jelentkezzen](../../log-analytics/log-analytics-add-solutions.md) funkciókat adnak hozzá Naplóelemzési további adatokat és elemzésére szolgáló eszközöket. Napló elemzési megoldásokat programot, a képi megjelenítés és az adatokat, amely a metrikákat biztosít egy adott területre beszerzési szabályok gyűjteménye. A megoldás is határozhatnak meg új rekordtípusokat gyűjtendő, és ezeket a rekordokat napló keresések vagy új felhasználó csatolófunkcióit elemzése.

[Betekintést & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) a Naplóelemzési platformra épül. Naplóelemzési lehetőségeket, és fizessen / GB okozhatnak a szolgáltatásba, vagy váltson a munkaterület Insight & Analytics csomagra, és kell fizetnie a szolgáltatás által kezelt csomópontonként. Betekintést & Analytics kínál felülbírálja a Naplóelemzési által kínált lehetőségeket. A HBase figyelésére szolgáló megoldás Naplóelemzési vagy a betekintést, és az elemzés érhető el.

Amikor egy HDInsight HBase figyelésére szolgáló megoldás, létre kell hoznia egy Naplóelemzési munkaterület. Egyes munkaterületeken, mint egy egyedi Naplóelemzési környezetben a saját adattárház, az adatforrások és a megoldások. Több munkaterületek hozhatja létre, az előfizetés támogatja a több környezetekben, például üzemi és teszteléséhez.

## <a name="provision-hdinsight-hbase-monitoring"></a>Provision HDInsight HBase figyelése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) használata az Azure-előfizetéshez.
2. Az a **új** ablaktábla **piactér**, jelölje be **figyelés + felügyeleti**.
3. Az a **figyelés + felügyeleti** ablaktáblán válassza előbb **láthatja az összes**.

    ![Figyelési + felügyelet](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. Az átjáróra a listában, keresse meg a **megoldások** sávon. Jobb oldalán **megoldások**, jelölje be **további**.

    ![Figyelési + felügyelet](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. Az a **megoldások** ablaktáblán jelölje ki a HDInsight HBase figyelési felügyeleti megoldást egy munkaterület hozzáadása.

    ![Felügyeleti megoldások ablaktábla](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. A felügyeleti megoldás ablaktáblában tekintse át a a felügyeleti megoldás, és válassza **létrehozása**. 
7. Az a *felügyeleti megoldás neve* ablaktáblában válasszon társítani a felügyeleti megoldás, vagy hozzon létre egy új Naplóelemzési munkaterület meglévő munkaterületet, és állítsa be azt.
8. Az Azure-előfizetéssel, erőforráscsoport és és a megfelelő helyen munkaterület beállításainak módosítása. 
    ![megoldás munkaterület](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Kattintson a **Létrehozás** gombra.  
10. Az új felügyeleti megoldás a munkaterület használatához navigáljon **Naplóelemzési** > ***munkaterületnevet*** > **megoldások**. A felügyeleti megoldás bejegyzést listában jelennek meg. Válassza ki az navigáljon a megoldás.

    ![Napló elemzési megoldások](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. A HDInsight HBase figyelési megoldást jelenik meg.

    ![HDInsight HBase megoldások ablaktábla](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Az összefoglaló csempéket a adatok nem jeleníthetők meg, mert még nincs konfigurálva a HDInsight HBase-fürtöt, szeretnék adatokat küldeni a Naplóelemzési.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>HDInsight HBase-fürtöt csatlakoztatása szolgáltatáshoz

HDInsight HBase figyelési által biztosított eszközöket használja, szüksége a fürt konfigurálásához arra, hogy a metrikák a régió server átjárócsomópontokkal és ZooKeeper csomópontok szolgáltatáshoz továbbítja. Ez a konfiguráció végezhető el a parancsfájlművelet futtatott a HDInsight HBase-fürtöt.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Napló Analytics munkaterület Azonosítóját és kulcsát beolvasása

Napló Analytics munkaterületének Azonosítóját és kulcsát ahhoz, hogy a fürt csomópontja Naplóelemzés szolgáltatással való hitelesítésre van szüksége. Ezek az értékek beolvasása:

1. A HBase figyelés panel az Azure portálon válassza ki a áttekintése.

    ![A HBase figyelési megoldás ablaktábla](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Válassza ki az OMS-portállal egy új böngészőlapon vagy ablakban indítsa el az OMS-portálon.

    ![Válassza ki az OMS-portálon](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Jelölje ki az OMS-portálon otthoni beállításait.

    ![OMS-portál](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Válassza ki a forrás, a Linux-kiszolgálók csatlakoztatva.

    ![Válassza ki a csatlakoztatott adatforrás és a Linux-kiszolgálókon](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Megjegyzés: a munkaterület azonosítója és elsődleges kulcs értékét, jelenik meg, ezeket az értékeket a parancsfájlművelet van szüksége.

### <a name="run-the-script-action"></a>A parancsfájl művelet

Ahhoz, hogy az adatok gyűjtése a HDInsight HBase-fürtöt, a fürt összes csomópontjának egy parancsfájlművelet futtathatók:

1. Nyissa meg a ablaktábla a HDInsight HBase-fürtöt az Azure portálon.
2. Válassza ki **parancsfájl-műveletek**.

    ![A Parancsfájlműveletek](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Válassza ki **új küldési**.

    ![Új elküldése](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. Az a **küldje el a parancsfájl** művelete, a parancsfájl típusát állítva `"- Custom"`.
5. Adja meg a parancsfájl nevét.
6. Az a **Bash parancsfájlok URI**, illessze be a következő URI Azonosítót:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Az a **csomóponttípusok**, jelölje be mindhárom (**Head**, **régió**, **ZooKeeper**).
8. Az a **paraméterek** szövegmezőben adja meg a munkaterület azonosítója és a kulcsát, minden egyes befoglaló idézőjelben értékét, és szóközzel elválasztva.

        "<Workspace ID>" "<Workspace Key>"

9. Válassza ki **parancsfájlműveletet** kattintva futtassa újra a művelet, amikor új csomópontokat ad hozzá a fürtöt.

    ![Parancsfájl naplózásnál beállításai](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Kattintson a **Létrehozás** gombra.
11. A parancsfájlművelet futtatásához néhány percet vesz igénybe. A Parancsfájlműveletek panelről állapotát nyomon követheti.

    ![A parancsfájlművelet fut](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. A parancsfájl művelet befejeződése után meg kell jelennie egy zöld pipa jelzi a parancsfájl neve a listaelem mellett.

    ![Parancsfájl a művelet befejeződött](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>A HDInsight HBase figyelésére szolgáló megoldás megtekintése

A parancsfájl művelet befejeződése után megtekintheti az adatokat a figyelés megoldás néhány percen belül.

1. Az Azure portálon keresse meg a HDInsight HBase megoldás ablaktáblán.
2. Válassza ki a **áttekintése** fülre.
3. A **összegzés**, megjelenik egy csempe figyelt régió kiszolgálók számát.

    ![Figyelési összefoglalás Csempéje](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Válassza ki a csempe, amely a régió kiszolgálók számát. A fő irányítópult jelenik meg.
5. Ezt az irányítópultot a régiókban, használja az írási előre napló (WAL) száma, Log Analyticshez keresések gyűjteménye statisztikája hozzáférést biztosít (mint például a régió kiszolgáló naplózza, Phoenix naplókat, és kivételeket) és a népszerű diagramok megjelenítéséhez vonatkozó gyűjteménye metrikákat. 

    ![Fő irányítópult](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Ezek közül kiválasztásával fog részletekbe menően tárhatják fel a napló keresési nézetben, ahol pontosítsa a lekérdezést, és áttekintheti a részletes adatokat.

## <a name="next-steps"></a>További lépések

* [A Naplóelemzési riasztásokat hoznak létre](../../log-analytics/log-analytics-alerts-creating.md)
* [Napló keresések rendelkező adatok kereséséhez az Azure Naplóelemzés](../../log-analytics/log-analytics-log-searches.md).
