---
title: Új verzió – Azure HDInsight egy HBase-fürt migrálása
description: Hogyan lehet HBase-fürtök áttelepítése egy új verzióra.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: a152b815daeefa4c199af9b159eee8e5783971e2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143317"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Az Apache HBase-fürt áttelepítése új verzióra

Ez a cikk az Azure HDInsight az Apache HBase-fürt egy újabb verzióra való frissítéséhez szükséges lépéseket ismerteti.

> [!NOTE]  
> A frissítés során állásidő minimális, perc sorrendjéről kell lennie. Az üzemszünet kiüríteni a memóriában lévő összes adatot, majd konfigurálja, és indítsa újra az új fürtön a szolgáltatásokat az idő okozza a lépéseket. Az eredmények csomópontok, adatok mennyisége és más változók számától függően változhat.

## <a name="review-apache-hbase-compatibility"></a>Tekintse át az Apache HBase-kompatibilitás

Az Apache HBase a frissítés előtt győződjön meg arról, a HBase-verziók a forrás és cél fürtökön kompatibilisek. További információkért lásd: [elérhető HDInsight az Apache Hadoop-összetevők és verziók](../hdinsight-component-versioning.md).

> [!NOTE]  
> Kifejezetten ajánljuk, hogy tekintse át az a verzió-kompatibilitási mátrixot a [HBase könyv](https://hbase.apache.org/book.html#upgrading).

Íme egy példa verziójú kompatibilitási mátrix. Y azt jelzi, hogy kompatibilitási és N azt jelzi, hogy egy esetleges kompatibilitási:

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

Az Azure HDInsight az Apache HBase-fürt frissítéséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy az alkalmazás kompatibilis az új verziót, ahogyan az a HBase-kompatibilitási mátrix és a kiadási megjegyzések. Az alkalmazás tesztelése a HDInsight és a HBase cél verzióját futtató fürtre.

2. [Egy új cél HDInsight-fürt beállítása](../hdinsight-hadoop-provision-linux-clusters.md) ugyanazt a tárfiókot, használatával, de egy másik tároló neve:

    ![Ugyanazt a tárfiókot használják, de egy másik tároló létrehozása](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Kiüríti a forrás HBase-fürtöt, amely a fürt frissíti. A HBase a bejövő adatokat ír egy memórián belüli tároló nevű egy _kapott_. Után a kapott elér egy bizonyos méretet, a HBase kiürítése, lemezre, hosszú távú tároláshoz a fürt storage-fiókban. A régi fürt törlésekor az memstores újrahasznosítására, potenciálisan adatvesztés. A kapott, minden egyes manuálisan kiüríteni a lemezre, futtassa a következő szkriptet. Ennek a parancsfájlnak a legújabb verzióra van az Azure-ban a [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
6. Jelentkezzen be a [Apache Ambari](https://ambari.apache.org/) a régi fürtön (https://OLDCLUSTERNAME.azurehdidnsight.net) , és állítsa le a HBase-szolgáltatásokat. Amikor a rendszer kéri, győződjön meg arról, hogy szeretné-e leállítani a szolgáltatásokat, jelölje be a jelölőnégyzetet, a hbase-hez a karbantartási mód bekapcsolása. A csatlakozás és az Ambari további információkért lásd: [kezelése a HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

    ![Az Ambari, kattintson a szolgáltatások > HBase > állítsa le a szolgáltatási műveletek](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Ellenőrizze a karbantartási mód bekapcsolása HBase jelölőnégyzet, majd adja meg](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Jelentkezzen be az Ambari az új HDInsight-fürtön. Módosítsa a `fs.defaultFS` mutasson a tároló nevére, az eredeti fürt által használt HDFS-beállítás. Ez a beállítás alatt áll **HDFS > Configs > Speciális > speciális hely**.

    ![Az Ambari, kattintson a szolgáltatások > HDFS > Configs > Speciális](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Az Ambari a tároló nevének módosítása](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Ha fokozott ír szolgáltatással rendelkező HBase-fürtök, de nem használja, kihagyhatja ezt a lépést. Szükség esetén csak a HBase-fürtök fokozott ír a szolgáltatással.**
   
   Módosítsa a `hbase.rootdir` átirányítása, az eredeti fürthöz a tároló elérési útja.

    ![Az Ambari a HBase rootdir tároló nevének módosítása](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Ha frissít a HDInsight 3.6-os 4.0-s, kövesse az alábbi lépéseket, egyéb folytassa a 10. lépés:
    1. Indítsa újra az összes szükséges szolgáltatásokat az Ambari kiválasztásával **szolgáltatások** > **minden szükséges indítsa újra a**.
    1. A HBase szolgáltatás leállítása.
    1. A Zookeeper-csomóponthoz ssh-KAPCSOLATOT, és hajtsa végre a [zkCli](https://github.com/go-zkcli/zkcli) parancs `rmr /hbase-unsecure` Zookeeper a HBase legfelső szintű znode eltávolítása.
    1. Indítsa újra a HBase.
1. Ha mellett 4.0 bármely más HDInsight verziójáról frissít, kövesse az alábbi lépéseket:
    1. Mentse a módosításokat.
    1. Ambari jelzett módon, indítsa újra az összes szükséges szolgáltatást.
1. Az alkalmazás az új fürtre mutat.

    > [!NOTE]  
    > A statikus DNS, az alkalmazás módosítja a frissítése. Fix kódolása helyett a DNS, konfigurálhatja egy olyan CNAME REKORDOT a tartománynév DNS-beállítások, amely a fürt neve. Egy másik lehetőség, használhat egy konfigurációs fájlt az alkalmazás újbóli telepítése nélkül frissíthető.

12. Indítsa el a bevitelt megtekintheti, ha minden rendben van a várt módon működik.
13. Ha az új fürt kielégítő, törölje az eredeti fürthöz.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [Apache HBase](https://hbase.apache.org/) és a HDInsight-fürtök frissítése, lásd az alábbi cikkeket:

* [Egy HDInsight-fürt frissítése újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Figyelése és felügyelete az Azure HDInsight az Apache Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md)
* [Az Apache Hadoop-összetevők és verziók](../hdinsight-component-versioning.md)
* [Az Apache Ambari konfigurációk optimalizálása](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
