---
title: Apache Hive lekérdezése a JDBC illesztőprogramon keresztül – Azure HDInsight
description: Használja a JDBC-illesztőprogramot egy Java-alkalmazásból az Apache Hive-lekérdezések elküldéséhez a Hadoop hdinsight-on. Csatlakozzon programozott módon és a SQuirrel SQL kliensből.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7d1a77800093ae01bc4eb1e1269d1e9a60f9ce26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616651"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Az Apache Hive lekérdezése a HDInsight JDBC-illesztőjén keresztül

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Ismerje meg, hogyan használhatja a Java-alkalmazások JDBC-illesztőprogramját apache hive-lekérdezések elküldéséhez az Apache Hadoop nak az Azure HDInsightban. A jelen dokumentumban található információk bemutatják, hogyan lehet programozott módon csatlakozni, valamint a SQuirreL SQL-ügyfélből.

A Hive JDBC felületről a [HiveJDBCInterface című témakörben](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight Hadoop-fürt. A létrehozásról az Azure HDInsight – Első lépések című [témakörben látható.](apache-hadoop-linux-tutorial-get-started.md) Győződjön meg arról, hogy a HiveServer2 szolgáltatás fut.
* A [Java Developer Kit (JDK) 11-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) vagy újabb verziója.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). A SQuirreL egy JDBC ügyfélalkalmazás.

## <a name="jdbc-connection-string"></a>JDBC-kapcsolati sztring

JDBC-kapcsolatok egy HDInsight-fürthöz az Azure-ban a 443-as porton keresztül készülnek, és a forgalom SSL használatával védett. A nyilvános átjáró, amely mögött a fürtök ülnek átirányítja a forgalmat a portra, amely hiveserver2 ténylegesen figyel. A következő kapcsolati karakterlánc a HDInsight formátumát mutatja:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Cserélje le a `CLUSTERNAME` kifejezést a HDInsight-fürt nevére.

Vagy az **Ambari felhasználói felületén keresztül > Ambari felhasználói felületén keresztül > configs > Advanced**.

