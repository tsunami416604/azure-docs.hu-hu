---
title: A HBase és a Phoenix biztonsági mentés és a replikáció - Azure HDInsight beállítása |} Microsoft Docs
description: Biztonsági mentés és a HBase és a Phoenix replikációs beállítása.
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
ms.openlocfilehash: f43edaf16784e5ba5cc3d2b39df285582954210b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165332"
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>Biztonsági mentés és a HBase és a HDInsight Phoenix replikáció beállítása

A HBase esetlegesen korán adatvesztéssel szembeni támogatja több megközelítés közül:

* Másolás a `hbase` mappa
* Exportálja majd importálja
* Táblázatok másolása
* Pillanatképek
* Replikáció

> [!NOTE]
> Apache Phoenix, hogy a metaadatok biztonsági mentése biztonsági mentésekor a rendszer katalógus HBase táblákat tárolja a HBase táblákat, a metaadatokat.

A következő szakaszok ismertetik a használati forgatókönyvtől ezen módszerek mindegyikéhez.

## <a name="copy-the-hbase-folder"></a>Másolja a hbase-mappát

Ezt a módszert használja minden HBase adatok, anélkül, hogy tudni jelölni egy részhalmazát táblák vagy oszlopcsaláddal másolja. További szempontok nagyobb ellenőrzést nyújtanak.

A HDInsight HBase a fürt, vagy az Azure Storage blobs, vagy az Azure Data Lake Store létrehozásakor kijelölt alapértelmezett a tárolót használja. Mindkét esetben a HBase tárolja az adatok és metaadatok fájlok a következő elérési úton:

    /hbase

* Az Azure Storage-fiók a `hbase` mappája a blob-tároló gyökerében található:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* Az Azure Data Lake Store a `hbase` mappában található a fürt kiépítésekor a megadott elérési útjának gyökeréhez. Az útvonalgyökér jellemzően rendelkezik egy `clusters` mappában, a HDInsight-fürt után egy almappát:

    ```
    /clusters/<clusterName>/hbase
    ```

Mindkét esetben a `hbase` mappa tartalmazza, amely a HBase kiürítésekor lemezre összes adatot, de nem tartalmazza a memóriában lévő adatokat. Mielőtt ezt a mappát, az a HBase adatok pontos ábrázolását számíthat, le kell állítani a fürtöt.

A fürt törlése után hagyja meg az adatokat a hely, vagy másolja az adatokat egy új helyre:

* Hozzon létre egy új HDInsight-példányt, a jelenlegi tárolási helyre mutat. Az új példány jön létre a meglévő adatokat.

