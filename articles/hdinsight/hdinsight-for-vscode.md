---
title: Az Azure HDInsight-eszközök – Visual Studio Code-ot használja a Hive, LLAP vagy PySpark |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozásához és elküldéséhez a lekérdezések és a parancsfájlokat.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 9603751db01eaffdf9fbe26164aed53017c5e23c
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499538"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight Tools for Visual Studio Code használata

Ismerje meg, hogyan használható a [Azure HDInsight Tools for Visual Studio Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) (a VS Code) létrehozásához és elküldéséhez [Apache Hive](https://hive.apache.org/) batch-feladatokat, interaktív Apache Hive-lekérdezéseket és PySpark szkripteket. Az Azure HDInsight-eszközök a VS Code által támogatott platformokat is telepíthető. Ezek közé tartozik a Windows, a Linux és a macOS. A különböző platformok előfeltételei is megtalálhatja.


## <a name="prerequisites"></a>Előfeltételek

A következő elemekre szükség, az ebben a cikkben ismertetett lépéseket követve:

- A HDInsight-fürt. Hozzon létre egy fürtöt, tekintse meg [HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono csak akkor szükséges, Linux és MacOS rendszeren.

## <a name="install-the-hdinsight-tools"></a>A HDInsight Tools telepítése
   
Az előfeltételek telepítése után az Azure HDInsight Tools for VS Code is telepítheti. 

### <a name="to-install-azure-hdinsight-tools"></a>Az Azure HDInsight-eszközök telepítése

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali panelen válassza ki a **bővítmények**. A Keresés mezőbe írja be a **HDInsight**.

3. A **Azure HDInsight Tools**válassza **telepítése**. Néhány másodperc elteltével a **telepítése** gomb felirata **Újrabetöltés**.

4. Válassza ki **Újrabetöltés** aktiválása a **Azure HDInsight Tools** bővítmény.

5. Válassza ki **Reload Window** megerősítéséhez. Látható **Azure HDInsight Tools** a a **bővítmények** ablaktáblán.

   ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Nyissa meg a HDInsight-munkaterület

Munkaterület létrehozása a VS Code-ban, mielőtt az csatlakozna az Azure-bA.

### <a name="to-open-a-workspace"></a>Munkaterület megnyitása

1. Az a **fájl** menüjében válassza **mappa megnyitása**. Ezután kijelölni a munkahelyi mappa egy létező mappát, vagy hozzon létre egy újat. A bal oldali panelen megjelenik a mappa.

2. A bal oldali panelen válassza ki a **új fájl** mellett a munkahelyi mappa ikonra.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)

3. Adjon nevet az új fájl a .hql (Hive-lekérdezések) vagy a (Spark-szkriptet) .py fájl kiterjesztése. 

## <a name="connect-to-hdinsight-cluster"></a>Csatlakozhat a HDInsight-fürt

Elküldés előtt szkriptek HDInsight-fürtök a VS Code-ból, szeretné-e csatlakozni az Azure-fiókjába, vagy egy fürtöt (az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott fiók).

### <a name="to-connect-to-azure"></a>Csatlakozás az Azure-bA

1. Hozzon létre egy új munkahelyi mappa és a egy új parancsfájlt, ha már nincs rájuk.

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és ezt követően a helyi menüben válassza **HDInsight: bejelentkezési**. Is megadhat **Ctrl + Shift + P**, majd adja meg **HDInsight: bejelentkezési**.

    ![HDInsight Tools for Visual Studio Code-bejelentkezés](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Jelentkezzen be, kövesse a bejelentkezési utasításait a **kimeneti** ablaktáblán.
    + Globális környezetben, a HDInsight bejelentkezési aktiválják Azure jelentkezzen be a folyamatot.

        ![Bejelentkezési utasítások az Azure-hoz](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Más környezetek esetén kövesse a bejelentkezési utasításokat.

        ![Bejelentkezési utasítások az más környezetben](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

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
    - Konfiguráció beállítása

<h3 id="linkcluster">A fürt összekapcsolása</h3>

Kapcsolat egy normál fürt használatával egy [Apache Ambari](https://ambari.apache.org/) felügyelt felhasználónév, vagy egy biztonságos Hadoop-fürtön vállalati biztonsági csomag hivatkozásra a tartomány felhasználónév használatával (például: user1@contoso.com).
1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: egy fürtöt**.

   ![hivatkozás fürt parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Adja meg a HDInsight fürt URL-cím-bemenet felhasználónév -> > be a jelszót a select -> fürttípus -> azt látható sikeres info ellenőrzési át.
   
   ![hivatkozás fürt párbeszédpanel](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > A társított felhasználónevet és jelszót használják, ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított. 
   
3. Parancs használatával megtekintheti a társított fürt **lista fürt**. Most már küldhet egy parancsfájlt, hogy a társított fürtöt.

   ![a csatolt fürt](./media/hdinsight-for-vscode/linked-cluster.png)

4. Is megszüntetheti a fürt által bevitelével **HDInsight: a fürt leválasztása** a parancskatalógus.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Egy általános Apache Livy-végpontra mutat.

1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**, majd adja meg **HDInsight: egy fürtöt**.
2. Válassza ki **általános Livy végpont**.
3. Adja meg az általános Livy-végpont, például: http://10.172.41.42:18080.
4. Válassza ki **alapszintű** mikor van szükség az általános Livy-végpont, egyébként, engedélyezési válassza **None**.
5. A bemeneti felhasználónév válassza **alapszintű** step4 a.
6. Be a jelszót, ha a select **alapszintű** step4 a.
7. Az általános livy endpoint összekapcsolás sikerült.

   ![a csatolt általános livy-fürthöz](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>HDInsight-fürtök listázása

A kapcsolat teszteléséhez, a HDInsight-fürtök listázhatja:

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>A HDInsight-fürtök listázása az Azure-előfizetéshez
1. Megnyit egy munkaterületet, és csatlakoztassa az Azure-bA. További információkért lásd: [nyissa meg a HDInsight-munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure](#connect-to-hdinsight-cluster).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: lista fürt** a helyi menüből. 

3. A HDInsight-fürtök megjelennek a **kimeneti** ablaktáblán.

    ![Egy fürt alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Egy alapértelmezett fürt beállítása
1. Megnyit egy munkaterületet, és csatlakozzon az Azure-bA. Lásd: [nyissa meg a HDInsight-munkaterület](#open-hdinsight-workspace) és [csatlakozás az Azure](#connect-to-hdinsight-cluster).

2. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: állítsa be a fürt alapértelmezett**. 

3. Válasszon egy fürtöt, az alapértelmezett fürt meg a jelenlegi parancsfájlt. Az eszközök automatikusan frissítse a konfigurációs fájl **. VSCode\settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása
1. Nyissa meg a parancskatalógust kiválasztásával **CTRL + SHIFT + P**.

2. Adja meg **HDInsight: állítsa be az Azure-környezet**.

3. Válasszon ki egy környezetet, például az "Azure" vagy "AzureChina" az alapértelmezett bejelentkezési bejegyzést.

4. Ugyanakkor az eszköz már mentette az alapértelmezett bejelentkezési bejegyzést **. VSCode\settings.json**. Emellett közvetlenül is frissíti, a konfigurációs fájlban. 

   ![Alapértelmezett bejelentkezési belépési konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Interaktív Hive-lekérdezések elküldéséhez, Hive kötegelt parancsfájlok

HDInsight Tools for VS Code elküldheti az interaktív Hive-lekérdezések, Hive kötegelt szkriptek HDInsight-fürtök.

1. Ha még nem tette, hozzon létre egy új munkamappát és egy új Hive-szkriptfájlt.

2. Az Azure-fiók vagy a hivatkozás-fürtök csatlakoztatásához.

3. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **HDInsight: interaktív Hive-** elküldheti a lekérdezést, vagy használja a helyi **Ctrl + Alt + I**. Válassza ki **HDInsight: Hive kötegelt** küldje el a szkriptet, vagy használja a helyi **Ctrl + Alt + H**. 

5. Válassza ki a fürtöt, ha még nem adott meg alapértelmezett fürt. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. Néhány pillanat múlva a lekérdezési eredmények jelennek meg, egy új lapon.

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTS** (EREDMÉNYEK) panel: A teljes eredményt elmentheti CSV-, JSON- vagy Excel-fájlban a helyi útvonalon, de kijelölhet csupán pár sort is.

    - **MESSAGES** (ÜZENETEK) panel: A **sor** számának kiválasztásakor a futó szkript első sorához ugrik.

## <a name="submit-interactive-pyspark-queries"></a>PySpark interaktív lekérdezések elküldése

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>A HDInsight Spark-fürtök interaktív PySpark-lekérdezések elküldéséhez.

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
4. Jelölje ki ezt a szkriptet. Ezután kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **HDInsight: PySpark interaktív**, vagy használja a helyi **Ctrl + Alt + I**.

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

### <a name="to-disable-environment-check"></a>Környezet ellenőrzés letiltása

Alapértelmezés szerint a HDInsight tools környezet ellenőrzése és függő csomagok telepítéséhez fog mikor a PySpark interaktív lekérdezések elküldéséhez. Környezet ellenőrzése letiltásához állítsa be a **hdinsight.disablePysparkEnvironmentValidation** való **Igen** alatt **felhasználói beállítások**.

   ![A beállítások a környezet ellenőrzés beállítása](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Másik lehetőségként kattintson **letiltása érvényesítési** gombot, ha a párbeszédpanel.

   ![Párbeszédpanelen adja meg a környezet ellenőrzése](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 Spark2.2/2.3 használata nem támogatott

PySpark3 már nem támogatott a Spark 2.2-es és Spark2.3 fürtön, csak a "PySpark" Python esetében támogatott. Ismert probléma, amely a spark 2.2-es/2.3 a helyrendszerekre sikertelen, és a Python3.

   ![Az elküldés elemre kattintva python3 szabályzatbeolvasási hiba](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Kövesse a lépéseket Python2.x használja: 

1. Telepítse a Python 2.7-t helyi számítógépen, és adja hozzá rendszerbeli elérési úton.

2. Indítsa újra a VSCode.

3. Python 2-re kattintva válthat a **Python XXX** a Status sávot, majd válassza ki a cél Python.

   ![Válassza ki a python-verzió](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

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
4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: PySpark Batch**, vagy használja a helyi **Ctrl + Alt + H**. 

5. Válassza ki a fürt, amelyre szeretné elküldeni a PySpark feladatot. 

   ![Küldje el a Python-feladat eredménye](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Miután elküldött egy Python-feladatot, a naplók küldése megjelennek a **kimeneti** ablak a VS Code-ban. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felületének URL-cím** is látható. Megnyithatja az URL-címet egy webböngészőben a feladat állapotának nyomon követését.

## <a name="apache-livy-configuration"></a>Az Apache Livy-konfiguráció

[Az Apache Livy](https://livy.incubator.apache.org/) konfiguráció támogatott, azt meg a **. VSCode\settings.json** munkahelyi terület mappában. Jelenleg a livy-konfigurációt csak támogatja a Python-szkriptet. További információ: [Livy információs](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Hogy miként indítható el livy-konfiguráció**
   
Megtalálhatja **fájl** menüben válassza **beállítások**, és válassza a **beállítások** helyi menüben. Kattintson a **MUNKATERÜLET BEÁLLÍTÁSAINAK** lapon megkezdheti a livy-konfiguráció beállítása.

Egy fájlt is elküldhet, figyelje meg a .vscode mappába kerül automatikusan a munkahelyi mappa. Annak a livy-konfiguráció kattintva **.vscode\settings.json**.

+ A Projektbeállítások között:

    ![Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>A beállítások **driverMomory** és **executorMomry**, állítsa be az értéket az egység, például 1 g vagy 1024 m. 

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
    | appId | Ez a munkamenet-alkalmazás alkalmazásazonosítója |  Karakterlánc |
    | appInfo | Az alkalmazás részletes adatai | Térkép kulcs = érték |
    | napló | A napló sorok | karakterláncok |
    | state |   A batch-állapot | sztring |

>[!NOTE]
>A hozzárendelt livy-konfigurációs megjelennek a tesztkimenet ablaktáblán amikor küldje el a szkriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrálása az Azure HDInsight Explorerből

Az Azure HDInsight bővült a bal oldali panelen. Keresse meg, és közvetlenül a fürt kezeléséhez.

1. Bontsa ki a **AZURE HDINSIGHT**, ha nem a bejelentkezési, jelenik **jelentkezzen be az Azure-bA...**  hivatkozásra.

    ![Jelentkezzen be a hivatkozás képe](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Kattintson a **jelentkezzen be Azure**, bejelentkezési hivatkozásra, és a kód böngésző alján jobb oldalon.

    ![Bejelentkezési utasítások az más környezetben](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Kattintson a **nyissa meg a & másolása** gomb megnyílik a böngészőben, illessze be a kódot, kattintson a **Folytatás** gombra, adja meg a bejelentkezési kapcsolatos mutató sikeresen fogja látni.

4. Miután bejelentkezett, a rendelkezésre álló előfizetésekről és a fürtök (a HBase, Spark és Hadoop támogatottak) jelenik meg **AZURE HDINSIGHT**. 

   ![Az Azure HDInsight-előfizetés](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Bontsa ki a fürtöt, hive metaadatok adatbázis és tábla sémájának megtekintéséhez.

   ![Azure HDInsight-fürt](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

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

* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások VPN-en keresztül távolról](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [IntelliJ-hez készült Azure eszközkészlet használata Apache Spark-alkalmazások távolról az ssh-n keresztül](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight Tools használata az Azure Toolkit for Eclipse Apache Spark-alkalmazások létrehozásához](spark/apache-spark-eclipse-tool-plugin.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](spark/apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](spark/apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](spark/apache-spark-jupyter-notebook-install-locally.md)
* [A Microsoft Power bi-ban az Azure HDInsight az Apache Hive-adatok megjelenítése](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [A Visual Studio Code a PySpark interaktív környezetének beállítása](set-up-pyspark-interactive-environment.md)
* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: interaktív adatelemzés a Spark on HDInsight használatával, BI-eszközökkel végrehajtása](spark/apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: használja a Spark on HDInsight az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Hozzon létre és alkalmazások futtatásához
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](spark/apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](spark/apache-spark-job-debugging.md)



