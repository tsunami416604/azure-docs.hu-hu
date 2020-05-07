---
title: HBase-fürt migrálása egy új verzióra – Azure HDInsight
description: Apache HBase-fürtök migrálása egy újabb verzióra az Azure HDInsight-ben.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: d7fb7b6b409a4e24be97ee61fc7ba1f0c0a93202
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792632"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase-fürt migrálása egy új verzióra

Ez a cikk azt ismerteti, hogyan lehet frissíteni az Apache HBase-fürtöt az Azure HDInsight egy újabb verzióra.

A frissítés közbeni állásidőnek minimálisnak kell lennie a percek sorrendjében. Ezt az állásidőt a memóriában tárolt összes érték kiürítésének lépései okozták, majd a szolgáltatások konfigurálásának és újraindításának ideje az új fürtön. Az eredmények a csomópontok számától, az adatmennyiségtől és más változóktól függően változhatnak.

## <a name="review-apache-hbase-compatibility"></a>Az Apache HBase kompatibilitásának áttekintése

Az Apache HBase frissítése előtt győződjön meg arról, hogy a forrás-és a HBase lévő verziók kompatibilisek. További információ: [Apache Hadoop összetevők és verziók elérhetők a HDInsight](../hdinsight-component-versioning.md)-ben.

