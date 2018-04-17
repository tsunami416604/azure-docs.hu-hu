---
title: A Visual Studio Code PySpark interaktív környezet beállítása az Azure HDInsight eszközök - |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozása és elküldése a lekérdezések és parancsfájlok.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>A Visual Studio Code a PySpark interaktív környezet beállítása

A következő lépések bemutatják a Python-csomagokat a telepítést futtatja **HDInsight: PySpark interaktív**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>MacOS és Linux a PySpark interaktív környezet beállítása
Ha használ **python 3.x**, kell használnia a parancs **pip3** a következő lépéseket:

1. Győződjön meg arról, hogy **Python** és **pip** vannak telepítve.
 
    ![Python pip verziója](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Telepítse a Jupyter.
    ```
    sudo pip install jupyter
    ```
   A Linux és macOS láthatja a következő hibaüzenet:

   ![Hiba: 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Telepítés **libkrb5-fejlesztői** (a Linux esetén). Előfordulhat, hogy a következő hibaüzenet jelenik meg:

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
   ![A burkoló kernelek telepítése](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Telepítse a burkoló kernelek. Futtatás **pip megjelenítése sparkmagic**. A kimeneti elérési útját jeleníti meg a **sparkmagic** telepítését. 

    ![sparkmagic helye](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. A helyen, és futtassa:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec telepítése](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. A telepítési állapotát ellenőrizni.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec listája](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Az elérhető kernelek: 
    - **python2** és **pysparkkernel** megfelelnek **python 2.x**. 
    - **python3** és **pyspark3kernel** megfelelnek **python 3.x**. 

8. Indítsa újra a VS kódot, és ezután térjen vissza a parancsprogram-szerkesztő futtató **HDInsight: PySpark interaktív**.

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Bemutató
* HDInsight Visual STUDIO Code: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [A HDInsight eszközzel Azure a Visual Studio Code](hdinsight-for-vscode.md)
* [Az intellij-t Azure eszközkészlet segítségével hozza létre, és küldje el a Spark Scala-alkalmazások](spark/apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](spark/apache-spark-eclipse-tool-plugin.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](spark/apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Zeppelin használja az Azure HDInsight Hive-lekérdezések futtatásához ](hdinsight-connect-hive-zeppelin.md)
