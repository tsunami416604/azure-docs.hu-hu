---
title: Oktatóanyag – írni a Storage és Data Lake Storage – Azure HDInsight használata az Apache Storm
description: Oktatóanyag – az Apache Storm használata Azure HDInsight a HDFS-kompatibilis tárolóba való írása.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428343"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Oktatóanyag: Az Azure HDInsight az Apache Storm az Apache Hadoop HDFS-be írási

Ez az oktatóanyag bemutatja, hogyan Apache Storm használatával adatokat írni az Apache Storm on HDInsight által használt HDFS-kompatibilis tárolóba. HDInsight az Azure Storage és az Azure Data Lake Storage is használhatja HDFS-kompatibilis tárolóba. A Storm biztosít egy [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) összetevő található, írja az adatokat a HDFS. Ez a dokumentum információt nyújt a HdfsBolt a írása vagy a tároló típusa.

Ebben a dokumentumban használt példatopológiát a HDInsight alatt futó Stormmal összetevők támaszkodik. Megkövetelheti, hogy a módosítás az Azure Data Lake Storage való más Apache Storm-fürtök együttes használata.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Konfigurálja a fürt parancsfájlművelet
> * Felépítése és becsomagolása a topológia
> * Üzembe helyezése és futtatása a topológia
> * Kimeneti adatok megtekintése
> * A topológia leállítása

## <a name="prerequisites"></a>Előfeltételek

* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks)

* [Az Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepített](https://maven.apache.org/install.html) Apache megfelelően.  Maven egy projektet a Java-projektek rendszert hozhat létre.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

* A [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) a fürtök elsődleges tárhelyeként. Ez akkor lehet `wasb://` az Azure Storage esetében `abfs://` az Azure Data Lake Storage Gen2 vagy `adl://` az Azure Data Lake Storage Gen1. Ha biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2, az URI lesz `wasbs://` vagy `abfss://`, illetve lásd még a [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md).

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

Alapértelmezés szerint a HDInsight alatt futó Stormmal nem tartalmaz meg az összetevők, amelyek `HdfsBolt` használ a Storm osztályútvonal az Azure Storage vagy a Data Lake Storage folytatott kommunikációhoz. A következő parancsfájlművelet használatával ezek az összetevők hozzáadása a `extlib` a fürtön futó Storm címtárat:

| Tulajdonság | Érték |
|---|---|
|Szkripttípus |-Egyéni|
|Bash parancsfájl URI azonosítója |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Csomóponttípus(ok) |Nimbus, a felügyelő|
|Paraméterek |None|

Ez a szkript használatával a fürt további információkért lásd: a [testreszabása HDInsight-fürtök parancsfájlműveletekkel](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentumot.

## <a name="build-and-package-the-topology"></a>Felépítése és becsomagolása a topológia

1. Töltse le a példában projektet a [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) a fejlesztői környezetbe.

2. Egy parancssort, terminál, vagy héjastól munkamenetben módosítása címtárak, a letöltött projekt gyökérkönyvtárában. Hozhat létre, és a topológia csomagot, használja a következő parancsot:

    ```cmd
    mvn compile package
    ```

    A build és a csomagolási befejezése után nincs-e egy új könyvtárat nevű `target`, nevű fájlt tartalmazza, amelyek `StormToHdfs-1.0-SNAPSHOT.jar`. Ez a fájl tartalmazza a lefordított topológiát.

## <a name="deploy-and-run-the-topology"></a>Üzembe helyezése és futtatása a topológia

1. A következő parancs használatával másolja ki a topológiát a HDInsight-fürthöz. Cserélje le `CLUSTERNAME` a fürt nevére.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. A feltöltés befejezését követően a következő használatával a HDInsight-fürthöz SSH használatával csatlakozhat. Cserélje le `CLUSTERNAME` a fürt nevére.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A csatlakozás után a következő paranccsal hozzon létre egy fájlt `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Használja a következő szöveget a tartalmát, a `dev.properties` fájlt. Igény szerint felügyel alapján a [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Mentse a fájlt, használja a __Ctrl + X__, majd __Y__, és végül __Enter__. Az értékeket a fájlban a tároló URL-CÍMÉT és a könyvtár neve írt adatok megadása

1. A következő paranccsal indítsa el a topológia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Ez a parancs elindítja a topológia a Nimbus csomópontot a fürthöz elküldésével a fluxus keretrendszer használatával. Határozza meg a topológia a `writetohdfs.yaml` fájl fájlt tartalmazza. A `dev.properties` fájl szűrőként van átadva, és a fájlban szereplő értékek a topológia szerint olvasható.

## <a name="view-output-data"></a>Kimeneti adatok megtekintése

Az adatok megtekintéséhez használja a következő parancsot:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Ez a topológia által létrehozott fájlok listája jelenik meg. Az alábbi lista a következő egy példa az előző parancs által visszaadott adatok:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>A topológia leállítása

Storm-topológiák futtassa addig, amíg leállt, vagy a fürt törlődik. A topológia leállításához használja a következő parancsot:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan Apache Storm használatával adatokat írni az Apache Storm on HDInsight által használt HDFS-kompatibilis tárolóba.

> [!div class="nextstepaction"]
> Fedezze fel más [a HDInsight Apache Storm-példák](apache-storm-example-topology.md)