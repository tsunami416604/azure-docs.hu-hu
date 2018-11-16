---
title: Java felhasználói függvény (UDF) az Apache Hive a HDInsight – Azure
description: Ismerje meg, hogyan hozhat létre egy Java-alapú felhasználói függvény (UDF), amely az Apache Hive együttműködve. Ebben a példában UDF alakít egy szöveges karakterláncot kisbetűssé tábláját.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 562320b49f92d9ebb0c74c06e27fab63681955be
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634073"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Használja a Java UDF-ben az Apache Hive a HDInsight

Ismerje meg, hogyan hozhat létre egy Java-alapú felhasználói függvény (UDF), amely az Apache Hive együttműködve. Ebben a példában a Java UDF alakítja minden-kisbetűs karakterek szöveges karakterláncot tartalmazó táblát.

## <a name="requirements"></a>Követelmények

* Egy HDInsight-fürt 

    > [!IMPORTANT]
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    Ez a dokumentum a lépések többségét mindkét Windows - és Linux-alapú fürtökön működik. Töltse fel a lefordított UDF-ben a fürthöz, majd futtassa használt lépéseket azonban olyan Linux-alapú fürtök jellemző. Hivatkozások, amelyek a Windows-alapú fürtök is használható információk állnak rendelkezésre.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 vagy újabb (vagy egy azzal egyenértékű, például az OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Egy szövegszerkesztő vagy a Java ide Környezethez

    > [!IMPORTANT]
    > Ha egy Windows-ügyfélen a Python-fájlokat hoz létre, egy szerkesztőt, amelynek LF használja, mint egy sor vége kell használnia. Ha nem biztos abban, hogy a szerkesztő LF Karakterrel vagy a CRLF használja, tekintse meg a [hibaelhárítás](#troubleshooting) szükséges lépéseket a CR karakter eltávolítása a következő szakaszban.

## <a name="create-an-example-java-udf"></a>Hozzon létre próbaképpen egy Java UDF-ben 

1. Egy parancssorból a következő használatával hozzon létre egy új Maven-projektet:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Ha Powershellt használ, a paraméterek idézőjelbe kell tenni. Például: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Ez a parancs létrehoz egy könyvtárat nevű **exampleudf**, amely tartalmazza a Maven-projektet.

2. Miután létrejött a projekt, törölje a **exampleudf/src/tesztelési** a projekt részeként létrehozott címtárakat.

3. Nyissa meg a **exampleudf/pom.xml**, és cserélje le a meglévő `<dependencies>` a következő XML-bejegyzés:

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

    Ezek a bejegyzések határozza meg, hogyan hozhat létre a projektet. Pontosabban a verziója, amely a projekt használja a Java, és hogyan hozhat létre egy uberjar a fürtön való üzembe helyezéshez.

    Mentse a fájlt, miután a módosítások történtek-e.

4. Nevezze át **exampleudf/src/main/java/com/microsoft/examples/App.java** való **ExampleUDF.java**, és nyissa meg a szerkesztőben.

5. Cserélje le a tartalmát a **ExampleUDF.java** az alábbi fájlt, majd mentse a fájlt.

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

1. A következő paranccsal fordításához és az UDF csomag:

    ```bash
    mvn compile package
    ```

    Ez a parancs létrehozza, és azokat az UDF-csomagok a `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` fájlt.

2. Használja a `scp` parancsot a fájl átmásolása a HDInsight-fürt.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Cserélje le `myuser` a fürt SSH-felhasználói fiókkal. Cserélje le `mycluster` a fürt nevére. Ha az SSH-fiókhoz jelszót használt, a jelszó megadására kéri. Ha tanúsítványt használt, előfordulhat, hogy szeretné használni a `-i` paraméterrel adja meg a titkos kulcs fájlját.

3. Csatlakozhat a fürthöz SSH használatával.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Az SSH-munkamenetben másolja a jar-fájlt HDInsight tároló.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Az UDF-ben, a Hive használata

1. A következő segítségével indítsa el a Beeline-ügyfél az SSH-munkamenetből.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Ez a parancs feltételezi, hogy használja-e az alapértelmezett **rendszergazdai** a fürt bejelentkezési fiókjának.

2. Ha akkor érkeznek a `jdbc:hive2://localhost:10001/>` kéri, adja meg a következőt adja hozzá az UDF-ben a Hive-, és közzéteheti függvényében.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Ez a példa feltételezi, hogy a fürt alapértelmezett tárolója az Azure Storage. Ha a fürt a Data Lake Store; ezek helyett használja, módosítsa a `wasb:///` értéket a következőre `adl:///`.

3. Az UDF segítségével kisbetűs karakterláncokat tábla lekért értékek.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Ez a lekérdezés kiválasztja azt az eszközplatformot (Android, Windows, iOS, stb.) a táblából, konvertálja a karakterláncot, alacsonyabb eset, és megjeleníti őket. A kimenetben megjelenik az alábbi szöveghez hasonló:

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

A Hive használata egyéb módjai, lásd: [Hive használata a HDInsight](hdinsight-use-hive.md).

Hive User-Defined funkciók további információkért lásd: [Hive-operátorok és a felhasználó által megadott függvények](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) szakaszt a Hive wiki az Apache.org webhelyen.
