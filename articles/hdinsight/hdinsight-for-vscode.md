---
title: "Az Azure HDInsight Tools - használja a Visual Studio Code a Hive, LLAP vagy pySpark |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja a Azure HDInsight Tools for Visual Studio Code létrehozásához, lekérdezések és parancsfájlok."
Keywords: "VScode, az Azure HDInsight eszközök, struktúra, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktív struktúra, interaktív lekérdezés"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 36ce117076ed5c15ddff850485d8f8912ec53caf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>A HDInsight eszközzel Azure a Visual Studio Code

Megtudhatja, hogyan használhatja a Azure HDInsight Tools for Visual Studio Code (VSCode) elküldeni a Hive-kötegelt feladatok, interaktív Hive-lekérdezéseket és pySpark parancsfájlok. Az Azure HDInsight Tools telepíthető Windows, Linux és MacOS VSCode által támogatott platformokon. Az Előfeltételek különböző platformokon található.


## <a name="prerequisites"></a>Előfeltételek

Az alábbi elemek szükségesek a cikk befejezése:

- A HDInsight-fürthöz.  Hozzon létre egy fürtöt, tekintse meg [első lépései a hdinsight eszközzel]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [A Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Monó](http://www.mono-project.com/docs/getting-started/install/). Monó van csak a Linux és MacOS szükséges.

## <a name="install-the-hdinsight-tools"></a>A HDInsight-eszközök telepítése
   
Az előfeltételek telepítése után az Azure HDInsight Tools VSCode telepíthető. 

**Telepítse az Azure HDInsight eszközök**

1. Nyissa meg **a Visual Studio Code**.
2. Kattintson a **bővítmények** a bal oldali ablaktáblán. Adja meg **HDInsight** be a keresőmezőbe.
3. Kattintson a **telepítése** melletti **Azure HDInsight eszközök**. Néhány másodpercen belül a **telepítése** gomb változik **Újrabetöltés**.
4. Kattintson a **Újrabetöltés** aktiválja a **Azure HDInsight eszközök** bővítmény.
5. Kattintson a **Újrabetöltés ablak** megerősítéséhez. Látható **Azure HDInsight eszközök** a Bővítmények ablaktáblán.

   ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>HDInsight munkaterület megnyitása

Munkaterület VSCode kell létrehozása, mielőtt az csatlakozna az Azure-bA.

**A munkaterület megnyitása**

1. Az a **fájl** menüben kattintson a **mappa megnyitása**, adjon meg egy létező mappát, vagy hozzon létre egy új mappát, a munkahelyi mappák. A mappa a bal oldali ablaktáblán jelenik meg.
2. A bal oldali ablaktáblán kattintson a **új fájl** a munkahelyi mappák melletti ikonra.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)
3. Nevezze el az új fájlt a .hql (Hive-lekérdezések) vagy (a külső parancsfájl) .py fájl kiterjesztése. Figyelje meg a **XXXX_hdi_settings.json** konfigurációs fájl automatikusan hozzáadódik a munkahelyi mappák.
4. Nyissa meg **XXXX_hdi_settings.json** a **EXPLORER**, vagy kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be a **konfigurációs beállítása**. A fájlban a mintában látható módon konfigurálhatja a bejelentkezési bejegyzés, a alapértelmezett fürt és a feladat elküldése paramétereit. Is hagyhatja a többi paraméter üres.

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Elküldés előtt parancsfájlok a HDInsight-fürtökhöz a VSCode, csatlakozni kell az Azure-fiókjával.

**Csatlakozás az Azure-bA**

