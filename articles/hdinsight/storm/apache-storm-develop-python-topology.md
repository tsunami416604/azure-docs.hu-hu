---
title: Apache Storm Python-összetevőkkel – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Olyan Apache Storm-topológiát, amely Python-összetevőket használ az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460024"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Apache Storm topológiák fejlesztése python használatával a HDInsighton

Ismerje meg, hogyan hozhat létre Python-összetevőket használó Apache Storm-topológiát. [Apache Storm](https://storm.apache.org/) Az Apache Storm több nyelvet is támogat, még akkor is, ha egy topológia több nyelvösszetevőit is kombinálhatja. A [Flux](https://storm.apache.org/releases/current/flux.html) keretrendszer (a Storm 0.10.0-val bevezetett) lehetővé teszi, hogy könnyen hozzon létre python-összetevőket használó megoldásokat.

> [!IMPORTANT]  
> A jelen dokumentumban szereplő információkat a Storm a HDInsight 3.6-on tesztelte.

## <a name="prerequisites"></a>Előfeltételek

* Apache Storm-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza a **Storm** for **Cluster típus**lehetőséget.

* Helyi viharfejlesztési környezet (nem kötelező). A helyi Storm-környezetcsak akkor szükséges, ha helyileg szeretné futtatni a topológiát. További információt a [Fejlesztői környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html)című témakörben talál.

* [Python 2.7 vagy újabb](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.

## <a name="storm-multi-language-support"></a>Storm többnyelvű támogatás

Az Apache Stormot úgy tervezték, hogy bármilyen programozási nyelven írt összetevőkkel működjön. Az összetevőknek meg kell érteniük, hogyan kell dolgozni a Takarékosság definícióstorm. A Python számára egy modul az Apache Storm projekt részeként érhető el, amely lehetővé teszi a Storm könnyű felületét. Ezt a modult [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)a.

A Storm egy Java-folyamat, amely a Java virtuális gépen (JVM) fut. A más nyelveken írt összetevők alfolyamatokként kerülnek végrehajtásra. A Storm kommunikál ezekkel a subprocesses használatával JSON üzeneteket küldött stdin/stdout használatával. Az összetevők közötti kommunikációval kapcsolatos további részletek a [Multi-lang Protokoll](https://storm.apache.org/releases/current/Multilang-protocol.html) dokumentációjában találhatók.

## <a name="python-with-the-flux-framework"></a>Python a Flux keretrendszerrel

A Flux keretrendszer lehetővé teszi a Storm topológiák az összetevőktől elkülönítve történő definiálását. A Flux-keretrendszer yaml-t használ a Storm topológia meghatározásához. A következő szöveg egy példa arra, hogyan hivatkozhatsz egy Python-összetevőre a YAML-dokumentumban:

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

Az `FluxShellSpout` osztály a spout-ot megvalósító `sentencespout.py` parancsfájl elindításához használatos.

A Flux elvárja, hogy a `/resources` Python-parancsfájlok a topológiát tartalmazó jar fájlkönyvtárában legyenek. Így ez a példa tárolja `/multilang/resources` a Python-parancsfájlokat a címtárban. A `pom.xml` fájl a következő XML-t használja:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Mint korábban említettük, `storm.py` van egy fájl, amely végrehajtja a Takarékosság meghatározása Storm. A Flux `storm.py` keretrendszer automatikusan tartalmazza a projekt felépítését, így nem kell aggódnia, beleértve azt.

## <a name="build-the-project"></a>A projekt felépítése

1. Töltse le [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)a projektet a alkalmazásból.

1. Nyisson meg egy parancssort, `hdinsight-python-storm-wordcount-master`és keresse meg a projekt gyökér: . Írja be a következő parancsot:

    ```cmd
    mvn clean compile package
    ```

    Ez a `target/WordCount-1.0-SNAPSHOT.jar` parancs olyan fájlt hoz létre, amely a lefordított topológiát tartalmazza.

## <a name="run-the-storm-topology-on-hdinsight"></a>A Storm-topológia futtatása a HDInsighton

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) másolja a fájlt a `WordCount-1.0-SNAPSHOT.jar` Storm hdinsight-fürtre. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. A fájl feltöltése után csatlakozzon a fürthöz az SSH használatával:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből a következő paranccsal indítsa el a topológiát a fürtön:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Miután elkezdődött, a Storm topológia addig tart, amíg le nem áll.

1. A Storm felhasználói felülethasználatával tekintse meg a topológia a fürtön. A Storm felhasználói felülete a helyen `https://CLUSTERNAME.azurehdinsight.net/stormui`található. Cserélje `CLUSTERNAME` le a fürt nevét.

1. Állítsd meg a Vihar topológiát. A következő paranccsal állítsa le a topológiát a fürtön:

    ```bash
    storm kill wordcount
    ```

    Másik lehetőségként használhatja a Storm felhasználói felületet. A topológia **topológiája csoportban** válassza a **Leölés**lehetőséget.

## <a name="run-the-topology-locally"></a>A topológia helyi futtatása

A topológia helyi futtatásához használja a következő parancsot:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Ehhez a parancshoz helyi Storm-fejlesztői környezet szükséges. További információt a [Fejlesztői környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html)című témakörben talál.

A topológia indítása után a következő szöveghez hasonló információkat bocsát ki a helyi konzolra:

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

A topológia leállításához használja a __Ctrl+ C billentyűkombinációt.__

## <a name="next-steps"></a>További lépések

Tekintse meg a következő dokumentumokat a Python HDInsight használatával való használatának egyéb módjairól: [A Python-felhasználók által definiált függvények (UDF) használata az Apache Pig és az Apache Hive használatáról.](../hadoop/python-udf-hdinsight.md)
