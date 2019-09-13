---
title: Azure HDInsight a Visual Studio Code-hoz
description: Ismerje meg, hogyan hozhat létre és küldhet le lekérdezéseket és parancsfájlokat a Spark &-struktúra eszközeivel (Azure HDInsight) a Visual Studio Code használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 51f0e223abd7103663fddd8c06dcdf0be549c671
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70930806"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>A Spark &-struktúra eszközeinek használata a Visual Studio Code-hoz

Megtudhatja, hogyan hozhat létre és küldhet el Apache Hive batch-feladatokat, interaktív kaptár-lekérdezéseket és PySpark parancsfájlokat Apache Sparkhoz a Spark & kaptár Tools for Visual Studio Code használatával. Először is leírjuk, hogyan telepítheti a Spark & kaptár-eszközöket a Visual Studio Code-ban, majd megtudhatja, hogyan küldhet feladatokat a Spark &-struktúra eszközein.  

A Spark & struktúra eszközei a Visual Studio Code által támogatott platformokon telepíthetők, amelyek Windows, Linux és macOS rendszereken is használhatók. Vegye figyelembe a következő előfeltételeket a különböző platformokon.


## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- Egy Azure-beli HDInsight-fürt. Fürt létrehozásához tekintse meg [az Ismerkedés a HDInsight szolgáltatással](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)című témakört. Vagy használjon egy Apache Livy-végpontot támogató Spark és kaptár fürtöt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Monó](https://www.mono-project.com/docs/getting-started/install/). A Mono csak Linux és macOS rendszerekhez szükséges.
- [PySpark interaktív környezet a Visual Studio Code](set-up-pyspark-interactive-environment.md)-hoz.
- Egy **HDexample**nevű helyi könyvtár.  Ez a cikk a **C:\HD\HDexample**-t használja.

## <a name="install-spark--hive-tools"></a>A Spark & Kas eszközeinek telepítése

Az előfeltételek teljesítése után a Spark & kaptár Tools for Visual Studio Code-ot a következő lépésekkel telepítheti:

1. Nyissa meg a Visual Studio Code-ot.

2. A menüsávban lépjen a**bővítmények** **megtekintése** > elemre.

3. A keresőmezőbe írja be a **Spark & kaptár**kifejezést.

