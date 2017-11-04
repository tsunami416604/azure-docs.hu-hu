---
title: "A Visual Studio Code PySpark interaktív környezet beállítása az Azure HDInsight eszközök - |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja a Azure HDInsight Tools for Visual Studio Code létrehozásához, lekérdezések és parancsfájlok."
Keywords: "VScode, az Azure HDInsight eszközök, struktúra, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív struktúra, interaktív lekérdezés"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 24839aadaee07b98ac5a6e6cfd14e44de54e7e7e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>A Visual Studio Code PySpark interaktív környezet beállítása

A következő lépések bemutatják, hogyan python-csomagok futtatásakor **HDInsight: PySpark interaktív**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>MacOS és Linux a PySpark interaktív környezet beállítása
Parancsot kell használnia **pip3** az alábbi lépéseket, ha a **python 3.x**.
1. Győződjön meg arról, hogy a **Python** és **pip** telepítve.
 
    ![Python pip verziója](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Jupyter telepítése
    ```
    sudo pip install jupyter
    ```
    +  A következő hibaüzenet lehet, hogy érkeznek, a Linux és MacOS:

        ![error1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + Libkrb5-dev(For Linux only) telepítése, lehet, hogy a következő hibaüzenetet jeleníti meg:

        ![error2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Sparkmagic telepítése
   ```
   sudo pip install sparkmagic
   ```

4. Győződjön meg arról, hogy ipywidgets megfelelően van telepítve a futtatásával:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![A burkoló kernelek telepítése](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Telepítse a burkoló kernelek. Futtatás **pip megjelenítése sparkmagic** és az elérési utat mutatja, hogy sparkmagic telepítve van. 

    ![sparkmagic helye](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Keresse meg a helyét, és futtassa:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec telepítése](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. A telepítési állapotát ellenőrizni: 

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec listája](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Az elérhető kernelek: **python2** és **pysparkkernel** megfelelnek **python 2.x**, **python3** és  **pyspark3kernel** megfelelnek **python 3.x**. 

8. Indítsa újra a VScode, és vissza a parancsprogram-szerkesztő futtató **HDInsight: PySpark interaktív**.

## <a name="next-steps"></a>Következő lépések

### <a name="demo"></a>Bemutató
* HDInsight VScode: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

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
* [Hive-adatok ábrázolása a Microsoft Power bi-ban az Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Zeppelin használja az Azure HDInsight Hive-lekérdezések futtatásához](hdinsight-connect-hive-zeppelin.md)
