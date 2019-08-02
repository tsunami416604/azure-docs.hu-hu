---
title: Azure HDInsight a Visual Studio Code-hoz
description: Ismerje meg, hogyan hozhat létre és küldhet le lekérdezéseket és parancsfájlokat a Spark &-struktúra eszközeivel (Azure HDInsight) a Visual Studio Code használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489016"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>A Spark &-struktúra eszközeinek használata a Visual Studio Code-hoz

Megtudhatja, hogyan hozhat létre és küldhet el Apache Hive batch-feladatokat, interaktív kaptár-lekérdezéseket és PySpark parancsfájlokat Apache Sparkhoz a Spark & kaptár Tools for Visual Studio Code használatával. Először is leírjuk, hogyan telepítheti a Spark & kaptár-eszközöket a Visual Studio Code-ban, majd végigvezeti a feladatok a kaptárba és a Sparkba való beküldésének módján.  

A Spark & struktúra eszközei a Visual Studio Code által támogatott platformokon telepíthetők, amelyek Windows, Linux és macOS rendszereken is használhatók. Az alábbiakban megtalálhatja a különböző platformokra vonatkozó előfeltételeket.


## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- An méretű HDInsight-fürt. Fürt létrehozásához tekintse meg [az Ismerkedés a HDInsight szolgáltatással](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)című témakört. Vagy egy Spark/kaptár fürt, amely támogatja a Livy-végpontot.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Monó](https://www.mono-project.com/docs/getting-started/install/). A Mono csak a Linux és a macOS rendszerekhez szükséges.
- [PySpark interaktív környezet beállítása a Visual Studio Code-hoz](set-up-pyspark-interactive-environment.md).
- Egy **HDexample**nevű helyi könyvtár.  Ez a cikk a **C:\HD\HDexample**-t használja.

## <a name="install-spark--hive-tools"></a>A Spark & Kas eszközeinek telepítése

Az előfeltételek elvégzése után telepítheti a Spark & kaptár Tools for Visual Studio Code-ot.  A Spark & kaptár eszközeinek telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Visual Studio Code-ot.

2. A menüsávban navigáljon a bővítmények **megtekintéséhez** > .

3. A keresőmezőbe írja be a **Spark & kaptár**kifejezést.

