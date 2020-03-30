---
title: Halommemória-memóriaképek engedélyezése apache hadoop-szolgáltatásokhoz a HDInsightban – Azure
description: Engedélyezze a halommemória-memóriaképeket az Apache Hadoop-szolgáltatásokhoz linuxos HDInsight-fürtökből a hibakereséshez és elemzéshez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75658797"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Halommemória-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight-alapú

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

A halommemória-memóriaképek az alkalmazás memóriájának pillanatképét tartalmazzák, beleértve a változók értékét a memóriakép létrehozásakor. Tehát hasznosak a futásidőben előforduló problémák diagnosztizálására.

## <a name="services"></a>Szolgáltatások

A halommemória-memóriaképek et a következő szolgáltatásokhoz engedélyezheti:

* **Apache hcatalog** - tempelton
* **Apache kaptár** - kaptár2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - resourcemanager, nodemanager, timelineserver
* **Apache hdfs** - datanode, secondarynamenode, namenode

Engedélyezheti a halommemória-memóriaképeket a térképhez, és csökkentheti a HDInsight által futtatott folyamatokat.

## <a name="understanding-heap-dump-configuration"></a>A halommemória-memóriakép konfigurációjának ismertetése

A halommemória-memóriaképek engedélyezése a jvm-nek adott beállításokkal (más néven opts vagy paraméterek) a szolgáltatás indításakor. A legtöbb [Apache Hadoop-szolgáltatás](https://hadoop.apache.org/) esetében módosíthatja a szolgáltatás indításához használt rendszerhéj-parancsfájlt, hogy átadja ezeket a beállításokat.

Minden parancsfájlban van egy exportálás az ** \* \_OPTS**számára, amely tartalmazza a JVM-nek átadott beállításokat. A **hadoop-env.sh** parancsfájlban például a kezdő `export HADOOP_NAMENODE_OPTS=` sor tartalmazza a NameNode szolgáltatás beállításait.

A leképezési és csökkentési folyamatok némileg eltérőek, mivel ezek a műveletek a MapReduce szolgáltatás gyermekfolyamatai. Minden leképezési vagy csökkentési folyamat egy gyermektárolóban fut, és két bejegyzés tartalmazza a JVM-beállításokat. Mindkettő a **mapred-site.xml fájlban**található:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Javasoljuk, hogy az [Apache Ambari](https://ambari.apache.org/) használatával módosítsa a parancsfájlokat és a mapred-site.xml beállításokat, mivel az Ambari kezeli a módosítások replikálását a fürt csomópontjai között. Az [Apache Ambari használata](#using-apache-ambari) című részben további lépéseket.

### <a name="enable-heap-dumps"></a>Halomürítések engedélyezése

A következő beállítás engedélyezi a halommemória-memóriaképeket OutOfMemoryError esetén:

    -XX:+HeapDumpOnOutOfMemoryError

A **+** beállítás azt jelzi, hogy ez a beállítás engedélyezve van. Ez a beállítás alapértelmezés szerint le van tiltva.

> [!WARNING]  
> A halommemória-memóriaképek alapértelmezés szerint nincsenek engedélyezve a Hadoop-szolgáltatások hdinsight-on, mivel a memóriaképfájlok nagyok lehetnek. Ha engedélyezi a hibaelhárítást, ne felejtse el letiltani őket, miután reprodukálta a problémát, és összegyűjtötte a memóriaképfájlokat.

### <a name="dump-location"></a>Kiírás helye

A memóriaképfájl alapértelmezett helye az aktuális munkakönyvtár. A fájl tárolási helyét a következő beállítással szabályozhatja:

    -XX:HeapDumpPath=/path

Ha például `-XX:HeapDumpPath=/tmp` a rendszer a dumps-ot a /tmp könyvtárban tárolja, akkor a rendszer a dumps-ot tárolja.

### <a name="scripts"></a>Scripts

**OutOfMemoryError** esetén parancsfájlis aktiválható. Például egy értesítést, hogy tudja, hogy a hiba történt. Az alábbi beállítással parancsfájlt indíthat el egy __OutOfMemoryError hibán:__

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Mivel az Apache Hadoop egy elosztott rendszer, minden használt parancsfájlt a fürt minden csomópontjára kell helyezni, amelyen a szolgáltatás fut.
> 
> A parancsfájlnak olyan helyen is kell lennie, amely elérhető a szolgáltatás által futtatott fiók számára, és végrehajtási engedélyeket kell biztosítania. Előfordulhat például, hogy parancsfájlokat `/usr/local/bin` szeretne `chmod go+rx /usr/local/bin/filename.sh` tárolni, és olvasási és végrehajtási engedélyek et szeretne megadni.

## <a name="using-apache-ambari"></a>Az Apache Ambari használata

Egy szolgáltatás konfigurációjának módosításához kövesse az alábbi lépéseket:

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

2. A bal oldali lista segítségével jelölje ki a módosítani kívánt szolgáltatási területet. Például, **HDFS**. A középső területen válassza a **Configs** fület.

    ![Az Ambari web képe a HDFS Configs lapon kiválasztva](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. A **Szűrő...** bejegyzés használatával adja meg az **opts értéket.** Csak az ezt a szöveget tartalmazó elemek jelennek meg.

    ![Apache Ambari konfigurációs szűrt lista](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Keresse ** \* \_** meg annak a szolgáltatásnak az OPTS-bejegyzését, amelyhez engedélyezni szeretné a halommemória-írásokat, és adja hozzá az engedélyezni kívánt beállításokat. Az alábbi képen hozzáadtam `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` a **HADOOP\_NAMENODE\_OPTS** bejegyzéshez:

    ![Apache Ambari hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ha engedélyezi a halommemória-írásokat a térképhez, vagy csökkenti a gyermekfolyamatot, keresse meg a **mapreduce.admin.map.child.java.opts** és **a mapreduce.admin.reduce.child.java.opts nevű mezőket.**

    A **Mentés** gombbal mentheti a módosításokat. A módosításokat leíró rövid feljegyzést is megadhat.

5. A módosítások alkalmazása után egy vagy több szolgáltatás mellett megjelenik az **Újraindítás szükséges** ikon.

    ![újraindítás szükséges ikon és újraindítás gomb](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Jelölje ki az újraindítást igénylő egyes szolgáltatást, és a Karbantartási **mód bekapcsolása**a Service Actions **(Szervizműveletek)** gombbal kapcsolja be a karbantartási módot. A karbantartási mód megakadályozza, hogy a szolgáltatás újraindításkor riasztások at hozzon létre.

    ![A hdi karbantartási mód menüjének bekapcsolása](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Miután engedélyezte a karbantartási módot, használja az **Újraindítás** gombot a szolgáltatás **hoz az összes**

    ![Apache Ambari Újraindítás minden érintett bejegyzés](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Az **Újraindítás** gomb bejegyzései más szolgáltatások esetében eltérőek lehetnek.

8. A szolgáltatások újraindítása után a **Szervizműveletek** gombbal kapcsolja ki a **karbantartási módot.** Ez a mabari a szolgáltatás riasztásai figyelésének folytatásához.