---
title: Az Azure HDInsight Tools – Visual Studio Code használata a Hive, LLAP vagy pySpark
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozásához és elküldéséhez a lekérdezések és a parancsfájlokat.
keywords: A VS Code, az Azure HDInsight-eszközök, Hive, Python, PySpark, Spark, HDInsight, Hadoop LLAP, interaktív Hive, interaktív lekérdezés
services: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 58f930b7bb1dee8f8f95b6627ebf70fe095126c0
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697849"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight Tools for Visual Studio Code használata

Ismerje meg, hogyan használhatja az Azure HDInsight Tools for Visual Studio Code (a VS Code) létrehozásához és elküldéséhez a Hive kötegelt feladatokat, interaktív Hive-lekérdezéseket és pySpark szkripteket. Az Azure HDInsight-eszközök a VS Code által támogatott platformokat is telepíthető. Ezek közé tartozik a Windows, a Linux és a macOS. A különböző platformok előfeltételei is megtalálhatja.


## <a name="prerequisites"></a>Előfeltételek

A következő elemekre szükség, az ebben a cikkben ismertetett lépéseket követve:

- A HDInsight-fürt. Hozzon létre egy fürtöt, tekintse meg [HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono csak akkor szükséges, Linux és MacOS rendszeren.

## <a name="install-the-hdinsight-tools"></a>A HDInsight Tools telepítése
   
Az előfeltételek telepítése után az Azure HDInsight Tools for VS Code is telepítheti. 

**Telepítse az Azure HDInsight-eszközök**

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali panelen válassza ki a **bővítmények**. A Keresés mezőbe írja be a **HDInsight**.

3. A **Azure HDInsight eszközök**válassza **telepítése**. Néhány másodperc elteltével a **telepítése** gomb felirata **Újrabetöltés**.

4. Válassza ki **Újrabetöltés** aktiválása a **Azure HDInsight eszközök** bővítmény.

5. Válassza ki **Reload Window** megerősítéséhez. Látható **Azure HDInsight eszközök** a a **bővítmények** ablaktáblán.

   ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Nyissa meg a HDInsight-munkaterület

Munkaterület létrehozása a VS Code-ban, mielőtt az csatlakozna az Azure-bA.

**Munkaterület megnyitása**

1. Az a **fájl** menüjében válassza **mappa megnyitása**. Ezután kijelölni a munkahelyi mappa egy létező mappát, vagy hozzon létre egy újat. A bal oldali panelen megjelenik a mappa.

2. A bal oldali panelen válassza ki a **új fájl** mellett a munkahelyi mappa ikonra.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)

3. Adjon nevet az új fájl a .hql (Hive-lekérdezések) vagy a (Spark-szkriptet) .py fájl kiterjesztése. Figyelje meg, hogy egy **XXXX_hdi_settings.json** konfigurációs fájl automatikusan hozzáadódik a munkahelyi mappa.

4. Nyissa meg **XXXX_hdi_settings.json** a **EXPLORER**, vagy kattintson a jobb gombbal, válassza ki a parancsprogram-szerkesztő **konfigurációs beállítása**. Konfigurálhatja a bejelentkezési belépési, a fürt alapértelmezett és a feladat beküldése paramétereit a fájlban a mintában látható módon. Ön is a fennmaradó paraméterekkel üresen hagyhatja.

## <a name="connect-to-hdinsight-cluster"></a>Csatlakozhat a HDInsight-fürt

Elküldés előtt szkriptek HDInsight-fürtök a VS Code-ból, szeretné-e csatlakozni az Azure-fiókjába, vagy egy fürtöt (az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott fiók).

**Csatlakozás az Azure-bA**

