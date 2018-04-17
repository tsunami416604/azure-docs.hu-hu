---
title: Az Azure HDInsight Tools - használja a Visual Studio Code a Hive, LLAP vagy pySpark |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozása és elküldése a lekérdezések és parancsfájlok.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: jgao
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 0074486d3d7fb58bc6e3adcbe4245ec53e7e4cde
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight eszközök segítségével a Visual Studio Code

Ismerje meg, hogyan használható az Azure HDInsight Tools a Visual Studio (kód VS) létrehozása és elküldése a Hive-kötegelt feladatok, interaktív Hive-lekérdezéseket és pySpark parancsfájlok. Az Azure HDInsight Tools VS kód által támogatott platformok is telepíthető. Ezek közé tartozik a Windows, Linux és macOS. Az Előfeltételek különböző platformokon található.


## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szükség a cikkben szereplő lépések végrehajtását:

- A HDInsight-fürthöz.  Hozzon létre egy fürtöt, tekintse meg [első lépései a hdinsight eszközzel]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Monó](http://www.mono-project.com/docs/getting-started/install/). Monó van csak a Linux és macOS szükséges.

## <a name="install-the-hdinsight-tools"></a>A HDInsight-eszközök telepítése
   
Az előfeltételek telepítése után telepítheti az Azure HDInsight Tools for VS kód. 

**Telepítse az Azure HDInsight eszközök**

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali panelen válassza ki a **bővítmények**. A keresési mezőbe, írja be a **HDInsight**.

3. A **Azure HDInsight eszközök**, jelölje be **telepítése**. Néhány másodpercen belül a **telepítése** gomb felirata **Újrabetöltés**.

4. Válassza ki **Újrabetöltés** aktiválja a **Azure HDInsight eszközök** bővítmény.

5. Válassza ki **Újrabetöltés ablak** megerősítéséhez. Látható **Azure HDInsight eszközök** a a **bővítmények** ablaktáblán.

   ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>HDInsight munkaterület megnyitása

Munkaterület létrehozása a Visual STUDIO Code, mielőtt csatlakozna az Azure-bA.

**A munkaterület megnyitása**

1. Az a **fájl** menü **mappa megnyitása**. Ezután a munkahelyi mappák kijelöl egy meglevő mappába, vagy hozzon létre egy újat. A mappa a bal oldali ablaktáblán jelenik meg.

2. A bal oldali panelen válassza ki a **új fájl** a munkahelyi mappák melletti ikonra.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)

3. Nevezze el az új fájlt a .hql (Hive-lekérdezések) vagy (a külső parancsfájl) .py fájl kiterjesztése. Figyelje meg, hogy egy **XXXX_hdi_settings.json** konfigurációs fájl automatikusan hozzáadódik a munkahelyi mappák.

4. Nyissa meg **XXXX_hdi_settings.json** a **EXPLORER**, vagy kattintson a jobb gombbal a parancsprogram-szerkesztő kiválasztásához **konfigurációs beállítása**. Bejelentkezési bejegyzést, az alapértelmezett fürt és a feladat elküldése paramétereit a fájlban a mintában látható módon is konfigurálhatja. Is hagyhatja a többi paraméter üres.

## <a name="connect-to-hdinsight-cluster"></a>HDInsight-fürthöz csatlakozik

Elküldés előtt parancsfájlok a HDInsight-fürtökhöz a Visual STUDIO Code, szeretné-e csatlakozni az Azure-fiókjával, vagy hivatkozás egy fürt (Ambari használatával felhasználónév/jelszó adatokat vagy tartományhoz csatlakozó fiókot).

**Csatlakozás az Azure-bA**