![JDBC kapcsolati karakterlánc beszereznie az Ambari-n keresztül](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Állomásnév a kapcsolati karakterláncban

A kapcsolati karakterláncban a "CLUSTERNAME.azurehdinsight.net" állomásnév megegyezik a fürt URL-címével. Az Azure Portalon keresztül juthat hozzá. 

### <a name="port-in-connection-string"></a>Port a kapcsolati karakterláncban

Csak a **443-as port** használatával csatlakozhat a fürthöz az Azure virtuális hálózaton kívüli bizonyos helyekről. A HDInsight egy felügyelt szolgáltatás, ami azt jelenti, hogy a fürthöz való összes kapcsolat biztonságos átjárón keresztül történik. A HiveServer 2-höz közvetlenül az 10001-es vagy 10000-es porton nem lehet csatlakozni, mert ezek a portok nincsenek kitéve a külvilágnak. 

## <a name="authentication"></a>Hitelesítés

A kapcsolat létrehozásakor a HDInsight-fürt felügyeleti nevét és jelszavát kell használnia a fürtátjáró hitelesítéséhez. JDBC-ügyfelekből, például a SQuirreL SQL-ből való csatlakozáskor meg kell adnia a rendszergazda nevét és jelszavát az ügyfélbeállításokban.

A Java-alkalmazásokból a nevet és a jelszót kell használnia a kapcsolat létrehozásakor. A következő Java-kód például új kapcsolatot nyit meg a kapcsolati karakterlánc, a rendszergazdaneve és a jelszó használatával:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Csatlakozás a SQuirreL SQL-ügyfélhez

A SQuirreL SQL egy JDBC-ügyfél, amely a HIVe-lekérdezések hdinsight-fürtdel történő távoli futtatására használható. A következő lépések feltételezik, hogy már telepítette a SQuirreL SQL-t.

1. Hozzon létre egy könyvtárat, amely a fürtből másolandó bizonyos fájlokat tartalmazza.

2. A következő parancsfájlban `sshuser` cserélje le a fürt SSH felhasználói fióknevét.  Cserélje `CLUSTERNAME` le a HDInsight-fürt nevére.  Parancssorból módosítsa a munkakönyvtárat az előző lépésben létrehozottkönyvtárra, majd írja be a következő parancsot a FÁJLOK HDInsight-fürtből történő másolásához:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Indítsa el a SQuirreL SQL alkalmazást. Az ablak bal oldalán válassza az **Illesztőprogramok**lehetőséget.

    ![Az ablak bal oldalán található Illesztőprogramok lap](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Az **Illesztőprogramok** párbeszédpanel tetején lévő ikonok **+** közül válassza ki az ikont az illesztőprogram létrehozásához.

    ![A SQuirreL SQL alkalmazás-illesztőprogramok ikonja](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Az Illesztőprogram hozzáadása párbeszédpanelen adja meg a következő adatokat:

    |Tulajdonság | Érték |
    |---|---|
    |Név|Hive|
    |Példa URL-címe|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Extra osztály elérési útja|A **Hozzáadás** gombbal hozzáadhatja a korábban letöltött összes jar fájlt.|
    |Osztály neve|org.apache.hive.jdbc.HiveDriver|

   ![illesztőprogram hozzáadása párbeszédpanel paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   A beállítások mentéséhez válassza az **OK gombot.**

6. A SQuirreL SQL ablak bal oldalán válassza az **Aliasok**lehetőséget. Ezután **+** válassza ki az ikont a kapcsolatalias létrehozásához.

    ![SQuirreL SQL új alias hozzáadása párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Az **Alias hozzáadása** párbeszédpanelen a következő értékeket használja:

    |Tulajdonság |Érték |
    |---|---|
    |Név|Hive a HDInsight-on|
    |Illesztőprogram|A legördülő menü segítségével **Hive** válassza ki a Hive-illesztőprogramot.|
    |URL-cím|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.|
    |Felhasználónév|A HDInsight-fürt fürtbejelentkezési fiókneve. **Az**alapértelmezett admin .|
    |Jelszó|A fürt bejelentkezési fiókjának jelszava.|

    ![alias hozzáadása párbeszédpanel paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > A **Teszt** gombbal ellenőrizze, hogy a kapcsolat működik-e. Amikor **a Csatlakozás: Hive a HDInsight** párbeszédpanelen jelenik meg, válassza a **Csatlakozás** lehetőséget a teszt végrehajtásához. Ha a teszt sikeres, megjelenik egy **sikeres kapcsolat** párbeszédpanel. Ha hiba történik, olvassa el a [Hibaelhárítás című témakört.](#troubleshooting)

    A kapcsolatalias mentéséhez használja az **Alias hozzáadása** párbeszédpanel alján található **Ok** gombot.

8. A **Csatlakozás menüből** a SQuirreL SQL tetején található legördülő menüben válassza **a Hive a HDInsight on**lehetőséget. Amikor a rendszer kéri, válassza a **Csatlakozás lehetőséget.**

    ![kapcsolat párbeszédpanel paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. A csatlakozás után írja be a következő lekérdezést az SQL-lekérdezés párbeszédpanelre, majd válassza a **Futtatás** ikont (egy futó személyt). Az eredményterületnek meg kell jelennie a lekérdezés eredményeivel.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![sql lekérdezési párbeszédpanel, eredményekkel együtt](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Csatlakozás egy példa Java-alkalmazásból

A rendszer a segítségével java ügyfél lelheti le [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)a Hive-t a HDInsight-on. Kövesse a tárházban található utasításokat a minta létrehozásához és futtatásához.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Váratlan hiba történt SQL-kapcsolat megnyitása közben

**Jelenségek**: Ha 3.3-as vagy újabb verziójú HDInsight-fürthöz csatlakozik, előfordulhat, hogy váratlan hiba történt. A hiba veremnyomata a következő sorokkal kezdődik:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Ok**: Ezt a hibát a SQuirreL részét képező régebbi commons-codec.jar fájl okozza.

**Megoldás**: A hiba kijavításához kövesse az alábbi lépéseket:

1. Lépjen ki a SQuirreL-ből, majd lépjen a könyvtárba, ahol `C:\Program Files\squirrel-sql-4.0.0\lib`a SQuirreL telepítve van a rendszeren, talán . A SquirreL könyvtárban `lib` a könyvtár alatt cserélje le a meglévő commons-codec.jar-ot a HDInsight-fürtből letöltöttkönyvtárra.

1. Indítsa újra a SQuirreL-t. A hiba már nem fordulhat elő, ha a HIVe hdinsight-on csatlakozik.

### <a name="connection-disconnected-by-hdinsight"></a>A HDInsight által megszakadt kapcsolat

**Tünetek**: Amikor hatalmas mennyiségű adatot próbál letölteni (mondjuk több GB-t) a JDBC / ODBC-n keresztül, a kapcsolatot a HDInsight váratlanul leválasztja letöltés közben. 

**Ok**: Ezt a hibát az átjárócsomópontok korlátozása okozza. Amikor adatokat kap a JDBC/ODBC-től, minden adatnak át kell haladnia az átjáró csomóponton. Az átjárót azonban nem úgy tervezték, hogy hatalmas mennyiségű adatot töltsön le, így a kapcsolatot az átjáró bezárhatja, ha nem tudja kezelni a forgalmat.

**Felbontás**: Kerülje a JDBC/ODBC illesztőprogram használatát hatalmas mennyiségű adat letöltéséhez. Adatok másolása közvetlenül a blob storage helyett.


## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan használhatja a JDBC-t a Hive-val való együttműködésre, az alábbi hivatkozások segítségével további módszereket fedezhet fel az Azure HDInsight használatával való együttműködésre.

* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI szolgáltatással az Azure HDInsightban.](apache-hadoop-connect-hive-power-bi.md)
* [Jelenítse meg az interaktív lekérdezéshive-adatokat az Azure HDInsight Power BI szolgáltatásában.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Az Apache Zeppelin segítségével apache hive-lekérdezéseket futtataz az Azure HDInsightban.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Az Excel csatlakoztatása a HDInsighthoz a Microsoft Hive ODBC illesztőprogrammal](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával.](apache-hadoop-connect-excel-power-query.md)
* [Csatlakozzon az Azure HDInsighthoz, és futtasson Apache Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával.](apache-hadoop-visual-studio-tools-get-started.md)
* [Használja az Azure HDInsight eszközt a Visual Studio-kódhoz.](../hdinsight-for-vscode.md)
* [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md)
* [Az Apache Hive használata a HDInsight segítségével](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight segítségével](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
