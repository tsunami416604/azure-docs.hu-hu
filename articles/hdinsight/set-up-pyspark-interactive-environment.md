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
ms.date: 10/27/2017
ms.openlocfilehash: 82aebd780b7b821675a6ee891cc81e2b1c6fdafb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437214"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A Visual Studio Code a PySpark interaktív környezetének beállítása

A következő lépések bemutatják, hogyan telepítheti a Python-csomagok futtatásával **HDInsight: A PySpark interaktív**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>A PySpark interaktív környezetet, macOS és Linux rendszeren
Ha használ **python 3.x**, kell használnia a parancs **pip3** a következő lépéseket:

1. Győződjön meg arról, hogy **Python** és **pip** vannak telepítve.
 
    ![Python-pip verzió](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  A Jupyter telepítése.
    ```
    sudo pip install jupyter
    ```
   A következő hibaüzenet láthatja a Linux és MacOS rendszeren:

   ![Hiba: 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Telepítés **libkrb5-fejlesztési** (a csak Linux). Láthatja, hogy a következő hibaüzenetet kapja:

   ![Hiba: 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Telepítés **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Győződjön meg arról, hogy **ipywidgets** megfelelően van-e telepítve a következő futtatásával:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Telepítse a burkoló kernelekkel](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Telepítse a burkoló kernelekkel. Futtatás **pip megjelenítése sparkmagic**. A kimeneti elérési útját jeleníti meg a **sparkmagic** telepítését. 

    ![sparkmagic helye](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Nyissa meg a helyet, és futtassa:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec telepítése](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Ellenőrizze a telepítés állapotát.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec listája](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    A rendelkezésre álló kernelekkel: 
    - **python2** és **pysparkkernel** felelnek meg **python 2.x**. 
    - **python3** és **pyspark3kernel** felelnek meg **python 3.x**. 

8. Indítsa újra a VS Code, és ezután lépjen vissza a parancsprogram-szerkesztő futtató **HDInsight: A PySpark interaktív**.

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
