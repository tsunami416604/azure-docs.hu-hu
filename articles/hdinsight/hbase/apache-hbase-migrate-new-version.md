---
title: Új verzió – Azure HDInsight egy HBase-fürt migrálása
description: Hogyan lehet HBase-fürtök áttelepítése egy új verzióra.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 1e62495de35c8df4f446d371a0bbbcdc80c7118d
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650103"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Az Apache HBase-fürt áttelepítése új verzióra

Feladat-alapú fürtök esetében például [Apache Spark](https://spark.apache.org/) és [Apache Hadoop](https://hadoop.apache.org/), egyszerű frissítése – lásd: [újabb verzióra való frissítése HDInsight-fürt](../hdinsight-upgrade-cluster.md):

1. Átmeneti (helyileg tárolt) adatok biztonsági másolatát.
2. Törölje a meglévő fürtből.
3. Új fürt létrehozása a virtuális hálózat ugyanabban az alhálózatban.
4. Átmeneti adatok importálása.
5. Indítsa el a feladatokat, és folytatja a műveletet az új fürtön.

Frissítése az [Apache HBase](https://hbase.apache.org/) fürt további lépésekre van szükség, ebben a cikkben leírtak szerint.

> [!NOTE]  
> A frissítés során állásidő minimális, perc sorrendjéről kell lennie. Az üzemszünet kiüríteni a memóriában lévő összes adatot, majd konfigurálja, és indítsa újra az új fürtön a szolgáltatásokat az idő okozza a lépéseket. Az eredmények csomópontok, adatok mennyisége és más változók számától függően változhat.

## <a name="review-apache-hbase-compatibility"></a>Tekintse át az Apache HBase-kompatibilitás

Az Apache HBase a frissítés előtt győződjön meg arról, a HBase-verziók a forrás és cél fürtökön kompatibilisek. További információkért lásd: [elérhető HDInsight az Apache Hadoop-összetevők és verziók](../hdinsight-component-versioning.md).

> [!NOTE]  
> Kifejezetten ajánljuk, hogy tekintse át az a verzió-kompatibilitási mátrixot a [HBase könyv](https://hbase.apache.org/book.html#upgrading).

Íme egy példa verziójú kompatibilitási mátrix, ahol Y azt jelzi, hogy kompatibilitási és N azt jelzi, hogy egy esetleges kompatibilitási:

| Való kompatibilitásának típusa | Főverzió| Alverzió | Javítás |
| --- | --- | --- | --- |
| Ügyfél-kiszolgáló átviteli kompatibilitása | N | I | I |
| Kiszolgálók kompatibilitása | N | I | I |
| Fájl kompatibilitása | N | I | I |
| Ügyfél API-kompatibilitás | N | I | I |
| Ügyfél bináris kompatibilitás | N | N | I |
| **Kiszolgálóoldali korlátozott API-kompatibilitás** |  |  |  |
| Stable | N | I | I |
| Folyamatosan fejlődő | N | N | I |
| Nem stabil | N | N | N |
| Függőségi kompatibilitása | N | I | I |
| Működési kompatibilitása | N | N | I |

> [!NOTE]  
> A HBase verzió kibocsátási megjegyzésekben használhatatlanná tévő inkompatibilitásokat le kell írni.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>A frissítés ugyanazt az Apache HBase-főverzió

A következő forgatókönyv olyan ugyanazzal a verzióval HBase fő 3.6 (mindkettő származnak, az Apache HBase 1.1.2) a HDInsight 3.4-es verzióra. Más verziófrissítések hasonlóak, mindaddig, amíg nincsenek kompatibilitási problémák a forrás és cél között.

1. Győződjön meg arról, hogy az alkalmazás kompatibilis az új verziót, ahogyan az a HBase-kompatibilitási mátrix és a kiadási megjegyzések. Az alkalmazás tesztelése a HDInsight és a HBase cél verzióját futtató fürtre.

2. [Egy új cél HDInsight-fürt beállítása](../hdinsight-hadoop-provision-linux-clusters.md) ugyanazt a tárfiókot, használatával, de egy másik tároló neve:

    ![Ugyanazt a tárfiókot használják, de egy másik tároló létrehozása](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Kiüríti a forrás HBase-fürtöt. Ez a frissít, amelyről a fürtöt. A HBase a bejövő adatokat ír egy memórián belüli tároló nevű egy _kapott_. Miután a kapott elér egy bizonyos méretet, a kapott ki van-e ürítve lemezre, hosszú távú tároláshoz a fürt storage-fiókban. A régi fürt törlésekor az memstores újrahasznosítására, potenciálisan adatvesztés. A kapott, minden egyes manuálisan kiüríteni a lemezre, futtassa a következő szkriptet. Ennek a parancsfájlnak a legújabb verzióra van az Azure-ban a [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Állítsa le a régi HBase-fürt Adatbetöltési.
5. Győződjön meg arról, hogy a kapott friss adatokat ki van-e ürítve, futtassa újra az előző parancsfájlt.
6. Jelentkezzen be [Apache Ambari](https://ambari.apache.org/) a régi fürtön (https://OLDCLUSTERNAME.azurehdidnsight.net) , és állítsa le a HBase-szolgáltatásokat. Amikor a rendszer kéri, győződjön meg arról, hogy szeretné-e leállítani a szolgáltatásokat, jelölje be a jelölőnégyzetet, a hbase-hez a karbantartási mód bekapcsolása. A csatlakozás és az Ambari további információkért lásd: [kezelése a HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

    ![Az Ambari kattintson az intézménye lapjára, majd a bal oldali menüben, HBase, majd állítsa le a szolgáltatási műveletek](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Ellenőrizze a karbantartási mód bekapcsolása HBase jelölőnégyzet, majd adja meg](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Jelentkezzen be az Ambari az új HDInsight-fürtön. Módosítsa a `fs.defaultFS` mutasson a tároló nevére, az eredeti fürt által használt HDFS-beállítás. Ez a beállítás alatt áll **HDFS > Configs > Speciális > speciális hely**.

    ![Az Ambari kattintson az intézménye lapjára, majd a bal oldali menüben, majd a Configs fülre, majd a Speciális lap alatt HDFS](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Az Ambari a tároló nevének módosítása](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Mentse a módosításokat.
9. Ambari jelzett módon, indítsa újra az összes szükséges szolgáltatást.
10. Az alkalmazás az új fürtre mutat.

    > [!NOTE]  
    > A statikus DNS, az alkalmazás módosítja a frissítése. Fix kódolása helyett a DNS, konfigurálhatja egy olyan CNAME REKORDOT a tartománynév DNS-beállítások, amely a fürt neve. Egy másik lehetőség, használhat egy konfigurációs fájlt az alkalmazás újbóli telepítése nélkül frissíthető.

11. Indítsa el a bevitelt megtekintheti, ha minden rendben van a várt módon működik.
12. Ha az új fürt kielégítő, törölje az eredeti fürthöz.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [Apache HBase](https://hbase.apache.org/) és a HDInsight-fürtök frissítése, lásd az alábbi cikkeket:

* [Egy HDInsight-fürt frissítése újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Figyelése és felügyelete az Azure HDInsight az Apache Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md)
* [Az Apache Hadoop-összetevők és verziók](../hdinsight-component-versioning.md)
* [Az Apache Ambari konfigurációk optimalizálása](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
