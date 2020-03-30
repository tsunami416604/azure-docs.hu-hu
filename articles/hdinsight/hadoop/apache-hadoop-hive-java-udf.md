---
title: Java-felhasználó által definiált függvény (UDF) az Apache Hive Azure HDInsight-mal
description: Ismerje meg, hogyan hozhat létre egy Java-alapú, felhasználó által definiált függvényt (UDF), amely együttműködik az Apache Hive-vel. Ez a példa az UDF-fájl a szöveges karakterláncok táblázatát kisbetűssé alakítja.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327211"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Java UDF használata Apache Hive-val a HDInsightban

Ismerje meg, hogyan hozhat létre egy Java-alapú, felhasználó által definiált függvényt (UDF), amely együttműködik az Apache Hive-vel. Ebben a példában a Java UDF a szöveges karakterláncokat tartalmazó táblázatot kisbetűs karakterekké alakítja.

## <a name="prerequisites"></a>Előfeltételek

* Egy Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)
* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.
* A fürtök [elsődleges tárolóURI-séma.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez wasb:// lenne az Azure Storage, abfs:// az Azure Data Lake Storage Gen2 vagy adl:// az Azure Data Lake Storage Gen1 számára. Ha a biztonságos átvitel engedélyezve van `wasbs://`az Azure Storage számára, az URI lesz.  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

* Szövegszerkesztő vagy Java IDE

    > [!IMPORTANT]  
    > Ha a Python-fájlokat Windows-ügyfélen hozza létre, olyan szerkesztőt kell használnia, amely az LF-et használja sorvégződésként. Ha nem biztos abban, hogy a szerkesztő lf-et vagy CRLF-et használ- e, olvassa el a Hibaelhárítás című [szakaszt](#troubleshooting) a CR-karakter eltávolításának lépéseit.

## <a name="test-environment"></a>Vizsgálati környezet

A cikkhez használt környezet egy Windows 10-et futtató számítógép volt.  A parancsok végrehajtása a parancssorban történt, és a különböző fájlokat a Jegyzettömbbel szerkesztették. Módosítsa ennek megfelelően a környezethez.

A parancssorból adja meg az alábbi parancsokat a munkakörnyezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Példa létrehozása Java UDF-re

1. Hozzon létre egy új Maven projektet a következő parancs megadásával:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Ez a parancs `exampleudf`létrehoz egy könyvtár nevű , amely tartalmazza a Maven projekt.

2. A projekt létrehozása után törölje `exampleudf/src/test` a projekt részeként létrehozott könyvtárat a következő parancs megadásával:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Nyissa `pom.xml` meg az alábbi parancs megadásával:

    ```cmd
    notepad pom.xml
    ```

    Ezután cserélje `<dependencies>` le a meglévő bejegyzést a következő XML-re:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Ezek a bejegyzések határozzák meg a HDInsight 3.6-ban található Hadoop és Hive verzióját. A [HDInsight-összetevő verziószámozási](../hdinsight-component-versioning.md) dokumentumában a HAdoop és a Hive HDInsight-hoz mellékelt verzióival kapcsolatos információkat talál.

    Adjon `<build>` hozzá egy `</project>` szakaszt a fájl végén lévő sor hoz. Ennek a szakasznak a következő XML-t kell tartalmaznia:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Ezek a bejegyzések határozzák meg a projekt felépítésének módját. Pontosabban a Java, amely a projekt által használt, és hogyan hozhat létre egy uberjar a fürtközponti telepítéshez.

    Mentse a fájlt a módosítások végrehajtása után.

4. Adja meg az alábbi parancsot `ExampleUDF.java`egy új fájl létrehozásához és megnyitásához:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Ezután másolja be az alábbi java kódot az új fájlba. Ezután zárja be a fájlt.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Ez a kód egy karakterlánc-értéket elfogadó UDF-et valósít meg, és a karakterlánc kisbetűs verzióját adja vissza.

## <a name="build-and-install-the-udf"></a>Az UDF létrehozása és telepítése

Az alábbi parancsokban `sshuser` cserélje le a tényleges felhasználónevet, ha más. Cserélje `mycluster` le a tényleges fürtnévre.

1. Az UDF fordítása és csomagolása a következő parancs megadásával:

    ```cmd
    mvn compile package
    ```

    Ez a parancs az UDF-et `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` a fájlba építi és csomagolja.

2. A `scp` paranccsal másolja a fájlt a HDInsight-fürtbe a következő parancs megadásával:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Csatlakozzon a fürthöz SSH használatával a következő parancs megadásával:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. A megnyitott SSH-munkamenetből másolja a jar fájlt a HDInsight-tárolóba.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Az UDF használata a Hive-ból

1. Indítsa el a Beeline ügyfelet az SSH munkamenetből a következő parancs megadásával:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Ez a parancs feltételezi, hogy a fürt bejelentkezési fiókjához a **rendszergazda** alapértelmezett beállítását használta.

2. Miután megérkezett `jdbc:hive2://localhost:10001/>` a kérdés, adja meg a következő tadd hozzá az UDF-t a Hive, és tegye elérhetővé függvényként.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Az UDF segítségével a táblából beolvasott értékeket kisbetűs karakterláncokká alakíthatja át.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Ez a lekérdezés kiválasztja az állapotot a táblából, a karakterláncot kisbetűssé alakítja, majd megjeleníti őket a módosítatlan névvel együtt. A kimenet a következő szöveghez hasonló:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Hibaelhárítás

A struktúrafeladat futtatásakor a következő höz hasonló hibára kerülhet szó:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ezt a problémát a Python-fájl sorvégződései okozhatják. Sok Windows-szerkesztő alapértelmezés szerint a CRLF-et használja sorvégeként, de a Linux alkalmazások általában LF-et várnak.

A következő PowerShell-utasításokkal távolíthatja el a CR-karaktereket, mielőtt feltöltené a fájlt a HDInsightba:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>További lépések

A Hive használatának egyéb módjai az [Apache Hive használata a HDInsight segítségével](hdinsight-use-hive.md).

A Hive-felhasználó által definiált függvényekkel kapcsolatos további információkért lásd: A Hive wiki [Apache Hive operátorok és Felhasználó által definiált függvények](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) című szakaszában apache.org.
