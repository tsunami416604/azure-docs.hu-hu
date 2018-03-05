---
title: "A Hadoop-szolgáltatás a HDInsight - Azure halommemória memóriaképek engedélyezése |} Microsoft Docs"
description: "A Hibakeresés és elemzésére szolgáló Hadoop Linux-alapú HDInsight-fürtök szolgáltatásai halommemória memóriaképek engedélyezése."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 2bc7b35a87f3973c59fb36372d4edad86412ea0e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Halommemória memóriaképek a Linux-alapú HDInsight Hadoop-szolgáltatások engedélyezése

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Halommemória memóriaképek tartalmazza az alkalmazás memória, beleértve a változók értékeit a biztonsági másolat létrehozásakor pillanatképet. Ezért futás közben felmerülő problémák diagnosztizálásához.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a HDInsight-fürtök Linux használó dolgozhat. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Services

A következő szolgáltatások halommemória memóriaképek engedélyezéséhez:

* **hcatalog** -tempelton
* **Hive** -hiveserver2-n, metaadattárhoz, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode, secondarynamenode, namenode

Is engedélyezheti a térkép halommemória memóriaképek és csökkentse a HDInsight által futtatott folyamatok.

## <a name="configuration"></a>Understanding halommemória memóriakép konfiguráció

Úgy, hogy a beállítások engedélyezve vannak a halommemória memóriaképek (néven is ismert, azt, vagy paraméterek) számára a JVM-et a szolgáltatás indításakor. A legtöbb Hadoop-szolgáltatásokra módosíthatja a héjparancsfájlt át ezeket a beállításokat a szolgáltatás elindításához használja.

Minden parancsprogram esetén nincs az exportálás  **\* \_OPTS**, amely tartalmazza a JVM-et átadott beállítást. Például a **hadoop-env.sh** parancsfájl, a sor kezdődő `export HADOOP_NAMENODE_OPTS=` a NameNode szolgáltatás beállításait tartalmazza.

Rendelve, és csökkentse folyamatok kissé eltérő, mert ezek a műveletek a MapReduce szolgáltatás egyik gyermekfolyamata. Minden egyes hozzárendelését, vagy csökkentse folyamat egy gyermek tárolóban fut, és a JVM beállításokat tartalmazó két bejegyzést is tartalmaz. Mindkét szereplő **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Az Ambari leíró replikálni a módosításokat a fürt csomópontjai között, a parancsfájlok és a mapred-site.xml beállítások módosítása Ambari használatát javasoljuk. Tekintse meg a [Ambari használatával](#using-ambari) szakasz lépéseit.

### <a name="enable-heap-dumps"></a>Halomürítések engedélyezése

A következő beállítás lehetővé teszi, hogy halommemória memóriaképek egy OutOfMemoryError esetén:

    -XX:+HeapDumpOnOutOfMemoryError

A  **+**  azt jelzi, hogy ez a beállítás engedélyezve van-e. Ez a beállítás alapértelmezés szerint le van tiltva.

> [!WARNING]
> Halommemória memóriaképek nem engedélyezettek a HDInsight Hadoop-szolgáltatás alapértelmezés szerint, lehet, hogy nagy a memóriakép fájlokhoz. Ha engedélyezi ezeket a hibaelhárításhoz, ne felejtse el őket tiltani, miután a probléma másolható és a memóriaképek összegyűjtött.

### <a name="dump-location"></a>Biztonsági másolat helye

A biztonsági másolat fájl alapértelmezett helye az aktuális munkakönyvtárban. Szabályozhatja, ha a fájl található a következő beállítás használatával:

    -XX:HeapDumpPath=/path

Használata esetén például `-XX:HeapDumpPath=/tmp` hatására a memóriaképek könyvtárban kell tárolni.

### <a name="scripts"></a>Parancsprogramok

Egy parancsfájlt is el lehet indítani amikor egy **OutOfMemoryError** következik be. Például váltanak ki egy értesítést, így megtudhatja, hogy a hiba. A következő kapcsoló használatával indul el, a parancsfájl egy __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Mivel a Hadoop elosztott rendszer, bármely használt parancsfájl kell elhelyezni, amely a szolgáltatás fut a fürt összes csomópontján.
> 
> A parancsfájl kell is lehet, amely elérhető a fiók a szolgáltatás fut, és biztosítania kell a helyre végrehajtási engedélyeket. Például előfordulhat, hogy a parancsprogramok tárolásához kívánja `/usr/local/bin` és `chmod go+rx /usr/local/bin/filename.sh` adjon olvasási és végrehajtási engedélyeket.

## <a name="using-ambari"></a>Ambari használatával

A szolgáltatás konfigurációjának módosítása, tegye a következőket:

1. Nyissa meg a fürt Ambari webes felhasználói Felületét. Az URL-cím https://YOURCLUSTERNAME.azurehdinsight.net.

    Amikor a rendszer kéri, a helyhez, a HTTP-fiók nevének hitelesíteni (alapértelmezett: admin) és a jelszót a fürt számára.

   > [!NOTE]
   > Kérheti másodszor Ambari által a felhasználónevet és jelszót. Ha igen, adja meg az azonos fióknevet és jelszót

2. A lista a bal oldali meg és jelölje ki a módosítani kívánt szolgáltatási terület. Például **HDFS**. A központ területen válassza ki a **Configs** fülre.

    ![Ambari webes kijelölt HDFS Configs lap képe](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Használja a **szűrő...**  bejegyzést, írjon be **jelentésküldési**. Csak a tartalmazó ezt a szöveget elemek jelennek meg.

    ![Szűrt lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Keresés a  **\* \_OPTS** szolgáltatás bejegyzése szeretné a halommemória memóriaképek engedélyezése, majd adja meg az engedélyezni kívánt beállításokat. Az alábbi képen felvett `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` számára a **HADOOP\_NAMENODE\_OPTS** bejegyzést:

    ![HADOOP_NAMENODE_OPTS with -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Ha halommemória engedélyezése a térkép listázása, vagy csökkentse gyermekfolyamat, keresse meg a mezők nevű **mapreduce.admin.map.child.java.opts** és **mapreduce.admin.reduce.child.java.opts**.

    Használja a **mentése** gombra a módosítások mentéséhez. A módosítások leíró rövid megjegyzés adhat meg.

5. A módosítások léptek érvénybe, ha a **újraindítás szükséges** ikon jelenik meg egy vagy több szolgáltatás mellett.

    ![Indítsa újra a szükséges ikonra, és indítsa újra a gomb](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Válassza ki a számítógép újraindítását igénylő minden szolgáltatást, és használja a **szolgáltatás műveletek** gombra kattint, hogy **kapcsolja be a karbantartási mód**. Karbantartási mód megakadályozza, hogy a riasztások generálása a szolgáltatásból, ha indítja újra.

    ![Kapcsolja be a karbantartási mód menü](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Miután engedélyezte a karbantartási mód, használja a **indítsa újra a** gombra a szolgáltatás számára **indítsa újra az összes végrehajtott**

    ![Indítsa újra az összes érintett bejegyzés](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > a bejegyzéseket a **indítsa újra a** gomb más szolgáltatásaihoz eltérő lehet.

8. A szolgáltatások újraindítása, ha a **szolgáltatás műveletek** gombra kattint, hogy **kapcsolja ki a karbantartási mód**. Az Ambari riasztások a szolgáltatás figyelésének folytatása.