> [!NOTE]  
> Javasoljuk, hogy tekintse át a verzió kompatibilitási mátrixát a [HBase-könyvben](https://hbase.apache.org/book.html#upgrading). A HBase verziójának kibocsátási megjegyzései között szerepelnie kell a betörési inkompatibilitásnak.

Íme egy példa a verzió kompatibilitási mátrixára. Az Y a kompatibilitást jelzi, az N pedig potenciális inkompatibilitást jelez:

| Kompatibilitási típus | Főverzió| Másodlagos verzió | Patch |
| --- | --- | --- | --- |
| Ügyfél – kiszolgáló huzalok kompatibilitása | N | I | I |
| Kiszolgáló – kiszolgáló kompatibilitás | N | I | I |
| Fájlformátumok kompatibilitása | N | I | I |
| Ügyfél API-kompatibilitása | N | I | I |
| Ügyfél bináris kompatibilitása | N | N | I |
| **Kiszolgálóoldali korlátozott API-kompatibilitás** |  |  |  |
| Stable | N | I | I |
| Fejlődő | N | N | I |
| Instabil | N | N | N |
| Függőségek kompatibilitása | N | I | I |
| Működési kompatibilitás | N | N | I |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Frissítés ugyanazzal az Apache HBase főverzióval

Az Apache HBase-fürt Azure HDInsight való frissítéséhez hajtsa végre a következő lépéseket:

1. Győződjön meg arról, hogy az alkalmazás kompatibilis az új verzióval, ahogy az a HBase kompatibilitási mátrixban és a kibocsátási megjegyzésekben is látható. Tesztelje alkalmazását egy olyan fürtön, amely a HDInsight és a HBase cél verzióját futtatja.

1. [Hozzon létre egy új cél HDInsight-fürtöt](../hdinsight-hadoop-provision-linux-clusters.md) ugyanazzal a Storage-fiókkal, de egy másik tároló nevével:

    ![Használja ugyanazt a Storage-fiókot, de hozzon létre egy másik tárolót.](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Ürítse ki a forrás HBase-fürtöt, amely a frissíteni kívánt fürt. A HBase beírja a beérkező adatot a memóriában tárolt tárolóba, amelyet _memstore_nevezünk. Ha a memstore elér egy adott méretet, a HBase kiüríti azt a lemezre a fürt Storage-fiókjában lévő hosszú távú tároláshoz. A régi fürt törlésekor a rendszer újrahasznosítja a memstores, ami esetleg elveszíti az adatvesztést. Az egyes táblák memstore manuális kiürítéséhez futtassa az alábbi szkriptet. A szkript legújabb verziója az Azure [githubon](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)érhető el.

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
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
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

1. Állítsa le a betöltést a régi HBase-fürtön.

1. Annak ellenőrzéséhez, hogy a memstore lévő legutóbbi összes érték ki van-e ürítve, futtassa újra az előző parancsfájlt.

1. Jelentkezzen be az [Apache Ambari](https://ambari.apache.org/) a régi fürtön`https://OLDCLUSTERNAME.azurehdidnsight.net`(), és állítsa le a HBase szolgáltatásokat. Amikor a rendszer felszólítja, hogy erősítse meg a szolgáltatások leállítását, jelölje be a HBase karbantartási módjának bekapcsolására szolgáló jelölőnégyzetet. További információ a Ambari-hez való csatlakozásról és a használatával kapcsolatban: [HDInsight-fürtök kezelése a Ambari webes felületének használatával](../hdinsight-hadoop-manage-ambari.md).

    ![A Ambari-ben kattintson a szolgáltatások > HBase > leállítás a szolgáltatási műveletek alatt elemre.](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Jelölje be a karbantartási mód bekapcsolása a HBase jelölőnégyzetet, majd erősítse meg](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Jelentkezzen be a Ambari-be az új HDInsight-fürtön. Módosítsa a `fs.defaultFS` HDFS beállítást úgy, hogy az az eredeti fürt által használt tároló nevére mutasson. Ez a beállítás a **HDFS > konfigurációk területen > advanced > Advanced Core-site**.

    ![A Ambari-ben kattintson a szolgáltatások > HDFS > konfigurációk > speciális elemre.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![A Ambari módosítsa a tároló nevét](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Ha nem használ HBase-fürtöket a továbbfejlesztett írási funkcióval, hagyja ki ezt a lépést. Csak a továbbfejlesztett írási funkcióval rendelkező HBase-fürtökre van szükség.

   Módosítsa az `hbase.rootdir` elérési utat úgy, hogy az az eredeti fürt tárolójára mutasson.

    ![A Ambari módosítsa a HBase rootdir tartozó tároló nevét.](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Ha a HDInsight 3,6-et 4,0-re frissíti, kövesse az alábbi lépéseket, ellenkező esetben ugorjon a 10. lépésre:
    1. Indítsa újra az összes szükséges szolgáltatást a Ambari-ben a **szolgáltatások** > **újraindítása**lehetőség kiválasztásával.
    1. Állítsa le a HBase szolgáltatást.
    1. SSH-t a Zookeeper csomóponthoz, és [zkCli](https://github.com/go-zkcli/zkcli) hajtsa `rmr /hbase-unsecure` végre a zkCli parancsot, hogy eltávolítsa a HBase gyökérszintű znode a Zookeeper.
    1. Indítsa újra a HBase.

1. Ha a 4,0-es verzión kívül más HDInsight is frissít, kövesse az alábbi lépéseket:
    1. Mentse a módosításokat.
    1. Indítsa újra az összes szükséges szolgáltatást a Ambari által jelzett módon.

1. Mutasson az alkalmazást az új fürtre.

    > [!NOTE]  
    > Az alkalmazás statikus DNS-je módosul a frissítéskor. A DNS nem megfelelő kódolása helyett beállíthat egy CNAME nevet a tartománynév DNS-beállításai között, amelyek a fürt nevére mutatnak. Egy másik lehetőség az alkalmazáshoz tartozó konfigurációs fájl használata, amelyet újbóli üzembe helyezés nélkül frissíthet.

1. Indítsa el a betöltést, és ellenőrizze, hogy minden a várt módon működik-e.

1. Ha az új fürt kielégítő, törölje az eredeti fürtöt.

## <a name="next-steps"></a>További lépések

Az [Apache HBase](https://hbase.apache.org/) és a HDInsight-fürtök frissítésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight-fürt frissítése újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Azure-HDInsight monitorozása és kezelése az Apache Ambari webes felhasználói felületén](../hdinsight-hadoop-manage-ambari.md)
* [Összetevők és verziók Apache Hadoop](../hdinsight-component-versioning.md)
* [Apache-HBase optimalizálása](../optimize-hbase-ambari.md)
