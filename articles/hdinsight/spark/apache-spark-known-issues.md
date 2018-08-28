---
title: Az Azure HDInsight az Apache Spark-fürt hibáinak elhárítása
description: Ismerje meg az Apache Spark-fürtök Azure HDInsight és az azok megkerüléséhez kapcsolatos problémákat.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 92baa28393100abe3d7694920e5ee327966db927
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048308"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>HDInsight-fürt az Apache Spark ismert problémái

Ez a dokumentum nyomon követi az összes ismert problémák a HDInsight Spark nyilvános előzetes verzió.  

## <a name="livy-leaks-interactive-session"></a>Livy elveszít interaktív munkamenet
Az interaktív munkamenet életben a Livy újraindítását követően (az Ambari vagy átjárócsomópontjával 0 virtuális gép újraindítása miatt), az interaktív feladat munkamenet kiszivárgott. Ennek eredményeképpen új feladatok is elakadt elfogadva állapotban.

**Megoldás:**

Az alábbi eljárás segítségével orvosolhatja a problémát:

1. Ssh átjárócsomópontjával be. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Futtassa a következő parancsot az alkalmazásazonosítót a livy-n keresztül lépések interaktív feladatok található. 
   
        yarn application –list
   
    Az alapértelmezett feladat nevek lesznek Livy, ha a feladatok a Livyvel interaktív munkamenethez indított nincsenek megadva kifejezett nevek. A Jupyter notebook indította Livy-munkamenet, feladat neve kezdődik remotesparkmagics_ *. 
3. Futtassa a következő parancsot ezen feladatok leállítása. 
   
        yarn application –kill <Application ID>

Új feladatok kezdjen programokat futtatni. 

## <a name="spark-history-server-not-started"></a>Spark-Előzménykiszolgáló nem indult el
Spark-Előzménykiszolgáló nem indult el automatikusan a fürt létrehozása után.  

**Megoldás:** 

Indítsa el manuálisan a korábbi kiszolgáló Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>A Spark naplókönyvtár kulcstartóbeli probléma
hdiuser lekérdezi a következő hiba, amikor egy feladat használatával küldjön spark-submit szkripttel:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
És nincs illesztőprogram naplófájlt. 

**Megoldás:**

1. Adja hozzá a Hadoop-csoport hdiuser. 
2. Adja meg a 777 engedélyek /var/log/spark a fürt létrehozása után. 
3. Frissítse az Ambari segítségével lehet 777 engedélyekkel könyvtár spark napló helyét.  
4. Futtassa a spark-submit sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>A Phoenix a Spark összekötő nem támogatott.

HDInsight Spark-fürtök nem támogatják a Spark-Phoenix-összekötő.

**Megoldás:**

A Spark-HBase-összekötő inkább kell használnia. Az utasításokért lásd: [használata Spark-HBase-összekötő](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>A Jupyter notebooks kapcsolatos problémák
Az alábbiakban néhány, a Jupyter notebookok kapcsolatos ismert problémákat.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>A fájlnevekben nem ASCII-karaktereket notebookok
Ne használjon a Jupyter notebook fájlnevek nem ASCII-karaktereket. Ha megpróbálja feltölteni egy fájlt, amely rendelkezik a nem ASCII-fájl nevét, a Jupyter felületen, bármilyen hibaüzenet nélkül sikertelen lesz. Jupyter nem teszi lehetővé a fájl feltöltéséhez, de egy látható hiba nem throw vagy.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Hiba történt a nagyobb méretű notebookok betöltése
Előfordulhat, hogy a hibával találkozik **`Error loading notebook`** Ha a jegyzetfüzet-nál nagyobb terhelése.  

**Megoldás:**

Ha ez a hibaüzenet azt, nem jelenti az adatok sérült vagy elveszett.  A notebookok továbbra is a lemezen vannak `/var/lib/jupyter`, és az SSH a fürtbe érheti el őket. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Miután csatlakozott a fürthöz SSH használatával, másolhatja a notebookok a fürtről a helyi gépen (a szolgáltatáskapcsolódási pont vagy WinSCP) biztonsági mentéséhez a notebookot a fontos adatok elvesztésének megelőzése érdekében. Ezek közül SSH-alagutat az átjárócsomópont-es porton keresztül az átjáró nélkül férhetnek hozzá a Jupyter 8001 be.  Itt törölje a jelet a jegyzetfüzet kimenetét, és mentse el újra a notebook méretének minimalizálása érdekében.

Ez a hiba a jövőbeni megakadályozni, kövesse az ajánlott eljárásokat:

* Fontos, hogy maradjon kicsi a jegyzetfüzet méretét. A Spark-feladatok bármely olyan kimenete, amely küld vissza a Jupyter a notebookot a rendszer megőrzi.  Általában célszerű jupyterrel, Cellafunkciók ajánlott futó elkerülése érdekében `.collect()` on nagy RDD vagy dataframes; helyette, ha szeretne betekintés az RDD tartalmát, érdemes futó `.take()` vagy `.sample()` , hogy a kimenet nem get túl nagy.
* Is amikor menti egy jegyzetfüzetet, törölje az összes kimeneti cellák méretének csökkentése érdekében.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Notebook első indításkor a vártnál hosszabb idő alatt
A Jupyter notebook használatával Spark Magic Quadrant kód utasításnak elsőként több mint egy percet is igénybe vehet.  

**Magyarázat:**

Ez akkor fordul elő, mert az első kódcella futtatásakor. A háttérben munkamenet-konfiguráció- és Spark-, SQL kezdeményez, és a Hive-környezetek. Ezek a környezetek vannak beállítva, az első utasítás futtatása, és azt a benyomást biztosít ez után az utasítás végrehajtásához hosszú időbe telt meg.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter notebook időkorlátot adja meg a munkamenet létrehozása
Ha Spark-fürt kifogyott az erőforrásokból, a Jupyter notebookot a Spark és a PySpark kernelt fogja próbál meg létrehozni a munkamenet időkorlátja. 

**Megoldások:** 

1. A Spark-fürt által az egyes erőforrások felszabadítása:
   
   * Más Spark-jegyzetfüzetek leállítása a Bezárás és Halt menü fog, vagy kattintson a leállítás a notebook Explorerben.
   * Más Spark-alkalmazások, a YARN leállítása.
2. Indítsa újra a notebook próbált elindításához. Elegendő erőforrást hozhat létre egy munkamenetet most elérhetőnek kell lennie.

## <a name="see-also"></a>Lásd még
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

