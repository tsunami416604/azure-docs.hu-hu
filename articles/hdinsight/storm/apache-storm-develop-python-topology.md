---
title: Python-összetevők – Azure HDInsight az Apache Storm
description: Ismerje meg, hogyan hozhat létre egy Python-összetevők használó Apache Storm-topológiák.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: az Apache storm python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: 87d13f8b0df4d9a357218b34035993663dfda8b3
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634592"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Python használata a HDInsight Apache Storm-topológiák fejlesztése

Ismerje meg, hogyan hozhat létre egy [Apache Storm](https://storm.apache.org/) topológiát használó Python-összetevők. Az Apache Storm több nyelvet is támogat, még akkor is lehetővé teszi egy topológiában számos nyelvből összetevőket kombinálja. A [fluxus](https://storm.apache.org/releases/current/flux.html) keretrendszer (a Storm 0.10.0-s ismertetése) lehetővé teszi, hogy könnyedén hozhat létre-et használó Python-összetevők.

> [!IMPORTANT]  
> A jelen dokumentumban lévő információk teszteltünk, a HDInsight 3.6-alapú Storm segítségével. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ez a projekt kódját mindig elérhető legyen [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7-es vagy újabb

* Java JDK 1.8-as vagy újabb

* [Az Apache Maven 3](https://maven.apache.org/download.cgi)

* (Nem kötelező) Egy helyi Storm-fejlesztési környezetre. A Storm helyi környezetben csak akkor van szükség, ha helyileg futtatja a topológia. További információkért lásd: [a fejlesztési környezet beállítása](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>A Storm nyelvek támogatása

Az Apache Storm használatával tetszőleges programozási nyelven írt összetevők dolgozhat úgy lett kialakítva. Az összetevők értenie kell dolgozni a [Thrift-definíció a Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). A Python a modul, amely lehetővé teszi, hogy könnyen csatlakozhatnak a Storm az Apache Storm-projekt részeként biztosított. Ez a modul címen található [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

A Storm Java folyamat, amely a Java virtuális gép (JVM) érhető el. Más nyelveken írt összetevőket magában, végre kell hajtani. A Storm használata a JSON-üzenetek stdin/stdout keresztül küldi el ezeket magában kommunikál. Összetevők közötti kommunikációt a további részletek találhatók a [Multi-lang protokoll](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentációját.

## <a name="python-with-the-flux-framework"></a>Python és a fluxus keretrendszer

A fluxus keretrendszer lehetővé teszi, hogy meghatározza az összetevőket külön, a Storm-topológiák. A fluxus keretrendszer YAML használ a Storm-topológia meghatározására. A következő példa bemutatja, hogyan való hivatkozáshoz egy Python-összetevő a YAML-dokumentumban a következő szöveget:

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

Az osztály `FluxShellSpout` való elindítására szolgál a `sentencespout.py` valósítja meg a spout-szkript.

Fluxus vár a Python-szkriptek kell lennie a `/resources` könyvtárat a jar-fájlt, amely tartalmazza a topológia belül. Így az ebben a példában a Python-szkriptek tárolja a `/multilang/resources` könyvtár. A `pom.xml` a fájlt a következő XML-kódot tartalmazza:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Ahogy korábban említettük, van egy `storm.py` fájlt, amely megvalósítja az Thrift-definíció, a Storm. Tartalmazza a fluxus keretrendszer `storm.py` automatikusan Ha a projekt létrejött, így nem kell aggódnia, többek között.

## <a name="build-the-project"></a>A projekt felépítése

A projekt gyökérkönyvtárában használja a következő parancsot:

```bash
mvn clean compile package
```

Ez a parancs létrehoz egy `target/WordCount-1.0-SNAPSHOT.jar` fájlt, amely tartalmazza a lefordított topológiát.

## <a name="run-the-topology-locally"></a>A topológia helyileg történő futtatása

A topológia helyi futtatását, használja a következő parancsot:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Ez a parancs a helyi Storm környezet szükséges. További információkért lásd: [a fejlesztési környezet beállítása](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Miután a topológia elindul, bocsát ki a helyi konzol az alábbi szöveghez hasonló információt:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


A topológia leállításához használja __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>A HDInsight a Storm-topológia futtatásához

1. A következő parancs használatával másolja a `WordCount-1.0-SNAPSHOT.jar` fájlt alatt futó Storm HDInsight-fürtön:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Cserélje le `sshuser` és a fürthöz az SSH-felhasználót. Cserélje le `mycluster` a fürt nevére. A rendszer felkérheti adja meg a jelszót az SSH-felhasználót.

    Az SSH és SCP-je használatával további információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután a fájl fel lett töltve, csatlakozzon a fürthöz SSH használatával:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből a következő parancs használatával indítsa el a topológia a fürtön:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. A Storm felhasználói felülete segítségével megtekintheti a topológia a fürtön. A Storm felhasználói felülete a következő helyen található https://mycluster.azurehdinsight.net/stormui. Cserélje le `mycluster` a fürt nevére.

> [!NOTE]  
> Megkezdése után a Storm-topológia fut, amíg leállt. A topológia leállítása, használja a következő módszerek egyikét:
>
> * A `storm kill TOPOLOGYNAME` parancsot a parancssorból
> * A **Kill** gomb a Storm felhasználói felületén.


## <a name="next-steps"></a>További lépések

A Python használata a HDInsight egyéb módjaira vonatkozóan az alábbi dokumentumokban talál:

* [Hogyan használható a Python MapReduce-feladatok streameléshez](../hadoop/apache-hadoop-streaming-python.md)
* [Python felhasználói definiált függvények (UDF) az Apache Pig- és Apache Hive használata](../hadoop/python-udf-hdinsight.md)