1. Hozzon létre egy új munkahelyi mappa és a egy új parancsfájlt, ha már nincs rájuk.

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és ezt követően a helyi menüben válassza **HDInsight: bejelentkezési**. Is megadhat **Ctrl + Shift + P**, majd adja meg **HDInsight: bejelentkezési**.

    ![HDInsight Tools for Visual Studio Code jelentkezzen be](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Jelentkezzen be, kövesse a bejelentkezési utasításait a **kimeneti** ablaktáblán.

    **Azure:** ![HDInsight Tools for Visual Studio Code bejelentkezési adatai](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Miután csatlakozott, az Azure-fiók nevét a VS Code ablakának bal alsó állapotjelző sáv jelenik meg. 

    > [!NOTE]
    > Egy ismert Azure hitelesítési probléma miatt egy böngészőben nyissa meg a privát vagy inkognitó üzemmódban kell. Ha az Azure-fiókjával két tényező engedélyezve van, ajánlott telefonos hitelesítés használata a PIN-kód-hitelesítés helyett.
  

4. Kattintson a jobb gombbal a parancsprogram-szerkesztő megnyitása a helyi menüből. A helyi menüből hajtsa végre a következő feladatokat:

    - Kijelentkezés
    - Fürtök listázása
    - Alapértelmezett fürtök beállítása
    - Interaktív Hive-lekérdezések küldése
    - Hive-os kötegelt szkriptek küldése
    - PySpark interaktív lekérdezések elküldése
    - Küldje el a PySpark kötegelt parancsfájlok
    - Set-konfigurációk

<a id="linkcluster"></a>**A fürt összekapcsolása**

Hivatkozás egy normál fürt kezelése az Ambari felhasználónév használatával is, is hivatkozásra a biztonsági hadoop-fürt használatával: tartomány felhasználónév (például: user1@contoso.com).
1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: egy fürtöt**.

   ![hivatkozás fürt parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Adja meg a HDInsight fürt URL-cím-bemenet felhasználónév -> > be a jelszót a select -> fürttípus -> azt látható sikeres info ellenőrzési át.
   
   ![hivatkozás fürt párbeszédpanel](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > A társított felhasználónevet és jelszót használják, ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított. 
   
3. Parancs használatával megtekintheti a társított fürt **lista fürt**. Most már küldhet egy parancsfájlt, hogy a társított fürtöt.

   ![a csatolt fürt](./media/hdinsight-for-vscode/linked-cluster.png)

4. Is megszüntetheti a fürt által bevitelével **HDInsight: a fürt leválasztása** a parancskatalógus.

## <a name="list-hdinsight-clusters"></a>HDInsight-fürtök listázása

A kapcsolat teszteléséhez, a HDInsight-fürtök listázhatja:

**A HDInsight-fürtök listázása az Azure-előfizetéshez**
1. Megnyit egy munkaterületet, és csatlakoztassa az Azure-bA. További információkért lásd: [nyissa meg a HDInsight-munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure](#connect-to-azure).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: lista fürt** a helyi menüből. 

3. A Hive és a Spark-fürtök megjelennek a **kimeneti** ablaktáblán.

    ![Egy fürt alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Egy alapértelmezett fürt beállítása
1. Megnyit egy munkaterületet, és csatlakozzon az Azure-bA. Lásd: [nyissa meg a HDInsight-munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure](#connect-to-azure).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: állítsa be a fürt alapértelmezett**. 

3. Válasszon egy fürtöt, az alapértelmezett fürt meg a jelenlegi parancsfájlt. Az eszközök automatikusan frissítse a konfigurációs fájl **XXXX_hdi_settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása 
1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**.

2. Adja meg **HDInsight: állítsa be az Azure-környezet**.

3. Válassza ki Azure-AzureChina egyik módja az alapértelmezett bejelentkezési bejegyzésként.

4. Ugyanakkor az eszköz már mentette az alapértelmezett bejelentkezési bejegyzést **XXXX_hdi_settings.json**. Emellett közvetlenül is frissíti, a konfigurációs fájlban. 

   ![Alapértelmezett bejelentkezési belépési konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Interaktív Hive-lekérdezések küldése

HDInsight Tools for VS Code elküldheti a HDInsight interaktív lekérdezési fürtökhöz való interaktív Hive-lekérdezéseket.

1. Ha még nem tette, hozzon létre egy új munkamappát és egy új Hive-szkriptfájlt.

2. Csatlakozzon az Azure-fiókhoz, majd konfigurálja az alapértelmezett fürtöt, ha még nem tette.

3. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson jobb gombbal a szkriptszerkesztőre, majd válassza a **HDInsight: Hive Interactive (Interaktív Hive)** lehetőséget a lekérdezés küldéséhez. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. A lekérdezés eredményei hamarosan megjelennek egy új lapon.

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTS** (EREDMÉNYEK) panel: A teljes eredményt elmentheti CSV-, JSON- vagy Excel-fájlban a helyi útvonalon, de kijelölhet csupán pár sort is.

    - **MESSAGES** (ÜZENETEK) panel: A **sor** számának kiválasztásakor a futó szkript első sorához ugrik.

Az interaktív lekérdezés futtatása lényegesen kevesebb időt vesz igénybe, mint [egy Hive-os kötegelt feladat futtatása](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Hive-os kötegelt szkriptek küldése

1. Ha még nem tette, hozzon létre egy új munkamappát és egy új Hive-szkriptfájlt.

2. Csatlakozzon az Azure-fiókhoz, majd konfigurálja az alapértelmezett fürtöt, ha még nem tette.

3. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Kattintson jobb gombbal a szkriptszerkesztőre, majd válassza a **HDInsight: Hive Batch (Kötegelt Hive)** lehetőséget egy Hive-feladat küldéséhez. 

4. Válassza ki a küldés célpontjául szolgáló fürtöt.  

    Egy Hive-feladat elküldése után a küldés sikerességére vonatkozó információk és a feladatazonosító megjelenik az **OUTPUT** (KIMENET) panelen. A Hive-feladat megnyitja a **WEB BROWSER** (WEBBÖNGÉSZŐ) felületet is, itt jelennek a valós idejű feladatnaplók és a feladat állapota.

   ![Hive-feladat küldésének eredménye](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Az [interaktív Hive-lekérdezés küldése](#submit-interactive-hive-queries) lényegesen kevesebb időt vesz igénybe, mint egy kötegelt feladat küldése.

## <a name="submit-interactive-pyspark-queries"></a>PySpark interaktív lekérdezések elküldése
HDInsight Tools for VS Code is lehetővé teszi, hogy a Spark-fürtök interaktív PySpark-lekérdezések elküldéséhez.
1. Hozzon létre egy új munkahelyi mappa és a egy új parancsfájlt a .py kiterjesztéssel, ha még nem rendelkezik őket.

2. Csatlakozhat az Azure-fiókjával, ha még nem tette.

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
4. Jelöljön ki ezeket a parancsfájlokat. Ezután kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **HDInsight: PySpark interaktív**.

5. Ha még nem telepítette a **Python** a VS Code-bővítmény kiválasztása a **telepítése** gombra az alábbi ábrán látható módon:

    ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Ha még nem tette, telepítse a rendszer a Python-környezetet. 
   - A Windows, töltse le és telepítse [Python](https://www.python.org/downloads/). Majd ellenőrizze, hogy `Python` és `pip` a rendszer elérési ÚTJA.

   - Útmutatás a macOS és Linux: [a PySpark interaktív környezetének beállítása a Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Válassza ki a fürt, amelyhez a PySpark lekérdezést. Hamarosan után, a lekérdezés eredménye megjelenik az új jobb oldali lapon:

   ![Küldje el a Python-feladat eredménye](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Az eszköz is támogatja a **SQL záradék** lekérdezés.

   ![Küldje el a Python-feladat eredményének](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) a beküldés állapotát a bal oldali sávon, amikor lekérdezést futtat az alsó állapot jelenik meg. További lekérdezések ne küldjön el, ha az állapot értéke **PySpark kernelt (foglalt)**. 

>[!NOTE]
>A fürtök is fenntartható a munkamenet-információk. A definiált változó, a függvény és a megfelelő értékeket a munkamenetet, így azok között ugyanazon fürt több szolgáltatás-hívást lehet hivatkozni tartanak. 
 

## <a name="submit-pyspark-batch-job"></a>PySpark batch-feladat elküldése

1. Hozzon létre egy új munkahelyi mappa és a egy új parancsfájlt a .py kiterjesztéssel, ha még nem rendelkezik őket.

2. Csatlakozhat az Azure-fiókjával, ha ezt még nem tette meg.

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
4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: PySpark Batch**. 

5. Válassza ki a fürt, amelyre szeretné elküldeni a PySpark feladatot. 

   ![Küldje el a Python-feladat eredménye](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Miután elküldött egy Python-feladatot, a naplók küldése megjelennek a **kimeneti** ablak a VS Code-ban. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felületének URL-cím** is látható. Megnyithatja az URL-címet egy webböngészőben a feladat állapotának nyomon követését.

>[!NOTE]
>PySpark3 Livy 0,4 (amely a spark 2.2-es HDI-fürt) többé nem támogatott. Csak a "PySpark" támogatott python. Ismert probléma, amely a spark 2.2-es terjeszt sikertelen, és a python3.
   
## <a name="livy-configuration"></a>Livy-konfiguráció
Livy-konfiguráció támogatott, azt beállíthatók a project Settings munkahelyi mappáját. További információ: [Livy információs](https://github.com/cloudera/livy/blob/master/README.rst ).

+ A Projektbeállítások között:

    ![Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ A támogatott Livy-konfigurációk:   

    **POST /batches**   
    A kérelem törzse

    | név | leírás | type | 
    | :- | :- | :- | 
    | fájl | Hajtsa végre az alkalmazást tartalmazó fájlt | elérési út (kötelező) | 
    | proxyUser | Felhasználó megszemélyesítése a feladat futtatásakor | sztring | 
    | Osztálynév | Alkalmazás Java/Spark main osztály | sztring |
    | args | Az alkalmazás a parancssori argumentumok | karakterláncok | 
    | JAR-fájlok kivételével | Ebben a munkamenetben használandó jars | Karakterlánc listája | 
    | pyFiles | Ebben a munkamenetben használandó Python-fájlok | Karakterlánc listája |
    | fájl megjelenítése | Ebben a munkamenetben használni kívánt fájlok | Karakterlánc listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | sztring |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | int |
    | executorMemory | Memória / végrehajtó folyamat használata | sztring |
    | executorCores | Minden egyes végrehajtó használandó magok száma | int |
    | numExecutors | A munkamenet elindításához végrehajtóval száma | int |
    | archívum | Ebben a munkamenetben használandó archívum | Karakterlánc listája |
    | üzenetsor | A neve, amelyhez a YARN várólista elküldve | sztring |
    | név | A munkamenet neve | sztring |
    | megerősítési | Spark-konfiguráció tulajdonságai | Térkép kulcs = érték |

    Választörzs   
    A létrehozott Batch-objektum.

    | név | leírás | type | 
    | :- | :- | :- | 
    | id | A munkamenet-azonosító | int | 
    | appId | Ez a munkamenet-alkalmazás alkalmazásazonosítója |  Sztring |
    | appInfo | Az alkalmazás részletes adatai | Térkép kulcs = érték |
    | napló | A napló sorok | karakterláncok |
    | state |   A batch-állapot | sztring |


## <a name="additional-features"></a>További funkciók

A VS Code HDInsight támogatja a következő funkciókat:

- **Automatikus kitöltés IntelliSense**. Javaslatok szót kulcsszó, módszerek, változók és így tovább. Különböző ikonjai a különböző objektumokat.

    ![HDInsight Tools for Visual Studio Code IntelliSense objektumtípusok](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Az IntelliSense hiba jelölő**. A nyelvi szolgáltatás kiemeli a szerkesztési által jelzett hibákat a Hive-parancsfájlnak.     
- **Szintaxis kiemelések**. A nyelvi szolgáltatás használ a különböző színek megkülönböztetéséhez változók, kulcsszavakat, adattípus, funkciók és így tovább. 

    ![Kiemeli a HDInsight Tools for Visual Studio Code-szintaxis](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>További lépések

### <a name="demo"></a>Bemutató
* A VS Code HDInsight: [videó](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [VPN-en keresztül távolról Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ssh-n keresztül távolról Spark-alkalmazások hibakeresése az IntelliJ-hez készült Azure eszközkészlet használata](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Az Eclipse-hez készült Azure-eszközkészlet HDInsight Tools használata Spark-alkalmazások létrehozásához](spark/apache-spark-eclipse-tool-plugin.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](spark/apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [A Visual Studio Code a PySpark interaktív környezetének beállítása](set-up-pyspark-interactive-environment.md)
* [Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használata ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Hozzon létre és alkalmazások futtatásához
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](spark/apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](spark/apache-spark-job-debugging.md)



