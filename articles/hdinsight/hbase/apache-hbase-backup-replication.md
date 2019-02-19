---
title: Az Apache HBase- és Apache Phoenix biztonsági mentésének és replikációjának – Azure HDInsight beállítása
description: Biztonsági mentés és replikáció a HBase és a Phoenix beállítása.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: d182d23bf4b3f4dc1ed42a737e8fe8b753c035ae
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340738"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Állítsa be a biztonsági mentés és replikáció a Apache HBase és a HDInsight az Apache Phoenixhez

Az Apache HBase számos megközelítés az adatvesztéssel szembeni esetlegesen korán támogatja:

* Másolás a `hbase` mappa
* Exportálja majd importálja
* Tábla másolása
* Pillanatképek
* Replikáció

> [!NOTE]  
> Az Apache Phoenix, hogy a metaadatok biztonsági mentése biztonsági mentésekor a rendszer katalógus HBase táblákat tárolja a HBase-táblákat, a metaadatokat.

A következő szakaszok ismertetik a használati forgatókönyvtől függ, ezek a módszerek mindegyike esetében.

## <a name="copy-the-hbase-folder"></a>Másolja a hbase-mappát

Ezt a módszert használja a Másolás összes HBase, anélkül, hogy táblák vagy oszlopcsaláddal egy adott sorkészletét jelölik ki. Későbbi megközelítések nagyobb ellenőrzést biztosít.

A HDInsight HBase használja az alapértelmezett tároló, a fürt, vagy az Azure Storage-blobokkal, vagy az Azure Data Lake Storage létrehozásakor kiválasztott. Mindkét esetben a HBase az adatoknak és metaadatoknak fájlok a következő elérési úton tárolja:

    /hbase

* Az Azure Storage-fiókot a `hbase` gyökerében található blob-tárolóban található mappa:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* Az Azure Data Lake Storage a `hbase` mappát egy fürt üzembe helyezésekor megadott legfelső szintű útvonalon található. A gyökér elérési útja általában rendelkezik egy `clusters` mappát egy olyan almappát, a HDInsight-fürt elnevezett:

    ```
    /clusters/<clusterName>/hbase
    ```

Mindkét esetben a `hbase` mappát tartalmaz minden olyan adat, amelyet a HBase kiürítésekor lemezre, de nem tartalmazza a memóriában lévő adatok. Mielőtt ezt a mappát, egy pontos adatokat választ, a HBase számíthat, le kell állítani a fürtöt.

Miután törli a fürtöt, hagyja meg az adatokat helyben, vagy is másolja az adatokat egy új helyre:

* Hozzon létre egy új HDInsight-példányt, a jelenlegi tárolási helyre mutat. Az új példány létrehozása a meglévő adatokat.

