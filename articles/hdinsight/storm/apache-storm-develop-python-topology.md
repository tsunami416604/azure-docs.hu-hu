---
title: A Python comopnents - Azure HDInsight alatt futó Apache Storm |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Python-összetevők használó alatt futó Apache Storm-topológiák.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
keywords: Apache storm python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 9137e509ff352bdcb7a74b652b5c7c7edef2d7ea
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131160"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Python használata a HDInsight alatt futó Apache Storm-topológiák fejlesztése

Megtudhatja, hogyan hozzon létre egy Python-összetevők használó alatt futó Apache Storm-topológiák. Apache Storm több nyelv használatát, még akkor is így egyesítése egy topológia több nyelv-összetevőt. A fluxus keretrendszer (Storm 0.10.0-s bevezetett) lehetővé teszi, hogy könnyen létrehozhat megoldások, amelyek a Python-összetevőket használnak.

> [!IMPORTANT]
> A jelen dokumentumban szereplő információk teszteltük a HDInsight 3.6 alatt futó Storm használatával. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ez a projekt kódját érhető el: [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7 vagy újabb

* Java JDK 1.8 vagy újabb

* 3 maven

* (Választható) A helyi Storm környezet. A helyi Storm-környezet csak akkor van szükség, ha a topológia helyileg futtatja. További információkért lásd: [a fejlesztési környezet létrehozása](http://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>A Storm több nyelv támogatása

Apache Storm használható bármely programozási nyelv használatával készítettek összetevők úgy lett kialakítva. Az összetevők kell megtudhatja, hogyan használható a [Thrift-definíciók alatt futó Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Python a modul biztosítja az Apache Storm projekt, amely lehetővé teszi, hogy könnyen csatoló Storm részeként. A modul található [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

A Storm egy Java folyamat futtatja a Java virtuális gép (JVM). Az egyéb nyelven írt összetevők vannak végre, mert magában. A Storm ezek magában stdin/stdout keresztüli üzenetküldés JSON használatával kommunikál. Összetevők közötti kommunikáció további részletek találhatók a [Multi-lang protokoll](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentációját.

## <a name="python-with-the-flux-framework"></a>A fluxus keretrendszerrel Python

A fluxus keretrendszer megadhatja az összetevőket külön-külön a Storm-topológiák. A fluxus keretrendszer YAM használ a Storm-topológia meghatározására. A következő példa bemutatja, hogyan való hivatkozáshoz egy Python-összetevő a YAM dokumentumban áll:

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

Az osztály `FluxShellSpout` való elindítására szolgál a `sentencespout.py` parancsfájl, amely megvalósítja a spout.

Fluxus vár a Python-parancsfájlokat kell lennie a `/resources` belül a jar-fájlra, amely tartalmazza a topológia könyvtárába. Ebben a példában a Python parancsfájlok tárolja, a `/multilang/resources` könyvtár. A `pom.xml` tartalmazza ezt a fájlt a következő XML-kód használatával:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

A korábban említett van egy `storm.py` fájl, amely megvalósítja az alatt futó Storm Thrift-definíciók. A fluxus tartalmazza `storm.py` automatikusan amikor a projekt épül, így nem kell aggódnia, beleértve azt.

## <a name="build-the-project"></a>A projekt felépítése

A projekt gyökérkönyvtárában használja a következő parancsot:

```bash
mvn clean compile package
```

Ezzel a paranccsal létrejön egy `target/WordCount-1.0-SNAPSHOT.jar` a lefordított topológia tartalmazó fájlt.

## <a name="run-the-topology-locally"></a>Futtassa helyben a topológia

A topológia helyileg futtatta, használja a következő parancsot:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Ez a parancs a helyi Storm környezet szükséges. További információkért lásd: [a fejlesztési környezet létrehozása](http://storm.apache.org/releases/current/Setting-up-development-environment.html)

Egyszer a topológia indul el, akkor bocsát ki a helyi konzol, az alábbihoz hasonló információkat:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


A topológia leállítása, használja a __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>A HDInsight a Storm-topológia futtatása

1. Másolja az alábbi parancs segítségével a `WordCount-1.0-SNAPSHOT.jar` fájl alatt futó Storm on HDInsight-fürt:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Cserélje le `sshuser` a fürthöz az SSH felhasználóval. Cserélje le `mycluster` a fürt nevéhez. Adja meg a jelszót az SSH-felhasználó kérheti.

    További információ az SSH és az SCP használatával, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A fájl a feltöltést követően csatlakozzon a fürthöz SSH használatával:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből a következő parancs használatával indítsa el a topológia a fürtre:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. A Storm felhasználói felülete segítségével megtekintheti a topológia a fürtön. A Storm felhasználói felülete a következő helyen található https://mycluster.azurehdinsight.net/stormui. Cserélje le `mycluster` elemet a fürt nevére.

> [!NOTE]
> Miután elindult, a Storm-topológia fut. csak a A topológia leállítása, használja a következő módszerek egyikét:
>
> * A `storm kill TOPOLOGYNAME` parancsot a parancssorból
> * A **Kill** gomb a Storm felhasználói felületén.


## <a name="next-steps"></a>További lépések

Tekintse át a Python és a HDInsight együttes használata egyéb módjai a következő dokumentumokat:

* [Python használata az adatfolyamként történő MapReduce-feladatok](../hadoop/apache-hadoop-streaming-python.md)
* [Python felhasználó definiált függvény (UDF) a Pig és a Hive használata](../hadoop/python-udf-hdinsight.md)