1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájlt, ha még nem rendelkezik ilyennel.
2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: bejelentkezési** a helyi menüből. Is **CTRL + SHIFT + P** , majd gépelje be **HDInsight: bejelentkezési**.

    ![A HDInsight Tools for Visual Studio Code-e jelentkezni](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Bejelentkezési utasításait a **kimeneti** ablaktábla a bejelentkezéshez.

    **Azure:** ![a HDInsight Tools for Visual Studio Code bejelentkezési adatai](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    A csatlakozás után az Azure-fiók neve megjelenik az állapotsor bal alsó VSCode ablak. 

    > [!NOTE]
    > Nyissa meg a böngészőt a személyes vagy incognito üzemmódban ismert Azure hitelesítési probléma miatt. Ha az Azure-fiókjával van engedélyezve, a két tényező, javasoljuk, hogy PIN-kód helyett phone hitelesítés használatára.
  

4. Kattintson a jobb gombbal a parancsfájl szerkesztése a helyi menü megnyitásához. A helyi menüből a következő feladatokat végezheti el:

    - Kijelentkezés
    - Lista fürtök
    - Alapértelmezett fürt
    - Interaktív Hive-lekérdezések elküldése
    - Küldje el a Hive parancsfájl
    - Interaktív PySpark lekérdezések elküldése
    - Küldje el a PySpark kötegelt parancsprogramot
    - Konfiguráció beállítása

## <a name="list-hdinsight-clusters"></a>A HDInsight-fürtök felsorolása

A kapcsolat teszteléséhez a HDInsight-fürtök jeleníthetők meg:

**Az Azure-előfizetés a HDInsight-fürtök listázásához**
1. Nyissa meg a munkaterületet, és csatlakozzon az Azure-bA. Lásd: [nyissa meg a HDInsight munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure-bA](#connect-to-azure).
2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: lista fürt** a helyi menüből. 
3. A Hive és a Spark-fürt megjelennek a **kimeneti** ablaktáblán.

    ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Alapértelmezett fürt
1. Nyissa meg a munkaterületet, és csatlakozzon az Azure-bA. Lásd: [nyissa meg a HDInsight munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure-bA](#connect-to-azure).
2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és kattintson **HDInsight: állítsa be alapértelmezett fürt**. 
3. Válassza ki a fürt alapértelmezett fürtként meg a jelenlegi parancsfájlt. Az eszközök automatikusan frissíti a konfigurációs fájl **XXXX_hdi_settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Állítsa be az Azure környezetbe 
1. Nyissa meg a parancs palettát billentyűkombináció lenyomásával **CTRL + SHIFT + P**.
2. Adja meg **HDInsight: állítsa be az Azure-alapú környezetben**.
3. Válassza ki az Azure és a AzureChina egyik módja az alapértelmezett bejelentkezési bejegyzésének.
4. Ugyanakkor az eszköz már mentve kijelölt alapértelmezett bejelentkezési hatálybalépés **XXXX_hdi_settings.json**. Akkor is közvetlenül frissíteni ezt a konfigurációs fájlt. 

   ![alapértelmezett bejelentkezési bejegyzés konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Interaktív Hive-lekérdezések elküldése

A HDInsight Tools for VSCode engedélyezi, hogy küldje el az interaktív lekérdezés HDInsight-fürtök interaktív Hive-lekérdezéseket.

1. Hozzon létre egy új munkahelyi mappa és egy új Hive parancsfájl Ha még nem rendelkezik ilyennel.
2. Csatlakozzon az Azure-fiókjával, és válassza az alapértelmezett fürt, ha még nem tette meg.
3. Másolja és illessze be az alábbi kódot a Hive-fájlba, majd mentse.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és kattintson **HDInsight: interaktív Hive** elküldeni. Az eszközök is engedélyezi, hogy küldje el a teljes parancsfájl használatával a helyi menü helyett egy kódblokkot. Hamarosan után, a lekérdezési eredmény megjelenik az új lapon:

   ![interaktív Hive eredménye](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **EREDMÉNYEK** panel: a teljes eredmény menti CSV-, JSON-, EXCEL helyi elérési utat, vagy csak kiválaszthatnak egy többsoros.
    - **ÜZENETEK** panel: kattintva **sor** számát, azt ugrik a futó parancsprogram az első sort.

Hasonlítsa [Hive kötegelt feladatként fut](#submit-hive-batch-scripts), az interaktív lekérdezés sokkal kevesebb időt vesz igénybe.

## <a name="submit-hive-batch-scripts"></a>Küldje el a Hive kötegelt parancsfájlok

1. Hozzon létre egy új munkahelyi mappa és egy új Hive parancsfájl Ha még nem rendelkezik ilyennel.
2. Csatlakozzon az Azure-fiókjával, és válassza az alapértelmezett fürt, ha még nem tette meg.
3. Másolja és illessze be az alábbi kódot a Hive-fájlba, majd mentse.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és kattintson **HDInsight: Hive kötegelt** elküldeni a Hive feladatot. 
4. Válassza ki a fürtöt, terjeszt helyét.  

    A Hive feladat elküldése után a elküldése sikeres adatait és jobid látható **kimeneti** panel. Ekkor megnyílik, és **WEBBÖNGÉSZŐ** megjelenő állapot és a feladat valós idejű naplózó.

   ![küldje el a Hive feladat eredménye](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Hasonlítsa [interaktív Hive-lekérdezések elküldése](#submit-interactive-hive-queries), a kötegelt sokkal hosszabb időt vesz igénybe.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark lekérdezések elküldése
A HDInsight Tools for VSCode lehetővé teszik a Spark-fürtök interaktív PySpark lekérdezések.
1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájl .py kiterjesztésű Ha még nem rendelkezik ilyennel.
2. Csatlakozás az Azure-fiókjával, ha még nem tette meg.
3. Másolja és illessze be a parancsfájlt a következő kódot:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Jelölje ki a parancsfájlokat és kattintson a jobb gombbal a parancsprogram-szerkesztő, majd kattintson a **HDInsight: PySpark interaktív**.
5. Kattintson a következő **telepítése** gombra kattint, ha nem telepített **Python** VSCode bővítményt.
    ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Ha állítsa be python-környezetben a rendszer nem telepít. 
   - A windows, töltse le és telepítse [Python](https://www.python.org/downloads/). Ezután győződjön meg arról, hogy `Python` és `pip` a rendszer elérési ÚTJA.
   - Az utasítás MacOS és Linux rendszerekhez, lásd: [beállítva fel PySpark interaktív környezetet a Visual Studio Code](set-up-pyspark-interactive-environment.md).
7. Válassza ki a fürt elküldeni a PySpark lekérdezést. Hamarosan után, a lekérdezés eredménye látható a jobb oldali új lapon:

   ![küldje el a python feladat eredménye](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Az eszköz is támogatja a lekérdezést a **SQL záradék**.

   ![küldje el a python feladat eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) küldésének állapotát a bal alsó állapotsor jelenít meg, amikor a futó lekérdezések. További lekérdezések nem tudnak küldeni, ha az állapot **PySpark Kernel (foglalt)**, ellenkező esetben a futó lefagy.
9. A fürtök is fenntartható a munkamenetet. Például **egy = 100**, már ehhez a munkamenethez fürt megtartásához most csak futtatása **nyomtatása egy** fürthöz.
 

## <a name="submit-pyspark-batch-job"></a>Küldje el a PySpark kötegelt

1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájl .py kiterjesztésű Ha még nem rendelkezik ilyennel.
2. Csatlakozás az Azure-fiókjával, ha még nem tette meg.
3. Másolja és illessze be a parancsfájlt a következő kódot:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és kattintson **HDInsight: PySpark kötegelt**. 
5. Válassza ki a fürt a PySpark feladat elküldéséhez. 

   ![küldje el a python feladat eredménye](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

A python feladat elküldése után küldésének naplók megjelenik-e a **kimeneti** VSCode ablakában. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felület URL-cím** is látható. Nyomon követheti a feladat állapotát egy webböngészőben nyissa meg az URL-címet.


## <a name="additional-features"></a>További funkciók

A HDInsight VSCode támogatja a következő szolgáltatásokat:

- **Automatikus kitöltés IntelliSense**. Javaslatok vannak előtti körül kulcsszót, módszer, változókat, stb. Az objektumok különböző típusú ikonjai különböző:

    ![A HDInsight Tools for Visual Studio Code IntelliSense objektumtípusok](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense hiba jelölő**. A nyelvi szolgáltatás kiemeli a Hive parancsfájl szerkesztési jelzett hibákat.     
- **Szintaxis emeli ki**. A nyelvi szolgáltatás különböző szín megkülönböztetéséhez változók, kulcsszavakat, adattípus, Funkciók, stb. 

    ![A HDInsight Tools for Visual Studio Code szintaxis kiemelések](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Következő lépések

### <a name="demo"></a>Bemutató
* HDInsight VScode: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [A Visual Studio Code PySpark interaktív környezet beállítása](set-up-pyspark-interactive-environment.md)
* [Az intellij-t Azure eszközkészlet segítségével hozza létre, és küldje el a Spark Scala-alkalmazások](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](hdinsight-apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Hive-adatok ábrázolása a Microsoft Power bi-ban az Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása ](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](hdinsight-apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Spark on HDInsight használata valós idejű streamelési alkalmazások készítéséhez](hdinsight-apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Létrehozása és alkalmazások futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](hdinsight-apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](hdinsight-apache-spark-job-debugging.md)



