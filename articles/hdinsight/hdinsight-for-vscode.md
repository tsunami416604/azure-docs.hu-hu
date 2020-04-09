---
title: Az Azure HDInsight a Visual Studio-hoz kód
description: Ismerje meg, hogyan használhatja a Spark & Hive-eszközöket (Azure HDInsight) a Visual Studio-kódhoz. Az eszközök segítségével lekérdezéseket és parancsfájlokat hozhat létre és küldhet be.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: de433d85c2f04a7140fbcb918730218ac3a05e54
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878629"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>A Spark & Hive-eszközök használata a Visual Studio-kódhoz

Ismerje meg, hogyan használhatja az Apache Spark & Hive-eszközöket a Visual Studio-kódhoz. Az eszközök segítségével apache hive kötegelt feladatokat, interaktív Hive-lekérdezéseket és Az Apache Spark PySpark-parancsfájlokat hozhat létre és küldhet el. Először ismertetjük, hogyan telepítheti a Spark & Hive-eszközöket a Visual Studio-kódban. Ezután bemutatjuk, hogyan küldhet be feladatokat a Spark & Hive-eszközökbe.  

A Spark & Hive-eszközök a Visual Studio-kód által támogatott platformokra telepíthetők. Vegye figyelembe a különböző platformok következő előfeltételeit.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következő elemek szükségesek:

- Egy Azure-beli HDInsight-fürt. Fürt létrehozásáról a HDInsight – Első lépések című [témakörben látható.](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md) Vagy használjon egy Spark és Hive-fürt, amely támogatja az Apache Livy-végpont.
- [Visual Studio kód](https://code.visualstudio.com/).
- [Monó](https://www.mono-project.com/docs/getting-started/install/). A Monó csak Linux és macOS esetén szükséges.
- [PySpark interaktív környezet a Visual Studio-kódhoz.](set-up-pyspark-interactive-environment.md)
- Egy helyi könyvtár. Ez a cikk a **C:\HD\HDexample értéket**használja.

## <a name="install-spark--hive-tools"></a>A Spark & Hive-eszközök telepítése

Miután teljesítette az előfeltételeket, az alábbi lépésekkel telepítheti a Spark & A Hive-eszközök a Visual Studio-kódhoz alkalmazást:

1. Nyissa meg a Visual Studio Code-ot.

2. A menüsorban keresse meg a**Bővítmények** **megtekintése** > lapot.

3. A keresőmezőbe írja be a **Spark & Hive**.

4. A keresési eredmények között válassza a **Spark & Hive-eszközök elemet,** majd a **Telepítés**lehetőséget:

   ![Spark & Hive a Visual Studio Code Python telepítéséhez](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Szükség esetén válassza **az Újratöltés** lehetőséget.

## <a name="open-a-work-folder"></a>Munkahelyi mappa megnyitása

Munkahelyi mappa megnyitásához és fájl létrehozásához a Visual Studio-kódban kövesse az alábbi lépéseket:

1. A menüsorból keresse meg a Mappa > **fájlmegnyitása...** **File**  >  **C:\HD\HD példa**, majd kattintson a **Mappa kijelölése** gombra. A mappa az **Intéző** nézetben jelenik meg a bal oldalon.

2. Explorer **Explorer** nézetben jelölje ki a **HDpélda** mappát, majd a munkamappa melletti **Új fájl** ikont:

   ![Visual Studio kód új fájl ikon](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nevezze el az új `.hql` fájlt a (Hive-lekérdezések) vagy a `.py` (Spark-parancsfájl) fájlkiterjesztéssel. Ez a példa a **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

Egy nemzeti felhőbeli felhasználó esetében kövesse az alábbi lépéseket az Azure-környezet beállításához, majd az **Azure: Bejelentkezés** paranccsal jelentkezzen be az Azure-ba:

1. Nyissa meg a > **Fájlbeállítások** > **beállításai lapot.** **File**
2. Keresés a következő karakterláncon: **Azure: Cloud**.
3. Válassza ki a nemzeti felhőt a listából:

   ![Alapértelmezett bejelentkezési bejegyzés konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Csatlakozás Azure-fiókhoz

Ahhoz, hogy parancsfájlokat küldhessen be a fürtöknek a Visual Studio-kódból, csatlakoznia kell az Azure-fiókjához, vagy fürthöz kell kapcsolódnia. Használja az Apache Ambari felhasználónevét és jelszavát, illetve a tartományhoz csatlakozó fiókot. Az Azure-hoz való csatlakozáshoz kövesse az alábbi lépéseket:

1. A menüsorban keresse meg a**Parancspaletta** **megtekintése...** > és írja be az **Azure: Bejelentkezés:**

    ![Spark & Hive eszközök a Visual Studio kódbejelentkezéshez](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Kövesse a bejelentkezési utasításokat az Azure-ba való bejelentkezéshez. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio-kód ablak alján található állapotsoron.  

## <a name="link-a-cluster"></a>Fürt csatolása

### <a name="link-azure-hdinsight"></a>Hivatkozás: Azure HDInsight

Normál fürtöt [apache Ambari](https://ambari.apache.org/)által felügyelt felhasználónévvel, vagy tartományfelhasználónévvel (például: `user1@contoso.com`) kapcsolhat össze egy Enterprise Security Pack biztonságos Hadoop-fürtsegítségével.

1. A menüsorban keresse meg a**Parancspaletta** **megtekintése...** > és írja be a **Spark / Hive: Fürt csatolása parancsot.**

   ![Parancspaletta hivatkozásfürt parancsa](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Válassza ki az Azure HDInsight csatolt **fürttípust.**

3. Adja meg a HDInsight-fürt URL-címét.

4. Adja meg ambari felhasználónevét; az alapértelmezett **admin**.

5. Adja meg ambari jelszavát.

6. Válassza ki a fürttípusát.

7. Állítsa be a fürt megjelenítendő nevét (nem kötelező).

8. Tekintse át **a OUTPUT** nézetet ellenőrzéscéljából.

   > [!NOTE]  
   > A csatolt felhasználónév és jelszó akkor használatos, ha a fürt bejelentkezett az Azure-előfizetésbe, és csatolt egy fürt.  

### <a name="link-generic-livy-endpoint"></a>Link: Általános Livy-végpont

1. A menüsorban keresse meg a**Parancspaletta** **megtekintése...** > és írja be a **Spark / Hive: Fürt csatolása parancsot.**

2. Válassza ki az **Általános livy-végpont**csatolt fürttípust.

3. Adja meg az általános Livy-végpontot. Például:\:http //10.172.41.42:18080.

4. Válassza az **Alap szintű** vagy a **Nincs**engedélyezési típust.  Ha az **Alapszintű**:  
    &emsp;A. Adja meg ambari felhasználónevét; az alapértelmezett **admin**.  
    &emsp;B. Adja meg ambari jelszavát.

5. Tekintse át **a OUTPUT** nézetet ellenőrzéscéljából.

## <a name="list-clusters"></a>Fürtök listázása

1. A menüsorban keresse meg a**Parancspaletta** **megtekintése...** > és írja be a **Spark / Hive: List Cluster parancsot.**

2. Válassza ki a kívánt előfizetést.

3. Tekintse át a **OUTPUT** nézetet. Ez a nézet a csatolt fürtöt (vagy fürtöket) és az Azure-előfizetésösszes fürtjeit jeleníti meg:

    ![Alapértelmezett fürtkonfiguráció beállítása](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Az alapértelmezett fürt beállítása

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Jelölje ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld.hql** fájlt. Megnyílik a forgatókönyv-szerkesztőben.

3. Kattintson a jobb gombbal a parancsfájlszerkesztőre, majd válassza a **Spark / Hive: Set Default Cluster parancsot.**  

4. [Csatlakozzon](#connect-to-an-azure-account) Azure-fiókjához, vagy csatoljon egy fürtöt, ha még nem tette meg.

5. Jelöljön ki egy fürtöt az aktuális parancsfájl alapértelmezett fürtjeként. Az eszközök automatikusan frissítik **a. VSCode\settings.json konfigurációs** fájl:

   ![Alapértelmezett fürtkonfiguráció beállítása](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interaktív Hive-lekérdezések és Hive-kötegparancsfájlok küldése

A Spark & Hive-eszközök a Visual Studio-kód, interaktív Hive-lekérdezések és Hive kötegelt parancsfájlok a fürtök.

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Jelölje ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld.hql** fájlt. Megnyílik a forgatókönyv-szerkesztőben.

3. Másolja a következő kódot a Hive-fájlba, majd mentse azt:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Csatlakozzon](#connect-to-an-azure-account) Azure-fiókjához, vagy csatoljon egy fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza a **Hive: Interaktív** parancsot a lekérdezés elküldéséhez, vagy használja a Ctrl+Alt+I billentyűparancsot.  Válassza a **Hive: Batch lehetőséget** a parancsfájl elküldéséhez, vagy használja a Ctrl+Alt+H billentyűparancsot.  

6. Ha nem adott meg alapértelmezett fürtöt, jelöljön ki egy fürtöt. Az eszközök lehetővé teszik, hogy a teljes parancsfájl helyett egy kódblokkot küldjön be a helyi menü használatával. Néhány pillanat múlva a lekérdezés eredményei egy új lapon jelennek meg:

   ![Interaktív Apache Hive-lekérdezés eredménye](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **EREDMÉNYEK** panel: A teljes eredményt CSV-, JSON- vagy Excel-fájlként mentheti egy helyi elérési útra, vagy csak több sort jelölhet ki.

    - **ÜZENETEK** panel: Amikor kiválaszt egy **sorszámot,** az a futó parancsfájl első sorára ugrik.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark-lekérdezések küldése

Interaktív PySpark-lekérdezések elküldéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Hozzon létre egy új **HelloWorld.py** fájlt a [korábbi](#open-a-work-folder) lépéseket követve.

3. Másolja a következő kódot a parancsfájlba:

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

4. [Csatlakozzon](#connect-to-an-azure-account) Azure-fiókjához, vagy csatoljon egy fürtöt, ha még nem tette meg.

5. Válassza ki az összes kódot, kattintson a jobb gombbal a parancsfájl-szerkesztő, és válassza **a Spark: PySpark Interactive** a lekérdezés elküldéséhez. Vagy használja a Ctrl+Alt+I parancsikont.

   ![pyspark interaktív helyi menü](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Jelölje ki a fürtöt, ha nem adott meg alapértelmezett fürtöt. Néhány pillanat múlva a **Python Interactive** eredményei egy új lapon jelennek meg. Az eszközök lehetővé teszik egy kódblokk elküldését a teljes parancsfájl helyett a helyi menü használatával:

   ![pyspark interaktív python interaktív ablak](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Írja be **a%%info**értéket, majd nyomja le a Shift+Enter billentyűkombinációt a feladatadatainak megtekintéséhez (nem kötelező):

   ![pyspark interaktív nézetfeladat-információk](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Az eszköz támogatja a **Spark SQL-lekérdezést** is:

   ![Pyspark Interaktív nézet eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   A beküldési állapot az alsó állapotsor bal oldalán jelenik meg, amikor lekérdezéseket futtat. Ne küldjön más lekérdezéseket, ha az állapot **PySpark Kernel (foglalt)**.  

   > [!NOTE]
   >
   > Ha a **Python-bővítmény engedélyezve van** a beállításokban (alapértelmezés szerint be van jelölve), a beküldött pyspark interakciós eredmények a régi ablakot fogják használni:
   >
   > ![A pyspark interaktív python bővítmény le tiltva](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark kötegelt feldolgozás elküldése

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HD example** mappát, ha be van zárva.  

2. Hozzon létre egy új **BatchFile.py** fájlt a [korábbi](#open-a-work-folder) lépések végrehajtásával.

3. Másolja a következő kódot a parancsfájlba:

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

4. [Csatlakozzon](#connect-to-an-azure-account) Azure-fiókjához, vagy csatoljon egy fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájlszerkesztőre, majd válassza a **Spark: PySpark Batch parancsot,** vagy használja a Ctrl+Alt+H billentyűparancsot.

6. Válassza ki azt a fürtöt, amelybe el szeretné küldeni a PySpark-feladatot:

   ![Python-feladat eredményének kimenetének küldése](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Miután elküldte a Python-feladatot, a küldési naplók megjelennek a Visual Studio-kód **KIMENET ablakában.** A Spark felhasználói felület url-címe és a fonal felhasználói felületének URL-címe is megjelenik. Az URL-címet egy webböngészőben megnyithatja a feladat állapotának nyomon követéséhez.

## <a name="apache-livy-configuration"></a>Apache Livy konfiguráció

[Az Apache Livy](https://livy.incubator.apache.org/) konfigurációja támogatott. Konfigurálhatja **a. VSCode\settings.json** fájlt a munkaterület mappájában. Jelenleg a Livy-konfiguráció csak a Python-parancsfájlt támogatja. További információ: [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**A Livy-konfiguráció aktiválása**

módszer  
1. A menüsorban keresse meg a > **Fájlbeállítások** > **beállításai lapot.** **File**
2. A **Keresési beállítások mezőbe** írja be a **HDInsight-feladatbeküldéset: Livy Conf**.  
3. A megfelelő keresési eredményhez válassza a Szerkesztés lehetőséget a **settings.json mezőben.**

2. módszer Fájl küldése, `.vscode` és a mappa automatikus hozzáadása a munkahelyi mappához. A Livy-konfigurációt a **.vscode\settings.json**lehetőség kiválasztásával láthatja.

+ A projekt beállításai:

    ![HDInsight Apache Livy konfiguráció](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >A **driverMemory** és **executorMemory** beállításokhoz állítsa be az értéket és az egységet. Például: 1g vagy 1024m.

+ Támogatott Livy konfigurációk:

    **POST /kötegek** Kérelem törzse

    | név | leírás | type |
    | --- | --- | --- |
    | file | A végrehajtandó alkalmazást tartalmazó fájl | Elérési út (kötelező) |
    | proxyUser | A feladat futtatásakor megszemélyesítő felhasználó | Sztring |
    | Osztálynév | Alkalmazás Java/Spark főosztály | Sztring |
    | args | Az alkalmazás parancssori argumentumai | Karakterláncok listája |
    | Tégelyek | Az ebben a munkamenetben használandó treik | Karakterláncok listája | 
    | pyFiles fájlok | A munkamenetben használandó Python-fájlok | Karakterláncok listája |
    | Fájlokat | A munkamenetben használandó fájlok | Karakterláncok listája |
    | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Sztring |
    | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | Int |
    | executorMemory memória | A végrehajtófolyamatonként használandó memória mennyisége | Sztring |
    | executorCores | Az egyes végrehajtókhoz használandó magok száma | Int |
    | numExecutors | Az ehhez a munkamenethez elindítandó végrehajtók száma | Int |
    | Archívum | Archívumok kell használni ebben az ülésen | Karakterláncok listája |
    | üzenetsor | A küldendő YARN-várólista neve| Sztring |
    | név | A munkamenet neve | Sztring |
    | Conf | Spark-konfiguráció tulajdonságai | Key=val térképe |

    Választörzs A létrehozott Köteg objektum.

    | név | leírás | type |
    | --- | ---| --- |
    | ID (Azonosító) | Munkamenet-azonosító | Int |
    | appId | A munkamenet alkalmazásazonosítója | Sztring |
    | appInfo | Részletes alkalmazásinformációk | Key=val térképe |
    | Napló | Naplósorok | Karakterláncok listája |
    | state |Köteg állapota | Sztring |

    > [!NOTE]
    > A hozzárendelt Livy konfiguráció jelenik meg a kimeneti ablaktáblán, amikor elküldi a parancsfájlt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integráció az Azure HDInsightbal az Explorerből

A fürtökben lévő Hive-táblázat előnézetét közvetlenül az **Azure HDInsight-kezelőn** keresztül tekintheti meg:

1. [Csatlakozzon](#connect-to-an-azure-account) Azure-fiókjához, ha még nem tette meg.

2. Válassza ki az **Azure** ikont a bal szélső oszlopból.

3. A bal oldali ablaktáblából bontsa ki az **AZURE: HDINSIGHT csomópontot.** Az elérhető előfizetések és fürtök listája.

4. Bontsa ki a fürtet a Hive metaadat-adatbázis és a táblaséma megtekintéséhez.

5. Kattintson a jobb gombbal a Hive-táblára. Például: **hivesampletable**. Válassza **az Előnézet**lehetőséget.

   ![Spark & Hive a Visual Studio Kód előzetes struktúratáblájában](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Megnyílik **az Eredmények előnézete** ablak:

   ![Spark & Hive a Visual Studio Kód előzetes verzióeredményének ablakában](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- EREDMÉNYEK panel

   A teljes eredményt csv-, JSON- vagy Excel-fájlként mentheti egy helyi elérési útra, vagy csak több sort jelölhet ki.

- ÜZENETEK panel
   1. Ha a táblázatban a sorok száma meghaladja a 100-at, a következő üzenet jelenik meg: "Az első 100 sor jelenik meg a Hive-táblában."
   2. Ha a táblázatban a sorok száma 100-nál kisebb vagy egyenlő, a következő üzenet jelenik meg: "60 sor jelenik meg a Hive-táblához."
   3. Ha nincs tartalom a táblázatban, a következő üzenet`0 rows are displayed for Hive table.`jelenik meg: " "

        >[!NOTE]
        >
        >Linux alatt telepítse az xclip-et a másolási táblázat adatainak engedélyezéséhez.
        >
        >![Spark & Hive visual studio-kód Linux alatt](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>További funkciók

A Spark & A Hive for Visual Studio Code a következő szolgáltatásokat is támogatja:

- **IntelliSense automatikus kiegészítés**. Javaslatok jelennek meg kulcsszavakhoz, metódusokhoz, változókhoz és egyéb programozási elemekhez. A különböző ikonok különböző típusú objektumokat jelölnek:

    ![Spark & Hive eszközök a Visual Studio Kód IntelliSense objektumaihoz](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense hibajelölő**. A nyelvi szolgáltatás kiemeli a Hive-parancsfájl szerkesztési hibáit.     
- **Szintaxis kiemeli**. A nyelvi szolgáltatás különböző színeket használ a változók, kulcsszavak, adattípusok, függvények és egyéb programozási elemek megkülönböztetésére:

    ![Spark & Hive-eszközök a Visual Studio kódszintaxisának kiemeléseihez](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Csak olvasói szerepkör

Azok a felhasználók, akik csak olvasószerepkört kapnak a fürthöz, nem küldhetnek el feladatokat a HDInsight-fürtbe, és nem tekinthetik meg a Hive-adatbázist. Lépjen kapcsolatba a fürt rendszergazdájával, és frissítse a szerepkört a [**HDInsight-fürtoperátorra**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) az [Azure Portalon.](https://ms.portal.azure.com/) Ha érvényes Ambari hitelesítő adatokkal rendelkezik, manuálisan is összekapcsolhatja a fürtöt az alábbi útmutatás segítségével.

### <a name="browse-the-hdinsight-cluster"></a>Tallózás a HDInsight-fürtben  

Ha az Azure HDInsight explorer t választja egy HDInsight-fürt kibontásához, a rendszer kéri, hogy kapcsolja össze a fürtöt, ha rendelkezik a fürt csak olvasószerepkörrel. Az alábbi módszerrel csatolhatja a fürthöz az Ambari hitelesítő adatait.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>A feladat elküldése a HDInsight-fürtnek

Amikor feladatot küld egy HDInsight-fürtbe, a rendszer kéri, hogy kapcsolja össze a fürtöt, ha csak olvasói szerepkörben van a fürtben. Az alábbi lépésekkel a fürthöz ambari hitelesítő adatok használatával csatolhat.

### <a name="link-to-the-cluster"></a>Hivatkozás a fürtre

1. Adjon meg érvényes Ambari felhasználónevet.
2. Adjon meg érvényes jelszót.

   ![Spark & Hive-eszközök a Visual Studio kódfelhasználónevéhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive-eszközök a Visual Studio-kód jelszavához](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >A csatolt `Spark / Hive: List Cluster` fürt ellenőrzéséhez használhatja:
  >
  >![Spark & Hive-eszközök a Visual Studio kódolvasóhoz csatolva](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="browse-a-data-lake-storage-gen2-account"></a>Böngésszen egy Data Lake Storage Gen2 fiókban

Válassza ki az Azure HDInsight explorer t a Data Lake Storage Gen2 fiók bővítéséhez. A rendszer kéri, hogy adja meg a storage-hozzáférési kulcsot, ha az Azure-fiók nem fér hozzá a Gen2 storage. A hozzáférési kulcs ellenőrzése után a Data Lake Storage Gen2 fiók automatikusan ki van bontva.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Feladatok küldése HDInsight-fürtbe a Data Lake Storage Gen2 szolgáltatással

Feladat küldése egy HDInsight-fürtbe a Data Lake Storage Gen2 használatával. A rendszer kéri, hogy adja meg a storage-hozzáférési kulcsot, ha az Azure-fiók nem rendelkezik írási hozzáféréssel a Gen2 storage.You're prompted to enter the storage access key if your Azure account has no write access to Gen2 storage. A hozzáférési kulcs érvényesítése után a feladat sikeresen elküldésre kerül.

![Spark & Hive-eszközök a Visual Studio kódhozzáférési kulcsához](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> A tárfiók hozzáférési kulcsa az Azure Portalon. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../storage/common/storage-account-keys-manage.md)

## <a name="unlink-cluster"></a>Fürt leválasztása

1. A menüsorban nyissa meg a**Parancspaletta** **megtekintése** > lapot, majd írja be a **Spark / Hive: Cluster leválasztása**.  

2. Jelölje ki a leválasztani kívánt fürtöt.  

3. Az ellenőrzéshez tekintse meg a **OUTPUT** nézetet.  

## <a name="sign-out"></a>Kijelentkezés  

A menüsorban nyissa meg a**Parancspaletta** **megtekintése** > lapot, és írja be az **Azure: Kijelentkezés**című lapot.

## <a name="next-steps"></a>További lépések

A Spark & Hive for Visual Studio Code használatával készült videót a [Spark & Hive for Visual Studio Code (Spark & Hive for Visual Studio Code) (Spark & Hive for Visual Studio Code) (A Spark & A Kaptár a Visual Studio-kódhoz ) mutatja be.](https://go.microsoft.com/fwlink/?linkid=858706)