4. Válassza ki a **Spark & struktúra eszközöket** a keresési eredmények közül, majd válassza a **telepítés**lehetőséget:

   ![Spark & struktúra a Visual Studio Code Python telepítéséhez](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Szükség esetén válassza az **Újratöltés** lehetőséget.


## <a name="open-a-work-folder"></a>Munkahelyi mappa megnyitása

Az alábbi lépéseket követve nyisson meg egy munkahelyi mappát, és hozzon létre egy fájlt a Visual Studio Code-ban:

1. A menüsávban nyissa meg a **fájl** > **megnyitása** > **C:\HD\HDexample**mappát, majd kattintson a **mappa kiválasztása** gombra. A mappa a bal oldali **Explorer** nézetben jelenik meg.

2. A **Explorer** nézetben válassza ki a **HDexample** mappát, majd válassza az **új fájl** ikont a munkahelyi mappa mellett:

   ![Új fájl](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nevezze el az új fájlt a `.hql` (kaptár-lekérdezések) vagy a `.py` (Spark script) fájlkiterjesztés használatával. Ez a példa a **HelloWorld. HQL**-t használja.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

A nemzeti Felhőbeli felhasználók számára az alábbi lépéseket követve állítsa be először az Azure-környezetet, majd **használja az Azure-t:**  Jelentkezzen be az Azure-ba való bejelentkezéshez:
   
1. Válassza a **File\Preferences\Settings**lehetőséget.
2. Keresse meg a következő karakterláncot: **Azure Felhő**
3. Válassza ki a nemzeti felhőt a listából:

   ![Bejelentkezési bejegyzés alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Kapcsolódás Azure-fiókhoz

Mielőtt parancsfájlokat küldhessen a fürtökhöz a Visual Studio Code-ból, csatlakoznia kell az Azure-fiókjához, vagy hozzá kell kötnie egy fürtöt (az Apache Ambari username és Password hitelesítő adatokkal vagy egy tartományhoz csatlakoztatott fiókkal). Az Azure-hoz való kapcsolódáshoz kövesse az alábbi lépéseket:

1. A menüsávban lépjen a parancs- > **paletta**megtekintése elemre, és **írja be az Azure-t:** Bejelentkezés:

    ![A Spark & struktúra eszközei a Visual Studio Code-beli bejelentkezéshez](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezési utasításokat. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.  
  

## <a name="link-a-cluster"></a>Fürt csatolása

### <a name="link-azure-hdinsight"></a>Hivatkozás Azure HDInsight

Az [Apache Ambari](https://ambari.apache.org/)által felügyelt Felhasználónév használatával összekapcsolhatja a normál fürtöt, vagy a biztonságos Hadoop-fürtöt tartományi Felhasználónév használatával is összekapcsolhatja (például: `user1@contoso.com`).

1. A menüsávban lépjen a**parancs-paletta** **megtekintése** > elemre, és **írja be a Spark/kaptár parancsot: Fürt**csatolása.

   ![fürt csatolása parancs](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Válassza a csatolt fürt típusa **Azure HDInsight**elemet.

3. Adja meg a HDInsight-fürt URL-címét.

4. Adja meg a Ambari felhasználónevét; az alapértelmezett érték a **rendszergazda**.

5. Adja meg a Ambari jelszavát.

6. Válassza ki a fürt típusát.

7. Adja meg a fürt megjelenítendő nevét (nem kötelező).

8. Tekintse át az ellenőrzés **kimeneti** nézetét.

   > [!NOTE]  
   > A rendszer a csatolt felhasználónevet és jelszót használja, ha a fürt mind az Azure-előfizetésbe bejelentkezett, mind a fürthöz csatlakozik.  


### <a name="link-generic-livy-endpoint"></a>Hivatkozás Általános Livy végpont

1. A menüsávban lépjen a**parancs-paletta** **megtekintése** > elemre, és **írja be a Spark/kaptár parancsot: Fürt**csatolása.

2. Válassza a csatolt fürt típusa **általános Livy végpont**elemet.

3. Adja meg az általános Livy-végpontot. Például: http\://10.172.41.42:18080.

4. Válassza az **Alap** vagy a **none**engedélyezési típust.  Ha az **alapszintű**lehetőséget választja:  
    &emsp;a. Adja meg a Ambari felhasználónevét; az alapértelmezett érték a **rendszergazda**.  
    &emsp;b. Adja meg a Ambari jelszavát.

5. Tekintse át az ellenőrzés **kimeneti** nézetét.

## <a name="list-clusters"></a>Fürtök listázása

1. A menüsávban lépjen a**parancs-paletta** **megtekintése** > elemre, és **írja be a Spark/kaptár parancsot: Fürt**listázása.

2. Válassza ki a kívánt előfizetést.

3. Tekintse át a **kimeneti** nézetet. Ez a nézet megjeleníti a társított fürtöt (vagy fürtöket) és az Azure-előfizetéshez tartozó összes fürtöt:

    ![Alapértelmezett fürtkonfiguráció beállítása](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Az alapértelmezett fürt beállítása

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld. HQL** fájlt. Megnyílik a parancsfájl-szerkesztőben.

3. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, **majd válassza a Spark/struktúra elemet: Állítsa be az**alapértelmezett fürtöt.  

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Válasszon ki egy fürtöt az aktuális parancsfájl alapértelmezett fürtjéként. Az eszközök automatikusan frissítik a t **. VSCode\settings.json** -konfigurációs fájl:

   ![A fürtkonfiguráció alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interaktív struktúra-lekérdezések és a kaptár batch-parancsfájlok beküldése

A Spark & kaptár Tools for Visual Studio Code használatával interaktív kaptár-lekérdezéseket és a fürtökhöz tartozó Batch-parancsfájlokat küldhet.

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld. HQL** fájlt. Megnyílik a parancsfájl-szerkesztőben.


3. Másolja és illessze be a következő kódot a kaptár-fájlba, majd mentse:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza a struktúra elemet **: Interaktív** a lekérdezés elküldéséhez, vagy használja a Ctrl + Alt + I billentyűparancsot.  Struktúra **kiválasztása:**  A szkript elküldéséhez, vagy használja a CTRL + ALT + H billentyűparancsot.  

6. Ha nem adott meg alapértelmezett fürtöt, válasszon ki egy fürtöt. Az eszközök lehetővé teszik a teljes parancsfájl helyett a kód egy blokkjának beküldését a helyi menü használatával. Néhány pillanat elteltével a lekérdezés eredménye megjelenik egy új lapon:

   ![Az interaktív Hive eredményei](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Eredmények** panel: A teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti egy helyi elérési útra, vagy egyszerűen több sort is kijelölhet.

    - **Üzenetek** panel: Amikor kijelöl egy **sorszámot, a futó** parancsfájl első sorára ugrik.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark-lekérdezések küldése

Az interaktív PySpark-lekérdezések elküldéséhez kövesse az alábbi lépéseket:

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Hozzon létre egy új **HelloWorld.py** -fájlt a [korábbi](#open-a-work-folder) lépéseket követve.

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

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Válassza ki az összes kódot, kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a Spark elemet **: PySpark interaktív** a lekérdezés elküldéséhez. Vagy használja a Ctrl + Alt + I billentyűparancsot.

   ![pyspark interaktív helyi menü](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Ha nem adott meg alapértelmezett fürtöt, válassza ki a fürtöt. Néhány pillanat elteltével a **Python interaktív** eredményei egy új lapon jelennek meg. Az eszközök lehetővé teszik a teljes parancsfájl helyett a kód egy blokkjának beküldését a helyi menü használatával:

   ![pyspark interaktív Python interaktív ablak](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Adja meg a **%% info**értéket, majd nyomja le a SHIFT + ENTER billentyűkombinációt a feladatok adatainak megtekintéséhez (nem kötelező):

   ![feladattal kapcsolatos információk megtekintése](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Az eszköz a **Spark SQL** -lekérdezést is támogatja:

   ![Pyspark interaktív nézet eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   A lekérdezések futtatásakor a küldés állapota az alsó állapotsor bal oldalán jelenik meg. Ne küldjön más lekérdezéseket, ha az állapota **PySpark kernel (foglalt)** .  

   > [!NOTE] 
   >
   > Ha a **Python-bővítmény engedélyezve** van a beállításokban (alapértelmezés szerint ki van választva), a beküldött pyspark interakció eredményei a régi ablakot fogják használni:
   >
   > ![pyspark Interactive Python-bővítmény letiltva](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>PySpark-kötegelt feladatok elküldése

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha le van zárva.  

2. Hozzon létre egy új **BatchFile.py** -fájlt a [korábbi](#open-a-work-folder) lépések követésével.

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

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, **majd válassza a Spark elemet: PySpark batch**, vagy használja a CTRL + ALT + H billentyűparancsot. 

6. Válasszon ki egy fürtöt, hogy küldje el a PySpark-feladatot a következőnek:

   ![Python-feladatok eredményének beküldése](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Miután elküldte a Python-feladatot, a küldési naplók a Visual Studio Code **kimenet** ablakában jelennek meg. A Spark felhasználói felület URL-címe és a fonal felhasználói felületének URL-címe is megjelenik. A feladatok állapotának nyomon követéséhez megnyithatja az URL-címet egy böngészőben.

## <a name="apache-livy-configuration"></a>Apache Livy-konfiguráció

Az [Apache Livy](https://livy.incubator.apache.org/) -konfiguráció támogatott. Konfigurálhatja azt a alkalmazásban **. VSCode\settings.json** -fájl a munkaterület mappában. A Livy-konfiguráció jelenleg csak a Python-szkripteket támogatja. További részletekért lásd: [LIVY readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**A Livy-konfiguráció elindítása**

1\. módszer  
1. A menüsorban lépjen a **fájl** > **beállításai** > **Beállítások**elemre.  
2. A **keresési beállítások** mezőben adja meg **a HDInsight-feladatok beküldését: Livy conf**.  
3. A megfelelő keresési eredményhez válassza a **Szerkesztés a Settings. JSON** fájlban lehetőséget.

2\. módszer   
Küldje el a fájlt, és figyelje meg, hogy a rendszer automatikusan hozzáadja a. vscode mappát a munkahelyi mappához. A Livy konfigurációját a **. vscode\settings.JSON**lehetőség kiválasztásával tekintheti meg.

+ A projekt beállításai:

    ![Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >A **driverMemory** és a **executorMemory** beállításnál állítsa be az értéket és az egységet. Példa: 1g vagy 1024m.

+ Támogatott Livy konfigurációk:   

    **/Batches közzététele**   
    A kérés törzse

    | name | description | type | 
    | :- | :- | :- | 
    | file | A végrehajtandó alkalmazást tartalmazó fájl | elérési út (kötelező) |
    | proxyUser | A felhasználó megszemélyesítése a feladatok futtatásakor | Sztring |
    | className | Java/Spark fő osztály alkalmazása | Sztring |
    | args | Az alkalmazás parancssori argumentumai | karakterláncok listája |
    | jars | a munkamenetben használandó tégelyek | karakterláncok listája | 
    | pyFiles | A munkamenetben használni kívánt Python-fájlok | karakterláncok listája |
    | files | a munkamenetben használni kívánt fájlok | karakterláncok listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Sztring |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | Int |
    | executorMemory | A felhasználható memória mennyisége/végrehajtó folyamat | Sztring |
    | executorCores | Az egyes végrehajtók számára használandó magok száma | Int |
    | numExecutors | A munkamenet elindításához szükséges végrehajtók száma | Int |
    | archives | A munkamenetben használni kívánt archívumok | karakterláncok listája |
    | queue | A beküldeni kívánt FONÁL-várólista neve| Sztring |
    | name | A munkamenet neve | Sztring |
    | conf | Spark-konfiguráció tulajdonságai | Kulcs leképezése = val |

    Válasz törzse   
    A létrehozott batch-objektum.

    | name | description | type |
    | :- | :- | :- | 
    | id | Munkamenet-azonosító | Int | 
    | appId | A munkamenet alkalmazás-azonosítója | Sztring |
    | alkalmazásadatokat | Részletes alkalmazásadatok | Kulcs leképezése = val |
    | log | Naplózási sorok | karakterláncok listája |
    | state |Köteg állapota | Sztring |

    >[!NOTE]
    >A hozzárendelt Livy konfiguráció a kimenet ablaktáblán jelenik meg a parancsfájl elküldésekor.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integráció az Azure HDInsight az Intézőből

Az **Azure HDInsight** hozzá lett adva a Explorer nézethez. A fürtöket közvetlenül az **Azure HDInsight**segítségével böngészheti és kezelheti.

1. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

2. A menüsávban lépjen a **View** > **Explorer**elemre.

3. A bal oldali panelen bontsa ki az **Azure HDINSIGHT**elemet.  Az elérhető előfizetések és fürtök (Spark, Hadoop és HBase támogatottak) a következők:

   ![Azure HDInsight-előfizetés](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Bontsa ki a fürtöt a struktúra metaadat-adatbázisának és a tábla sémájának megtekintéséhez:

   ![Azure HDInsight-fürt](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Struktúra előnézete tábla
Az **Azure HDInsight** Explorer segítségével közvetlenül megtekintheti a fürtökben található kaptár-táblázatot:
1. [](#connect-to-an-azure-account) Ha még nem tette meg, kapcsolódjon az Azure-fiókjához.

2. Válassza ki az **Azure** ikont a bal szélső oszlopból.

3. A bal oldali panelen bontsa ki az **Azure HDINSIGHT**elemet. Megjelenik az elérhető előfizetések és fürtök.

4. Bontsa ki a fürtöt a kaptár metaadatainak adatbázisának és a tábla sémájának megtekintéséhez.

5. Kattintson a jobb gombbal a kaptár táblára. Például: **hivesampletable**. Válassza az **előnézet**lehetőséget. 

   ![Spark & struktúra a Visual Studio Code előzetes struktúra-táblájához](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Megnyílik az **előnézet eredményei** ablak:

   ![A Spark & struktúra a Visual Studio Code előzetes verziójának eredményei ablak](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- EREDMÉNYEK panel

   A teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti a helyi elérési útra, vagy egyszerűen több sort is kijelölhet.

- ÜZENETEK panel
   1. Ha a tábla sorainak száma nagyobb, mint 100, a következő üzenet jelenik meg: "Az első 100 sor jelenik meg a kaptár táblában."
   2. Ha a tábla sorainak száma 100-nál kisebb vagy azzal egyenlő, a következőhöz hasonló üzenet jelenik meg: "60 sor jelenik meg a kaptár táblában."
   3. Ha nincs tartalom a táblában, a következő üzenet jelenik meg: "0 sor jelenik meg a kaptár táblában."

        >[!NOTE]
        >
        >A Linux rendszerben telepítse a xclip-t a másolási tábla adatkezelésének engedélyezéséhez.
        >
        >![Spark & struktúra a Visual Studio Code-hoz Linux rendszerben](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>További funkciók

A Spark & kaptár a Visual Studio Code-hoz a következő funkciókat is támogatja:

- **IntelliSense automatikus kiegészítése**. Javaslatok a kulcsszavakra, a metódusokra, a változókra és az egyéb programozási elemekre. A különböző ikonok különböző típusú objektumokat jelölnek:

    ![A Spark &-struktúra eszközei a Visual Studio Code IntelliSense-objektumok típusaihoz](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense-hiba jelölője**. A nyelvi szolgáltatás a struktúra parancsfájljában a szerkesztési hibákat aláhúzza.     
- A **szintaxis kiemeli**. A nyelvi szolgáltatás különböző színeket használ a változók, kulcsszavak, adattípus, függvények és egyéb programozási elemek megkülönböztetéséhez:

    ![A Spark & a kaptár Tools for Visual Studio Code szintaxisának főbb jellemzői](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Azok a felhasználók, akik csak olvasó szerepkört kapnak a fürthöz, többé nem küldhetnek feladatokat a HDInsight-fürtnek, és nem tekinthetik meg a kaptár-adatbázist. Forduljon a fürt rendszergazdájához, és frissítse a szerepkört a [Azure Portal](https://ms.portal.azure.com/) [**HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) . Ha érvényes Ambari hitelesítő adatokkal rendelkezik, manuálisan is összekapcsolhatja a fürtöt a következő útmutatás segítségével.

### <a name="browse-the-hdinsight-cluster"></a>Tallózás a HDInsight-fürtben  

Amikor kijelöli az Azure HDInsight Explorer alkalmazást a HDInsight-fürt kibontásához, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha a fürt csak olvasó szerepkörrel rendelkezik. A következő módszer használatával a fürthöz a Ambari hitelesítő adataival lehet hivatkozni. 

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Küldje el a feladatot a HDInsight-fürtnek

Ha a feladatot egy HDInsight-fürtre küldi, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha a fürt csak olvasó szerepköre van. A következő lépések végrehajtásával hivatkozhat a fürtre Ambari hitelesítő adatok használatával.

### <a name="link-to-the-cluster"></a>A fürtre mutató hivatkozás

1.  Érvényes Ambari-felhasználónevet adjon meg.
2.  Adjon meg egy érvényes jelszót.

   ![Spark &-struktúra eszközei a Visual Studio Code felhasználónévhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![A Spark &-struktúra eszközei a Visual Studio Code Passwordhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >A következő paranccsal `Spark / Hive: List Cluster` ellenőrizhető a csatolt fürt:
  >
  >![Spark &-struktúra eszközei a Visual Studio Code Readerhez csatolt](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2-fiók tallózása

Amikor kijelöli az Azure HDInsight Explorer alkalmazást Data Lake Storage Gen2 fiók kibontásához, a rendszer kéri, hogy adja meg a Storage-hozzáférési kulcsot, ha az Azure-fiókja nem fér hozzá a Gen2-tárolóhoz. A hozzáférési kulcs érvényesítése után a rendszer automatikusan kibontja a Data Lake Storage Gen2 fiókot.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Feladatok elküldése egy HDInsight-fürtbe Data Lake Storage Gen2

Ha Data Lake Storage Gen2 használatával küld el egy feladatot egy HDInsight-fürthöz, a rendszer arra kéri, hogy adja meg a tárterület-hozzáférési kulcsot, ha az Azure-fiókja nem rendelkezik írási hozzáféréssel a Gen2-tárolóhoz. A hozzáférési kulcs érvényesítése után a rendszer sikeresen elküldi a feladatot.

![Spark &-struktúra eszközei a Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>A Storage-fiók elérési kulcsát a Azure Portal kérheti le. További információ: [hozzáférési kulcsok megtekintése és másolása](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Fürt leválasztása

1. A menüsávban válassza a**parancs-paletta** **megtekintése** > elemet, majd írja be **a Spark/kaptár parancsot: Fürt**leválasztása.  

2. Válassza ki a leválasztani kívánt fürtöt.  

3. Tekintse meg az ellenőrzés **kimeneti** nézetét.  

## <a name="sign-out"></a>Kijelentkezés  

A menüsávban válassza a parancs- > **paletta**megtekintése elemet, majd írja be **az Azure-t: Kijelentkezés**.


## <a name="next-steps"></a>További lépések
A Spark & kaptár Visual Studio Code-hoz való használatát bemutató videó: [spark &-struktúra a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)-hoz.
