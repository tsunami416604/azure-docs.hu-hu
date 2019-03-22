---
title: Az Apache Ambari Tez nézet használata a HDInsight – Azure
description: Ismerje meg, az Apache Ambari Tez nézet használata a HDInsight a Tez-feladatok hibakereséséhez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 7d8a654b6b2f429119323fa5d18eb0596f63e6f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105267"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Az Apache Ambari-nézetek használata a HDInsight Apache Tez-feladatok hibakereséséhez

A [Apache Ambari](https://ambari.apache.org/) webes felhasználói Felületet, a HDInsight tartalmaz egy [Apache TEZ](https://tez.apache.org/) megtekintése és használata a Tez-feladatok hibakereséséhez használható. A Tez nézet lehetővé teszi a feladatban egy grafikont a csatlakoztatott elemek megjelenítése, minden egyes cikk részletesen és statisztikák és a naplózási információk lekéréséhez.

> [!IMPORTANT]  
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight összetevők verziószámozása](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek

* Egy Linux-alapú HDInsight-fürt. A fürt létrehozásának lépéseiért lásd: [Linux-alapú HDInsight használatának első lépései](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Egy HTML5-támogatással rendelkező modern webböngésző.

## <a name="understanding-apache-tez"></a>Az Apache Tez ismertetése

Tez adatfeldolgozás az Apache Hadoop, amely nagyobb sebességre képes, mint a hagyományos MapReduce feldolgozási biztosít egy bővíthető keretrendszer. A Linux-alapú HDInsight-fürtök esetén az alapértelmezett szolgáltatás a Hive.

Tez létrehoz egy irányított aciklikus Graph (DAG), amely leírja a feladatok által szükséges műveletek sorrendjét. Egyes műveleteket hívják csúcspontokat, és hajtsa végre a teljes feladat egy részét. A tényleges végrehajtása a csúcspont által leírt munka feladat neve, és előfordulhat, hogy legyen elosztva a fürtben több csomóponton.

### <a name="understanding-the-tez-view"></a>A Tez nézet ismertetése

A Tez nézet futó folyamatok, egyaránt előzményadatokat, és információkat nyújt. Ezeket az információkat jeleníti meg, hogyan egy feladatot a fürtök elosztva. Feladatok és a csúcspontok által használt számlálókat, és a feladathoz kapcsolódó hibaadatok is megjeleníti. Felajánlhatja, hogy a következő esetekben hasznos információkat:

* Figyelés hosszú ideig futó folyamatok, a térkép állapotának megtekintése és csökkentési feladatokat.
* Megtudhatja, hogyan lehet javítani, feldolgozás vagy miért volt sikertelen sikeres vagy sikertelen folyamatok előzményadatok elemzése.

## <a name="generate-a-dag"></a>Hozzon létre egy DAG

A Tez nézet csak adatokat tartalmaz, ha egy feladatot, használja a Tez-motor fut, vagy lett korábban lefutott. Egyszerű Hive-lekérdezések használata a Tez nélkül feloldható. Összetettebb lekérdezi, hogy tegye szűrést, csoportosítás, rendezése, illesztések stb. a Tez motort használják.

A következő lépések használatával, amely Tez Hive-lekérdezések futtatásához:

1. A böngészőben navigáljon a https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a HDInsight-fürt neve.

2. Az oldal tetején lévő menüben válassza a **nézetek** ikonra. Ez az ikon egy sorozat négyzetes tűnik. Válassza ki a legördülő listában megjelenő, **Hive-nézet**.

    ![Hive-nézetek kijelölése](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Ha a Hive-nézet betölti, illessze be a következő lekérdezést a Lekérdezésszerkesztő, és kattintson a **végrehajtása**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    A feladat befejezését követően kimenetnek kell megjelennie a jelenik meg a **lekérdezési folyamat eredményei** szakaszban. Az eredmények az alábbi szöveghez hasonló lesz:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Válassza ki a **Log** fülre. Az alábbi szöveghez hasonló információt jelenik meg:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Mentse a **alkalmazásazonosító** értékét, mivel ezt az értéket használja a következő szakaszban.

## <a name="use-the-tez-view"></a>A Tez-nézet használata

1. Az oldal tetején lévő menüben válassza a **nézetek** ikonra. Válassza ki a legördülő listában megjelenő, **Tez nézet**.

    ![Tez nézetek kijelölése](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. A Tez nézet betöltésekor láthatja a fürtön, amely jelenleg fut, vagy hive-lekérdezések listáját futottak.

    ![Minden adatbázis-elérhetőségi csoport](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Ha csak egy bejegyzést, akkor a lekérdezéshez, amely az előző szakaszban futtatta. Ha több, az oldal tetején a mezők alapján kereshet.

4. Válassza ki a **Lekérdezésazonosítóval** a Hive-lekérdezést. A lekérdezés információkat jelenít meg.

    ![DAG-részletek](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Ezen a lapon lévő lapok engedélyezése a következő információkat tekintheti meg:

   * **Lekérdezési adatok**: A Hive-lekérdezés részleteit.
   * **Idősor**: Információ arról, hogy mennyi ideig tartott feldolgozási minden egyes fázisa.
   * **Konfigurációk**: Ehhez a lekérdezéshez használt konfigurációja.

     A __lekérdezés részletei__ hivatkozások segítségével talál információkat a __alkalmazás__ vagy a __DAG__ ehhez a lekérdezéshez.
    
   * A __alkalmazás__ hivatkozásra a YARN-alkalmazást ehhez a lekérdezéshez információit jeleníti meg. Innen érheti el a YARN-alkalmazásnaplók.
   * A __DAG__ hivatkozás ehhez a lekérdezéshez irányított aciklikus diagramhoz információit jeleníti meg. Itt megtekintheti a DAG grafikus ábrázolását. A DAG belül a csúcspontokat információk is megtalálhatók.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan használható az Apache Tez nézet, tudjon meg többet [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md).

Részletes technikai információkat az Apache Tez, tekintse meg a [Apache Tez lapot a Hortonworks](https://hortonworks.com/hadoop/tez/).

Az Apache Ambari használata a HDInsight további információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
