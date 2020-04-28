---
title: Apache Storm Python-összetevőkkel – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre olyan Apache Storm topológiát, amely a Python-összetevőket használja az Azure HDInsight-ben
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77460024"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Apache Storm-topológiák fejlesztése a Python használatával a HDInsight-on

Megtudhatja, hogyan hozhat létre egy Python-összetevőket használó [Apache Storm](https://storm.apache.org/) -topológiát. Apache Storm több nyelvet is támogat, ami lehetővé teszi, hogy a különböző nyelvekről származó összetevőket egyetlen topológiában egyesítse. A [flow](https://storm.apache.org/releases/current/flux.html) Framework (a Storm 0.10.0-mel bevezetett) segítségével könnyedén hozhat létre Python-összetevőket használó megoldásokat.

> [!IMPORTANT]  
> A dokumentumban található információk a Storm on HDInsight 3,6-es verziójában lettek tesztelve.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa**lehetőséget.

* Egy helyi Storm fejlesztői környezet (opcionális). Helyi Storm-környezetre csak akkor van szükség, ha helyileg szeretné futtatni a topológiát. További információ: [fejlesztői környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 vagy újabb](https://www.python.org/downloads/).

* A [Java Developer Kit (JDK) 8-as verziója](https://aka.ms/azure-jdks).

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

## <a name="storm-multi-language-support"></a>Multi-Language Storm-támogatás

Apache Storm a programozási nyelv használatával írt összetevőkkel való együttműködésre lett tervezve. Az összetevőknek meg kell érteniük a Storm-definícióval való munkát. A Python esetében a Apache Storm projekt részeként egy modult biztosítunk, amely lehetővé teszi a Storm használatával történő egyszerű kapcsolódást. A modult itt találja: [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

A Storm egy Java-folyamat, amely a Java virtuális gépon (JVM) fut. A más nyelveken írt összetevők alfolyamatként lesznek végrehajtva. A Storm ezeket az alfolyamatokat a stdin/StdOut-on küldött JSON-üzenetek használatával kommunikál. Az összetevők közötti kommunikációról további részleteket a [multi-lang Protocol](https://storm.apache.org/releases/current/Multilang-protocol.html) dokumentációjában talál.

## <a name="python-with-the-flux-framework"></a>Python a Flux-keretrendszerrel

A Flux-keretrendszer lehetővé teszi, hogy a Storm-topológiákat külön határozza meg az összetevőktől. A Flux-keretrendszer a YAML használatával határozza meg a Storm-topológiát. A következő szöveg szemlélteti, hogyan hivatkozhat egy Python-összetevőre az YAML dokumentumban:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A osztály `FluxShellSpout` a kiöntőt megvalósító `sentencespout.py` szkript elindítására szolgál.

A Flux azt várja, hogy a Python-szkriptek a topológiát tartalmazó jar-fájlban lévő `/resources` könyvtárba legyenek. Így ez a példa a Python-parancsfájlokat `/multilang/resources` a címtárban tárolja. A `pom.xml` tartalmazza ezt a fájlt a következő XML használatával:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Ahogy korábban már említettük, van `storm.py` egy fájl, amely megvalósítja a takarékosság definícióját a Storm számára. A Flux-keretrendszer `storm.py` automatikusan magában foglalja a projekt felépítését, így nem kell aggódnia.

## <a name="build-the-project"></a>A projekt felépítése

1. Töltse le a projektet [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)innen:.

1. Nyisson meg egy parancssort, és navigáljon a projekt `hdinsight-python-storm-wordcount-master`gyökeréhez:. Írja be a következő parancsot:

    ```cmd
    mvn clean compile package
    ```

    Ez a parancs létrehoz `target/WordCount-1.0-SNAPSHOT.jar` egy fájlt, amely tartalmazza a lefordított topológiát.

## <a name="run-the-storm-topology-on-hdinsight"></a>A Storm-topológia futtatása a HDInsight

1. Az [SSH-parancs](../hdinsight-hadoop-linux-use-ssh-unix.md) használatával másolja `WordCount-1.0-SNAPSHOT.jar` a fájlt a Storm on HDInsight-fürtre. Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. A fájl feltöltése után csatlakozzon a fürthöz az SSH használatával:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetben használja a következő parancsot a fürt topológiájának elindításához:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Az indítás után a Storm-topológia a Leállításig fut.

1. A Storm felhasználói felület használatával megtekintheti a fürt topológiáját. A Storm felhasználói felülete a `https://CLUSTERNAME.azurehdinsight.net/stormui`következő helyen található:. Cserélje `CLUSTERNAME` le a nevet a fürt nevére.

1. Állítsa le a Storm-topológiát. A fürt topológiájának leállításához használja a következő parancsot:

    ```bash
    storm kill wordcount
    ```

    Azt is megteheti, hogy a Storm felhasználói felületét használja. A topológiához tartozó **topológiai műveletek** területen válassza a **kill**(Törlés) lehetőséget.

## <a name="run-the-topology-locally"></a>A topológia helyi futtatása

A topológia helyi futtatásához használja a következő parancsot:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Ehhez a parancshoz helyi Storm fejlesztési környezet szükséges. További információ: [fejlesztői környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

A topológia elindítása után a következő szöveghez hasonló módon bocsát ki információkat a helyi konzolra:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

A topológia leállításához használja a __CTRL + C billentyűkombinációt__.

## <a name="next-steps"></a>További lépések

A Python és a HDInsight használatának egyéb módjairól a következő dokumentumokban talál további információt: a [Python felhasználói függvények (UDF) használata az Apache Pig és a Apache Hiveban](../hadoop/python-udf-hdinsight.md).
