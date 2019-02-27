---
title: Storage és Data Lake Storage – Azure HDInsight az Apache Storm írása
description: Ismerje meg, hogyan használható az Apache Storm HDInsight a HDFS-kompatibilis tárolóba való írása.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 34056b3a562d3110569e05c5c50ae4d9dae2375a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868136"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>A HDInsight Apache Storm írhat az Apache Hadoop HDFS-be

Ismerje meg, hogyan használható [Apache Storm](https://storm.apache.org/) adatokat írni az Apache Storm on HDInsight által használt HDFS-kompatibilis tárolóba. HDInsight az Azure Storage és az Azure Data Lake Storage is használhatja HDFS-kompatibilis tárolóba. A Storm biztosít egy [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) összetevő található, írja az adatokat a HDFS. Ez a dokumentum információt nyújt a HdfsBolt a írása vagy a tároló típusa. 

> [!IMPORTANT]  
> Ebben a dokumentumban használt példatopológiát a HDInsight alatt futó Stormmal összetevők támaszkodik. Megkövetelheti, hogy a módosítás az Azure Data Lake Storage való más Apache Storm-fürtök együttes használata.

## <a name="get-the-code"></a>A kód letöltése

Letölthető érhető el a projektet, amely tartalmazza az ebben a topológiában [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Ez a projekt fordításához, szükség van a következő konfigurációt a fejlesztési környezetet:

* [Java JDK 1.8](https://aka.ms/azure-jdks) vagy újabb verzió. A HDInsight 3.5 vagy magasabb verziójához Java 8 szükséges.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Az alábbi környezeti változók állíthatók be a Java és a JDK fejlesztői munkaállomáson történő telepítésekor. Érdemes azonban ellenőriznie, hogy a fentiek léteznek-e, és a rendszer számára megfelelő értékeket tartalmaznak-e.

* `JAVA_HOME` – arra a könyvtárra mutasson, ahová a JDK telepítve lett.
* `PATH` – a következő elérési utakat kell tartalmaznia:
  
    * `JAVA_HOME` (vagy ezzel egyenértékű elérési út).
    * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési út).
    * A Maven telepítési könyvtára.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>A HdfsBolt használata a HDInsight

> [!IMPORTANT]  
> A HdfsBolt használata a HDInsight alatt futó Stormmal, előtt meg kell először használjon szkriptműveletet szükséges jar fájlok másolása a `extpath` a Storm. További információkért tekintse meg a fürt szakasz konfigurálása.

A HdfsBolt megtudhatja, hogyan írni a HDFS biztosító fájl sémát használó. A HDInsight használja az alábbi rendszerek egyikét:

* `wasb://`: Az Azure Storage-fiókot használni.
* `abfs://`: Az Azure Data Lake Storage Gen2 együtt használni.
* `adl://`: Az Azure Data Lake Storage Gen1 használni.

Az alábbi táblázatban a különböző helyzetekhez a fájl rendszer használatának példái:

| Séma | Megjegyzések |
| ----- | ----- |
| `wasb:///` | Az alapértelmezett tárfiókot az Azure Storage-fiókban található blob-tárolóra |
| `abfs:///` | Az alapértelmezett tárfiók egy Azure Data Lake Storage Gen2-fiókban lévő könyvtár |
| `adl:///` | Az alapértelmezett tárfiókot az Azure Data Lake Storage Gen1 könyvtár. Fürt létrehozása során megadhatja a címtár Data Lake Storage, amely a fürt HDFS gyökere. Ha például a `/clusters/myclustername/` könyvtár. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Egy nem alapértelmezett (további) az Azure storage-fiók a fürthöz társított. |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | Egy nem alapértelmezett (további) az Azure storage-fiók a fürthöz társított. |
| `adl://STORENAME/` | A fürt által használt Data Lake Storage gyökérmappájában. Ez a séma lehetővé teszi, hogy a könyvtár, amely tartalmazza a fürt fájlrendszer kívül található adatokhoz való hozzáférést. |

További információkért lásd: a [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) referencia az Apache.org webhelyen.

### <a name="example-configuration"></a>Konfigurációs példa

A következő yaml-kódot egy olyan, a rendszer a `resources/writetohdfs.yaml` fájlban szerepel a példában. Ez a fájl határozza meg, a Storm topology használatával a [fluxus](https://storm.apache.org/releases/1.1.2/flux.html) Apache Storm-keretrendszer.

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

Ez a YAML határozza meg a következő elemek:

* `syncPolicy`: Határozza meg, amikor fájlok szinkronizálva/kiürített a fájlrendszerben. Ebben a példában minden 1000 rekordokat.
* `fileNameFormat`: A fájlok írásához használt elérési út és fájlnév névminta határozza meg. Ebben a példában az elérési út a szűrők használatával futásidőben, és a fájl kiterjesztése `.txt`.
* `recordFormat`: A belső írt fájlok formátumát határozza meg. Ebben a példában mezők be vannak elválasztva az `|` karakter.
* `rotationPolicy`: Határozza meg, mikor elforgatása a fájlokat. Ebben a példában elforgatás nélkül történik.
* `hdfs-bolt`: Az előző összetevők működését visszafelé használja, mint a konfigurációs paramétereket az `HdfsBolt` osztály.

A fluxus keretrendszer további információkért lásd: [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>A fürt konfigurálása

Alapértelmezés szerint a HDInsight alatt futó Stormmal nem tartalmazza az összetevőket, amelyek HdfsBolt használ a Storm osztályútvonal az Azure Storage vagy a Data Lake Storage folytatott kommunikációhoz. A következő parancsfájlművelet használatával ezek az összetevők hozzáadása a `extlib` a fürtön futó Storm címtárat:

* Szkript URI-ja: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* A csomópontok a alkalmazni: Nimbus, a felügyelő
* Paraméterek: None

Ez a szkript használatával a fürt további információkért lásd: a [testreszabása HDInsight-fürtök parancsfájlműveletekkel](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentumot.

## <a name="build-and-package-the-topology"></a>Felépítése és becsomagolása a topológia

1. Töltse le a példában projektet a [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) a fejlesztői környezetbe.

2. Egy parancssort, terminál, vagy héjastól munkamenetben módosítása címtárak, a letöltött projekt gyökérkönyvtárában. Hozhat létre, és a topológia csomagot, használja a következő parancsot:
   
        mvn compile package
   
    A build és a csomagolási befejezése után nincs-e egy új könyvtárat nevű `target`, nevű fájlt tartalmazza, amelyek `StormToHdfs-1.0-SNAPSHOT.jar`. Ez a fájl tartalmazza a lefordított topológiát.

## <a name="deploy-and-run-the-topology"></a>Üzembe helyezése és futtatása a topológia

1. A következő parancs használatával másolja ki a topológiát a HDInsight-fürthöz. Cserélje le **felhasználói** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Amikor a rendszer kéri, adja meg a jelszót az SSH-felhasználó a fürt létrehozásakor használt. Jelszó helyett a nyilvános kulcs használatakor lehetséges, hogy szeretné használni a `-i` paraméterrel adja meg a megfelelő titkos kulcs elérési útját.
   
   > [!NOTE]  
   > Az `scp` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. A feltöltés befejezését követően a következő használatával a HDInsight-fürthöz SSH használatával csatlakozhat. Cserélje le **felhasználói** a fürt létrehozásakor használt SSH-felhasználónévvel. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Amikor a rendszer kéri, adja meg a jelszót az SSH-felhasználó a fürt létrehozásakor használt. Jelszó helyett a nyilvános kulcs használatakor lehetséges, hogy szeretné használni a `-i` paraméterrel adja meg a megfelelő titkos kulcs elérési útját.
   
   További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. A csatlakozás után a következő paranccsal hozzon létre egy fájlt `dev.properties`:

        nano dev.properties

4. Használja a következő szöveget a tartalmát, a `dev.properties` fájlt:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > Ez a példa feltételezi, hogy a fürt egy Azure Storage-fiókot használja az alapértelmezett tárolóként. Ha a fürt az Azure Data Lake Storage Gen2 használ, használja a `hdfs.url: abfs:///` helyette. Ha a fürt az Azure Data Lake Storage Gen1 használ, használja a `hdfs.url: adl:///` helyette.
    
    Mentse a fájlt, használja a __Ctrl + X__, majd __Y__, és végül __Enter__. Ez a fájl értékeinek beállítása a Data Lake Storage URL-címe és a könyvtár nevét, amely az adatok írása.

3. A következő paranccsal indítsa el a topológia:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Ez a parancs elindítja a topológia a Nimbus csomópontot a fürthöz elküldésével a fluxus keretrendszer használatával. Határozza meg a topológia a `writetohdfs.yaml` fájl fájlt tartalmazza. A `dev.properties` fájl szűrőként van átadva, és a fájlban szereplő értékek a topológia szerint olvasható.

## <a name="view-output-data"></a>Kimeneti adatok megtekintése

Az adatok megtekintéséhez használja a következő parancsot:

    hdfs dfs -ls /stormdata/

Ez a topológia által létrehozott fájlok listája jelenik meg.

Az alábbi lista a következő egy példa az előző parancs által visszaadott adatok:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>A topológia leállítása

Storm-topológiák futtassa addig, amíg leállt, vagy a fürt törlődik. A topológia leállításához használja a következő parancsot:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan írhat az Azure Storage és az Azure Data Lake Storage Apache Storm használatával, Fedezze fel, más [a HDInsight Apache Storm-példák](apache-storm-example-topology.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
