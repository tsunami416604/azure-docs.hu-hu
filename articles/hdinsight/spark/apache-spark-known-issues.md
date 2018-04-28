---
title: Az Azure HDInsight az Apache Spark-fürt elhárítása |} Microsoft Docs
description: További tudnivalók az Apache Spark on Azure HDInsight és azok megkerülő fürtökkel kapcsolatos problémák.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 664c97117de793209007843fa23c98f52c2b079d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>A HDInsight az Apache Spark-fürt kapcsolatos ismert problémák

Ez a dokumentum nyomon követi az összes ismert problémák a HDInsight Spark nyilvános előzetes verzióhoz.  

## <a name="livy-leaks-interactive-session"></a>Livy szivárgást interaktív munkamenet
Újraindításakor Livy (az Ambari vagy headnode 0 virtuális gép újraindítása miatt) egy interaktív munkamenet-életben egy interaktív feladat munkamenet kiszivárgott. Ennek eredményeképpen új feladatokat is elakadt a elfogadott állapotban.

**Megoldás:**

Az alábbi eljárás segítségével orvosolhatja a problémát:

1. Ssh headnode be. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő parancsot a Livy keresztül elindított interaktív feladatokat az alkalmazás azonosítóit kereséséhez. 
   
        yarn application –list
   
    Az alapértelmezett feladat neve lesz Livy, ha a feladatok nincs explicit módon megadott nevű egy Livy interaktív munkamenet lett elindítva. A Jupyter notebook indította Livy munkamenet, feladat neve kezdődik remotesparkmagics_ *. 
3. A következő parancsot a kill ezeket a feladatokat. 
   
        yarn application –kill <Application ID>

Új feladatok elinduló. 

## <a name="spark-history-server-not-started"></a>Spark előzmények kiszolgáló nem indult el
Spark előzmények kiszolgáló nem automatikusan elindul a fürt létrehozása után.  

**Megoldás:** 

Manuálisan indítsa el az előzmények server Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>A Spark naplókönyvtár engedély probléma
hdiuser lekérdezi a következő hiba, ha egy feladat használatával elküldése spark-nyújt:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
Nevével, és nincs illesztőprogram napló. 

**Megoldás:**

1. Hdiuser hozzáadása a Hadoop-csoporthoz. 
2. Adja meg a 777 engedélyek /var/log/spark a fürt létrehozása után. 
3. Frissítse az Ambari segítségével lehet 777 engedélyekkel könyvtár spark napló helyét.  
4. Futtassa a spark-nyújt, sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>A Spark-Phoenix összekötő nem támogatott

HDInsight Spark-fürtök nem támogatják a Spark-Phoenix összekötő.

**Megoldás:**

A Spark-HBase-összekötő kell helyette használni. Az utasításokért lásd: [használata Spark-HBase-összekötő](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter notebookok kapcsolatos problémák
Az alábbiakban néhány Jupyter notebookok kapcsolatos ismert problémák.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>A fájlnevek nem ASCII-karaktereket notebookokban
Ne használjon nem ASCII-karaktereket a Jupyter notebook elnevezése. Ha mégis megpróbálja feltölteni a fájlt a Jupyter felhasználói felületen, amelynek a nem ASCII-fájl nevét, nem minden hibaüzenet nélkül. Jupyter nem teszi lehetővé a fájl feltöltéséhez, de a megjelenő hibaüzenet nem lépett vagy.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Nagyobb méretű notebookok betöltése közben hiba
Láthatja, hogy hiba **`Error loading notebook`** Ha nagyobb méretű notebookok tölthető be.  

**Megoldás:**

Ha ez a hibaüzenet azt jelenti az adatok elveszett vagy sérült.  Továbbra is a lemezen vannak jegyzetfüzetek `/var/lib/jupyter`, és azok eléréséhez a fürthöz SSH is. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Miután csatlakozott az SSH-fürtjéhez, átmásolhatja a jegyzetfüzetek a fürt a helyi számítógépen (SCP vagy WinSCP használatával) biztonsági mentéséhez a fontos adatokról a notebook az adatvesztés elkerülése érdekében. Ezek közül SSH-alagút azokat a headnode porton 8001 Jupyter eléréséhez az átjárón keresztül nélkül.  Ott törölje a notebook kimenetét, és mentse a notebook méretének minimalizálása érdekében.

Ez a hiba megakadályozza a jövőben történik, kövesse az néhány ajánlott eljárás:

* Fontos, hogy maradjon kicsi a notebook méretét. A Spark feladatok bármely olyan kimenete, amely küld vissza a Jupyter van őrzi meg a notebook.  A legjobb Jupyter általában futó elkerülése érdekében `.collect()` a nagy RDD vagy dataframes; helyette, ha szeretné bepillanthat, hogy egy RDD tartalmát, fontolja meg a futó `.take()` vagy `.sample()` , hogy a kimenet nem get túl nagy.
* Is a notebook mentésekor törölje az összes kimeneti cellák méretének csökkentése érdekében.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Kezdeti indítási notebook vártnál hosszabb ideig tart.
Jupyter notebook használatával Spark magic utasításnak elsőként kód több mint egy percbe is beletelhet.  

**Magyarázat:**

Ez akkor fordul elő, mert az első kódcella futtatásakor. A háttérben kezdeményez a munkamenet-konfigurációhoz és Spark, SQL, és a Hive-környezeteket. Miután ezek a környezetek vannak beállítva, az első utasításban fut, és ezáltal a benyomást, amely az utasítás hosszú időt vett igénybe befejezéséhez.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter notebook időkorlátot adja meg a munkamenet létrehozása
Amikor Spark-fürt kifogyott az erőforrásokból, a Spark- és PySpark kernelek a Jupyter notebook a rendszer túllépi az időkorlátot a munkamenet létrehozására tett kísérlet. 

**Megoldást:** 

1. Szabadítson fel a Spark-fürt egyes erőforrások:
   
   * Egyéb külső notebookok leállítása a zárja be és Halt menü címen, vagy kattintson a leállítás a notebook Explorer.
   * A YARN más Spark-alkalmazások leállítása.
2. Indítsa újra a notebook kívánt elindításához. Elegendő erőforrást ahhoz, hogy hozzon létre most egy munkamenet elérhetőnek kell lennie.

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