* Másolás a `hbase` mappa egy másik Azure Storage blob-tároló vagy a Data Lake Store helyét, és indítsa el az új fürt tárolt. Az Azure Storage használata [AzCopy](../../storage/common/storage-use-azcopy.md), és a Data Lake Store használatra [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportálja majd importálja

A forrás HDInsight-fürt a segédprogrammal Export (a HBase része) exportál adatokat az forrástábla a csatolt alapértelmezett tároló. Ezután másolja az exportált mappát a rendeltetési tárolási helyre, és futtassa a importálási segédprogramot a cél a HDInsight-fürtön.

A tábla első SSH-ból a forrás HDInsight-fürt átjárócsomópontjához exportálását, és futtassa a következő `hbase` parancs:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Importálja a tábla SSH a cél HDInsight-fürt átjárócsomópontjához, és futtassa a következő `hbase` parancs:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Adja meg a teljes exportálási elérési útját az alapértelmezett tároló vagy a csatlakoztatott tárolóval beállításokat. Például az Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Az Azure Data Lake Store szintaxisa:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Ezt a módszert kínál a tábla szintű részletesség. Dátumtartomány a sorokat tartalmazza, amely lehetővé teszi a folyamat Növekményesen végrehajtását is megadható. Minden dátum ezredmásodpercben van megadva a Unix epoch óta.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Vegye figyelembe, hogy meg kell adnia az egyes sorok exportálása verzióinak száma. Az összes verzió a dátumtartományban, adja meg `<numberOfVersions>` nagyobb, mint a maximális lehetséges sor verziók, például a 100000 értékre.

## <a name="copy-tables"></a>Táblázatok másolása

A CopyTable segédprogram másol adatokat a forrástábla, soronként, egy meglévő céltábla, mint a forrás ugyanazon séma. A célként megadott táblája lehet ugyanabban a fürtben vagy egy másik HBase-fürtöt.

A használja CopyTable a fürtön belüli SSH azokat a forrás HDInsight-fürt átjárócsomópontjához, majd futtassa a `hbase` parancs:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Egy tábla egy másik fürtre másolása CopyTable használatához vegye fel a `peer` kapcsoló és a cél fürt címe:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

A cél címe a következő három részből áll:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` például akkor ZooKeeper csomópontok, vesszővel tagolt listáját:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` a HDInsight az alapértelmezett érték 2181 és `<ZnodeParent>` van `/hbase-unsecure`, így a teljes `<destinationAddress>` lenne:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Lásd: [manuálisan a ZooKeeper kvórum listájának összeállítása](#manually-collect-the-zookeeper-quorum-list) ebben a cikkben talál részletes beolvasni ezeket az értékeket a HDInsight-fürthöz.

A CopyTable segédprogram is támogatja a paraméterek az időtartományt a sorok másolása megadásához és a oszlopcsaláddal részhalmazát másolása egy táblázatban. CopyTable által támogatott paraméterek teljes listájának megtekintéséhez futtassa CopyTable paraméterek nélkül:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable megvizsgálja az egész tábla tartalmat, amely a célként megadott táblája keresztül fogja másolni. Ez csökkentheti a HBase-fürtöt teljesítmény, amíg CopyTable hajt végre.

> [!NOTE]
> Automatizálható a táblák közötti másolását, tekintse meg a `hdi_copy_table.sh` a parancsfájl a [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) GitHub tárházából.

### <a name="manually-collect-the-zookeeper-quorum-list"></a>A ZooKeeper kvórum lista manuálisan gyűjtése

Ha mindkét HDInsight-fürtök az azonos virtuális hálózatban, korábban leírt, belső állomásnév automatikus. A HDInsight-fürtök két különálló virtuális hálózatokból VPN-átjáró által összekapcsolt CopyTable használatához szüksége lesz biztosítják a gazdagép és a kvórum a Zookeeper csomópontok IP-címeket.

A kvórum állomásnevek beszerzéséhez futtassa a következő curl-parancsot:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

A curl-parancsot egy JSON-dokumentum, a HBase konfigurációs adatait kéri le, és a grep parancs hatására az eredményobjektumoknak csak a "hbase.zookeeper.quorum" bejegyzés, például:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

A kvórum gazdagép nevének értéke a teljes karakterlánc jobb oldalán a kettőspont.

Ezek a gazdagépek számára az IP-címek lekéréséhez használja a fenti listán szereplő minden egyes állomás a következő curl-parancsot:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

A curl-parancsot a `<zookeeperHostFullName>` ZooKeeper-állomás, például a példa teljes DNS-neve `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. A parancs kimenetében tartalmaz az IP-cím a megadott állomás, például:

    100    "ip" : "10.0.0.9",

Miután összegyűjtötte az összes ZooKeeper csomópontok IP-címet a kvórum a, építse újra a cél címe:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

Ebben a példában:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Pillanatképek

A pillanatképek lehetővé teszik a vesz egy-időpontban a HBase datastore adatok biztonsági mentését. A pillanatképek minimális terhelés rendelkezik, és néhány másodpercen belül hajtható végre, mert egy pillanatkép művelet gyakorlatilag minden fájlok a tároló nevét, hogy azonnali rögzítése a metaadat-művelet. A pillanatkép időpontjában nem tényleges adatok másolásakor. A pillanatképek a HDFS, ahol frissítések, törlés és Beszúrás összes jelentésekként jelennek meg új adatok tárolt adatokat nem módosítható jellege támaszkodnak. Visszaállíthatja (*Klónozás*) pillanatkép ugyanazon fürt, vagy a pillanatkép exportálása egy másik fürthöz.

Pillanatkép, az SSH-kapcsolatot a HDInsight HBase átjárócsomópontjához létrehozása a fürt és a kezdő a `hbase` rendszerhéj:

    hbase shell

A hbase rendszerhéjból a pillanatkép paranccsal és a tábla és a pillanatkép nevét:

    snapshot '<tableName>', '<snapshotName>'

Egy pillanatkép visszaállítása belül név szerint a `hbase` rendszerhéj, először tiltsa le a táblázatban, akkor a pillanatkép visszaállítása és engedélyezze újra a táblázat:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Állítson vissza olyan pillanatképet egy új táblába, használja a clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Exportálja a pillanatkép HDFS használható a fürt egy másik, először készítsen pillanatképet a korábban leírt, és ezután a ExportSnapshot segédprogrammal. Futtassa ezt a segédprogramot a az SSH-munkameneten belül az átjárócsomóponthoz nem belül a `hbase` rendszerhéj:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

A `<hdfsHBaseLocation>` értéke lehet bármely, a tárolóhelyek a kiindulási fürt számára, és a hbase mappához a cél fürt által használt kell mutatnia. Például ha egy másodlagos Azure Storage-fiók, a forrás fürt csatolva van, és a fiók férhet hozzá a tárolóhoz, az alapértelmezett tároló a cél fürt által használt, használhatja ezt a parancsot:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

A pillanatkép exportálása után SSH-ból a cél fürtnek és a visszaállítási a pillanatképet a restore_snapshot segítségével átjárócsomópontjához leírt módon parancsot.

Pillanatképek adjon meg egy tábla egy teljes biztonsági mentés idején a `snapshot` parancsot. A pillanatképek nem biztosítanak lehetővé teszi Windows idő növekményes pillanatképek végrehajtásához, sem pedig adja meg az oszlopok családok ahhoz, hogy a pillanatkép szerepeljen részhalmaza.

## <a name="replication"></a>Replikáció

HBase-replikálás automatikusan kimenő tranzakciók forrás fürtről egy cél fürtre, egy aszinkron mechanizmus használatával, a kiindulási fürt minimális terhelés mellett. A HDInsight fürtök közötti replikáció beállíthat ahol:

* A forrás és cél fürtök szerepelnek, az azonos virtuális hálózatban.
* A forrás- és a célhelyek fürtök a VPN-átjáró által összekapcsolt különböző virtuális hálózatokon, de mindkét fürt azonos földrajzi helyen található.
* A forrás fürt és a célhelyek fürtök a VPN-átjáró által összekapcsolt különböző virtuális hálózatokon és az egyes fürtökön létezik különböző földrajzi helyen.

Replikációjának általános lépések a következők:

1. A kiindulási fürt a táblák létrehozásához és adatok feltöltése.
2. A fürt céltárhelyét hozzon létre üres céltáblák a forrástábla sémával.
3. A forrás fürt egy társközi a célfürt regisztrálásához.
4. A kívánt forrástáblákból a replikáció engedélyezése.
5. Meglévő adatok másolása a forrástáblákból a céltábla.
6. Replikáció automatikusan másolja a forrástáblákból új adatok módosításait a céltábla.

A HDInsight replikáció engedélyezéséhez a futó forrás HDInsight-fürt a parancsfájlművelet vonatkozik. A replikáció a fürtben, vagy a replikáció a virtuális hálózatokon, Azure Resource Manager-sablonok használatával létrehozott minta fürtök kísérletezhet bemutatóért lásd: [konfigurálása HBase-replikálás](apache-hbase-replication.md). Cikkben is tartalmaz útmutatásokat Phoenix metaadatok replikálása az engedélyezése.

## <a name="next-steps"></a>További lépések

* [HBase-replikáció konfigurálása](apache-hbase-replication.md)