* Másolás a `hbase` mappára egy másik Azure Storage blob-tároló vagy a Data Lake Storage helyét, és indítsa el egy új fürtöt az adatokkal. Az Azure Storage esetében használjon [AzCopy](../../storage/common/storage-use-azcopy.md), és a Data Lake Storage használatra [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportálja majd importálja

A kiindulási HDInsight-fürt segédprogrammal az exportálási (tartalmazza a hbase-ben) a forrástábla adatainak exportálása a csatolt alapértelmezett tároló. Ezután másolja az exportált mappát a rendeltetési tárolási helyre, és futtassa az importálás segédprogramot a cél HDInsight-fürtön.

Egy tábla első SSH-t a forrás HDInsight-fürt fő csomópontjának exportálhatja, és futtassa a következőt `hbase` parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Importálja a tábla SSH a cél HDInsight-fürt fő csomópontjának, és futtassa a következőt `hbase` parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Adja meg a teljes exportálási elérési út az alapértelmezett tárolóba vagy a csatlakoztatott tárolási beállításokat. Ha például az Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Az Azure Data Lake Storage Gen2 szintaxisa:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

Az Azure Data Lake Storage Gen1 szintaxisa:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Ez a megközelítés táblaszintű granularitási kínál. Megadhat egy adott időtartományban történő a sorok, amely lehetővé teszi, hogy a folyamat végrehajtása növekményesen is. Az egyes dátumokat ezredmásodpercben van megadva a Unix alapidőpont óta.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Vegye figyelembe, hogy meg kell adnia a verziók exportálhatja az egyes sorok számát. Az összes verzió belefoglalni a dátumtartományt, állítsa `<numberOfVersions>` nagyobb, mint a maximális lehetséges sor verziók, például 100000 értékre.

## <a name="copy-tables"></a>Tábla másolása

A CopyTable segédprogram adatokat másol a forrástábla, soronként, ugyanazzal a sémával, mint a forrás-cél meglévő tábla. A céltábla lehetnek ugyanazon a fürtön vagy egy másik HBase-fürt.

CopyTable belül használja a fürt SSH be a forrás HDInsight-fürt fő csomópontjának, és futtassa ezt a `hbase` parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

CopyTable használatával egy másik fürtön egy táblába, adja hozzá a `peer` kapcsoló és a cél fürt címe:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

A cél címe az alábbi három részből áll:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` például az Apache ZooKeeper-csomópontok, vesszővel tagolt listája:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` a HDInsight, az alapértelmezett érték 2181 és `<ZnodeParent>` van `/hbase-unsecure`, így a teljes `<destinationAddress>` lenne:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Lásd: [manuálisan gyűjtése az Apache ZooKeeper kvórum lista](#manually-collect-the-apache-zookeeper-quorum-list) ebben a cikkben részletes tájékoztatás a HDInsight-fürthöz tartozó értékek lekérésére.

A CopyTable segédprogram paraméterek használatával adja meg az időtartományt, másolja, és adja meg a részhalmazát oszlopcsaláddal egy tábla másolása sort is támogatja. Paraméterek CopyTable által támogatott teljes listájának megtekintéséhez futtassa paraméterek nélkül CopyTable:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable megvizsgálja a teljes tábla tartalmat, amely a céltábla át lesznek másolva. Ez csökkentheti a HBase-fürt teljesítmény, CopyTable végrehajtása közben.

> [!NOTE]  
> Automatizálhatja a táblák közötti másolását, tekintse meg a `hdi_copy_table.sh` szkriptet a [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) tárházban a Githubon.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Az Apache ZooKeeper kvórum lista manuálisan gyűjtése

Ha mindkét HDInsight-fürt ugyanazon a virtuális hálózaton, korábban leírt, belső állomásnév-feloldás automatikusan sor kerül. HDInsight-fürtök két külön virtuális hálózat VPN-átjáró köti, CopyTable használatához szüksége lesz biztosítják a gazdagép és a Zookeeper-csomópontok a kvórum az IP-címét.

A kvórum állomásnevek szerez be, futtassa a következő curl-parancsot:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

A curl-parancs egy JSON-dokumentumok, a HBase konfigurációs adatait kérdezi le, és a grep parancs hatására az eredményobjektumoknak csak a "hbase.zookeeper.quorum" bejegyzés, például:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

A kvórum gazdagép nevének értéke a teljes karakterlánc jobb oldalán a kettőspont.

Ezeket a gazdagépeket az IP-címek lekéréséhez használja a következő curl-parancsot az előző listában található mindegyik gazdagéphez:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

A curl parancs `<zookeeperHostFullName>` ZooKeeper-gazdagép, például teljes DNS-neve `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. A parancs kimenete tartalmazza az IP-cím a megadott gazdagépre, például:

    100    "ip" : "10.0.0.9",

Miután összegyűjtötte a kvórum a ZooKeeper-csomópontok IP-címét, építse újra a cél címe:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

Ebben a példában:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Pillanatképek

A pillanatképek lehetővé teszi egy-időponthoz adatok biztonsági mentését is a HBase-adattárhoz. A pillanatképek minimális fenntartási rendelkezik, és végezze el a másodpercen belül, mivel egy pillanatkép-készítési művelet lényegében egy metaadat-művelet rögzítése a storage-ban minden fájl nevét, hogy azonnali. A pillanatkép időpontjában nem tényleges adatokat másolja. A pillanatképek a HDFS, ahol frissítéseket, törléseket és beszúrások összes jelentésekként jelennek meg az új adatok tárolt adatokat nem módosítható jellege támaszkodnak. Visszaállíthatja az (*Klónozás*) pillanatkép ugyanazon fürt, vagy a pillanatkép exportálása egy másik fürthöz.

Segítségével készít pillanatképet, SSH-t az átjárócsomóponthoz, a HDInsight HBase-fürt és a kezdő a `hbase` rendszerhéj:

    hbase shell

A hbase rendszerhéjból a pillanatkép paranccsal és a pillanatkép és a tábla nevét:

    snapshot '<tableName>', '<snapshotName>'

Název pillanatkép visszaállítása a `hbase` rendszerhéj-, először tiltsa le a táblázatban, majd a pillanatkép-visszaállítás, és engedélyezze újra a táblázat:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Pillanatkép visszaállítása egy új táblába, clone_snapshot használja:

    clone_snapshot '<snapshotName>', '<newTableName>'

Exportálandó pillanatkép HDFS használható egy másik fürt, először hozzon létre a pillanatkép előzőekben leírtak szerint, majd a ExportSnapshot segédprogramot. A segédprogram futtatásához az SSH-munkameneten belül az átjárócsomóponthoz, nem belül a `hbase` rendszerhéj:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

A `<hdfsHBaseLocation>` lehet bármely, a tárolóhelyek a forrás-fürtön elérhető-e, és a hbase mappához a cél-fürt által használt kell mutatnia. Például ha egy másodlagos Azure Storage-fiókot, a forrás fürt csatlakoztatva van, és a fiók hozzáférést nyújt a tárolóhoz az alapértelmezett tároló, a cél-fürt által használt, ezt a parancsot használhatja:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

A pillanatkép exportálását követően SSH-t a célul szolgáló fürtcsomópont és a használatával a restore_snapshot pillanatkép visszaállítása a fő csomópont parancs, a fent leírtaknak megfelelően.

Pillanatképek adjon meg egy tábla egy teljes biztonsági mentés idején a `snapshot` parancsot. A pillanatképek teszi lehetővé az idő Windows növekményes pillanatképek végrehajtásához, sem pedig adja meg a pillanatkép foglalandó oszlopok családok alkészleteiben.

## <a name="replication"></a>Replikáció

HBase-replikáció automatikusan leküldéses tranzakciók forrás fürtről egy célul szolgáló fürtcsomópont, minimális fenntartási a kiindulási fürt egy aszinkron mechanizmus használata. A HDInsight, a fürtök közötti replikáció beállíthat ahol:

* A forrás és cél fürt ugyanazon a virtuális hálózaton találhatók.
* A forrás- és a célhelyek fürtöket egy VPN-átjáró által csatlakoztatott különböző virtuális hálózatokban lévő, de mindkét fürt ugyanazon a földrajzi helyen található.
* A forrás fürt és a célhelyek fürtök vannak egy VPN-átjáró által csatlakoztatott különböző virtuális hálózatokban lévő, és minden egyes fürt egy másik földrajzi helyen lévő létezik.

A replikálás beállítása az általános lépések a következők:

1. A kiindulási fürt a táblákat hozhat létre, és töltse ki az adatokat.
2. Hozzon létre üres céltáblák a célul szolgáló fürtcsomópont a forrástábla sémával.
3. A forrás fürt társ a célul szolgáló fürtcsomópont regisztrálásához.
4. Engedélyezze a replikációt a kívánt forrástáblákban.
5. Meglévő adatokat másol a forrástáblákból a céltáblákba.
6. Replikáció automatikusan átmásolja az új adatok módosításait a forrástáblákból a céltáblákba.

A HDInsight a replikáció engedélyezéséhez a futó forrás HDInsight-fürt Szkriptműveletet vonatkozik. A replikálást, a fürtben, vagy a virtuális hálózatba tartozó Azure Resource Management-sablonok használatával létrehozott minta fürtökön replikációs kísérletezhet bemutatóért lásd: [konfigurálása az Apache HBase-replikálás](apache-hbase-replication.md). A cikk Phoenix-metaadatok replikálást utasításokat is tartalmaz.

## <a name="next-steps"></a>További lépések

* [Az Apache HBase-replikálás konfigurálása](apache-hbase-replication.md)
