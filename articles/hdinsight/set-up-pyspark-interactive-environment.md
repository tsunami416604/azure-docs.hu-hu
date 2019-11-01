---
title: PySpark interaktív környezet az Azure HDInsight-eszközökkel
description: Megtudhatja, hogyan hozhat létre és küldhet el lekérdezéseket és parancsfájlokat a Visual Studio Code-hoz készült Azure HDInsight Tools használatával.
keywords: VScode, Azure HDInsight-eszközök, struktúra, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív struktúra, interaktív lekérdezés
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241543"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A PySpark interaktív környezet beállítása a Visual Studio Code-hoz

A következő lépések bemutatják, hogyan állíthatja be a PySpark interaktív környezetét a VS Code-ban.

A **Python/pip** parancs használatával virtuális környezetet építhet ki a saját elérési útjában. Ha más verziót szeretne használni, manuálisan kell módosítania a **Python/pip** parancs alapértelmezett verzióját. További részletek: [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Telepítse a [Pythont](https://www.python.org/downloads/) és a [pip](https://pip.pypa.io/en/stable/installing/)-et.

   + Telepítse a Pythont [https://www.python.org/downloads/ ról ](https://www.python.org/downloads/).
   + Telepítse a pip-et [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (ha nem a Python-telepítésből van telepítve).
   + Ellenőrizze, hogy a Python és a pip telepítése sikeresen megtörtént-e az alábbi parancsokkal. Választható

        ![A Python pip Version parancsának keresése](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Javasoljuk, hogy a macOS alapértelmezett verziójának használata helyett manuálisan telepítse a Pythont.

2. Telepítse a **virtualenv** az alábbi parancs futtatásával.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Egyéb csomagok

Ha hibaüzenetet kap, telepítse a szükséges csomagokat a következő parancsok futtatásával:

   ![A Pythonhoz készült libkrb5-csomag telepítése](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Indítsa újra a VS Code-ot, majd térjen vissza a HDInsight-t futtató parancsfájl-szerkesztőhöz **: PySpark Interactive**.

## <a name="next-steps"></a>Következő lépések

### <a name="demo"></a>Bemutató
* HDInsight a VS Code-hoz: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

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