4. Válassza ki a **Spark & struktúra eszközöket** a keresési eredmények közül, majd válassza a **telepítés**lehetőséget.  

   ![Spark & struktúra a Visual Studio Code Python telepítéséhez](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Szükség esetén **töltse be újra** .


## <a name="open-work-folder"></a>Munkahelyi mappa megnyitása

A következő lépések végrehajtásával nyisson meg egy munkahelyi mappát, és hozzon létre egy fájlt a Visual Studio Code-ban:

1. A menüsávban navigáljon a **fájl** > **megnyitása mappába..** . C:\HD\HDexample, majd kattintson a **mappa kiválasztása** gombra.   >  A mappa a bal oldali **Explorer** nézetben jelenik meg.

2. Az **Explorer** nézetből válassza ki a mappát, a **HDexample**, majd a munkahelyi mappa melletti **új fájl** ikont.

   ![Új fájl](./media/hdinsight-for-vscode/new-file.png)

3. Nevezze el az új fájlt a `.hql` (kaptár-lekérdezésekkel) vagy a `.py` (Spark script) kiterjesztésű fájllal.  Ez a példa a **HelloWorld. HQL**-t használja.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

A National Cloud User esetében kövesse az Azure-környezet első beállításának lépéseit, **majd használja az Azure-t: Jelentkezzen** be az Azure-ba való bejelentkezéshez.
   
1. Kattintson a **File\Preferences\Settings**elemre.
2. Keresés **az Azure-ban: Felhő**
3. Válassza ki a nemzeti felhőt a listából.

   ![Bejelentkezési bejegyzés alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Kapcsolódás Azure-fiókhoz

Mielőtt parancsfájlokat szeretne küldeni a fürtökhöz a Visual Studio Code-ból, csatlakoznia kell az Azure-fiókjához, vagy hozzá kell kötnie egy fürtöt (Ambari Felhasználónév/jelszó vagy tartományhoz csatlakoztatott fiók használatával).  Az Azure-hoz való kapcsolódáshoz kövesse az alábbi lépéseket:

1. A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , majd **adja meg az Azure-t: Jelentkezzen**be.

    ![A Spark & struktúra eszközei a Visual Studio Code-beli bejelentkezéshez](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezés utasításait. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.  
  

## <a name="link-a-cluster"></a>Fürt csatolása

### <a name="link-azure-hdinsight"></a>Hivatkozás: Azure HDInsight

Az [Apache Ambari](https://ambari.apache.org/) által felügyelt felhasználónévvel vagy a vállalati biztonsági csomag biztonságos Hadoop-fürttel való összekapcsolásához egy tartományi Felhasználónév (például: `user1@contoso.com`) használatával kapcsolódhat egy normál fürthöz.

1. A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , és **írja be a Spark/kaptár: Fürt**csatolása.

   ![fürt csatolása parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Válassza a csatolt fürt típusa **Azure HDInsight**elemet.

3. Adja meg a HDInsight-fürt URL-címét.

4. Adja meg a Ambari felhasználónevet, az alapértelmezett érték a **rendszergazda**.

5. Adja meg a Ambari jelszavát.

6. Válassza ki a fürt típusát.

7. Adja meg a fürt megjelenítendő nevét (nem kötelező).

8. Tekintse át az ellenőrzés **kimeneti** nézetét.

   > [!NOTE]  
   > A rendszer a csatolt felhasználónevet és jelszót használja, ha a fürt mind az Azure-előfizetésben jelentkezett be, mind a fürthöz csatlakoztatva van.  


### <a name="link-generic-livy-endpoint"></a>Hivatkozás: Általános Livy végpont

1. A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , és **írja be a Spark/kaptár: Fürt**csatolása.

2. Válassza a csatolt fürt típusa **általános Livy végpont**elemet.

3. Adja meg az általános Livy végpontot, például:\:http//10.172.41.42:18080.

4. Válassza az **Alap** vagy a **none**engedélyezési típust.  Ha alapszintű, akkor:  
    &emsp;a. Adja meg a Ambari felhasználónevet, az alapértelmezett érték a **rendszergazda**.  
    &emsp;b. Adja meg a Ambari jelszavát.

5. Tekintse át az ellenőrzés **kimeneti** nézetét.

## <a name="list-clusters"></a>Fürtök listázása

1. A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , és **írja be a Spark/kaptár: Fürt**listázása.

2. Válassza ki a kívánt előfizetést.

3. Tekintse át a **kimeneti** nézetet.  A nézet megjeleníti a társított fürt (eke) t és az Azure-előfizetésében lévő összes fürtöt.

    ![Alapértelmezett fürtkonfiguráció beállítása](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Alapértelmezett fürt beállítása

1. Nyissa meg újra a [korábban](#open-work-folder) a lezárt **HDexample** létrehozott mappát.  

2. Válassza ki a [korábban](#open-work-folder) létrehozott **HelloWorld. HQL** fájlt, és megnyílik a parancsfájl-szerkesztőben.

3. Kattintson a jobb gombbal a parancsfájl-szerkesztőre **, majd válassza a Spark/kaptár elemet: Állítsa be az**alapértelmezett fürtöt.  

4. [Kapcsolódjon](#connect-to-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Válasszon ki egy fürtöt az aktuális parancsfájl alapértelmezett fürtjéként. Az eszközök automatikusan frissítik a konfigurációs fájlt **. VSCode\settings.json**. 

   ![A fürtkonfiguráció alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Interaktív struktúra-lekérdezések elküldése, kaptár batch-parancsfájlok

A Spark & a kaptár Tools for Visual Studio Code használatával interaktív kaptár-lekérdezéseket küldhet, és a fürtökhöz tartozó Batch-parancsfájlokat.

1. Nyissa meg újra a [korábban](#open-work-folder) a **HDexample** létrehozott mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-work-folder) létrehozott **HelloWorld. HQL** fájlt, és megnyílik a parancsfájl-szerkesztőben.


3. Másolja ki és illessze be az alábbi kódot a Hive-fájlba, majd mentse el.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Kapcsolódjon](#connect-to-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a struktúra elemet **: Interaktív** a lekérdezés elküldéséhez, vagy a **Ctrl + Alt + I**Billentyűparancs használata.  Struktúra **kiválasztása: A szkript elküldéséhez vagy a **CTRL + ALT + H**Billentyűparancs használata.**  

6. Ha nem adott meg alapértelmezett fürtöt, válassza ki a fürtöt. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. Néhány pillanat elteltével a lekérdezés eredménye megjelenik egy új lapon.

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Eredmények** panel: A teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti a helyi elérési útra, vagy egyszerűen több sort is kijelölhet.

    - **Üzenetek** panel: Amikor **kiválasztja a** sorszámot, a futó parancsfájl első sorára ugrik.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark-lekérdezések küldése

Az alábbi lépéseket követve interaktív PySpark-lekérdezéseket küldhet:

1. Nyissa meg újra a [korábban](#open-work-folder) a **HDexample** létrehozott mappát, ha be van zárva.  

2. Hozzon létre egy új fájlt a [korábbi](#open-work-folder) lépéseket követve **HelloWorld.py** .

3. Másolja és illessze be a következő kódot a parancsfájlba:
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

4. [Kapcsolódjon](#connect-to-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Válassza ki az összes kódot, és kattintson a jobb gombbal a parancsfájl **-szerkesztőre, válassza a Spark elemet: PySpark interaktív** a lekérdezés elküldéséhez, vagy a **Ctrl + Alt + I**Billentyűparancs használata.

   ![pyspark interaktív helyi menü](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Ha nem adott meg alapértelmezett fürtöt, válassza ki a fürtöt. Néhány pillanat elteltével a **Python interaktív** eredményei egy új lapon jelennek meg. Az eszközök segítségével a helyi menüt használva egy kódblokkot is beküldhet az egész szkriptfájl helyett. 

   ![pyspark interaktív Python interaktív ablak](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Adja meg a **"%% info"** kifejezést, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a feladatok adatainak megtekintéséhez. (Választható lehetőség)

   ![feladattal kapcsolatos információk megtekintése](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Az eszköz támogatja a **Spark SQL** -lekérdezést is.

   ![Pyspark interaktív nézet eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   A lekérdezések futtatásakor megjelenik a beküldési állapot az alsó állapotsor bal oldalán. Ne küldjön más lekérdezéseket, ha az állapota **PySpark kernel (foglalt)** .  

   > [!NOTE] 
   >
   > Ha a **Python-bővítmény engedélyezve** van a beállításokban (az alapértelmezett beállítás be van jelölve), a beküldött pyspark interakció eredményei a régi ablakot fogják használni.
   >
   > ![pyspark Interactive Python-bővítmény letiltva](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>PySpark-kötegelt feladatok elküldése

1. Nyissa meg újra a [korábban](#open-work-folder) a **HDexample** létrehozott mappát, ha be van zárva.  

2. Hozzon létre egy új fájlt a [korábbi](#open-work-folder) lépéseket követve **BatchFile.py** .

3. Másolja és illessze be a következő kódot a parancsfájlba:

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

4. [Kapcsolódjon](#connect-to-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, **majd válassza a Spark elemet: PySpark batch**, vagy használja a **CTRL + ALT + H**billentyűparancsot. 

6. Válassza ki azt a fürtöt, amelyhez be szeretné küldeni a PySpark-feladatot. 

   ![Python-feladatok eredményének beküldése](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Miután elküldte a Python-feladatot, a küldési naplók a Visual Studio Code **kimenet** ablakában jelennek meg. A **Spark felhasználói felület URL-címe** és a **fonal felhasználói felületének URL-címe** is megjelenik. A feladatok állapotának nyomon követéséhez megnyithatja az URL-címet egy böngészőben.

## <a name="apache-livy-configuration"></a>Apache Livy-konfiguráció

Az [Apache Livy](https://livy.incubator.apache.org/) -konfiguráció támogatott, és a (z) beállítható **. VSCode\settings.json** a munkahelyi terület mappában. A Livy-konfiguráció jelenleg csak a Python-szkripteket támogatja. További részletek: [LIVY readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**A Livy-konfiguráció elindítása**

1\. módszer  
1. A menüsorban navigáljon a **fájl** > **beállításai** > **Beállítások**elemre.  
2. A **keresési beállítások** szövegmezőbe írja be **a HDInsight Job Sumission: Livy conf**.  
3. A megfelelő keresési eredményhez válassza a **Szerkesztés a Settings. JSON** fájlban lehetőséget.

2\. módszer   
Küldje el a fájlt, és figyelje meg, hogy a. vscode mappa automatikusan hozzáadódik a munkahelyi mappához. A Livy-konfigurációt a **. vscode\settings.JSON**elemre kattintva érheti el.

+ A projekt beállításai:

    ![Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>A Settings **driverMomory** és a **executorMomry**beállításnál állítsa be az értéket egységgel, például: 1g vagy 1024m. 

+ A támogatott Livy konfigurációk:   

    **/Batches közzététele**   
    Kérelem törzse

    | name | description | type | 
    | :- | :- | :- | 
    | file | A végrehajtandó alkalmazást tartalmazó fájl | elérési út (kötelező) | 
    | proxyUser | A felhasználó megszemélyesítése a feladatok futtatásakor | Karakterlánc | 
    | className | Java/Spark fő osztály alkalmazása | Karakterlánc |
    | args | Az alkalmazás parancssori argumentumai | karakterláncok listája | 
    | jars | a munkamenetben használandó tégelyek | Karakterláncok listája | 
    | pyFiles | A munkamenetben használni kívánt Python-fájlok | Karakterláncok listája |
    | files | a munkamenetben használni kívánt fájlok | Karakterláncok listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Karakterlánc |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | int |
    | executorMemory | A felhasználható memória mennyisége/végrehajtó folyamat | Karakterlánc |
    | executorCores | Az egyes végrehajtók számára használandó magok száma | int |
    | numExecutors | A munkamenet elindításához szükséges végrehajtók száma | int |
    | archives | A munkamenetben használni kívánt archívumok | Karakterláncok listája |
    | queue | Az elküldött fonal-várólista neve | Karakterlánc |
    | name | A munkamenet neve | sztring |
    | conf | Spark-konfiguráció tulajdonságai | Kulcs leképezése = val |

    Választörzs   
    A létrehozott batch-objektum.

    | name | description | type | 
    | :- | :- | :- | 
    | id | A munkamenet azonosítója | int | 
    | appId | A munkamenet alkalmazás-azonosítója |  Sztring |
    | alkalmazásadatokat | Az alkalmazás részletes adatai | Kulcs leképezése = val |
    | log | A naplósorok | karakterláncok listája |
    | state |   A köteg állapota | sztring |

>[!NOTE]
>A hozzárendelt Livy konfiguráció a kimenet ablaktáblán jelenik meg, amikor elküldi a parancsfájlt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integráció az Azure HDInsight az Intézőből

Az **Azure HDInsight** hozzá lett adva a Explorer nézethez. A fürtöket közvetlenül az **Azure HDInsight**keresztül böngészheti és kezelheti.

1. [Kapcsolódjon](#connect-to-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

2. A menüsávban navigáljon a **View** > **Explorer**elemre.

3. A bal oldali panelen bontsa ki az **Azure HDINSIGHT**elemet.  A rendszer felsorolja a rendelkezésre álló előfizetéseket és fürtöket (Spark, Hadoop és HBase). 

   ![Azure HDInsight-előfizetés](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Bontsa ki a fürtöt a struktúra metaadatainak adatbázisának és a tábla sémájának megtekintéséhez.

   ![Azure HDInsight-fürt](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Struktúra előnézete tábla
Az **Azure HDInsight** Explorer használatával közvetlenül megtekintheti a fürt (ek) struktúra-tábláját.
1. [](#connect-to-azure-account) Ha még nem tette meg, kapcsolódjon az Azure-fiókjához.

2. Kattintson az **Azure** ikonra a bal szélső oszlopból.

3. A bal oldali panelen bontsa ki az **Azure HDINSIGHT**elemet. A rendszer felsorolja a rendelkezésre álló előfizetéseket és fürtöket.

4. Bontsa ki a fürtöt a struktúra metaadatainak adatbázisának és a tábla sémájának megtekintéséhez.

5. Kattintson a jobb gombbal a kaptár táblára, például hivesampletable. Válassza az **előnézet**lehetőséget. 

   ![Spark & struktúra a Visual Studio Code előzetes struktúra-táblájához](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Megnyílik a **Preview Results (előzetes** verzió) ablak.

   ![A Spark & struktúra a Visual Studio Code előzetes verziójának eredményei ablak](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **Eredmények** panel

   A teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti a helyi elérési útra, vagy egyszerűen több sort is kijelölhet.

- **Üzenetek** panel
   1. Ha a tábla sorainak száma nagyobb, mint 100 sor, a következő üzenet jelenik meg: **Az első 100 sor jelenik meg a kaptár táblában**.
   2. Ha a tábla sorainak száma 100-nél kisebb vagy azzal egyenlő, az üzenet a következőt jeleníti meg: **60 sor jelenik meg a kaptár**-táblához.
   3. Ha nincs tartalom a táblában, az üzenet a következőket jeleníti meg: **a kaptár táblában 0 sor jelenik meg**.

>[!NOTE]
>
>A Linux rendszerben telepítse a xclip-t a táblázatok másolásának engedélyezéséhez.
>
>![Spark & struktúra a Visual Studio Code-hoz Linux rendszerben](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>További funkciók

A Spark & kaptár a Visual Studio Code-hoz a következő funkciókat támogatja:

- **IntelliSense automatikus kiegészítése**. Javaslatok a kulcsszó, a metódusok, a változók és így tovább. A különböző ikonok különböző típusú objektumokat jelölnek.

    ![A Spark &-struktúra eszközei a Visual Studio Code IntelliSense-objektumok típusaihoz](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense-hiba jelölője**. A nyelvi szolgáltatás aláhúzza a struktúra parancsfájljának szerkesztési hibáit.     
- A **szintaxis kiemeli**. A nyelvi szolgáltatás különböző színeket használ a változók, kulcsszavak, adattípusok, függvények és egyebek megkülönböztetéséhez. 

    ![A Spark & a kaptár Tools for Visual Studio Code szintaxisának főbb jellemzői](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Csak olvasó szerepkör

A csak fürtözött **olvasó**  **szerepkörrel** rendelkező felhasználók többé nem küldhetnek feladatot a HDInsight-fürtnek, és nem tekinthetik meg a struktúra-adatbázist. Forduljon a fürt rendszergazdájához, és frissítse a szerepkört a [Azure Portal](https://ms.portal.azure.com/) [ **HDInsight**   ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) . Ha ismeri a Ambari hitelesítő adatait, manuálisan is összekapcsolhatja a fürtöt az alábbi utasítások követésével.

### <a name="browse-hdinsight-cluster"></a>HDInsight-fürt tallózása  

Ha a HDInsight-fürt kibontásához az Azure HDInsight Explorerre kattint, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha csak olvasó szerepkört használ a fürthöz. Az alábbi lépések végrehajtásával hivatkozhat a fürtre Ambari hitelesítő adatok használatával. 

### <a name="submit-job-to-hdinsight-cluster"></a>Feladatok elküldése a HDInsight-fürtbe

Ha a feladatot egy HDInsight-fürtre küldi, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha csak olvasó szerepkört használ a fürthöz. Az alábbi lépések végrehajtásával hivatkozhat a fürtre Ambari hitelesítő adatok használatával. 

### <a name="link-to-cluster"></a>Fürtre mutató hivatkozás

1.  Adja meg a Ambari felhasználónevét 
2.  Adja meg a Ambari felhasználói jelszavát.

   ![Spark &-struktúra eszközei a Visual Studio Code-felhasználónévhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![A Spark &-struktúra eszközei a Visual Studio Code Passwordhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Használhatja a Spark/Kas: Fürt listázása a csatolt fürt vizsgálatához.
>
>![Spark &-struktúra eszközei a Visual Studio Code Readerhez csatolt](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>ADLS Gen2-fiók tallózása

Ha az Azure HDInsight Explorer alkalmazásra kattint egy ADLS Gen2 fiók kibontásához, a rendszer kérni fogja a Storage- **hozzáférési kulcs** megadását, ha az Azure-fiókja nem fér hozzá a Gen2-tárolóhoz. A ADLS Gen2 fiók automatikusan ki lesz bontva, ha a hozzáférési kulcs érvényesítése sikeresen megtörtént. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Feladatok elküldése a HDInsight-fürtbe ADLS Gen2

Ha ADLS Gen2 használatával küldi el a feladatot egy HDInsight-fürthöz, a rendszer kérni fogja a Storage- **hozzáférési kulcs** megadását, ha az Azure-fiókja nem rendelkezik írási hozzáféréssel a Gen2-tárolóhoz.  A rendszer sikeresen elküldi a feladatot, miután a hozzáférési kulcs érvényesítése sikeresen megtörtént. 

![Spark &-struktúra eszközei a Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>A Storage-fiók elérési kulcsát a Azure Portal lehet beszerezni. További információ: [hozzáférési kulcsok megtekintése és másolása](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Fürt leválasztása

1. A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , majd írja **be a Spark/kaptár: Fürt**leválasztása.  

2. Válassza ki a leválasztani kívánt fürtöt.  

3. Tekintse át az ellenőrzés **kimeneti** nézetét.  

## <a name="sign-out"></a>Kijelentkezés  

A menüsávban navigáljon a parancs palettájának **megtekintéséhez** > .. **.** , majd írja **be az Azure-t: Kijelentkezés**.


## <a name="next-steps"></a>További lépések
A Spark & kaptár Visual Studio Code-hoz való használatával kapcsolatos bemutató videó: [spark &-struktúra a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) -hoz
