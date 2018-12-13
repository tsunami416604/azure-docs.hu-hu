---
title: Az Apache Hive-lekérdezés a JDBC-illesztőprogram – Azure HDInsight segítségével
description: A Java-alkalmazás JDBC-illesztőprogram segítségével Hadoop on HDInsight az Apache Hive-lekérdezések elküldéséhez. Programozott módon, és az SQuirrel SQL-ügyfél csatlakoztatása.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: hrasheed
ms.openlocfilehash: c565ccf7ac04e2a3ba86e2fa256a05a9649d2de4
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166149"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Lekérdezés az Apache Hive a HDInsight a JDBC-illesztőprogram segítségével

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Ismerje meg, hogyan használható a JDBC-illesztővel Java-alkalmazás az Azure HDInsight az Apache Hadoop Apache Hive-lekérdezések elküldéséhez. A jelen dokumentumban lévő információk programozott módon, és az SQuirrel SQL-ügyfél mutatja be.

További információ a Hive JDBC-kapcsolaton: [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Előfeltételek

* A Hadoop HDInsight-fürtön.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight 3.3-as kivezetési](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](https://squirrel-sql.sourceforge.net/). SQuirreL JDBC ügyfélalkalmazás.

* A [Java fejlesztői készlet (JDK) 7-es verzió](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) vagy újabb verziója.

* [Apache Maven](https://maven.apache.org). Maven egy projektet a Java-projektek rendszert, ez a cikk társított projekt által használt hozhat létre.

## <a name="jdbc-connection-string"></a>JDBC-kapcsolati sztring

JDBC-kapcsolatok az Azure HDInsight-fürthöz végzett több, mint a 443-as, és a forgalom titkosításának SSL használatával. A nyilvános átjárót, a fürtök mögött található átirányítja a forgalmat a port, amelyet a hiveserver2-n keresztül ténylegesen figyel a következőn:. Az alábbi kapcsolati karakterláncot a formátum használatára a HDInsight látható:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Cserélje le a `CLUSTERNAME` kifejezést a HDInsight-fürt nevére.

## <a name="authentication"></a>Hitelesítés

A kapcsolat létrehozásakor, a fürt átjáró hitelesítése a HDInsight-fürt felügyeleti nevét és jelszavát kell használnia. Amikor JDBC-ügyfelek, például a SQuirreL SQL csatlakoztat, adjon meg a rendszergazdai felhasználónevét és jelszavát az ügyfélbeállításokban.

Java-alkalmazás kell használnia a név és jelszó-kapcsolat létrehozásakor. Például a következő Java-kódot megnyílik egy új kapcsolatot, a kapcsolati karakterláncot, rendszergazdai név és jelszó használatával:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Csatlakozás SQuirreL SQL-ügyféllel

SQuirreL SQL JDBC ügyfél, amely távolról futtathat Hive-lekérdezéseket a HDInsight-fürttel használható. A következő lépések azt feltételezik, hogy SQuirreL SQL már telepítve van.

1. Hozzon létre egy könyvtárat, amely a fájlokat tartalmazza. Például: `mkdir hivedriver`.

2. Egy parancssorból a következő parancsok használatával másolja a fájlokat a HDInsight-fürt:

    ```bash
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    ```

    Cserélje le `USERNAME` az a fürt SSH-felhasználói fiók nevét. Cserélje le `CLUSTERNAME` a HDInsight-fürt nevéhez.

3. A SQuirreL SQL-alkalmazás elindításához. Válassza ki a az ablak bal oldalán, **illesztőprogramok**.

    ![A bal oldali ablak illesztőprogramok lap](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. A tetején található ikonok közül a **illesztőprogramok** párbeszédpanelen válassza ki a **+** ikonra kattintva hozzon létre egy illesztőprogramot.

    ![Illesztőprogramok ikonok](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Illesztőprogram hozzáadása párbeszédpanelen adja meg a következő információkat:

    * **Név**: Hive
    * **Minta URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Elérési út felesleges osztály**: Használja a Hozzáadás gombra az összes korábban letöltött jar-fájlok hozzáadása
    * **Osztálynév**: org.apache.hive.jdbc.HiveDriver

   ![Adja hozzá az illesztőprogram párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Kattintson a **OK** ezek a beállítások mentéséhez.

6. SQuirreL SQL ablakban a bal oldalon válassza ki a **aliasok**. Kattintson a **+** ikonra kattintva hozzon létre egy kapcsolatot aliast.

    ![Új alias hozzáadása](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. A következő értékeket használja a **Alias hozzáadása** párbeszédpanel.

    * **Név**: A HDInsight Hive

    * **Illesztőprogram**: Használja a legördülő listából válassza ki a **Hive** illesztőprogram

    * **URL-CÍM**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

    * **Felhasználónév**: A fürt bejelentkezési fiók neve a HDInsight-fürthöz. A mező alapértelmezett értéke: `admin`.

    * **Jelszó**: A fürt bejelentkezési fiókjának jelszava.

 ![alias párbeszédpanel hozzáadása](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Használja a **teszt** gombra kattintva győződjön meg arról, hogy működik-e a kapcsolat. Amikor **csatlakozni: A HDInsight Hive** párbeszédpanel megjelenésekor válassza **Connect** a teszt végrehajtásához. Ha a teszt sikeres, megjelenik egy **sikeres csatlakozás** párbeszédpanel. Ha hiba lép fel, tekintse meg [hibaelhárítás](#troubleshooting).

    Szeretné menteni a kapcsolat alias, használja a **Ok** gomb alsó részén a **Alias hozzáadása** párbeszédpanel.

8. Az a **csatlakozni** SQuirreL SQL-tetején lévő legördülő listából válassza ki **Hive HDInsight**. Amikor a rendszer kéri, válassza ki a **Connect**.

    ![kapcsolódási párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. A csatlakozás után a következő lekérdezés írja be az SQL-lekérdezési párbeszéd, és válassza ki a **futtatása** ikonra. Az eredmények területen meg kell jelennie a lekérdezés eredményeit.

        select * from hivesampletable limit 10;

    ![SQL lekérdezési párbeszédpanel, beleértve az eredmények](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Csatlakozás a Java-alkalmazást például a

A HDInsight Hive-lekérdezéshez Java-ügyfél használatával például a rendelkezésre álló [ https://github.com/Azure-Samples/hdinsight-java-hive-jdbc ](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Kövesse az utasításokat, és futtassa a mintát az adattárban.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Váratlan hiba történt egy SQL-kapcsolat megnyitása

**A jelenség**: 3.3-as verziójának vagy újabb verziót egy HDInsight-fürtön való kapcsolódáskor, amelyek váratlan hiba történt a hiba jelenhet meg. Ennek a hibának a híváslánc a következő sorokat kezdődik:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**OK**: Ez a hiba oka egy régebbi verziójú SQuirreL mellékelt commons-codec.jar fájl.

**Feloldási**: Ez a hiba javításához használja a következő lépéseket:

1. A HDInsight-fürtből származó a commons-kodek jar-fájl letöltése.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. SQuirreL kilép, és keresse fel a könyvtárba SQuirreL helyéül a rendszeren. A SquirreL címtárban alatt a `lib` könyvtár, cserélje le a egy meglévő commons-codec.jar letöltött a HDInsight-fürtből.

3. Indítsa újra a SQuirreL. A hiba csak már nem fordulhat elő, amikor csatlakozik a HDInsight Hive.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan dolgozhat a Hive JDBC segítségével, egyéb módon az Azure HDInsight használata az alábbi hivatkozások segítségével.

* [A Microsoft Power BI az Azure HDInsight Hive-adatok vizualizálása](apache-hadoop-connect-hive-power-bi.md).
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel összekapcsolása a Hadooppal a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
