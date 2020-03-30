---
title: Az Azure HDInsight Apache Spark-fürtjével kapcsolatos problémák elhárítása
description: Ismerje meg az Apache Spark-fürtökkel kapcsolatos problémákat az Azure HDInsightban, és hogyan kerülje meg ezeket.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494536"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Ismert problémák az Apache Spark-fürthöz a HDInsighton

Ez a dokumentum nyomon követi a HDInsight Spark nyilvános előzetes verziójának összes ismert problémája.  

## <a name="apache-livy-leaks-interactive-session"></a>Az Apache Livy kiszivárogtatja az interaktív munkamenetet
Amikor [az Apache Livy](https://livy.incubator.apache.org/) újraindul [(az Apache Ambari-ból](https://ambari.apache.org/) vagy a headnode 0 virtuális gép újraindítása miatt) egy interaktív munkamenettel, amely még mindig él, egy interaktív feladatmunkamenet szivárog ki. Ennek eredményeképpen az új munkahelyek beragadhatnak az Elfogadott állapotba.

**Enyhítés:**

A probléma kerülő megoldásához kövesse az alábbi eljárást:

1. Ssh a headnode. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Futtassa a következő parancsot a Livy-n keresztül indított interaktív feladatok alkalmazásazonosítóinak megkereséséhez.

        yarn application –list

    Az alapértelmezett feladatnevek Livy lesznek, ha a feladatok egy Livy interaktív munkamenettel kezdődtek, explicit nevek nélkül. A [Jupyter Notebook](https://jupyter.org/)által indított Livy munkamenet `remotesparkmagics_*`esetén a feladat neve a alkalmazással kezdődik.

3. Futtassa a következő parancsot, hogy megöli ezeket a feladatokat.

        yarn application –kill <Application ID>

Az új feladatok elindulnak.

## <a name="spark-history-server-not-started"></a>A Spark History Server nem indult el
A Spark History Server nem indul el automatikusan a fürt létrehozása után.  

**Enyhítés:**

Indítsa el manuálisan az előzménykiszolgálót az Ambari szolgáltatásból.

## <a name="permission-issue-in-spark-log-directory"></a>Engedélyprobléma a Spark naplókönyvtárában
hdiuser kap a következő hiba, amikor beküld egy feladatot a spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

És nincs járművezetői napló megírva.

**Enyhítés:**

1. Adja hozzá a hdiusert a Hadoop csoporthoz.
2. 777-es engedélyek megadása a /var/log/spark kapcsolóhoz a fürt létrehozása után.
3. Frissítse a szikranapló helyét az Ambari használatával, hogy 777 engedélyekkel rendelkező könyvtár legyen.  
4. Futtassa a spark-submit-t sudo-ként.  

## <a name="spark-phoenix-connector-is-not-supported"></a>A Spark-Phoenix összekötő nem támogatott

A HDInsight Spark-fürtök nem támogatják a Spark-Phoenix-összekötőt.

**Enyhítés:**

Ehelyett a Spark-HBase összekötőt kell használnia. Az utasításokért olvassa el [a Spark-HBase összekötő használata című témakört.](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)

## <a name="issues-related-to-jupyter-notebooks"></a>A Jupyter notebookokkal kapcsolatos problémák

Az alábbiakban néhány ismert probléma kapcsolatos Jupyter notebookok.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Nem ASCII karakterekkel rendelkező jegyzetfüzetek fájlnevekben

Ne használjon nem ASCII karaktereket a Jupyter notebook fájlneveiben. Ha megpróbál feltölteni egy fájlt a Jupyter felhasználói felületen keresztül, amely nem ASCII fájlnévvel rendelkezik, hibaüzenet nélkül sikertelen. Jupyter nem engedi feltölteni a fájlt, de nem dobja a látható hiba sem.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Hiba nagyobb méretű jegyzetfüzetek betöltése közben

Előfordulhat, hogy **`Error loading notebook`** hibaüzenet jelenik meg, amikor nagyobb méretű jegyzetfüzeteket tölt be.  

**Enyhítés:**

Ha ez a hibaüzenet jelenik meg, az nem jelenti azt, hogy az adatok sérültek vagy elvesztek.  A jegyzetfüzetek továbbra is `/var/lib/jupyter`a lemezen vannak, és az SSH a fürtbe is hozzáférhet. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Miután ssh használatával csatlakozott a fürthöz, a fürtből a jegyzetfüzeteket átmásolhatja a helyi számítógépre (SCP vagy WinSCP használatával) biztonsági másolatként, hogy megakadályozza a fontos adatok elvesztését a notebookban. Ezután ssh alagút a headnode a port 8001 eléréséhez Jupyter anélkül, hogy az átjárón keresztül.  Innen törölheti a notebook kimenetét, és újra mentheti, hogy minimalizálja a jegyzetfüzet méretét.

A hiba jövőbeni előfordulásának megakadályozása érdekében néhány ajánlott eljárásnak a követését kell követnie:

* Fontos, hogy a notebook mérete kicsi legyen. A Spark-feladatok, amelyek a Jupyter-re visszaküldött kimeneti a noszna.  A Jupyter általában ajánlott, hogy ne `.collect()` fusson nagy RDD-ken vagy adatkereteken; ehelyett, ha azt szeretné, hogy kandikál `.take()` egy `.sample()` RDD tartalmát, fontolja meg a futás, vagy úgy, hogy a kimenet nem lesz túl nagy.
* A jegyzetfüzet mentésekor törölje az összes kimeneti cellát a méret csökkentéséhez.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>A notebook kezdeti indítása a vártnál tovább tart

A Jupyter-jegyzetfüzet első kódnyilatkozata a Spark magic használatával több mint egy percet is igénybe vehet.  

**Magyarázat:**

Ez azért történik, mert az első kódcella futtatásakor. A háttérben ez elindítja a munkamenet-konfigurációt, és a Spark, SQL és Hive-környezetek vannak beállítva. A környezetek beállítása után az első utasítás fut, és ez azt a benyomást kelti, hogy a kijelentés hosszú időt vett igénybe.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter notebook időkorlát létrehozása a munkamenet

Ha a Spark-fürt erőforrásokból ki, a Spark és a PySpark kernelek a Jupyter notebook időkorlátot próbál létrehozni a munkamenetet.

**Enyhítése:**

1. Szabadítson fel néhány erőforrást a Spark-fürtben a következő kon:

   * Más Spark-jegyzetfüzetek leállítása a Bezárás és a Leállítás menüben, vagy a Leállítás parancsra kattintva a jegyzetfüzet-kezelőben.
   * Más Spark-alkalmazások leállítása a YARN-tól.

2. Indítsa újra az elindítani kívánt jegyzetfüzetet. Elegendő erőforrásnak kell rendelkezésre állnia ahhoz, hogy most hozzon létre egy munkamenetet.

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)