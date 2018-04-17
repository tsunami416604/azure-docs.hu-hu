---
title: Ambari Tez nézet használata a HDInsight - Azure |} Microsoft Docs
description: Útmutató az Ambari Tez nézet használata a HDInsight-on Tez feladatokhoz.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 98874377f31a435e7dd9736410c123ef623928d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Az Ambari nézetek használata a HDInsight-on Tez feladatokhoz

Az Ambari webes felhasználói felületén, a HDInsight a Tez nézetet tartalmaz, amely megértéséhez, valamint a Tez használó feladatok debug használható. A Tez nézet lehetővé teszi a feladathoz egy grafikonon csatlakoztatott elemek megjelenítése, egyes elemek elemezze és statisztika és naplózási információk lekérdezéséhez.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú HDInsight-fürtöt. A fürt létrehozásának lépései: [Ismerkedés a Linux-alapú HDInsight eszközzel](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Egy HTML5-támogatással rendelkező modern webböngésző.

## <a name="understanding-tez"></a>Tez ismertetése

Tez egy bővíthető keretrendszer, amely a hagyományos MapReduce feldolgozási-nál nagyobb sebesség biztosítja az adatok feldolgozásához. Linux-alapú HDInsight-fürtök az alapértelmezett szolgáltatás a Hive esetén.

Tez létrehoz egy irányított aciklikus diagramhoz (DAG), amely leírja a feladatok által szükséges műveletek sorrendjét. Egyes műveletek csúcsban hívják, és egy adat, a teljes feladat végrehajtása. A munkahelyi csúcspont szerint tényleges végrehajtása feladat neve, és előfordulhat, hogy a fürt több csomópontja legyen elosztva.

### <a name="understanding-the-tez-view"></a>A Tez nézet ismertetése

A Tez nézet futó folyamatok előzményadatok és információkat is biztosít. Ezeket az információkat jeleníti meg, hogyan oszlik meg a feladat más fürtökre. Feladatok és a csúcsban által használt számlálókat, és a feladathoz kapcsolódó hibainformációk is megjeleníti. Felajánlhatja, hogy a következő esetekben hasznos információkat:

* Figyelési hosszan futó dolgozza fel a térkép állapotának megtekintése, és a feladatokat.
* Megtudhatja, hogyan lehet javítani, feldolgozás, vagy sikertelenségének sikeres vagy sikertelen folyamatok előzményadatainak elemzése.

## <a name="generate-a-dag"></a>Egy dag-csoport létrehozása

A Tez nézet csak adatokat tartalmaz, ha egy feladatot, amely használja a Tez motor éppen fut, vagy már korábban lefutott. Egyszerű Hive-lekérdezéseket is feloldható Tez használata nélkül. Összetettebb lekérdezi, hogy tegye szűrés csoportosítás, rendezés, illesztéseket, stb. a Tez motort használja.

Az alábbi lépések segítségével által használt Tez Hive-lekérdezések futtatása:

1. Egy böngészőben navigáljon https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a HDInsight-fürt neve.

2. Az oldal tetején a menüből válassza ki a **nézetek** ikonra. Ez az ikon négyzetes több tűnik. Válassza ki a legördülő listában megjelenő, **Hive view**.

    ![Hive-nézetek kijelölése](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Ha a Hive nézete betölti, illessze be a következő lekérdezés a lekérdezés-szerkesztő be, és kattintson **hajtható végre**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    A feladat befejezése után a megjelenő kimenetnek kell megjelennie a **lekérdezési folyamat eredményei** szakasz. Az eredmény az alábbihoz hasonló legyen:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Válassza ki a **napló** fülre. Információ az alábbihoz hasonló jelenik meg:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Mentse a **alkalmazásazonosító** érték, mivel ez az érték a következő szakaszban használható.

## <a name="use-the-tez-view"></a>A Tez nézetben

1. Az oldal tetején a menüből válassza ki a **nézetek** ikonra. Válassza ki a legördülő listában megjelenő, **Tez nézet**.

    ![Tez nézet kijelölése](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. A Tez nézet betöltésekor megjelenik, amely jelenleg fut, vagy hive-lekérdezések listáját a fürtön futottak.

    ![Minden DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Ha csak egy bejegyzés, a lekérdezés, amely futtatta az előző szakaszban is. Ha több bejegyzést, az oldal tetején a mezők használatával kereshet.

4. Válassza ki a **Lekérdezésazonosítóval** a Hive-lekérdezések. A lekérdezés információkat jelenít meg.

    ![DAG-részletek](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Ezen a lapon a lapok engedélyezi, hogy a következő információk megtekintése:

    * **Részletei lekérdezni**: a Hive-lekérdezés részleteiről.
    * **Az idősor**: mennyi ideig tartott minden szakaszhoz feldolgozási információt.
    * **Konfigurációk**: ehhez a lekérdezéshez használt konfigurációt.

    A __lekérdezés részletei__ hivatkozások segítségével talál információkat a __alkalmazás__ vagy a __DAG__ ehhez a lekérdezéshez.
    
    * A __alkalmazás__ hivatkozás ehhez a lekérdezéshez a YARN alkalmazással kapcsolatos információkat jeleníti meg. Itt a YARN alkalmazásnaplók végezheti el.
    * A __DAG__ hivatkozás ehhez a lekérdezéshez irányított aciklikus diagramhoz információit jeleníti meg. Itt megtekintheti a DAG grafikus ábrázolása. A DAG belül a csúcsban információk is megtalálhatók.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan használja a Tez, további információ [használata a HDInsight Hive](hadoop/hdinsight-use-hive.md).

Részletesebb műszaki információkat Tez, lásd: a [Hortonworks Tez lapon](http://hortonworks.com/hadoop/tez/).

Az Ambari és a HDInsight együttes használatával további információkért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
