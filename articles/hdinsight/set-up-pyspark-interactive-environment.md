---
title: Azure HDInsight-eszközök – interaktív PySpark-környezet a Visual Studio Code-hoz
description: Megtudhatja, hogyan hozhat létre és küldhet el lekérdezéseket és parancsfájlokat a Visual Studio Code-hoz készült Azure HDInsight Tools használatával.
keywords: VScode, Azure HDInsight-eszközök, struktúra, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív struktúra, interaktív lekérdezés
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 8e90be5a38ff40b748c11539e1d5c6558d198b62
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814233"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A PySpark interaktív környezet beállítása a Visual Studio Code-hoz

A következő lépések bemutatják, hogyan állíthatja be a PySpark interaktív környezetét a VS Code-ban.

A **Python/pip** parancs használatával virtuális környezetet építhet ki a saját elérési útjában. Ha más verziót szeretne használni, manuálisan kell módosítania a **Python/pip** parancs alapértelmezett verzióját. További részletek: [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Telepítse a [Pythont](https://www.python.org/downloads/) és a [pip](https://pip.pypa.io/en/stable/installing/)-et.
   
   + Telepítse a Pythont a alkalmazásból [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + A pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) telepítése (ha nem a Python-telepítésből van telepítve).
   + Ellenőrizze, hogy a Python és a pip telepítése sikeresen megtörtént-e az alábbi parancsokkal. Választható
 
        ![Python pip verziója](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Javasoljuk, hogy a macOS alapértelmezett verziójának használata helyett manuálisan telepítse a Pythont.


2. Telepítse a **virtualenv** az alábbi parancs futtatásával.
   
   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Egyéb csomagok

Ha hibaüzenetet kap, telepítse a szükséges csomagokat a következő parancsok futtatásával:

   ![Python pip verziója](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Indítsa újra a vs Code-ot, majd térjen vissza a HDInsight-t **futtató parancsfájl-szerkesztőhöz: PySpark interaktív**.

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Demó
* HDInsight a VS Code-hoz: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [A Visual Studio Code-hoz készült Azure HDInsight Tool használata](hdinsight-for-vscode.md)
* [A Azure Toolkit for IntelliJ használata Apache Spark Scala-alkalmazások létrehozásához és elküldéséhez](spark/apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése az Azure Toolkit for IntelliJ használatával SSH-n keresztül](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](spark/apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](spark/apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek egy Apache Spark-fürtben HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Apache Hive-adatmegjelenítés az Azure HDInsight Microsoft Power BIával](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Apache Hive-lekérdezések futtatása az Azure HDInsightban az Apache Zeppelin használatával](./interactive-query/hdinsight-connect-hive-zeppelin.md)
