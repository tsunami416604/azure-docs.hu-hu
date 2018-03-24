---
title: Tárolási/Data Lake Store - Azure HDInsight alatt futó Apache Storm írási |} Microsoft Docs
description: Ismerje meg, hogyan írni a HDFS-kompatibilis tárolót a HDInsight az Apache Storm használható. Az Azure Storage vagy az Azure Data Lake Store adja meg a HDFS-comptabile storage HDInsight. Ez a dokumentum, és a kapcsolódó példában bemutatják, hogyan HdfsBolt összetevő írni az alapértelmezett tárolása egy Storm on HDInsight-fürt használható.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 2310894e7257d0ddb919406a8f297089189a9484
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Írni HDFS az Apache Storm példatopológiái

Megtudhatja, hogyan használja a Storm a HDFS-kompatibilis tárolót a HDInsight alatt futó Apache Storm által használt adatokat írni. HDInsight is használhat HDFS-kompatibilis tárolóként tárolásához Azure Storage és az Azure Data Lake. Storm lehetővé egy [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) írja az adatokat HDFS-összetevő. Ez a dokumentum tájékoztatást nyújt a a HdfsBolt vagy a tároló típusa szerinti írásakor. 

> [!IMPORTANT]
> Ez a dokumentum használt példa topológiát HDInsight alatt futó Storm részét képező összetevők támaszkodik. Szükség lehet az Azure Data Lake Store más alatt futó Apache Storm-fürtök használata esetén működéséhez módosítása.

## <a name="get-the-code"></a>A kód letöltése

Ez a topológia tartalmazó projekt letölthető érhető el [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Ez a projekt fordítása, a következő konfigurációs a fejlesztési környezet szüksége:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verzióját. HDInsight 3.5-ös vagy újabb rendszer szükséges Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Az alábbi környezeti változókat a fejlesztő munkaállomás Java és a JDK telepítésekor lehet beállítani. Azonban ellenőrizni kell, hogy léteznek, illetve a rendszer a megfelelő értékeket tartalmaz.

* `JAVA_HOME` -a a JDK mappáját kell mutatnia.
* `PATH` -a következő elérési utakat kell tartalmaznia:
  
    * `JAVA_HOME` (vagy ezzel egyenértékű elérési).
    * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési).
    * A mappát, ahová a Maven telepítve van.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>A HdfsBolt használata a hdinsight eszközzel

