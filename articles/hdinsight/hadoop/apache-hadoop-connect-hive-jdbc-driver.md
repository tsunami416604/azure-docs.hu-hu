---
title: "Keresztül a JDBC-illesztőt - Azure HDInsight Hive lekérdezés |} Microsoft Docs"
description: "A Java-alkalmazás JDBC-illesztőt segítségével elküldeni a hdinsight Hadoop Hive-lekérdezéseket. Csatlakoztassa a programozott módon, és az SQuirrel SQL ügyfélről."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: ae92f7bb7b471c54b33e8714c5775c04c9db04e8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Lekérdezés Hive hdinsight JDBC-illesztőt keresztül

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Útmutató a Java-alkalmazás JDBC-illesztőt küldeni az Azure HDInsight Hadoop Hive-lekérdezéseket. A jelen dokumentumban szereplő információk programozott módon, és az SQuirrel SQL ügyfél mutatja be.

A Hive JDBC kapcsolaton további információkért lásd: [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Előfeltételek

* A Hadoop on HDInsight-fürt. Linux-alapú vagy a Windows-alapú fürtök működik.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight 3.3 használatból való kivonást](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL JDBC ügyfélalkalmazás.

* A [Java fejlesztői készlet (JDK) 7-es verzió](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) vagy újabb verzióját.

* [Apache Maven](https://maven.apache.org). Maven, a projekt felépítéséhez rendszer Java-projektek esetében ez a cikk társított projekt által használt.

## <a name="jdbc-connection-string"></a>JDBC-kapcsolati karakterlánc

Az Azure HDInsight-fürtök JDBC kapcsolatok válnak, több mint a 443-as, és a forgalom SSL használatával lett biztonságossá. A nyilvános átjáró, amely a fürtök elhelyezkedik mögött átirányítja a forgalmat a portot, amelyet a hiveserver2-n ténylegesen figyel a következőn:. A következő kapcsolati karakterláncot a HDInsight az formátumát mutatja:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Cserélje le a `CLUSTERNAME` kifejezést a HDInsight-fürt nevére.

## <a name="authentication"></a>Authentication

A kapcsolat létrehozásakor, a HDInsight fürt admin nevét és jelszavát, hogy a fürt átjáró hitelesítést kell használnia. JDBC-ügyfelek például SQuirreL SQL csatlakozáshoz meg kell adni a rendszergazda nevét és jelszavát ügyfélbeállításokat.

Java-alkalmazás kell használnia a nevet és jelszót egy kapcsolat. Például a következő Java-kódot megnyílik egy új kapcsolatot a kapcsolati karakterláncot, a felügyeleti neve és a jelszót:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Csatlakozás SQuirreL SQL-ügyféllel

SQuirreL SQL JDBC-ügyfél, amely segítségével távolról ugyanúgy futtathatják a Hive-lekérdezéseket a HDInsight-fürthöz. A következő lépések azt feltételezik, hogy SQuirreL SQL már telepítve van.

1. Másolja a Hive JDBC-illesztőprogramok a HDInsight-fürthöz.

    * A **Linux-alapú HDInsight** 3.5-ös vagy 3.6, az alábbi lépések segítségével töltse le a szükséges jar-fájlok a fürt.

        1. Hozzon létre egy könyvtárat, a tároló. Például: `mkdir hivedriver`.

        2. A parancssorból használja a fájlok másolását a HDInsight-fürthöz a következő parancsokat:

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

            Cserélje le `USERNAME` az SSH felhasználói fiók nevét a fürthöz. Cserélje le `CLUSTERNAME` a HDInsight-fürt nevéhez.

    * A **Windows-alapú HDInsight**, tegye a következőket a jar-fájlok letöltésére.

        1. Azure-portálról, jelölje be a HDInsight-fürthöz, és válassza a **távoli asztal** ikonra.

            ![Távoli asztali ikon](./media/apache-hadoop-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. A távoli asztal szakasz használata a **Connect** gombra kattintva csatlakozzon a fürthöz. A távoli asztal nincs engedélyezve, ha ezen a képernyőn adjon meg egy felhasználónevet és jelszót, majd válasszon **engedélyezése** távoli asztal engedélyezése a fürthöz.

            ![Távoli asztali szakasz](./media/apache-hadoop-connect-hive-jdbc-driver/remotedesktopblade.png)

            Miután kiválasztott **Connect**, egy. RDP-fájl letöltése. Ez a fájl segítségével indítsa el a távoli asztali ügyfél. Amikor a rendszer kéri, a felhasználónév és a távoli asztal eléréséhez megadott jelszó használata.

        3. A csatlakozás után másolja a következő fájlokat a távoli asztali munkamenetből a helyi számítógépre. Nevű helyi könyvtárba helyezze őket `hivedriver`.

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-JDBC-0.14.0.2.2.9.1-7-Standalone.JAR
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-Common-2.6.0.2.2.9.1-7.JAR
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.JAR

            > [!NOTE]
            > Lehet, hogy a fürt másik a verziószámok szerepel az elérési útját és fájlnevét.

        4. Miután befejezte a fájlok másolása a távoli asztali munkamenet leválasztása.

2. A SQuirreL SQL alkalmazás indításához. Válassza ki a az ablak bal oldalán, **illesztőprogramok**.

    ![A bal oldali ablak illesztőprogramok lap](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

3. A felső részén ikonok a **illesztőprogramok** párbeszédpanelen válassza a  **+**  ikonra kattintva hozzon létre egy illesztőprogramot.

    ![Illesztőprogramok ikonok](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

4. Illesztőprogram hozzáadása párbeszédpanelen adja meg a következő információkat:

    * **Név**: struktúra
    * **Példa URL-cím**:`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra osztály elérési útja**: korábban letöltött a Hozzáadás gombra a jar-fájlok hozzáadása
    * **Osztálynév**: org.apache.hive.jdbc.HiveDriver

   ![illesztőprogram párbeszédpanel hozzáadása](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Kattintson a **OK** ezek a beállítások mentéséhez.

5. Az SQuirreL SQL ablak bal oldalán válassza ki a **aliasok**. Kattintson a  **+**  ikonra kattintva hozzon létre egy kapcsolat aliast.

    ![Új alias hozzáadása](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

6. A következő értékeket használja a **hozzáadása Alias** párbeszédpanel.

    * **Név**: hdinsight Hive

    * **Illesztőprogram**: a legördülő lista segítségével válassza ki a **Hive** illesztőprogram

    * **URL-CÍM**:`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

    * **Felhasználónév**: a HDInsight-fürt a fürt bejelentkezési fiók neve. Az alapértelmezett érték `admin`.

    * **Jelszó**: a fürt bejelentkezési fiók jelszavát.

 ![Adja hozzá a alias párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    Használja a **teszt** gombra kattintva győződjön meg arról, hogy működik-e a kapcsolat. Ha **csatlakozni: hdinsight Hive** kiválasztása párbeszédpanel jelenik meg, **Connect** a vizsgálat végrehajtásához. Ha a teszt sikeres, megjelenik egy **sikeres kapcsolat** párbeszédpanel. Hiba esetén lásd: [hibaelhárítás](#troubleshooting).

    A kapcsolat alias mentéséhez használja a **Ok** gomb alján a **hozzáadása Alias** párbeszédpanel.

7. Az a **csatlakozni** válassza ki a legördülő menü felső részén SQuirreL SQL **hdinsight Hive**. Amikor a rendszer kéri, válassza ki a **Connect**.

    ![kapcsolódási párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

8. A csatlakozás után a következő lekérdezés írhat-e az SQL-lekérdezési párbeszédpanel megnyitása, és válassza a **futtatása** ikonra. Az eredmények területen meg kell jelennie a lekérdezés eredményeit.

        select * from hivesampletable limit 10;

    ![SQL lekérdezési párbeszédpanel megnyitása, beleértve az eredmények](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Csatlakoztatja a Java-alkalmazást például a

Példa a HDInsight Hive lekérdezés Java-ügyfél használatával érhető el: [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Kövesse az utasításokat a építsenek, és futtathatja a tárházban.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Váratlan hiba történt egy SQL-kapcsolat megnyitása

**A jelenség**: HDInsight-fürtök 3.3-as verziójának vagy újabb verziójú való csatlakozáskor jelenhet meg, amely a váratlan hiba történt hiba. Az alábbi veremkivonatban találhatók a hiba a következő sorokat kezdődik:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**OK**: Ez a hiba oka egy régebbi verziójú SQuirreL mellékelt commons-codec.jar fájl.

**Megoldási**: Ez a hiba elhárításához tegye a következőket:

1. Töltse le a commons-kodek jar-fájlt a HDInsight-fürthöz.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Lépjen ki a SQuirreL, és keresse meg a könyvtár ahol a SQuirreL telepítve van a rendszeren. A SquirreL könyvtárban a a `lib` directory, a név felülírandó a egy meglévő commons-codec.jar le: a HDInsight-fürthöz.

3. Indítsa újra az SQuirreL. A hiba már nem kell végrehajtani, a HDInsight Hive történő csatlakozás során.

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik megtudta, hogyan JDBC használhatja a Hive, az alábbi hivatkozások segítségével más módjai Azure HDInsight használata.

* [Hive-adatok ábrázolása a Microsoft Power bi-ban az Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása ](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel és a Hadoop csatlakoztatása a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight csatlakozzon és Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával futtassa](apache-hadoop-visual-studio-tools-get-started.md).
* [A HDInsight eszközzel Azure a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md)
* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
