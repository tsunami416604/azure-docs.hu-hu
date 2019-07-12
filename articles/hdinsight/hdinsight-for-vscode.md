---
title: Azure HDInsight-eszközök – Visual Studio Code-ot használja a Hive, LLAP vagy PySpark
description: Ismerje meg, hogyan használható az Azure HDInsight Tools for Visual Studio Code létrehozásához és elküldéséhez a lekérdezések és a parancsfájlokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ebc41fc74d24708a177bf554029df8384c49df05
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657241"
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

3. Nevezze el az új fájl-vagy a `.hql` (Hive-lekérdezések) vagy a `.py` fájlkiterjesztés (Spark-szkriptet).  Ez a példa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

1. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

2. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Állítsa be az Azure-környezet**.

3. Az alapértelmezett bejelentkezési bejegyzésként válasszon ki egy környezetet.

4. Ugyanakkor az eszköz már mentette az alapértelmezett bejelentkezési bejegyzést **. VSCode\settings.json**. Emellett közvetlenül is frissítheti azt a konfigurációs fájlban. 

   ![Alapértelmezett bejelentkezési belépési konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Csatlakozás az Azure-fiókhoz

Elküldés előtt szkriptek HDInsight-fürtökhöz a Visual Studio Code-ból, szeretné-e csatlakozni az Azure-fiókjába, vagy egy fürtöt (az Ambari felhasználónév/jelszó és a tartományhoz csatlakoztatott fiók).  A következő lépéseket csatlakozni az Azure-bA:

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , és adja meg **HDInsight: Bejelentkezési**.

    ![HDInsight Tools for Visual Studio Code-bejelentkezés](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Kövesse az utasításokat a bejelentkezést a **kimeneti** ablaktáblán.
    + Az Azure globális környezetben **HDInsight: Bejelentkezési** parancs elindítja a **jelentkezzen be Azure** művelet a HDInsight Explorerben, és ez fordítva is igaz.

        ![Bejelentkezési utasítások az Azure-hoz](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Más környezetekben kövesse a bejelentkezési utasításokat.

        ![Bejelentkezési utasítások az más környezetben](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Miután csatlakozott, az Azure-fiók nevét a Visual Studio Code-ablak bal alsó állapotjelző sáv jelenik meg.  
  

## <a name="link-a-cluster"></a>Egy fürtöt

### <a name="link-azure-hdinsight"></a>Hivatkozás: Azure HDInsight

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



### <a name="link-generic-livy-endpoint"></a>Hivatkozás: Általános Livy-végpont

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

3. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **HDInsight: Állítsa be a fürthöz tartozó alapértelmezett**.  

4. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

5. Válasszon egy fürtöt, az alapértelmezett fürt meg a jelenlegi parancsfájlt. Az eszközök automatikusan frissítse a konfigurációs fájl **. VSCode\settings.json**. 

   ![Készlet alapértelmezett fürtkonfiguráció](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Interaktív Hive-lekérdezések elküldéséhez, Hive kötegelt parancsfájlok

HDInsight Tools for Visual Studio Code interaktív Hive-lekérdezések elküldéséhez, és Hive-batch-szkriptek HDInsight-fürtök.

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Válassza ki a fájlt **HelloWorld.hql** létrehozott [korábbi](#open-hdinsight-work-folder) , és megnyílik a parancsprogram-szerkesztő.


3. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

5. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **HDInsight: Interaktív Hive** elküldheti a lekérdezést, vagy használja a helyi **Ctrl + Alt + I**.  Válassza ki **HDInsight: Hive kötegelt** küldje el a szkriptet, vagy használja a helyi **Ctrl + Alt + H**.  

6. Válassza ki a fürtöt, ha még nem adott meg alapértelmezett fürt. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. Néhány pillanat múlva a lekérdezési eredmények jelennek meg, egy új lapon.

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **EREDMÉNYEK** panelen: Mentse az egész eredményt CSV, JSON vagy Excel-fájl helyi elérési útra, vagy csak válassza ki a több sort.

    - **ÜZENETEK** panelen: Ha bejelöli **sor** számát, azt ugrik a parancsprogram futtatásához az első sort.

## <a name="submit-interactive-pyspark-queries"></a>PySpark interaktív lekérdezések elküldése

PySpark interaktív lekérdezések nyújthatja be az alábbi lépéseket követve:

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Hozzon létre egy új fájlt **HelloWorld.py** következő a [korábbi](#open-hdinsight-work-folder) lépéseket.

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

4. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

5. Válassza ki az összes a kódot, és kattintson a jobb gombbal a parancsprogram-szerkesztő, válassza ki **HDInsight: PySpark interaktív** elküldheti a lekérdezést, vagy használja a helyi **Ctrl + Alt + I**.

   ![Kattintson a jobb gombbal a pyspark interaktív](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Válassza ki a fürtöt, ha még nem adott meg alapértelmezett fürt. Néhány pillanat múlva a **Python interaktív eredmények** egy új lap jelenik meg. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. 

   ![interaktivní okno pyspark interaktív pythonu](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Adjon meg **"%% info"** , és nyomja le az **Shift + Enter** feladat adatainak megtekintéséhez. (Nem kötelező)

   ![feladat adatainak megtekintése](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Az eszköz is támogatja a **Spark SQL** lekérdezés.

   ![Pyspark interaktív nézet eredmény](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   A beküldési állapot jelenik meg az alsó állapotsor bal lekérdezések futtatásakor használ. További lekérdezések ne küldjön el, ha az állapot értéke **PySpark kernelt (foglalt)** .  

   > [!NOTE] 
   >
   > Amikor **Python-bővítmény engedélyezve van** nincs bejelölve, a beállítások (a az alapértelmezett beállítás be van jelölve), az elküldött pyspark interakció eredményeket fogja használni a régi ablakot.
   >
   > ![pyspark interaktív python bővítmény le van tiltva](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>PySpark batch-feladat elküldése

1. Nyissa meg újra a mappa **HDexample** létrehozott [korábbi](#open-hdinsight-work-folder) ha zárva.  

2. Hozzon létre egy új fájlt **BatchFile.py** következő a [korábbi](#open-hdinsight-work-folder) lépéseket.

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

4. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

5. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **HDInsight: PySpark Batch**, vagy használja a helyi **Ctrl + Alt + H**. 

6. Válassza ki a fürt, amelyre szeretné elküldeni a PySpark feladatot. 

   ![Küldje el a Python-feladat eredménye](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Miután elküldött egy Python-feladatot, a naplók küldése megjelennek a **kimeneti** ablak a Visual Studio Code-ban. A **Spark felhasználói felület URL-cím** és **Yarn felhasználói felületének URL-cím** is látható. Megnyithatja az URL-címet egy webböngészőben a feladat állapotának nyomon követését.

## <a name="apache-livy-configuration"></a>Az Apache Livy-konfiguráció

[Az Apache Livy](https://livy.incubator.apache.org/) konfiguráció támogatott, akkor beállíthatja a **. VSCode\settings.json** a munkahelyi terület mappában. Jelenleg a livy-konfigurációt csak támogatja a Python-szkriptet. További információ: [Livy információs](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Hogy miként indítható el livy-konfiguráció**

1\. módszer  
1. Lépjen a menüsoron **fájl** > **beállítások** > **beállítások**.  
2. Az a **keresés a beállításokban** szövegmezőben adja meg **HDInsight feladat Sumission: Livy-Conf**.  
3. Válassza ki **Szerkesztés a settings.json** a releváns keresési eredmény.

2\. módszer   
Fájl elküldése, figyelje meg, hogy a .vscode mappa automatikusan bekerül a munkahelyi mappa. Annak a livy-konfiguráció kattintva **.vscode\settings.json**.

+ A Projektbeállítások között:

    ![Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>A beállítások **driverMomory** és **executorMomry**, állítsa be az értéket az egység, például 1 g vagy 1024 m. 

+ A támogatott Livy-konfigurációk:   

    **POST /batches**   
    A kérelem törzse

    | name | description | type | 
    | :- | :- | :- | 
    | file | Hajtsa végre az alkalmazást tartalmazó fájlt | elérési út (kötelező) | 
    | proxyUser | Felhasználó megszemélyesítése a feladat futtatásakor | Karakterlánc | 
    | className | Alkalmazás Java/Spark main osztály | Karakterlánc |
    | args | Az alkalmazás a parancssori argumentumok | karakterláncok | 
    | jars | Ebben a munkamenetben használandó jars | Karakterlánc listája | 
    | pyFiles | Ebben a munkamenetben használandó Python-fájlok | Karakterlánc listája |
    | files | Ebben a munkamenetben használni kívánt fájlok | Karakterlánc listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Karakterlánc |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | int |
    | executorMemory | Memória / végrehajtó folyamat használata | Karakterlánc |
    | executorCores | Minden egyes végrehajtó használandó magok száma | int |
    | numExecutors | A munkamenet elindításához végrehajtóval száma | int |
    | archives | Ebben a munkamenetben használandó archívum | Karakterlánc listája |
    | queue | A neve, amelyhez a YARN várólista elküldve | Karakterlánc |
    | name | A munkamenet neve | Karakterlánc |
    | conf | Spark-konfiguráció tulajdonságai | Térkép kulcs = érték |

    Választörzs   
    A létrehozott Batch-objektum.

    | name | description | type | 
    | :- | :- | :- | 
    | id | A munkamenet-azonosító | int | 
    | appId | Ez a munkamenet-alkalmazás alkalmazásazonosítója |  Sztring |
    | appInfo | Az alkalmazás részletes adatai | Térkép kulcs = érték |
    | log | A napló sorok | karakterláncok |
    | state |   A batch-állapot | Karakterlánc |

>[!NOTE]
>A hozzárendelt livy-konfigurációs megjelennek a tesztkimenet ablaktáblán amikor küldje el a szkriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrálása az Azure HDInsight Explorerből

**Az Azure HDInsight** az Intézőben bővült. Keresse meg, és közvetlenül keresztül a fürt kezeléséhez **Azure HDInsight**.

1. [Csatlakozás](#connect-to-azure-account) és az Azure-fiók, vagy a fürt hivatkozásra, ha még nem tette.

2. Lépjen a menüsoron **nézet** > **Explorer**.

3. A bal oldali ablaktáblán, bontsa ki a **AZURE HDINSIGHT**.  A rendelkezésre álló előfizetésekről és a fürtök (a HBase, Spark és Hadoop támogatottak) jelennek meg. 

   ![Az Azure HDInsight-előfizetés](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Bontsa ki a fürtöt, hive metaadatok adatbázis és tábla sémájának megtekintéséhez.

   ![Az Azure HDInsight-fürt](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Hive-tábla előzetes verzió
Hive-tábla megtekinthetjük a fürt közvetlenül keresztül **Azure HDInsight** explorer.
1. [Csatlakozás](#connect-to-azure-account) az Azure-fiókjába, ha még nem tette.

2. Kattintson a **Azure** ikonra a bal szélső oszlopában.

3. A bal oldali ablaktáblán bontsa ki az AZURE HDINSIGHT. A rendelkezésre álló előfizetésekről és a fürtök jelennek meg.

4. Bontsa ki a fürtöt, hive metaadatok adatbázis és tábla sémájának megtekintéséhez.

5. Kattintson a jobb gombbal a Hive-tábla, például: hivesampletable. Válassza ki **előzetes**. 

   ![HDInsight vscode előzetes hive-tábla](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A **eredmények villámnézetében** ablakban nyílnak meg.

   ![HDInsight vscode előzetes eredmények ablak](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **EREDMÉNYEK** panel

   Mentse az egész eredményt CSV, JSON vagy Excel-fájl helyi elérési útra, vagy csak válassza ki a több sort.

- **ÜZENETEK** panel
   1. Ha a tábla sorainak száma 100 sornál nagyobb, az üzenet tartalmazza: **Az első 100 sorok jelennek meg a Hive-tábla**.
   2. Ha a tábla sorainak száma legfeljebb 100 sor, az üzenet tartalmazza: **60 sorok jelennek meg a Hive-tábla**.
   3. Nincs tartalom nem található a táblában, az üzenetet jelenít meg: **0 sor jelenik meg a Hive-tábla**.

>[!NOTE]
>
>A Linux xclip tábla adatok másolása engedélyezéséhez telepítse.
>
>![HDInsight vscode a linux-](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>További funkciók

A Visual Studio Code HDInsight támogatja a következő funkciókat:

- **Az automatikus kiegészítés IntelliSense**. Javaslatok szót kulcsszó, módszerek, változók és így tovább. Különböző ikonjai a különböző objektumokat.

    ![HDInsight Tools for Visual Studio Code IntelliSense objektumtípusok](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Az IntelliSense hiba jelölő**. A nyelvi szolgáltatás kiemeli a szerkesztési által jelzett hibákat a Hive-parancsfájlnak.     
- **Szintaxis kiemelések**. A nyelvi szolgáltatás használ a különböző színek megkülönböztetéséhez változók, kulcsszavakat, adattípus, funkciók és így tovább. 

    ![Kiemeli a HDInsight Tools for Visual Studio Code-szintaxis](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Csak olvasói szerepköre

Fürt rendelkező felhasználók **olvasó** **csak** **szerepkör** már nem lehet elküldeni a feladatot a HDInsight-fürthöz sem megtekintheti a Hive-adatbázis. Forduljon a szerepkör frissítése a fürt rendszergazdája [ **HDInsight** **fürt** **operátor** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) a a [ Az Azure portal](https://ms.portal.azure.com/). Ha ismeri az Ambari hitelesítő adatokat, manuálisan a fürtöt, az alábbi utasítások a következő kapcsolat.

### <a name="browse-hdinsight-cluster"></a>Keresse meg a HDInsight-fürt  

Amikor az Azure HDInsight explorer egy HDInsight-fürt kibontásához, mutató hivatkozást a fürtöt, ha a fürt csak olvasó szerepkör kéri. Kövesse az alábbi lépéseket a fürt Ambari hitelesítő adatok használatával mutat. 

### <a name="submit-job-to-hdinsight-cluster"></a>HDInsight-fürthöz feladat elküldése

HDInsight-fürthöz feladat elküldésekor a fürtöt mutat, ha a fürt csak olvasó szerepkör kéri. Kövesse az alábbi lépéseket a fürt Ambari hitelesítő adatok használatával mutat. 

### <a name="link-to-cluster"></a>Fürt mutató hivatkozás

1.  Az Ambari felhasználónév megadása 
2.  Adja meg az Ambari felhasználói jelszót.

   ![HDInsight Tools for Visual Studio Code-felhasználónév](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools for Visual Studio Code-jelszó](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>HDInsight használhatja: Lista fürt ellenőrizze a társított fürtöt.
>
>![HDInsight Tools for Visual Studio Code-olvasó társított](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Az Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Keresse meg az ADLS Gen2-fiók

Bontsa ki az ADLS Gen2 fiókkal az Azure HDInsight explorer kattint, amikor kéri, adja meg a tároló **hozzáférési kulcs** Ha Azure-fiók nem rendelkezik hozzáféréssel a Gen2-tárolót. Az ADLS Gen2 fiók lesz az automatikus kibontva, ha a hozzáférési kulcs ellenőrzése sikeresen befejeződött. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>HDInsight-fürt ADLS Gen2-feladatok elküldése

Egy HDInsight-fürtöt az ADLS Gen2 feladat elküldésekor kéri, adja meg a tároló **hívóbetű** Ha az Azure-fiók nem rendelkezik nincs írási hozzáférés a Gen2-tárolót.  A feladat sikerült benyújtani, ha a hozzáférési kulcs ellenőrzése sikeresen befejeződött. 

![HDInsight Tools for Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Tárfiók elérési kulcsának kaphat az Azure Portalról. További információ: [tárelérési kulcsok megtekintése és másolása](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Fürt leválasztása

1. Lépjen a menüsoron **nézet** > **Parancskatalógus...** , majd adja meg **HDInsight: A fürt leválasztása**.  

2. Válassza ki a leválasztani a fürtöt.  

3. Felülvizsgálat **kimeneti** ellenőrzési nézetet.  

## <a name="sign-out"></a>Kijelentkezés  

Lépjen a menüsoron **nézet** > **Parancskatalógus...** , majd adja meg **HDInsight: Kijelentkezési**.  Egy előugró ablak az kell az alsó jobb oldali sarokban megjelölve **kijelentkezési sikerült!** .


## <a name="next-steps"></a>További lépések
Egy bemutató videót, a HDInsight segítségével a Visual Studio Code, [HDInsight, a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
