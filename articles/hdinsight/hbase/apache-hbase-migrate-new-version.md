---
title: Új verzió - Azure HDInsight HBase-fürtöt áttelepítése |} Microsoft Docs
description: Hogyan telepítheti át a HBase-fürtökkel új verzióra.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 2ab0bc792777b73f878bc1728c0d8310ecf41167
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>HBase-fürtöt egy új verziójára való áttérést

Feladat-alapú fürtök esetében például a külső és a Hadoop, egyszerű frissítése – lásd: [újabb verzióra való frissítés HDInsight fürt](../hdinsight-upgrade-cluster.md):

1. Átmeneti (helyileg tárolt) adatok biztonsági mentését.
2. Törölje a meglévő fürtből.
3. Hozzon létre egy új fürt virtuális hálózat ugyanazon az alhálózaton.
4. Átmeneti adatok importálása.
5. Indítsa el a feladatok, és folytatja a műveletet az új fürtön.

HBase-fürtöt frissítése néhány további lépésekre van szükség, a cikkben leírtak szerint.

> [!NOTE]
> Frissítésekor az állásidő minimális, perc terabájtok rendelése kell lennie. Üzemszünet kiüríteni a memóriában lévő összes adatot, majd konfigurálja, és indítsa újra az új fürtön a szolgáltatásokat ideje okozza a lépéseket. Az eredmények csomópontok, adatok mennyisége és más változók számától függően változnak.

## <a name="review-hbase-compatibility"></a>Tekintse át a HBase kompatibilitási

A HBase alkalmazásra történő verziófrissítés előtt győződjön meg arról, a HBase-verziók a forrás és cél fürtökön kompatibilisek. További információkért lásd: [Hadoop-összetevők és a hdinsight eszközzel verziók](../hdinsight-component-versioning.md).

> [!NOTE]
> Erősen ajánlott, hogy tekintse át az a verzió-kompatibilitási mátrix a [HBase könyv](https://hbase.apache.org/book.html#upgrading).

Íme egy példa verzió kompatibilitási mátrix, ahol Y jelzi kompatibilitási, és N jelzi az esetleges kompatibilitási:

| Kompatibilitási típussal | Főverzió| Alverzió | Javítás |
| --- | --- | --- | --- |
| Ügyfél-kiszolgáló átviteli kompatibilitási | N | I | I |
| Kiszolgálók kompatibilitási | N | I | I |
| Fájl kompatibilitása | N | I | I |
| Ügyfél API-ja kompatibilitási | N | I | I |
| Ügyfél bináris kompatibilitási | N | N | I |
| **Kiszolgálóoldali korlátozott API kompatibilitási** |  |  |  |
| Stable | N | I | I |
| Folyamatosan változó | N | N | I |
| Instabil | N | N | N |
| A függőségi kompatibilitási | N | I | I |
| Működési kompatibilitási | N | N | I |

> [!NOTE]
> A legfrissebb az azonosított inkompatibilitásokat le kell írni a HBase verzió kibocsátási megjegyzésekben.

## <a name="upgrade-with-same-hbase-major-version"></a>A HBase ugyanazon főverzióját frissítése

A következő forgatókönyv olyan 3.6 (egyaránt kapható Apache HBase 1.1.2) ugyanazzal a verzióval HBase fő HDInsight 3.4-es rendszerről. Más verziófrissítések hasonlítanak, mindaddig, amíg nincsenek kompatibilitási problémák a forrás és cél verziói között.

1. Győződjön meg arról, hogy az alkalmazás kompatibilis az új verzióval, ahogy az a HBase-kompatibilitási mátrix és kibocsátási megjegyzések. Az alkalmazás tesztelése a HDInsight és a HBase a cél verzióját futtató fürtre.

2. [Állítson be egy új cél HDInsight-fürt](../hdinsight-hadoop-provision-linux-clusters.md) használja ugyanazt a tárfiókot, azonban egy másik tároló neve:

    ![Ugyanazt a tárfiókot használja, de egy másik tároló létrehozása](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Ürítse ki a forrás HBase-fürtöt. Ez az a fürt, ahol frissíti. A HBase bejövő adatokat ír egy memórián belüli tároló nevű egy _kapott_. Miután a kérést kapott elér egy adott méretet, a kapott ki van-e ürítve lemezre, hosszú távú tárolás, a fürt tárfiókban. Törölje a régi fürtöt, amikor a memstores újrahasznosítására, potenciálisan adatvesztés. A kapott minden táblához manuálisan kiüríteni a lemezre, futtassa a következő parancsfájlt. A parancsfájl legújabb verziója, az Azure [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
4. Állítsa le a régi HBase fürt adatfeldolgozást.
5. Győződjön meg arról, hogy a legutóbbi egyetlen megadott adattal sem a kérést kapott ki van ürítve, futtassa újra a korábbi parancsfájl.
6. Ambari jelentkezzen be a régi fürtön (https://OLDCLUSTERNAME.azurehdidnsight.net) és állítsa le a HBase-szolgáltatásokat. Győződjön meg arról, hogy szeretné-e leállítania a szolgáltatásokat kérő, jelölje be a jelölőnégyzetet, a HBase a karbantartási mód bekapcsolása. Csatlakozás és az Ambari használatával további információkért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

    ![A Ambari kattintson a szolgáltatások fülre, majd a bal oldali menüben HBase, majd a műveletek a szolgáltatás leállítása](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Ellenőrizze a kapcsolja be a karbantartási mód HBase jelölőnégyzetet, majd erősítse meg](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Jelentkezzen be az új HDInsight-fürt Ambari. Módosítsa a `fs.defaultFS` HDFS beállítást úgy, hogy a tároló neve, az eredeti fürt által használt mutasson. Ez a beállítás alatt áll **HDFS > Configs > Speciális > core-hely speciális**.

    ![Ambari kattintson a szolgáltatások lapon, majd a bal oldali menüből, majd a Configs fülre, majd a Speciális lap alá a HDFS](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Az Ambari módosítsa a tároló neve](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Mentse a módosításokat.
9. Indítsa újra az összes szükséges szolgáltatást, Ambari jelöli.
10. Mutasson az alkalmazás az új fürt számára.

    > [!NOTE]
    > A statikus DNS a frissítéskor alkalmazásmódosítás. Ahelyett, hogy a rögzített megadás erre a DNS-, konfigurálhatja egy olyan CNAME REKORDOT a tartománynév DNS-beállítások mutat, a fürt nevét. Egy másik lehetőség, hogy a konfigurációs fájlt használja az alkalmazás újbóli telepítése nélkül frissíthető.

11. Indítsa el az adatfeldolgozást megjelenítéséhez, ha minden működését.
12. Ha az új fürt kielégítő, az eredeti fürt törlése.

## <a name="next-steps"></a>További lépések

A HBase és a HDInsight-fürtök frissítésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight-fürtök frissítése újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Megfigyelés és kezelés Azure HDInsight az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md)
* [Hadoop-összetevők és verziói](../hdinsight-component-versioning.md)
* [Ambari használó konfigurációk optimalizálása](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
