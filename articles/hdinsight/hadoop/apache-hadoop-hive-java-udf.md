---
title: Java felhasználói függvény (UDF) az Apache Hive a HDInsight – Azure
description: Ismerje meg, hogyan hozhat létre egy Java-alapú felhasználói függvény (UDF), amely az Apache Hive együttműködve. Ebben a példában UDF alakít egy szöveges karakterláncot kisbetűssé tábláját.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: b8417fe4c15259a7fd485254cf9edd2c8c082e92
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629697"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Használja a Java UDF-ben az Apache Hive a HDInsight

Ismerje meg, hogyan hozhat létre egy Java-alapú felhasználói függvény (UDF), amely az Apache Hive együttműködve. Ebben a példában a Java UDF alakítja minden-kisbetűs karakterek szöveges karakterláncot tartalmazó táblát.

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight Hadoop-fürt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).
* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks)
* [Az Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepített](https://maven.apache.org/install.html) Apache megfelelően.  Maven egy projektet a Java-projektek rendszert hozhat létre.
* A [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) a fürtök elsődleges tárhelyeként. Ez akkor lehet wasb: / / az Azure Storage esetében abfs: / / az Azure Data Lake Storage Gen2 vagy adl: / / az Azure Data Lake Storage Gen1. Ha az Azure Storage vagy a Data Lake Storage Gen2 engedélyezve van a biztonságos átvitel, az URI a wasbs lesz: / / vagy abfss: / /, illetve lásd még a [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md).

* Egy szövegszerkesztő vagy a Java ide Környezethez

    > [!IMPORTANT]  
    > Ha egy Windows-ügyfélen a Python-fájlokat hoz létre, egy szerkesztőt, amelynek LF használja, mint egy sor vége kell használnia. Ha nem biztos abban, hogy a szerkesztő LF Karakterrel vagy a CRLF használja, tekintse meg a [hibaelhárítás](#troubleshooting) szükséges lépéseket a CR karakter eltávolítása a következő szakaszban.

## <a name="test-environment"></a>Tesztkörnyezet
Az ebben a cikkben használt környezet a Windows 10 rendszerű számítógép volt.  A parancsok végrehajtódtak parancsot, és a különböző fájlok is szerkeszthetők a Jegyzettömb alkalmazásban. Ennek megfelelően módosítsa a környezetnek.

Egy parancssorból adja meg a munkakörnyezet létrehozásához az alábbi parancsokat:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Hozzon létre próbaképpen egy Java UDF-ben

1. Hozzon létre egy új Maven-projektet a következő parancs beírásával:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Ez a parancs létrehoz egy könyvtárat nevű `exampleudf`, amely tartalmazza a Maven-projektet.

2. Miután létrejött a projekt, törölje a `exampleudf/src/test` létrehozott címtárakat a projekt részeként a következő parancs beírásával:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Nyissa meg `pom.xml` az alábbi parancs beírásával:

    ```cmd
    notepad pom.xml
    ```

    Ezután cserélje le a meglévő `<dependencies>` a következő XML-bejegyzés:

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

    Ezek a bejegyzések adja meg a Hadoop és a Hive HDInsight 3.6-os részét képező verzióját. Hadoop és a Hive a HDInsight megadott verzióiban információkat a [HDInsight összetevők verziószámozása](../hdinsight-component-versioning.md) dokumentumot.

    Adjon hozzá egy `<build>` előtt szakasz a `</project>` sort a fájl végén. Ez a szakasz a következő XML-kódot kell tartalmaznia:

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

    Ezek a bejegyzések határozza meg, hogyan hozhat létre a projektet. Pontosabban a verziója, amely a projekt használja a Java, és hogyan hozhat létre egy uberjar a fürtön való üzembe helyezéshez.

    Mentse a fájlt, miután a módosítások történtek-e.

4. Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Ezután másolja és illessze be az alábbi java-kódot az új fájlt. Zárja be a fájlt.

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

    Ez a kód egy UDF, amely egy karakterlánc értéket fogad el, és visszaadja a karakterláncot kisbetűssé verzióját valósítja meg.

## <a name="build-and-install-the-udf"></a>Hozhat létre és telepítse az UDF-ben

Cserélje le az alábbi parancsokat, `sshuser` Ha különböző tényleges felhasználónévvel. Cserélje le `mycluster` tényleges fürt nevét.

1. Fordítsa le, és az UDF csomag a következő parancs beírásával:

    ```cmd
    mvn compile package
    ```

    Ez a parancs létrehozza, és azokat az UDF-csomagok a `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` fájlt.

2. Használja a `scp` parancsot a fájl átmásolása a HDInsight-fürt a következő parancs beírásával:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Csatlakozzon a fürthöz SSH használatával a következő parancs beírásával:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. A megnyitott SSH-munkamenetből másolja a jar-fájlt HDInsight tároló.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Az UDF-ben, a Hive használata

1. Indítsa el a Beeline-ügyfél az SSH-munkamenetből a következő parancs beírásával:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Ez a parancs feltételezi, hogy használja-e az alapértelmezett **rendszergazdai** a fürt bejelentkezési fiókjának.

2. Ha akkor érkeznek a `jdbc:hive2://localhost:10001/>` kéri, adja meg a következőt adja hozzá az UDF-ben a Hive-, és közzéteheti függvényében.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Az UDF segítségével kisbetűs karakterláncokat tábla lekért értékek.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Ez a lekérdezés kiválasztja az állapot a táblából, konvertálja a karakterláncot, és a kisebb használatieset-majd megjeleníti őket a módosítás nélküli neve mellett. A kimenetben megjelenik az alábbi szöveghez hasonló:

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

Amikor futtatja a hive-feladatokban, találkozhat az alábbi szöveghez hasonló hibát:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ez a probléma oka a sorvégződések a Python-fájlt a. A CRLF karakterrel, a sor vége, de a Linux-alkalmazások általában számos Windows szerkesztők alapértelmezett várhatóan LF Karakterrel.

A következő PowerShell-utasítások segítségével a CR karakter eltávolítása előtt a fájl feltöltése a HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>További lépések

A Hive használata egyéb módjai, lásd: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).

Hive User-Defined funkciók további információkért lásd: [Apache Hive-operátorok és a felhasználó által megadott függvények](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) szakaszt a Hive wiki az Apache.org webhelyen.
