---
title: "Java felhasználói függvény (UDF) a Hive HDInsight - Azure |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Java-alapú felhasználói függvény (UDF), amely kompatibilis a struktúra. Ebben a példában UDF alakít egy szöveges karakterláncot kisbetűssé táblájában."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: cc6b63f40dd4323ecdf0dfb75c7c8036cbc7b0b7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Egy Java használni a Hive HDInsight UDF

Megtudhatja, hogyan hozzon létre egy Java-alapú felhasználói függvény (UDF), amely kompatibilis a struktúra. Ebben a példában a Java UDF alakítja át a táblázatokat szöveges karakterláncok minden-kisbetűs karaktereket.

## <a name="requirements"></a>Követelmények

* HDInsight-fürtök 

    > [!IMPORTANT]
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    A legtöbb ebben a dokumentumban a lépések mindkét Windows és Linux-alapú fürtökön. A lefordított UDF feltöltése a fürthöz, majd futtassa a lépéseire azonban jellemzőek Linux-alapú fürtökön. Információk a Windows-alapú fürtökön használható hivatkozásokkal.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 vagy újabb (vagy egy azzal egyenértékű, például OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Szöveg- vagy Java IDE

    > [!IMPORTANT]
    > Ha egy Windows-ügyfélen a Python-fájlokat hoz létre, egy sor befejezési LF használó szerkesztővé kell használnia. Ha nem biztos abban, hogy a szerkesztő LF vagy CRLF használja-e, tekintse meg a [hibaelhárítás](#troubleshooting) szakasz lépései a CR karakter eltávolítása.

## <a name="create-an-example-java-udf"></a>Hozzon létre egy Java UDF példa 

1. A parancssorból használja a következő új Maven-projekt létrehozása:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Ha PowerShell használ, a paraméterek idézőjelbe kell helyezni. Például: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Ezzel a paranccsal létrejön egy nevű könyvtár **exampleudf**, amely tartalmazza a Maven project.

2. A projekt létrehozása után törölje a **exampleudf/src/test** a projekt részeként létrehozott címtárakat.

3. Nyissa meg a **exampleudf/pom.xml**, és cserélje le a meglévő `<dependencies>` bejegyzés van a következő XML:

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

    Ezek a bejegyzések adja meg a Hadoop és a Hive HDInsight 3.5 részét képező verzióját. A Hadoop és a Hive HDInsight megadott verzióin információt a [HDInsight-összetevők verziószámozása](../hdinsight-component-versioning.md) dokumentum.

    Adja hozzá a `<build>` előtt szakasz a `</project>` sort a fájl végén. Ez a szakasz a következő XML kell tartalmaznia:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
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
                <version>2.3</version>
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

    Ezek a bejegyzések meghatározhatja, hogyan kell a projekt felépítéséhez. Pontosabban a verzióját a projekt használó Java és hogyan hozhat létre egy uberjar a fürt üzembe helyezése.

    Mentse a fájlt, miután a módosítások lettek bevezetve.

4. Nevezze át **exampleudf/src/main/java/com/microsoft/examples/App.java** való **ExampleUDF.java**, majd nyissa meg a fájlt a szerkesztőben.

5. Cserélje le a tartalmát a **ExampleUDF.java** a következő fájlt, majd mentse a fájlt.

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

    Ez a kód egy UDF fogad el egy olyan karakterláncértéket, és a karakterlánc kis verziójának visszaadó valósítja meg.

## <a name="build-and-install-the-udf"></a>Hozza létre és telepítse az UDF-ben

1. A következő paranccsal fordításához és az UDF csomag:

    ```bash
    mvn compile package
    ```

    Ez a parancs létrehozza, és az UDF-csomagok a `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` fájlt.

2. Használja a `scp` parancs a fájl átmásolása a HDInsight-fürthöz.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Cserélje le `myuser` a fürthöz SSH felhasználói fiókkal. Cserélje le `mycluster` a fürt nevéhez. Ha a jelszót biztonságos SSH-fiókjának biztonságát, a jelszó megadására kéri. Ha a tanúsítványt használja, előfordulhat, hogy szüksége a `-i` paraméterrel adhatja meg a titkos kulcs fájlja.

3. Csatlakozzon a fürthöz SSH használatával.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Az SSH-munkamenetet másolja a jar-fájlra HDInsight-tárolóba.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Az UDF-ben a Hive használata

1. A következő segítségével indítsa el a Beeline ügyfél az SSH-munkamenetet.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    A parancs feltételezi, hogy használja-e a rendszer az alapértelmezett **admin** a bejelentkezési fiók a fürt számára.

2. Miután a kiszolgálófarmban lévő a `jdbc:hive2://localhost:10001/>` kéri, írja be a következő Hive UDF és közzétenni függvényében.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Ez a példa feltételezi, hogy a fürt tárolóhelyét alapértelmezett Azure Storage. Ha a fürt helyette használja a Data Lake Store, módosítsa a `wasb:///` egy érték `adl:///`.

3. Az UDF segítségével olvassa be az táblából kisbetű karakterláncok értékeket átalakítani.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Ez a lekérdezés kiválasztja az eszköz platformjától (Android, Windows, iOS, stb.) a táblából, alakítsa át a alacsonyabb eset, és majd megjeleníti a karakterlánc. A kimenet az alábbihoz hasonló jelenik meg:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>További lépések

Más módokon történő együttműködésre a Hive, lásd: [használata a HDInsight Hive](hdinsight-use-hive.md).

Hive User-Defined funkciók további információkért lásd: [Hive operátor és a felhasználó által definiált függvényeket](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) szakaszt a Hive wiki az Apache.org webhelyen.
