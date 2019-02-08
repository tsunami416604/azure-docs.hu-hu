---
title: Az Azure HDInsight-eszközök – Visual Studio Code-ot használja a Hive, LLAP vagy PySpark |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozásához és elküldéséhez a lekérdezések és a parancsfájlokat.
Keywords: Visual Studio Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 3301f3cdea1795db3b17bc2fa7da05bf362c744d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895069"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Az Azure HDInsight Tools for Visual Studio Code használata

Ismerje meg, hogyan hozhat létre, és küldje el az Apache Hive kötegelt feladatokat, interaktív Hive-lekérdezéseket és PySpark szkripteket az Apache Spark az Azure HDInsight Tools for Visual Studio Code használata. Először ismertetjük a HDInsight-eszközök telepítése a Visual Studio Code-ban, és majd végigvezetjük a Hive és a Spark-feladatok elküldése hogyan.  

Az Azure HDInsight Tools-platformokat, amelyek támogatottak a Visual Studio Code-ban az többek között Windows, Linux és MacOS rendszeren is telepíthető. Az alábbiakban találja a különböző platformok előfeltételeit.


## <a name="prerequisites"></a>Előfeltételek

A következő elemekre szükség, az ebben a cikkben ismertetett lépéseket követve:

- Egy HDInsight-fürtön. Hozzon létre egy fürtöt, tekintse meg [HDInsight – első lépések](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono csak akkor szükséges, Linux és MacOS rendszeren.
- A [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) a Visual Studio Code.
- [A PySpark interaktív környezetének beállítása a Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Egy helyi könyvtárba nevű **HDexample**.  Ez a cikk **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Az Azure HDInsight-eszközök telepítése

Miután végrehajtotta az előfeltételeket, Azure HDInsight Tools for Visual Studio Code is telepítheti.  A következő lépéseket az Azure HDInsight Tools telepítése:

1. Nyissa meg a Visual Studio Code-ot.

2. Lépjen a menüsoron **nézet** > **bővítmények**.

3. A Keresés mezőbe írja be a **HDInsight**.

4. Válassza ki **Azure HDInsight Tools** a keresési eredmények, és válassza ki a **telepítése**.  

   ![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Válassza ki **Újrabetöltés** aktiválása a **Azure HDInsight Tools** bővítmény a telepítést követően.


## <a name="open-hdinsight-work-folder"></a>Nyissa meg a HDInsight munkahelyi mappa

A következő lépéseket a munkahelyi mappa megnyitásához, és hozzon létre egy fájlt a Visual Studio Code-ban:

1. Lépjen a menüsoron **fájl** > **mappa megnyitása...**   >  **C:\HD\HDexample**, majd válassza ki a **mappa kiválasztása** gombra. A mappa megjelenik a **Explorer** a bal oldali nézet.

2. Az a **Explorer** megtekinteni, válassza ki a mappát, **HDexample**, majd a **új fájl** mellett a munkahelyi mappa ikonra.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)

3. Adjon nevet az új fájl a .hql (Hive-lekérdezések) vagy a (Spark-szkriptet) .py fájl kiterjesztése.  Ez a példa **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Csatlakozhat a HDInsight-fürt

Elküldés előtt szkriptek HDInsight-fürtökhöz a Visual Studio Code-ból, szeretné-e csatlakozni az Azure-fiókjába, vagy egy fürtöt (az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott fiók).  A következő lépéseket csatlakozni az Azure-bA:

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Bejelentkezési**.

    ![HDInsight Tools for Visual Studio Code-bejelentkezés](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Bejelentkezési utasításait a **kimeneti** ablaktáblán.
    + Az Azure globális környezetben **HDInsight: Bejelentkezési** parancs elindítja a **jelentkezzen be Azure** művelet a HDInsight Explorerben, és ez fordítva is igaz.

        ![Bejelentkezési utasítások az Azure-hoz](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Más környezetek esetén kövesse a bejelentkezési utasításokat.

        ![Bejelentkezési utasítások az más környezetben](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

 Miután csatlakozott, az Azure-fiók nevét a Visual Studio Code-ablak bal alsó állapotjelző sáv jelenik meg.  
  

<h2 id="linkcluster">Hivatkozás létrehozásához: Azure HDInsight</h2>

Kapcsolat egy normál fürt használatával egy [Apache Ambari](https://ambari.apache.org/) felügyelt felhasználónév, vagy egy biztonságos Hadoop-fürtön vállalati biztonsági csomag hivatkozásra a tartomány felhasználónév használatával (például: user1@contoso.com).

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Egy fürtöt**.

   ![hivatkozás fürt parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Válassza ki a csatolt fürttípus **Azure HDInsight**.

3. Adja meg a HDInsight-fürt URL-CÍMÉT.

4. Adja meg az Ambari felhasználói nevét, az alapértelmezett a **rendszergazdai**.

5. Adja meg az Ambari-jelszó.

6. Válassza ki a fürt típusát.

7. Felülvizsgálat **kimeneti** ellenőrzési nézetet.

   > [!NOTE]  
   > A társított felhasználónevet és jelszót használják, ha a fürt egyaránt bejelentkezett az Azure-előfizetés és a fürthöz társított.  


## <a name="create-link-generic-livy-endpoint"></a>Hivatkozás létrehozásához: Általános Livy-végpont

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Egy fürtöt**.

2. Válassza ki a csatolt fürttípus **általános Livy-végpont**.

3. Adja meg az általános Livy-végpont, például: http\:/ / 10.172.41.42:18080.

4. Válassza ki a hitelesítési típus **alapszintű** vagy **None**.  Ha **alapszintű**, majd:  
    &emsp;a. Adja meg az Ambari felhasználói nevét, az alapértelmezett a **rendszergazdai**.  
    &emsp;b. Adja meg az Ambari-jelszó.

5. Felülvizsgálat **kimeneti** ellenőrzési nézetet.

## <a name="list-hdinsight-clusters"></a>HDInsight-fürtök listázása

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Fürt listában**.

2. Válassza ki a kívánt előfizetés azonosítóértékét.

3. Tekintse át a **kimeneti** megtekintése.  A csatolt fürt és az Azure-előfizetéshez tartozó összes fürt látható.

    ![Egy fürt alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Alapértelmezett fürt

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Válassza ki a fájlt **HelloWorld.hql** létrehozott [korábbi](#open-hdinsight-work-folder) , és megnyílik a parancsprogram-szerkesztő.

3. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

4. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **HDInsight: Állítsa be a fürthöz tartozó alapértelmezett**.  

5. Válasszon egy fürtöt, az alapértelmezett fürt meg a jelenlegi parancsfájlt. Az eszközök automatikusan frissítse a konfigurációs fájl **. VSCode\settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

1. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

2. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Állítsa be az Azure-környezet**.

3. Az alapértelmezett bejelentkezési bejegyzésként válasszon ki egy környezetet.

4. Ugyanakkor az eszköz már mentette az alapértelmezett bejelentkezési bejegyzést **. VSCode\settings.json**. Emellett közvetlenül is frissítheti azt a konfigurációs fájlban. 

   ![Alapértelmezett bejelentkezési belépési konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Interaktív Hive-lekérdezések elküldéséhez, Hive kötegelt parancsfájlok

HDInsight Tools for Visual Studio Code interaktív Hive-lekérdezések elküldéséhez, és Hive-batch-szkriptek HDInsight-fürtök.

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Válassza ki a fájlt **HelloWorld.hql** létrehozott [korábbi](#open-hdinsight-work-folder) , és megnyílik a parancsprogram-szerkesztő.

3. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

4. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **HDInsight: Interaktív Hive** elküldheti a lekérdezést, vagy használja a helyi **Ctrl + Alt + I**.  Válassza ki **HDInsight: Hive kötegelt** küldje el a szkriptet, vagy használja a helyi **Ctrl + Alt + H**.  

6. Válassza ki a fürtöt, ha még nem adott meg alapértelmezett fürt. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. Néhány pillanat múlva a lekérdezési eredmények jelennek meg, egy új lapon.

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **EREDMÉNYEK** panelen: Mentse az egész eredményt CSV, JSON vagy Excel-fájl helyi elérési útra, vagy csak válassza ki a több sort.

    - **ÜZENETEK** panelen: Ha bejelöli **sor** számát, azt ugrik a parancsprogram futtatásához az első sort.

## <a name="submit-interactive-pyspark-queries"></a>PySpark interaktív lekérdezések elküldése

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Hozzon létre egy új fájlt **HelloWorld.py** következő a [korábbi](#open-hdinsight-work-folder) lépéseket.

3. Egy Python-bővítmény javaslat párbeszédpanel fog kapni, ha még nem telepítette a Python esetében az előfeltételként szükséges szoftvert.  Telepítse, és töltse be újra a Visual Studio Code, a telepítés befejezéséhez.

    >![HDInsight Visual Studio Code Python-telepítés](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

5. Másolja és illessze be a parancsfájlt a következő kódot:
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

6. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **HDInsight: PySpark interaktív** elküldheti a lekérdezést, vagy használja a helyi **Ctrl + Alt + I**.  

7. Válassza ki a fürtöt, ha még nem adott meg alapértelmezett fürt. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. Néhány pillanat múlva a lekérdezési eredmények jelennek meg, egy új lapon.

   ![Küldje el a Python-feladat eredménye](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. Az eszköz is támogatja a **SQL záradék** lekérdezés.

   ![Küldje el a Python-feladat eredményének](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) a beküldés állapotát a bal oldali sávon, amikor lekérdezést futtat az alsó állapot jelenik meg. További lekérdezések ne küldjön el, ha az állapot értéke **PySpark kernelt (foglalt)**.  

>[!NOTE]  
>A fürtök is fenntartható a munkamenet-információk. A definiált változó, a függvény és a megfelelő értékeket a munkamenetet, így azok között ugyanazon fürt több szolgáltatás-hívást lehet hivatkozni tartanak. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 Spark2.2/2.3 használata nem támogatott

PySpark3 már nem támogatott a Spark 2.2-es és Spark2.3 fürtön, csak a "PySpark" Python esetében támogatott. Ismert probléma, amely a spark 2.2-es/2.3 a helyrendszerekre sikertelen, és a Python3.

   ![Az elküldés elemre kattintva python3 szabályzatbeolvasási hiba](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Kövesse a lépéseket Python2.x használja: 

1. Telepítse a Python 2.7-t helyi számítógépen, és adja hozzá rendszerbeli elérési úton.

2. Indítsa újra a Visual Studio Code-ot.

3. Python 2-re kattintva válthat a **Python XXX** a Status sávot, majd válassza ki a cél Python.

   ![Válassza ki a python-verzió](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>PySpark batch-feladat elküldése

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Hozzon létre egy új fájlt **BatchFile.py** következő a [korábbi](#open-hdinsight-work-folder) lépéseket.

3. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

4. Másolja és illessze be a parancsfájlt a következő kódot:

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

Miután elküldött egy Python-feladatot, a naplók küldése megjelennek a **kimeneti** ablak a Visual Studio Code-ban. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felületének URL-cím** is látható. Megnyithatja az URL-címet egy webböngészőben a feladat állapotának nyomon követését.

## <a name="apache-livy-configuration"></a>Az Apache Livy-konfiguráció

[Az Apache Livy](https://livy.incubator.apache.org/) konfiguráció támogatott, akkor beállíthatja a **. VSCode\settings.json** a munkahelyi terület mappában. Jelenleg a livy-konfigurációt csak támogatja a Python-szkriptet. További információ: [Livy információs](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Hogy miként indítható el livy-konfiguráció**

1. módszer  
1. Lépjen a menüsoron **fájl** > **beállítások** > **beállítások**.  
2. Az a **keresés a beállításokban** szövegmezőben adja meg **HDInsight feladat Sumission: Livy-Conf**.  
3. Válassza ki **Szerkesztés a settings.json** a releváns keresési eredmény.

2. módszer   
Fájl elküldése, figyelje meg, hogy a .vscode mappa automatikusan bekerül a munkahelyi mappa. Annak a livy-konfiguráció kattintva **.vscode\settings.json**.

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
    | proxyUser | Felhasználó megszemélyesítése a feladat futtatásakor | Karakterlánc | 
    | Osztálynév | Alkalmazás Java/Spark main osztály | Karakterlánc |
    | args | Az alkalmazás a parancssori argumentumok | karakterláncok | 
    | JAR-fájlok kivételével | Ebben a munkamenetben használandó jars | Karakterlánc listája | 
    | pyFiles | Ebben a munkamenetben használandó Python-fájlok | Karakterlánc listája |
    | fájl megjelenítése | Ebben a munkamenetben használni kívánt fájlok | Karakterlánc listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Karakterlánc |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | int |
    | executorMemory | Memória / végrehajtó folyamat használata | Karakterlánc |
    | executorCores | Minden egyes végrehajtó használandó magok száma | int |
    | numExecutors | A munkamenet elindításához végrehajtóval száma | int |
    | archívum | Ebben a munkamenetben használandó archívum | Karakterlánc listája |
    | várólista | A neve, amelyhez a YARN várólista elküldve | Karakterlánc |
    | név | A munkamenet neve | Karakterlánc |
    | megerősítési | Spark-konfiguráció tulajdonságai | Térkép kulcs = érték |

    Választörzs   
    A létrehozott Batch-objektum.

    | név | leírás | type | 
    | :- | :- | :- | 
    | id | A munkamenet-azonosító | int | 
    | appId | Ez a munkamenet-alkalmazás alkalmazásazonosítója |  String |
    | appInfo | Az alkalmazás részletes adatai | Térkép kulcs = érték |
    | napló | A napló sorok | karakterláncok |
    | state |   A batch-állapot | Karakterlánc |

>[!NOTE]
>A hozzárendelt livy-konfigurációs megjelennek a tesztkimenet ablaktáblán amikor küldje el a szkriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrálása az Azure HDInsight Explorerből

**Az Azure HDInsight** az Intézőben bővült. Keresse meg, és közvetlenül keresztül a fürt kezeléséhez **Azure HDInsight**.

1. [Csatlakozás](#connect-to-hdinsight-cluster) az Azure-fiókjába, ha még nem tette.

2. Lépjen a menüsoron **nézet** > **Explorer**.

3. A bal oldali ablaktáblán, bontsa ki a **AZURE HDINSIGHT**.  A rendelkezésre álló előfizetésekről és a fürtök (a HBase, Spark és Hadoop támogatottak) jelennek meg. 

   ![Az Azure HDInsight-előfizetés](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Bontsa ki a fürtöt, hive metaadatok adatbázis és tábla sémájának megtekintéséhez.

   ![Azure HDInsight-fürt](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>További funkciók

A Visual Studio Code HDInsight támogatja a következő funkciókat:

- **Automatikus kitöltés IntelliSense**. Javaslatok szót kulcsszó, módszerek, változók és így tovább. Különböző ikonjai a különböző objektumokat.

    ![HDInsight Tools for Visual Studio Code IntelliSense objektumtípusok](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Az IntelliSense hiba jelölő**. A nyelvi szolgáltatás kiemeli a szerkesztési által jelzett hibákat a Hive-parancsfájlnak.     
- **Szintaxis kiemelések**. A nyelvi szolgáltatás használ a különböző színek megkülönböztetéséhez változók, kulcsszavakat, adattípus, funkciók és így tovább. 

    ![Kiemeli a HDInsight Tools for Visual Studio Code-szintaxis](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Fürt leválasztása

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , majd adja meg **HDInsight: A fürt leválasztása**.  

2. Válassza ki a leválasztani a fürtöt.  

3. Felülvizsgálat **kimeneti** ellenőrzési nézetet.  


## <a name="logout"></a>Kijelentkezés  

Lépjen a menüsoron **nézet** > **Parancskatalógus...** , majd adja meg **HDInsight: Kijelentkezési**.  Egy előugró ablak az kell az alsó jobb oldali sarokban megjelölve **kijelentkezési sikerült!**.


## <a name="next-steps"></a>További lépések
Egy bemutató videót, a HDInsight segítségével a Visual Studio Code, [HDInsight, a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
