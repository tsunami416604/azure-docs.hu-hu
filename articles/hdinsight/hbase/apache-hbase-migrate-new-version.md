---
title: HBase-fürt áttelepítése új verzióra – Azure HDInsight
description: Az Apache HBase-fürtök áttelepítése egy újabb verzióra az Azure HDInsightban.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646506"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase-fürt áttelepítése új verzióra

Ez a cikk ismerteti az Apache HBase-fürt frissítéséhez szükséges lépéseket az Azure HDInsight egy újabb verzióra.

A frissítés közbeni állásidő minimális legyen, percek sorrendjében. Ezt az állásidőt az összes memórián belüli adat kiürítésének lépése, majd az új fürt szolgáltatásainak konfigurálásának és újraindításának ideje okozza. Az eredmények a csomópontok számától, az adatok mennyiségétől és más változóktól függően változnak.

## <a name="review-apache-hbase-compatibility"></a>Az Apache HBase kompatibilitásának áttekintése

Az Apache HBase frissítése előtt győződjön meg arról, hogy a forrás- és célfürtök HBase-verziói kompatibilisek. További információ: [Apache Hadoop-összetevők és a HDInsight segítségével elérhető verziók.](../hdinsight-component-versioning.md)

> [!NOTE]  
> Javasoljuk, hogy tekintse át a HBase könyv verziókompatibilitási [mátrixát.](https://hbase.apache.org/book.html#upgrading) A törési inkompatibilitásokat a HBase verziókiadási megjegyzésekben kell leírni.

Íme egy példa verziókompatibilitási mátrix. Az Y a kompatibilitást, az N pedig a lehetséges inkompatibilitást jelzi:

| Kompatibilitás típusa | Főverzió| Alverzió | Patch |
| --- | --- | --- | --- |
| Ügyfél-kiszolgáló vezetékkompatibilitás | N | I | I |
| Kiszolgáló-kiszolgáló kompatibilitás | N | I | I |
| Fájlformátum-kompatibilitás | N | I | I |
| ÜgyfélAPI-kompatibilitás | N | I | I |
| Ügyfél bináris kompatibilitása | N | N | I |
| **Kiszolgálóoldali korlátozott API-kompatibilitás** |  |  |  |
| Stable | N | I | I |
| Fejlődő | N | N | I |
| Instabil | N | N | N |
| Függőségkompatibilitás | N | I | I |
| Működési kompatibilitás | N | N | I |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Frissítés ugyanazzal az Apache HBase főverzióval

Az Apache HBase-fürt azure HDInsight szolgáltatásban való frissítéséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy az alkalmazás kompatibilis az új verzióval, ahogy az a HBase kompatibilitási mátrixban és a kibocsátási megjegyzésekben látható. Tesztelje az alkalmazást a HDInsight és a HBase célverzióját futtató fürtben.

1. [Hozzon létre egy új cél HDInsight-fürtöt](../hdinsight-hadoop-provision-linux-clusters.md) ugyanazzal a tárfiókkal, de más tárolónévvel:

    ![Használja ugyanazt a tárfiókot, de hozzon létre egy másik tárolót](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Ürítse ki a forrás HBase-fürtöt, amely a frissítve lévő fürt. A HBase bejövő adatokat ír egy memórián belüli tárolóba, amelyet _memstore-nak_neveznek. Miután a memstore elér egy bizonyos méretet, a HBase lemezre üríti a fürt tárfiókjában való hosszú távú tárolásérdekében. A régi fürt törlésekor a memáruházok újrahasznosításra kerülnek, ami adatvesztést jelenthet. Ha manuálisan szeretné kiüríteni az egyes tábla memstore-ját lemezre, futtassa a következő parancsfájlt. A parancsfájl legújabb verziója az Azure [GitHubján](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)található.

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

1. A régi HBase-fürtbe történő betöltés leállítása.

1. Annak érdekében, hogy a memstore-ban lévő legutóbbi adatok kiürítése megtörténjen, futtassa újra az előző parancsfájlt.

1. Jelentkezzen be az [Apache Ambari](https://ambari.apache.org/) `https://OLDCLUSTERNAME.azurehdidnsight.net`szolgáltatásba a régi fürtön ( ) és állítsa le a HBase-szolgáltatásokat. Amikor a rendszer kéri a szolgáltatások leállításának megerősítését, jelölje be a Bejelölést a HBase karbantartási módjának bekapcsolásához. Az Ambari hoz való csatlakozásról és az Ambari használatával kapcsolatos további tudnivalókért olvassa el [a HDInsight-fürtök kezelése az Ambari webfelhasználói felület használatával című témakört.](../hdinsight-hadoop-manage-ambari.md)

    ![Az Ambari ban kattintson a Szolgáltatások > HBase > Leállítása elemre a Szolgáltatási műveletek csoportban](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Jelölje be a HBase karbantartási módjának bekapcsolása jelölőnégyzetet, majd erősítse meg a](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Jelentkezzen be az Ambariba az új HDInsight-fürtön. Módosítsa `fs.defaultFS` a HDFS-beállítást úgy, hogy az eredeti fürt által használt tárolónévre mutasson. Ez a beállítás a **HDFS > Configs > Advanced > Advanced core-site**területen található.

    ![Az Ambari ban kattintson a Szolgáltatások > a HDFS > a Speciális > konfigurációs szolgáltatások elemre.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Az Ambari ban módosítsa a tároló nevét](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Ha nem használja a HBase-fürtöket az Enhanced Writes szolgáltatással, hagyja ki ezt a lépést. Csak a Továbbfejlesztett írási funkcióval rendelkező HBase-fürtök esetében szükséges.

   Módosítsa `hbase.rootdir` úgy az elérési utat, hogy az eredeti fürt tárolójára mutasson.

    ![Az Ambari ban módosítsa a HBase rootdir tárolónevét](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Ha a HDInsight 3.6-ot 4.0-ra frissíti, kövesse az alábbi lépéseket, különben ugorjon a 10.
    1. Indítsa újra az összes szükséges szolgáltatást az Ambari-ban a **Szolgáltatások** > **újraindítása minden szükséges lehetőség**kiválasztásával.
    1. Állítsa le a HBase szolgáltatást.
    1. SSH a Zookeeper csomópont, és végrehajtja a `rmr /hbase-unsecure` [zkCli](https://github.com/go-zkcli/zkcli) parancsot, hogy távolítsa el a HBase gyökér znode a Zookeeper.
    1. Indítsa újra a HBase-t.

1. Ha a 4.0-s verzión kívül más HDInsight-verzióra frissít, kövesse az alábbi lépéseket:
    1. Mentse a módosításokat.
    1. Indítsa újra az összes szükséges szolgáltatást az Ambari jelzésének szerint.

1. Irányítsa az alkalmazást az új fürtre.

    > [!NOTE]  
    > Az alkalmazás statikus DNS-e frissítéskor megváltozik. A DNS szigorú kódolása helyett a tartománynév DNS-beállításaiban konfigurálhat egy CNAME nevet, amely a fürt nevére mutat. Egy másik lehetőség, hogy egy konfigurációs fájlt az alkalmazás, amely újratelepítés nélkül frissíthető.

1. Indítsa el a betöltést, és nézze meg, hogy minden a várt módon működik-e.

1. Ha az új fürt megfelelő, törölje az eredeti fürtöt.

## <a name="next-steps"></a>További lépések

Az Apache [HBase](https://hbase.apache.org/) szolgáltatásról és a HDInsight-fürtök frissítéséről az alábbi cikkekben olvashat bővebben:

* [HDInsight-fürt frissítése újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Az Azure HDInsight figyelése és kezelése az Apache Ambari webes felhasználói felületével](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop-összetevők és -verziók](../hdinsight-component-versioning.md)
* [Konfigurációk optimalizálása az Apache Ambari használatával](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