1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájlt, ha már nincs rájuk.

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és ezt követően a helyi menüben válassza **HDInsight: bejelentkezési**. Megadhat **Ctrl + Shift + P**, majd adja meg **HDInsight: bejelentkezési**.

    ![A HDInsight Tools for Visual Studio Code-e jelentkezni](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Jelentkezzen be, kövesse a bejelentkezési utasításait a **kimeneti** ablaktáblán.

    **Azure:** ![a HDInsight Tools for Visual Studio Code bejelentkezési adatai](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Miután csatlakozott, az Azure-fiók neve jelenik meg a Visual STUDIO Code ablak bal alsó az állapotsorban. 

    > [!NOTE]
    > Egy ismert Azure hitelesítési probléma miatt egy böngészőben nyissa meg a személyes vagy incognito üzemmódban kell. Ha az Azure-fiókjával van engedélyezve, a két tényező, ajánlott phone hitelesítést használó hitelesítés helyett.
  

4. Kattintson a jobb gombbal a parancsprogram-szerkesztő megnyitása a helyi menüből. A helyi menüből a következő feladatokat végezheti el:

    - Kijelentkezés
    - Lista fürtök
    - Készlet alapértelmezett fürtök
    - Interaktív Hive-lekérdezések elküldése
    - Küldje el a Hive kötegelt parancsfájlok
    - Interaktív PySpark lekérdezések elküldése
    - Küldje el a PySpark kötegelt parancsfájlok
    - Set-konfigurációk

**Ha szeretné kapcsolni a fürt**

Egy normál fürt hivatkozás segítségével felügyelt Ambari felhasználónév, is hivatkozásra a biztonsági hadoop-fürthöz tartományi felhasználónevet használatával (például: user1@contoso.com).
1. Nyissa meg a parancs palettát kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: hivatkozás egy fürt**.

   ![hivatkozás fürt parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Adja meg a HDInsight fürt URL -> felhasználónév -> bemeneti be a jelszót válasszon -> fürttípus -> azt látható sikeres info ellenőrzési át.
   
   ![hivatkozás fürt párbeszédpanel](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz kapcsolódó társított felhasználónevet és jelszót használunk. 
   
3. Megjelenik egy társított fürt paranccsal **lista fürt**. Most is elküldhetik a csatolt fürt parancsfájlt.

   ![csatolt fürt](./media/hdinsight-for-vscode/linked-cluster.png)

4. Is megszüntetheti a fürt által beírása **HDInsight: egy fürt leválasztása** parancs palettáról.

## <a name="list-hdinsight-clusters"></a>A HDInsight-fürtök felsorolása

A kapcsolat teszteléséhez a HDInsight-fürtök jeleníthetők meg:

**Az Azure-előfizetés a HDInsight-fürtök listázásához**
1. Nyissa meg a munkaterületet, és csatlakozzon az Azure-bA. További információkért lásd: [nyissa meg a HDInsight munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure-bA](#connect-to-azure).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: lista fürt** a helyi menüből. 

3. A Hive és a Spark-fürt megjelennek a **kimeneti** ablaktáblán.

    ![Állítsa be alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Egy alapértelmezett fürt beállítása
1. Nyissa meg a munkaterületet, és csatlakozzon az Azure-bA. Lásd: [nyissa meg a HDInsight munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure-bA](#connect-to-azure).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: állítsa be alapértelmezett fürt**. 

3. Jelöljön ki egy fürtöt, az alapértelmezett fürt meg a jelenlegi parancsfájlt. Az eszközök automatikusan a konfigurációs fájl frissítése **XXXX_hdi_settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Állítsa be az Azure környezetbe 
1. Nyissa meg a parancs palettát kiválasztásával **CTRL + SHIFT + P**.

2. Adja meg **HDInsight: állítsa be az Azure-alapú környezetben**.

3. Válassza ki az Azure és a AzureChina egyik módja az alapértelmezett bejelentkezési bejegyzésének.

4. Ugyanakkor az eszköz már mentette az alapértelmezett bejelentkezési bejegyzést **XXXX_hdi_settings.json**. Akkor is közvetlenül frissíteni ezt a konfigurációs fájlt. 

   ![alapértelmezett bejelentkezési bejegyzés konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Interaktív Hive-lekérdezések elküldése

A HDInsight Tools for Visual STUDIO Code elküldheti a HDInsight-fürtökhöz interaktív lekérdezés interaktív Hive-lekérdezéseket.

1. Hozzon létre egy új munkahelyi mappa és egy új Hive parancsfájl ha már nincs rájuk.

2. Csatlakozás az Azure-fiókjával, és válassza az alapértelmezett fürt, ha még nem tette.

3. Másolja és illessze be az alábbi kódot a Hive-fájlba, és mentse azt.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: interaktív Hive** elküldeni. Az eszközök is engedélyezi, hogy küldje el a teljes parancsfájl használatával a helyi menü helyett egy kódblokkot. Miután, a lekérdezés eredményei jelennek meg egy új lapot.

   ![interaktív Hive eredménye](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **EREDMÉNYEK** panel: a teljes eredmény elmentse CSV, JSON vagy az Excel fájl helyi elérési utat, vagy csak kiválaszthatnak egy többsoros.

    - **ÜZENETEK** panel: kiválasztásakor **sor** számát, azt ugrik a futó parancsprogram az első sort.

Futtassa az interaktív lekérdezést gyorsabb a sokkal [Hive kötegelt feladatként fut](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Küldje el a Hive kötegelt parancsfájlok

1. Hozzon létre egy új munkahelyi mappa és egy új Hive parancsfájl ha már nincs rájuk.

2. Csatlakozás az Azure-fiókjával, és válassza az alapértelmezett fürt, ha még nem tette.

3. Másolja és illessze be az alábbi kódot a Hive-fájlba, és mentse azt.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: Hive kötegelt** elküldeni a Hive feladatot. 

4. Jelölje ki a fürtöt, amelyre szeretné elküldeni.  

    Miután elküldött egy Hive-feladat, a elküldése sikeres adatait és jobid megjelenik a **kimeneti** panel. A Hive-feladatokban is megnyílik **WEBBÖNGÉSZŐ**, amely a valós idejű feladatnaplóit és állapotát jeleníti meg.

   ![küldje el a Hive feladat eredménye](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Interaktív Hive-lekérdezések elküldése](#submit-interactive-hive-queries) mint elküld egy kötegelt feladatot sokkal kevesebb időt vesz igénybe.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark lekérdezések elküldése
A HDInsight Tools for Visual STUDIO Code is lehetővé teszi a Spark-fürtök interaktív PySpark lekérdezéseket küldeni.
1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájl .py kiterjesztésű Ha már nincs rájuk.

2. Ha még nem még meg csatlakozni az Azure-fiókjával.

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
4. Jelölje ki a parancsfájlokat. Ezután kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: PySpark interaktív**.

5. Ha még nem telepítette a **Python** bővítményt a Visual STUDIO Code, válassza ki a **telepítése** gombra kattint, a következő ábrán látható módon:

    ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. A Python-környezet telepítése a rendszer, ha még nem tette meg. 
   - A Windows, töltse le és telepítse [Python](https://www.python.org/downloads/). Ezután győződjön meg arról, hogy `Python` és `pip` a rendszer elérési ÚTJA.

   - MacOS és Linux útmutatásért lásd: [PySpark interaktív környezet beállítása a Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Jelölje ki a fürtöt, amelyre szeretné elküldeni a PySpark lekérdezést. Hamarosan után, a lekérdezés eredménye van az új jobb oldali lapon látható:

   ![Küldje el a Python feladat eredménye](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Az eszköz is támogatja a **SQL záradék** lekérdezés.

   ![Küldje el a Python feladat eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) küldésének állapota megjelenik az alsó állapotsorban amikor futtatja a lekérdezések balra. Ne küldjön további lekérdezések, ha az állapot **PySpark Kernel (foglalt)**. 

>[!NOTE]
>A fürtök is fenntartható a munkamenet-információk. A definiált változó, a függvény és a megfelelő értékek tárolják a munkamenetben, azok között az ugyanazon fürt több hívások lehet hivatkozni. 
 

## <a name="submit-pyspark-batch-job"></a>Küldje el a PySpark kötegelt

1. Hozzon létre egy új munkahelyi mappa és egy új parancsfájl .py kiterjesztésű Ha már nincs rájuk.

2. Csatlakozzon az Azure-fiókjával, ha még nem tette.

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
4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **HDInsight: PySpark kötegelt**. 

5. Jelölje ki a fürtöt, amelyre szeretné elküldeni a PySpark feladatot. 

   ![Küldje el a Python feladat eredménye](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

A Python feladat elküldése után küldésének naplók megjelennek a **kimeneti** ablak a Visual STUDIO Code. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felület URL-cím** is látható. Nyomon követheti a feladat állapotát egy webböngészőben nyissa meg az URL-címet.


   


## <a name="additional-features"></a>További funkciók

HDInsight Visual STUDIO Code támogatja a következő funkciókat:

- **Automatikus kitöltés IntelliSense**. Javaslatok felugró kulcsszó, módszerek, változók és így tovább. Különböző ikonjai a különböző objektumokat.

    ![A HDInsight Tools for Visual Studio Code IntelliSense objektumtípusok](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense hiba jelölő**. A nyelvi szolgáltatás kiemeli a Hive-parancsfájl a szerkesztési jelzett hibákat.     
- **Szintaxis emeli ki**. A nyelvi szolgáltatás használ a különböző színek megkülönböztetni azokat a változókat, kulcsszavak, adattípus, Funkciók, és így tovább. 

    ![A HDInsight Tools for Visual Studio Code szintaxis kiemelések](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Bemutató
* HDInsight Visual STUDIO Code: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az intellij-t Azure eszközkészlet segítségével VPN-en keresztül távolról Spark-alkalmazások](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az intellij-t Azure eszközkészlet segítségével SSH keresztül távolról Spark-alkalmazások](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások létrehozása](spark/apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](spark/apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Interaktív lekérdezés Hive-adatok ábrázolása a Power bi-ban az Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [A Visual Studio Code PySpark interaktív környezet beállítása](set-up-pyspark-interactive-environment.md)
* [Zeppelin használja az Azure HDInsight Hive-lekérdezések futtatásához ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Hozzon létre, és a futó alkalmazások
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](spark/apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](spark/apache-spark-job-debugging.md)



