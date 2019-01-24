---
title: A Visual Studio Code a PySpark interaktív környezetének beállítása az Azure HDInsight-eszközök –
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozásához és elküldéséhez a lekérdezések és a parancsfájlokat.
keywords: VScode, az Azure HDInsight-eszközök, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív Hive, interaktív lekérdezés
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: ef33d8962848636ee53ac6fd3f084b9c2a59e29d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820572"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A Visual Studio Code a PySpark interaktív környezetének beállítása

A következő lépések bemutatják, hogyan állíthatja be a PySpark interaktív környezetének a VS Code-ban.

Használjuk a **python/pip** parancsot hozhat létre a virtuális környezet az otthoni elérési úthoz. Ha szeretne egy másik verzióját használja, az alapértelmezett verzió módosítani szeretné **python/pip** manuálisan parancsot. További információ: [update-alternatívák](https://linux.die.net/man/8/update-alternatives).

1. Telepítés [Python](https://www.python.org/) és [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Telepítse a Pythont a [ https://pip.pypa.io/en/stable/installing ](https://www.python.org/).
   + Instalovat modul pip a [ https://pip.pypa.io/en/stable/installing ](https://pip.pypa.io/en/stable/installing/). (Ha nincs telepítve a Python-telepítés)
   + Ellenőrizze a Python, és a pip telepítve vannak a következő parancsokkal sikeresen. (Választható lehetőség)
 
        ![Python-pip verzió](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

    > [!NOTE]
    > Javasoljuk, hogy manuálisan telepítik a Pythont a MacOS alapértelmezett verzió használata helyett.


2. Telepítés **virtualenv** az alábbi parancs futtatásával.
   
   ```
   pip install virtualenv
   ```

3. Csak a Linux-parancsok Csengő futtatásával, ha a hibaüzenetet tapasztal a szükséges csomagok telepítéséhez.
   
    ![Python-pip verzió](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Indítsa újra a VS Code, és ezután lépjen vissza a parancsprogram-szerkesztő futtató **HDInsight: A PySpark interaktív**.

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Bemutató
* A VS Code HDInsight: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [A Visual Studio Code az Azure HDInsight-eszköz használata](hdinsight-for-vscode.md)
* [IntelliJ-hez készült Azure-eszközkészlet használatával hozzon létre, és küldje el az Apache Spark Scala-alkalmazások](spark/apache-spark-intellij-tool-plugin.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások távolról az ssh-n keresztül](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások VPN-en keresztül távolról](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight Tools használata az Azure Toolkit for Eclipse Apache Spark-alkalmazások létrehozásához](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](spark/apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [A Microsoft Power bi-ban az Azure HDInsight az Apache Hive-adatok megjelenítése](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához ](hdinsight-connect-hive-zeppelin.md)
