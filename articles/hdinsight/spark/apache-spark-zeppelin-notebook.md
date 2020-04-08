---
title: Zeppelin notebookok & Apache Spark-fürt – Azure HDInsight
description: Részletes útmutatást a Zeppelin-jegyzetfüzetek Apache Spark-fürtökkel való használatához az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 3c1369e813ba6518f6cd4b27082020ae36a24c82
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811208"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin-notebookok használata Apache Spark-fürtökön az Azure HDInsight rendszerében

A HDInsight Spark-fürtök [apache Zeppelin-jegyzetfüzeteket](https://zeppelin.apache.org/) tartalmaznak. A notebookok segítségével [apache spark-feladatok](https://spark.apache.org/) futtatásához. Ebben a cikkben megtudhatja, hogyan használhatja a Zeppelin-jegyzetfüzetet egy HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* A fürtök elsődleges tárolóURI-séma. A rendszer `wasb://` az Azure Blob `abfs://` Storage, az Azure `adl://` Data Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 lesz. Ha a blob storage biztonságos átvitele `wasbs://`engedélyezve van, az URI lesz.  További információ: [Biztonságos átvitel megkövetelése az Azure Storage-ban.](../../storage/common/storage-require-secure-transfer.md)

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin notebook indítása

1. A Spark-fürt **áttekintése területen**válassza a **Zeppelin-jegyzetfüzetet** **a Fürt irányítópultjai**között. Adja meg a fürt rendszergazdai hitelesítő adatait.  

   > [!NOTE]  
   > A fürtzeppelin-jegyzetfüzetét úgy is elérheti, hogy megnyitja a következő URL-címet a böngészőben. Cserélje le a **CLUSTERNAME-t** a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Hozzon létre új notebookot. A fejlécablakban keresse meg a Jegyzetfüzet Új jegyzet létrehozása című **elemet.** > **Create new note**

    ![Új Zeppelin notebook létrehozása](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Új Zeppelin notebook létrehozása")

    Adja meg a jegyzetfüzet nevét, majd válassza a **Jegyzet létrehozása**lehetőséget.

3. Győződjön meg arról, hogy a jegyzetfüzet fejlécében látható a csatlakoztatott állapot. Egy zöld pont jelöli a jobb felső sarokban.

    ![Zeppelin notebook állapota](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")

4. Töltse be a mintaadatokat egy ideiglenes táblába. Amikor létrehoz egy Spark-fürtöt a HDInsightban, `hvac.csv`a mintaadatfájl `\HdiSamples\SensorSampleData\hvac`a rendszer a társított tárfiókba másolja a programot.

    Az új jegyzetfüzetben alapértelmezés szerint létrehozott üres bekezdésbe illessze be a következő kódrészletet.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Nyomja **le a SHIFT + ENTER billentyűkombinációt,** vagy válassza a szövegkódrészlet futtatásához a bekezdés **Lejátszás** gombját. A bekezdés jobb sarkában lévő állapotnak a READY, PENDING, RUNNING-ról BEFEJEZETT állapotba kell lépnie. A kimenet ugyanannak a bekezdésnek az alján jelenik meg. A képernyőkép a következő képhez hasonlóan néz ki:

    ![Ideiglenes tábla létrehozása nyers adatokból](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Ideiglenes tábla létrehozása nyers adatokból")

    Az egyes bekezdések hez címet is megadhat. A bekezdés jobb sarkában válassza a **Beállítások** ikont (lánckerék), majd a **Cím megjelenítése**lehetőséget.  

    > [!NOTE]  
    > A(z) %spark2 értelmező nem támogatott a Zeppelin-jegyzetfüzetekben az összes HDInsight-verzióban, és a HDInsight 4.0-tól kezdve a%sh értelmező nem támogatott.

5. Most már futtathatja a Spark `hvac` SQL-utasítások az asztalon. Illessze be a következő lekérdezést egy új bekezdésbe. A lekérdezés beolvassa az épület azonosítóját. Szintén a különbség a cél és a tényleges hőmérséklet minden épület egy adott időpontban. Nyomja **le a SHIFT + ENTER billentyűkombinációt.**

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    A **%sql** utasítás az elején megmondja a jegyzetfüzetnek, hogy használja a Livy Scala értelmezőt.

6. A **sávdiagram** ikonra a megjelenítés módosításához kattintson.  **beállítások**, a **Sávdiagram**kiválasztása után jelennek meg, lehetővé teszi a **Billentyűk**és **az Értékek**lehetőséget.  A következő képernyőkép a kimenetet mutatja.

    ![Spark SQL-utasítás futtatása a notebookhasználatával1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Spark SQL-utasítás futtatása a notebookhasználatával1")

7. Spark SQL-utasítások is futtathatók a lekérdezés változói használatával. A következő kódrészlet bemutatja, hogyan `Temp`definiáljon egy változót a lekérdezésben azokkal a lehetséges értékekkel, amelyekkel le szeretne kérdezni. A lekérdezés első futtatásakor a rendszer automatikusan feltölt egy legördülő menüt a változóhoz megadott értékekkel.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Illessze be ezt a kódrészletet egy új bekezdésbe, és nyomja **le a SHIFT + ENTER billentyűkombinációt.** Ezután válassza a **65-öt** a **Temp** legördülő listából.

8. A **sávdiagram** ikonra a megjelenítés módosításához kattintson.  Ezután válassza ki a **beállításokat,** és hajtsa végre a következő módosításokat:

   * **Csoportok:**  Adja hozzá **a targettemp**ot.  
   * **Értékek:** 1. A **dátum**eltávolítása.  2. Adja hozzá **temp_diff.**  3.  Módosítsa a beszerzési szolgáltatót **SZUM-ról** **AVG-re.**  

     A következő képernyőkép a kimenetet mutatja.

     ![Spark SQL-utasítás futtatása a notebookhasználatával2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spark SQL-utasítás futtatása a notebookhasználatával2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hogyan használhatom a külső csomagokat a notebookkal?

A HDInsight-alapú Apache Spark-fürtben található Zeppelin-jegyzetfüzetek olyan külső, közösség által készített csomagokat is használhatnak, amelyek nem szerepelnek a fürtben. Keresse meg a [Maven-tárházban](https://search.maven.org/) a rendelkezésre álló csomagok teljes listáját. Más forrásokból is beszerezheti a rendelkezésre álló csomagok listáját. Például a közösség által készített csomagok teljes listája elérhető a [Spark-csomagokban.](https://spark-packages.org/)

Ebben a cikkben láthatja, hogyan használhatja a [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook.

1. Nyissa meg a tolmácsbeállításokat. A jobb felső sarokban jelölje ki a bejelentkezett felhasználónevet, majd válassza a **Tolmács**lehetőséget.

    ![Tolmács indítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive kimenet")

2. Görgessen **livy2**lapul, majd válassza **a szerkesztés**lehetőséget.

    ![Értelmező beállításainak módosítása1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Értelmező beállításainak módosítása1")

3. Lépjen a `livy.spark.jars.packages`billentyűre, és állítsa `group:id:version`be az értékét a formátumban. Tehát, ha a [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot szeretné használni, a kulcs `com.databricks:spark-csv_2.10:1.4.0`értékét a értékére kell állítania.

    ![Értelmező beállításainak módosítása2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Értelmező beállításainak módosítása2")

    Válassza a **Mentés,** majd az **OK gombot** a Livy-értelmező újraindításához.

4. Ha meg szeretné érteni, hogyan lehet megérkezni a fent megadott kulcs értékét, itt van, hogyan.

    a. Keresse meg a csomagot a Maven repositoryban. Ehhez a cikkhez [használtunk spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. A tárházból gyűjtse össze a **GroupId**, **ArtifactId**és **Version**értékét.

    ![Külső csomagok használata a Jupyter notebookkal](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Külső csomagok használata a Jupyter notebookkal")

    c. A három érték összefűzése kettősponttal (**: )** elválasztva

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Hol vannak a Zeppelin notebookok mentve?

A Zeppelin notebookok a fürt fejnokba kerülnek. Így ha törli a fürtöt, a jegyzetfüzetek is törlődnek. Ha meg szeretné őrizni a jegyzetfüzeteket későbbi használatra más fürtökön, a feladatok futtatása után exportálnia kell őket. Jegyzetfüzet exportálásához jelölje ki az **Exportálás** ikont az alábbi képen látható módon.

![Jegyzetfüzet letöltése](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "A jegyzetfüzet letöltése")

Ez a művelet jsonfájlként menti a jegyzetfüzetet a letöltési helyre.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Zeppelin-értelmezők `Shiro` hozzáférésének konfigurálása vállalati biztonsági csomag (ESP) fürtökben

Ahogy fentebb `%sh` említettük, a hdinsight 4.0-s évektől nem támogatott a tolmács. Továbbá, mivel `%sh` az értelmező potenciális biztonsági problémákat vet be, például a parancsrendszeri parancsokat használó kulcslapok elérését, a HDInsight 3.6 ESP-fürtökből is eltávolították. Ez `%sh` azt jelenti, hogy az értelmező nem érhető el, ha az **Új jegyzet létrehozása** gombra vagy alapértelmezés szerint a Tolmács felhasználói felületére kattint.

A kiemelt jogosultságú `Shiro.ini` tartományi felhasználók a fájl segítségével szabályozhatják a értelmező felhasználói felületéhez való hozzáférést. Csak ezek a `%sh` felhasználók hozhatnak létre új `%sh` értelmezőket, és állíthatnak be engedélyeket minden egyes új értelmezőhöz. A fájllal `shiro.ini` való hozzáférés szabályozásához kövesse az alábbi lépéseket:

1. Új szerepkör definiálása meglévő tartománycsoport név használatával. A következő példában `adminGroupName` az AAD kiemelt felhasználók csoportja. Ne használjon speciális karaktereket vagy szóközöket a csoport nevében. Az utána `=` lévő karakterek adják meg a szerepkörhöz szükséges engedélyeket. `*`azt jelenti, hogy a csoport teljes engedélyekkel rendelkezik.

    ```
    [roles]
    adminGroupName = *
    ```

2. Adja hozzá a Zeppelin tolmácsokhoz való hozzáférés új szerepét. A következő példában minden `adminGroupName` felhasználó hozzáférést kap a Zeppelin tolmácsokhoz, és új tolmácsokat hozhat létre. Több szerepkört is elhelyezhet `roles[]`a zárójelek között, vesszővel elválasztva. Ezután a szükséges engedélyekkel rendelkező felhasználók hozzáférhetnek a Zeppelin tolmácsokhoz.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy munkamenet-kezelés

A Zeppelin-jegyzetfüzet első kódbekezdése új Livy-munkamenetet hoz létre a fürtben. Ez a munkamenet meg van osztva az összes zeppelin-jegyzetfüzetközött, amelyet később létrehoz. Ha a Livy-munkamenetet bármilyen okból megölik, a feladatok nem fognak futni a Zeppelin notebookból.

Ebben az esetben a következő lépéseket kell tennie, mielőtt megkezdené a feladatok futtatását egy Zeppelin notebookból.  

1. Indítsa újra a Livy értelmezőt a Zeppelin notebookból. Ehhez nyissa meg a tolmácsbeállításokat a bejelentkezett felhasználónév kiválasztásával a jobb felső sarokban, majd válassza a **Tolmács**lehetőséget.

    ![Tolmács indítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive kimenet")

2. Görgessen **livy2,** majd válassza **az újraindítás**lehetőséget.

    ![A Livy-értelmező újraindítása](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Indítsa újra a Zeppelin tolmácsot")

3. Futtasson egy kódcellát egy meglévő Zeppelin notebookból. Ez a kód egy új Livy-munkamenetet hoz létre a HDInsight-fürtben.

## <a name="general-information"></a>Általános információk

### <a name="validate-service"></a>Szolgáltatás ellenőrzése

Az Ambari szolgáltatásának érvényesítéséhez `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` keresse meg a CLUSTERNAME fürtnevét.

A szolgáltatás parancssorból történő érvényesítéséhez az SSH a főcsomópontra. Váltson a felhasználóra `sudo su zeppelin`zeppelinre a parancs segítségével. Állapotparancsok:

|Parancs |Leírás |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Szolgáltatás állapota.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|A szolgáltatás verziója.|
|`ps -aux | grep zeppelin`|Azonosítsa a PID-t.|

### <a name="log-locations"></a>Naplóhelyek

|Szolgáltatás |Útvonal |
|---|---|
|zeppelin-szerver|/usr/hdp/current/zeppelin-server/|
|Kiszolgálói naplók|/var/log/zeppelin|
|Konfigurációs `Shiro`értelmező, , site.xml, log4j|/usr/hdp/current/zeppelin-server/conf or /etc/zeppelin/conf|
|PID-könyvtár|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése

1. Keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` meg, ahol a CLUSTERNAME a fürt neve.

1. Keresse meg a **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content.**

1. Módosítás `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`a következőre.

1. Adja `log4j.logger.org.apache.zeppelin.realm=DEBUG`hozzá a bővítményt.

1. Mentse a módosításokat, és indítsa újra a szolgáltatást.

## <a name="next-steps"></a>További lépések

[Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az Apache Spark Scala alkalmazások létrehozásához és elküldéséhez használja az IntelliJ IDEA HDInsight eszközök beépülő modulját](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
