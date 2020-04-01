---
title: 'Oktatóanyag: HDInsight Apache Storm to Storage - Azure/Data Lake'
description: Oktatóanyag – Ismerje meg, hogyan írhat az Apache Storm segítségével az Azure HDInsight HDFS-kompatibilis tárhelyére.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241213"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Oktatóanyag: Írjon az Apache Hadoop HDFS-nek az Apache Storm szolgáltatásból az Azure HDInsight szolgáltatásban

Ez az oktatóanyag bemutatja, hogyan lehet az Apache Storm segítségével adatokat írni az Apache Storm által a HDInsighton használt HDFS-kompatibilis tárhelyre. A HDInsight az Azure Storage és az Azure Data Lake Storage is használható HDFS-kompatibilis tárolóként. A Storm egy [HdfsBolt-összetevőt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) biztosít, amely adatokat ír a HDFS-be. Ez a dokumentum a HdfsBolt-tárolóbármelyik tárológépre történő írással kapcsolatos információkat tartalmazza.

A jelen dokumentumban használt példatopológia a Storm a HDInsight-on található összetevőkre támaszkodik. Előfordulhat, hogy módosítani kell az Azure Data Lake Storage-szal való együttműködésre, ha más Apache Storm-fürtökkel használja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fürt konfigurálása parancsfájlművelettel
> * A topológia összeállítása és csomagolása
> * A topológia telepítése és futtatása
> * Kimeneti adatok megtekintése
> * A topológia leállítása

## <a name="prerequisites"></a>Előfeltételek

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* A fürtök [elsődleges tárolóURI-séma.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez az `wasb://` Azure Storage, `abfs://` az Azure Data `adl://` Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében lesz. Ha a biztonságos átvitel engedélyezve van `wasbs://`az Azure Storage számára, az URI lesz.  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Konfigurációs példa

A következő YAML egy `resources/writetohdfs.yaml` részlet a példában szereplő fájlból. Ez a fájl határozza meg a Storm topológia segítségével [flux](https://storm.apache.org/releases/current/flux.html) keretapache storm.

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

Ez a YAML a következő elemeket határozza meg:

* `syncPolicy`: Azt határozza meg, hogy a fájlok mikor vannak szinkronizálva/kiürítve a fájlrendszerbe. Ebben a példában minden 1000 tuples.
* `fileNameFormat`: Meghatározza a fájlok írásakor használandó elérési utat és fájlnévmintát. Ebben a példában az elérési út futásidőben, szűrő használatával `.txt`érhető el, és a fájlkiterjesztés a .
* `recordFormat`: Meghatározza az írott fájlok belső formátumát. Ebben a példában a mezőket a `|` karakter haezteti.
* `rotationPolicy`: Meghatározza, hogy mikor kell elforgatni a fájlokat. Ebben a példában nem történik elforgatás.
* `hdfs-bolt`: Az előző összetevőket használja az `HdfsBolt` osztály konfigurációs paramétereként.

A Flux keretrendszerről további [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)információt a .

## <a name="configure-the-cluster"></a>A fürt konfigurálása

Alapértelmezés szerint a Storm on HDInsight nem `HdfsBolt` tartalmazza azokat az összetevőket, amelyek az Azure Storage vagy a Data Lake Storage a Storm osztályelérési útja során kommunikálnak. A következő parancsfájlművelettel adja hozzá `extlib` ezeket az összetevőket a Storm könyvtárához a fürtön:

| Tulajdonság | Érték |
|---|---|
|Parancsfájl típusa |- Egyéni|
|Bash parancsfájl URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Csomóponttípus(ok) |Nimbus, biztos|
|Paraméterek |None|

A parancsfájl fürttel való használatáról a [HDInsight-fürtök testreszabása parancsfájlműveletek használatával](./../hdinsight-hadoop-customize-cluster-linux.md) című dokumentumban talál további információt.

## <a name="build-and-package-the-topology"></a>A topológia összeállítása és csomagolása

1. Töltse le a [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) példaprojektet a fejlesztői környezetbe.

2. A parancssorból, terminálból vagy rendszerhéj-munkamenetből módosítsa a könyvtárakat a letöltött projekt gyökerére. A topológia létrehozásához és csomagolásához használja a következő parancsot:

    ```cmd
    mvn compile package
    ```

    A létrehozás és a csomagolás befejezése után `target`egy új könyvtár `StormToHdfs-1.0-SNAPSHOT.jar`neve is található, amely a . Ez a fájl tartalmazza a lefordított topológiát.

## <a name="deploy-and-run-the-topology"></a>A topológia telepítése és futtatása

1. A következő paranccsal másolja a topológiát a HDInsight-fürtbe. Cserélje `CLUSTERNAME` le a fürt nevére.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Miután a feltöltés befejeződött, az alábbiak használatával csatlakozhat a HDInsight-fürthöz az SSH használatával. Cserélje `CLUSTERNAME` le a fürt nevére.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A csatlakozás után a következő paranccsal hozzon létre egy nevű `dev.properties`fájlt:

    ```bash
    nano dev.properties
    ```

1. A `dev.properties` fájl tartalmaként a következő szöveget használja. Szükség szerint vizsgálja felül az [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme)alapján.

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    A fájl mentéséhez használja a __Ctrl + X__, y , és végül __enter__billentyűt. __Y__ A fájl értékei beállítják a tárolási URL-címet és azt a könyvtárnevet, amelybe az adatokat írják.

1. A topológia elindításához használja a következő parancsot:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Ez a parancs elindítja a topológiát a Flux-keretrendszer használatával a fürt Nimbus csomópontjára való elküldésével. A topológiát `writetohdfs.yaml` az edényben található fájl határozza meg. A `dev.properties` fájl szűrőként kerül átadásra, és a topoológia beolvassa a fájlban lévő értékeket.

## <a name="view-output-data"></a>Kimeneti adatok megtekintése

Az adatok megtekintéséhez használja a következő parancsot:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Megjelenik a topológia által létrehozott fájlok listája. Az alábbi lista az előző parancsok által visszaadott adatokat tartalmazza:

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

A storm topológiák a leállított állapotig vagy a fürt törléséig futnak. A topológia leállításához használja a következő parancsot:

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

Ebben az oktatóanyagban megtanulta, hogyan írhat adatokat az Apache Storm által a HDInsighton használt HDFS-kompatibilis tárolóba az Apache Storm segítségével.

> [!div class="nextstepaction"]
> Fedezzen fel más [Apache Storm-példákat a HDInsighthoz](apache-storm-example-topology.md)