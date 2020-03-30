---
title: PySpark interaktív környezet az Azure HDInsight-eszközökkel
description: Ismerje meg, hogyan használhatja az Azure HDInsight Tools for Visual Studio Code használatával lekérdezéseket és parancsfájlokat.
keywords: VScode,Azure HDInsight-eszközök,Struktúra,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interaktív struktúra,Interaktív lekérdezés
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73241543"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A PySpark interaktív környezet beállítása a Visual Studio-kódhoz

A következő lépések bemutatják, hogyan állíthatja be a PySpark interaktív környezetet a VS Code-ban.

**Python/pip** parancsot használunk a virtuális környezet létrehozásához az Otthoni útvonalon. Ha egy másik verziót szeretne használni, manuálisan kell módosítania a **python/pip** parancs alapértelmezett verzióját. További részletek: [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Telepítse [a Python](https://www.python.org/downloads/) és [pip](https://pip.pypa.io/en/stable/installing/).

   + Telepítse a [https://www.python.org/downloads/](https://www.python.org/downloads/)Pythont a programból.
   + Telepítse a [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) pip-et (ha nincs telepítve a Python-telepítésből).
   + Ellenőrizze, hogy a Python és a pip telepítése sikeresen megtörtént-e a következő parancsokkal. (Nem kötelező)

        ![Python pip verzióparancs ellenőrzése](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Javasoljuk, hogy manuálisan telepítse a Pythont a macOS alapértelmezett verziójának használata helyett.

2. Telepítse **virtualenv** futtatásával parancsot alább.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Egyéb csomagok

Ha hibaüzenetet észlel, telepítse a szükséges csomagokat a következő parancsok futtatásával:

   ![Libkrb5 csomag telepítése python-hoz](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Indítsa újra a VS-kódot, majd lépjen vissza a **HDInsight: PySpark Interactive**programot futtató parancsfájlszerkesztőhöz.

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Bemutató
* HDInsight vs kód: [Videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az Azure HDInsight eszköz használata a Visual Studio-kódhoz](hdinsight-for-vscode.md)
* [Apache Spark Scala-alkalmazások létrehozásához és elküldéséhez használja az Azure Toolkit for IntelliJ-t](spark/apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugelje az Apache Spark-alkalmazásokat az SSH-n keresztül](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Az Azure Toolkit for IntelliJ segítségével távolról debugolhat Apache Spark-alkalmazásokat VPN-en keresztül](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark-alkalmazások létrehozásához használja a HDInsight-eszközöket az Azure Eclipse eszközkészletében](spark/apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](spark/apache-spark-zeppelin-notebook.md)
* [A Jupyter-jegyzetfüzethez elérhető kernelek egy Apache Spark-fürthdinsighthoz](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI szolgáltatással az Azure HDInsightban](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Apache Hive-lekérdezések futtatása az Azure HDInsightban az Apache Zeppelin használatával](./interactive-query/hdinsight-connect-hive-zeppelin.md)
