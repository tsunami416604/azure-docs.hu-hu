---
title: Lekérdezés Apache Hive a JDBC-illesztőn keresztül – Azure HDInsight
description: Egy Java-alkalmazásból származó JDBC-illesztőprogrammal küldhet Apache Hive lekérdezéseket a HDInsight Hadoop. Programozott módon és a mókus SQL-ügyfélről is kapcsolódhat.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: cd8a6c7e7f5ddf781fcd63f3969eedd8f45424bc
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058617"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Lekérdezés Apache Hive a HDInsight JDBC-illesztőn keresztül

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Megtudhatja, hogyan használhatja a Java-alkalmazásokból származó JDBC-illesztőprogramot Apache Hive lekérdezéseknek az Azure HDInsight Apache Hadoop való elküldéséhez. A jelen dokumentumban található információk azt mutatják be, hogyan lehet programozott módon és a mókus SQL-ügyfélről csatlakozni.

További információ a kaptár JDBC felületéről: [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Előfeltételek

* An méretű HDInsight Hadoop-fürt. Az első lépésekhez tekintse meg [Az Azure HDInsight megismerését](apache-hadoop-linux-tutorial-get-started.md)ismertető témakört.
* A [Java Developer Kit (JDK) 11-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) vagy újabb verziója.
* [Mókus SQL](http://squirrel-sql.sourceforge.net/). A mókus egy JDBC ügyfélalkalmazás.

## <a name="jdbc-connection-string"></a>JDBC-kapcsolati sztring

Az Azure-beli HDInsight-fürtökkel létesített JDBC-kapcsolatok az 443-as porton keresztül érhetők el, és a forgalom SSL használatával van védve. A fürtök mögött található nyilvános átjáró átirányítja a forgalmat arra a portra, amelyet a HiveServer2 ténylegesen figyel. A következő kapcsolódási sztring a HDInsight használandó formátumot mutatja:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Cserélje le a `CLUSTERNAME` kifejezést a HDInsight-fürt nevére.

## <a name="authentication"></a>Authentication

A csatlakozás létrehozásakor a HDInsight-fürt rendszergazdájának felhasználónevét és jelszavát kell használnia a fürt átjárójának hitelesítéséhez. Ha olyan JDBC-ügyfelekről csatlakozik, mint például a mókus SQL, meg kell adnia a rendszergazda nevét és jelszavát az ügyfél beállításai között.

Egy Java-alkalmazásból a kapcsolatok létrehozásakor a nevet és a jelszót kell használnia. Például a következő Java-kód egy új kapcsolatokat nyit meg a kapcsolatok karakterlánca, a rendszergazda neve és a jelszó használatával:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Kapcsolat a mókus SQL-ügyféllel

A mókus SQL egy JDBC-ügyfél, amely a kaptár-lekérdezések távoli futtatására használható a HDInsight-fürttel. A következő lépések azt feltételezik, hogy már telepítette a mókus SQL-t.

1. Hozzon létre egy könyvtárat, amely bizonyos, a fürtből másolandó fájlokat tartalmaz.

2. A következő parancsfájlban cserélje le `sshuser` a parancsot a fürthöz tartozó SSH-felhasználói fiók nevére.  Cserélje `CLUSTERNAME` le a nevet a HDInsight-fürt nevére.  A parancssorban módosítsa a munkahelyi könyvtárat az előző lépésben létrehozottra, majd írja be a következő parancsot a fájlok HDInsight-fürtből való másolásához:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Indítsa el a mókus SQL-alkalmazást. Az ablak bal oldalán válassza az **illesztőprogramok**lehetőséget.

    ![Illesztőprogramok lap az ablak bal oldalán](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Az **illesztőprogramok** párbeszédpanel tetején található ikonok közül válassza az **+** ikont az illesztőprogram létrehozásához.

    ![Mókus SQL Application-illesztőprogramok ikon](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Az illesztőprogram hozzáadása párbeszédpanelen adja meg a következő adatokat:

    * **Név**: Hive
    * **Példa URL-címére**:`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra osztály elérési útja**: A **Hozzáadás** gomb használatával adja hozzá a korábban letöltött összes jar-fájlt
    * **Osztály neve**: org. Apache. kaptár. JDBC. HiveDriver

   ![illesztőprogram-párbeszédpanel hozzáadása paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   A beállítások mentéséhez kattintson **az OK gombra** .

6. A mókus SQL-ablak bal oldalán válassza az **aliasok**lehetőséget. Ezután válassza a **+** ikont a kapcsolódási alias létrehozásához.

    ![Mókus SQL új alias hozzáadása párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Használja az alábbi értékeket az **alias hozzáadása** párbeszédpanelen.

    * **Név**: Struktúra a HDInsight

    * **Illesztőprogram**: A **struktúra** illesztőprogramjának kiválasztásához használja a legördülő listát.

    * **URL-CÍM**:`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

    * **Felhasználónév**: A fürt bejelentkezési fiókjának neve a HDInsight-fürthöz. A mező alapértelmezett értéke: `admin`.

    * **Jelszó**: A fürt bejelentkezési fiókjának jelszava.

   ![alias hozzáadása párbeszédpanel paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > A **teszt** gomb használatával ellenőrizze, hogy a kapcsolódás működik-e. **Kapcsolódás a következőhöz: Ekkor megjelenik a** kaptár a HDInsight párbeszédpanelen, és válassza a **Kapcsolódás** lehetőséget a teszt végrehajtásához. Ha a teszt sikeres, a **sikeres kapcsolatok** párbeszédpanel jelenik meg. Ha hiba történik, tekintse meg a [Hibaelhárítás](#troubleshooting)című témakört.

    A kapcsolódási alias mentéséhez használja az **alias hozzáadása** párbeszédpanel alján található **OK** gombot.

8. A mókus SQL-oldal tetején található **Kapcsolódás** legördülő listáról válassza a **kaptár elemet a HDInsight**. Amikor a rendszer kéri, válassza ki a **Connect**.

    ![a kapcsolatok párbeszédpanel paramétereit tartalmazó párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Ha csatlakoztatva van, adja meg a következő lekérdezést az SQL-lekérdezés párbeszédpanelen, majd válassza a **Futtatás** ikont (egy futó személy). Az eredmények területnek a lekérdezés eredményét kell megjelenítenie.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![SQL-lekérdezési párbeszédpanel, beleértve az eredményeket](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Csatlakozási példa Java-alkalmazásból

A (z) HDInsight található struktúra lekérdezése Java-ügyféllel című példa a [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)következő címen érhető el:. A minta létrehozásához és futtatásához kövesse az adattár utasításait.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Váratlan hiba történt az SQL-kapcsolatok megnyitására tett kísérlet során.

**Tünetek**: Az 3,3-es vagy annál újabb verziójú HDInsight-fürtökhöz való kapcsolódáskor hibaüzenetet kaphat, ha váratlan hiba történt. A hiba verem-nyomkövetése a következő sorokkal kezdődik:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Ok**: Ezt a hibát a mókus által tartalmazott Commons-codec. jar fájl régebbi verziója okozta.

**Feloldási**: A hiba elhárításához kövesse az alábbi lépéseket:

1. Lépjen ki a Mókusból, majd nyissa meg azt a könyvtárat, ahol a mókus telepítve van a rendszeren. A mókus könyvtárában a `lib` könyvtár alatt cserélje le a meglévő Commons-codec. jar fájlt a HDInsight-fürtről letöltöttre.

2. Indítsa újra a mókust. A hiba többé nem fordulhat elő, amikor a HDInsight-beli struktúrához csatlakozik.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan használhatja a JDBC-t a kaptár használatára, az alábbi hivatkozásokat követve megismerheti az Azure HDInsight való munkát.

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