> [!IMPORTANT]
> A HDInsight alatt futó Storm a HdfsBolt használatához meg kell először egy parancsfájlművelettel szükséges jar fájlok másolása a `extpath` alatt futó Storm. További információkért lásd: a [konfigurálása a fürt](#configure) szakasz.

A HdfsBolt használja az Ön által biztosított annak megértése, hogyan lehet írni a HDFS fájl séma. A hdinsight eszközzel használja az alábbi rendszerek egyikét:

* `wasb://`: Egy Azure Storage-fiókot használni.
* `adl://`: Az Azure Data Lake Store használt.

Az alábbi táblázat példákat mutat be a fájl séma használatával különböző helyzetek kezelésére:

| séma | Megjegyzések |
| ----- | ----- |
| `wasb:///` | Az alapértelmezett tárfiók egy blob tároló, az Azure Storage-fiók |
| `adl:///` | Az alapértelmezett tárfiók út egy könyvtár az Azure Data Lake Store. Fürt létrehozása során, amely a gyökérkönyvtár, ahol a fürt HDFS Data Lake Store ad meg a könyvtárban. Például a `/clusters/myclustername/` könyvtár. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Egy nem alapértelmezett (további) Azure storage-fiók a fürthöz rendelt. |
| `adl://STORENAME/` | A Data Lake Store a fürt által használt gyökere. Ez a séma lehetővé teszi, hogy a fürt fájlrendszer tartalmazó könyvtár kívül található adatok eléréséhez. |

További információkért lásd: a [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) hivatkozás az Apache.org webhelyen.

### <a name="example-configuration"></a>Példa konfiguráció

A következő YAM fájlból a `resources/writetohdfs.yaml` a példában szereplő fájlt. Ez a fájl határozza meg, a Storm topology használatával a [fluxus](https://storm.apache.org/releases/1.1.0/flux.html) keretrendszere, amely alatt futó Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

A YAM meghatározza, hogy a következő elemek:

* `syncPolicy`: Határozza meg, mikor fájlok legyenek szinkronizálva/kiürített a fájlrendszerben. A példában minden 1000 rekordokat.
* `fileNameFormat`: Az elérési út és fájlnév mintát fájlok írásához használt határozza meg. Ebben a példában a szűrő használata futásidőben a az elérési út van megadva, és a fájl kiterjesztése `.txt`.
* `recordFormat`: A fájlok írása a belső formátum meghatározása. Ebben a példában szereplő mezők határolja a `|` karakter.
* `rotationPolicy`: Meghatározza, hogy mikor fájlok megváltoztatása. Ebben a példában elforgatás nélkül történik.
* `hdfs-bolt`: Az előző összetevők működését visszafelé használ konfigurációs paraméterei a `HdfsBolt` osztály.

A fluxus keretrendszerre további információkért lásd: [ https://storm.apache.org/releases/1.1.0/flux.html ](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>A fürt konfigurálása

Alapértelmezés szerint a HDInsight alatt futó Storm nem tartalmaz az összetevőket, amelyek HdfsBolt használ a Storm tartozó classpath Azure Storage vagy a Data Lake Store folytatott kommunikációhoz. A következő parancsfájlművelet ezek az összetevők hozzáadásához használja a `extlib` könyvtár alatt futó Storm a fürt:

* A parancsfájl URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Csomópontok alkalmazandó: Nimbus, a felügyelő
* Paraméterek: nincs

Ezt a parancsfájlt a fürt használatáról információkért lásd: a [Parancsfájlműveletek segítségével testre szabhatja HDInsight-fürtök](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentum.

## <a name="build-and-package-the-topology"></a>Hozza létre, és a topológia csomag

1. Töltse le a példában projektet a [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) a fejlesztői környezetében.

2. Egy parancssort, terminál, vagy héjas munkamenetből, módosítás könyvtárak a letöltött projektből gyökeréhez. Építsenek, és a topológia csomag, a következő paranccsal:
   
        mvn compile package
   
    Miután befejeződött a build és csomagolása, nincs-e egy új nevű könyvtár `target`, nevű fájlt tartalmaz, amelyek `StormToHdfs-1.0-SNAPSHOT.jar`. Ez a fájl tartalmazza a lefordított topológia.

## <a name="deploy-and-run-the-topology"></a>Regisztrálhat és futtathat a topológia

1. A következő paranccsal másolja át a topológia a HDInsight-fürthöz. Cserélje le **felhasználói** az a fürt létrehozásakor használt SSH-felhasználónév. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Amikor a rendszer kéri, adja meg az SSH-felhasználó a fürt létrehozásakor használt jelszót. Jelszó helyett használt nyilvános kulcsot, ha szeretne használni a `-i` paraméterrel adhatja meg a megfelelő titkos kulcs elérési útját.
   
   > [!NOTE]
   > További információk az `scp` a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután befejeződött a feltöltés, használja a következő való kapcsolódáshoz a HDInsight-fürthöz SSH használatával. Cserélje le **felhasználói** az a fürt létrehozásakor használt SSH-felhasználónév. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Amikor a rendszer kéri, adja meg az SSH-felhasználó a fürt létrehozásakor használt jelszót. Jelszó helyett használt nyilvános kulcsot, ha szeretne használni a `-i` paraméterrel adhatja meg a megfelelő titkos kulcs elérési útját.
   
   További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. A csatlakozás után az alábbi parancs segítségével hozzon létre egy fájlt `dev.properties`:

        nano dev.properties

4. Használja a következő szöveget a tartalmát a `dev.properties` fájlt:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Ez a példa feltételezi, hogy a fürt egy Azure Storage-fiókot használja, mint az alapértelmezett tároló. Ha a fürt használ az Azure Data Lake Store, `hdfs.url: adl:///` helyette.
    
    Mentse a fájlt, használja a __Ctrl + X__, majd __Y__, és végül __Enter__. Az értékeket a fájlban található a Data Lake store URL-cím és a könyvtár nevét, amely adatot ír be.

3. Az alábbi parancs segítségével indítsa el a topológia:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    A topológia a Nimbus csomópont a fürt való továbbítás a fluxus keretrendszer használatával indítja el. Határozza meg a topológia a `writetohdfs.yaml` a jar fájl. A `dev.properties` fájl szűrőként átadása, és az értékeket a fájlban található olvassák a topológiát.

## <a name="view-output-data"></a>Kimeneti adatok megtekintése

Az adatok megtekintéséhez használja a következő parancsot:

    hdfs dfs -ls /stormdata/

Ez a topológia által létrehozott fájlok listája jelenik meg.

A következő lista az adatok alapján az előző parancs retuned példája:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>A topológia leállítása

Storm-topológiák addig futnak, csak a, vagy a fürt törlődik. A topológia leállítása, használja a következő parancsot:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan használja a Storm Azure Storage és az Azure Data Lake Store, felderítése más [példák a HDInsight Storm](apache-storm-example-topology.md).

