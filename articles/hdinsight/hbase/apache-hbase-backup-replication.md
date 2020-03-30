---
title: Biztonsági mentés & replikáció az Apache HBase, Phoenix – Azure HDInsight rendszerhez
description: Az Apache HBase és az Apache Phoenix biztonsági mentésének és replikációjának beállítása az Azure HDInsightban
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495935"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Biztonsági mentés és replikáció beállítása az Apache HBase és az Apache Phoenix számára a HDInsighton

Az Apache HBase számos módszert támogat az adatvesztés elleni védelemhez:

* A `hbase` mappa másolása
* Exportálás, majd importálás
* Táblák másolása
* Pillanatképek
* Replikáció

> [!NOTE]  
> Az Apache Phoenix a metaadatait HBase-táblákban tárolja, így a metaadatokról biztonsági másolatot kell készíteni a HBase rendszerkatalógus-táblák biztonsági biztonsági másolatot.

A következő szakaszok ismertetik a használati forgatókönyv az egyes ilyen megközelítések.

## <a name="copy-the-hbase-folder"></a>A hbase mappa másolása

Ezzel a módszerrel az összes HBase-adatot másolhatja anélkül, hogy ki választhatna egy részhalmazt a táblákból vagy oszlopcsaládokból. A későbbi megközelítések nagyobb ellenőrzést biztosítanak.

A HBase a HDInsightban a fürt létrehozásakor kiválasztott alapértelmezett tárolót használja, akár az Azure Storage blobok, akár az Azure Data Lake Storage létrehozásakor. A HBase mindkét esetben a következő elérési út alatt tárolja az adatokat és a metaadatfájlokat:

    /hbase

* Egy Azure Storage-fiókban a `hbase` mappa a blobtároló gyökerében található:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* Az Azure Data Lake `hbase` Storage-ban a mappa a fürt kiépítésekor megadott gyökérelérési út alatt található. Ez a gyökérelérési `clusters` út általában egy mappával rendelkezik, amelynek almappája a HDInsight-fürtről kapta a nevét:

    ```
    /clusters/<clusterName>/hbase
    ```

A `hbase` mappa mindkét esetben tartalmazza az összes olyan adatot, amelyet a HBase lemezre ürített, de nem feltétlenül tartalmazza a memóriában lévő adatokat. Mielőtt a HBase-adatok pontos ábrázolásaként hivatkozhatna erre a mappára, le kell állítania a fürtöt.

A fürt törlése után az adatokat a helyén hagyhatja, vagy új helyre másolhatja az adatokat:

* Hozzon létre egy új HDInsight-példányt, amely az aktuális tárolási helyre mutat. Az új példány az összes meglévő adattal együtt jön létre.

* Másolja `hbase` a mappát egy másik Azure Storage blobtárolóba vagy a Data Lake Storage helyére, majd indítson el egy új fürtöt az adatokkal. Az Azure Storage használatához használja az [AzCopy](../../storage/common/storage-use-azcopy.md)és a Data Lake Storage használatát [az AdlCopy használatával.](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

## <a name="export-then-import"></a>Exportálás, majd importálás

A forrás HDInsight-fürtön a (HBase részét képező) [Exportálás segédprogrammal](https://hbase.apache.org/book.html#export) exportálhatja az adatokat egy forrástáblából az alapértelmezett csatolt tárolóba. Ezután átmásolhatja az exportált mappát a céltároló helyre, és futtathatja az [Importálás segédprogramot](https://hbase.apache.org/book.html#import) a cél HDInsight-fürtön.

A táblaadatok exportálásához először az SSH-t a forrás HDInsight-fürt főcsomópontjába, majd futtassa a következő `hbase` parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Az exportálási könyvtár nak már nem létezhet. A tábla neve nem imitot.

Táblaadatok importálásához az SSH a cél HDInsight-fürt főcsomópontjába, majd futtassa a következő `hbase` parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

A táblának már léteznie kell.

Adja meg a teljes exportálási útvonalat az alapértelmezett tárolóhoz vagy a csatolt tárolási lehetőségek bármelyikéhez. Például az Azure Storage-ban:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Az Azure Data Lake Storage Gen2 szintaxisa a következő:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

Az Azure Data Lake Storage Gen1 szintaxisa a következő:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Ez a megközelítés asztalszintű részletességet kínál. Megadhatja a sorokhoz tartozó dátumtartományt is, amely lehetővé teszi a folyamat növekményes elvégzését. Minden dátum ezredmásodpercben van a Unix korszak óta.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Ne feledje, hogy meg kell adnia az egyes sorok exportálandó verzióinak számát. Ha a dátumtartomány összes verzióját `<numberOfVersions>` fel szeretné vonni, állítsa a lehetséges sorverzióknál nagyobb értékre( például 100000).

## <a name="copy-tables"></a>Táblák másolása

A [CopyTable segédprogram](https://hbase.apache.org/book.html#copy.table) a forrástáblából sorról sorra másolja az adatokat egy meglévő céltáblába, amelynek sémája megegyezik a forráséval. A céltábla lehet ugyanazon a fürtön vagy egy másik HBase-fürtön. A táblanevekben a kis- és nagybetűk et nem lehet figyelembe.

A CopyTable fürtön belüli használatához az SSH-t a forrás HDInsight-fürt főcsomópontjába, majd futtassa a `hbase` következő parancsot:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Ha a CopyTable segítségével másik fürt táblájára `peer` szeretne másolni, adja hozzá a kapcsolót a célfürt címével:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

A célcím a következő három részből áll:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`az Apache ZooKeeper csomópontok vesszővel tagolt listája, például:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2o2oqawzlwevlfxgay2500xtg.dx.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`a HDInsight alapértelmezés szerint 2181, és `<ZnodeParent>` van `/hbase-unsecure`, így a teljes `<destinationAddress>` lenne:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay250 00xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2o2o2o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-nem biztonságos

Ebben a cikkben [az Apache ZooKeeper kvórumlistájának manuális gyűjtése](#manually-collect-the-apache-zookeeper-quorum-list) című témakörben olvashat részletesen arról, hogyan szerezheti be ezeket az értékeket a HDInsight-fürthöz.

A CopyTable segédprogram támogatja a másolandó sorok időtartományának megadására, valamint a másolandó tábla oszlopsorainak részhalmazának megadására is. A CopyTable által támogatott paraméterek teljes listájának megtekintéséhez futtassa a CopyTable programot paraméterek nélkül:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable ellenőrzi a teljes forrástábla tartalmát, amelyet a program átmásol a céltáblába. Ez csökkentheti a HBase fürt teljesítményét a CopyTable végrehajtása közben.

> [!NOTE]  
> Az adatok táblák közötti másolásának automatizálásához `hdi_copy_table.sh` tekintse meg a parancsfájlt az [Azure HBase-használat](https://github.com/Azure/hbase-utils/tree/master/replication) tárházában a GitHubon.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Az Apache ZooKeeper kvórumlistájának manuális gyűjtése

Ha mindkét HDInsight-fürt ugyanabban a virtuális hálózatban található, ahogy azt korábban leírtuk, a belső állomásnév feloldása automatikus. A CopyTable for HDInsight-fürtök használatát két különálló virtuális hálózatban, amelyeket egy VPN-átjáró köt össze, meg kell adnia a Zookeeper-csomópontok állomás IP-címét a kvórumban.

A kvórumállomásnevek beszerzéséhez futtassa a következő curl parancsot:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

A curl parancs beolvas egy JSON-dokumentumot hbase konfigurációs adatokkal, és a grep parancs csak a "hbase.zookeeper.quorum" bejegyzést adja vissza, például:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

A kvórumállomásnevek értéke a kettősponttól jobbra lévő teljes karakterlánc.

Az állomások IP-címeinek beolvasásához használja a következő curl parancsot az előző lista minden állomására vonatkozóan:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

Ebben a curl `<zookeeperHostFullName>` parancsban a ZooKeeper gazdagép teljes DNS-neve, például a példa. `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` A parancs kimenete tartalmazza a megadott állomás IP-címét, például:

    100    "ip" : "10.0.0.9",

Miután összegyűjtötte a kvórumban lévő összes ZooKeeper-csomópont IP-címét, építse újra a célcímet:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

A példánkban:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Pillanatképek

A pillanatképek lehetővé [teszik,](https://hbase.apache.org/book.html#ops.snapshots) hogy a HBase-adattárban lévő adatokról időben készítsen biztonsági másolatot. A pillanatképek minimális terheléssel rendelkeznek, és másodperceken belül befejeződnek, mivel a pillanatkép-művelet gyakorlatilag egy metaadat-művelet, amely rögzíti az adott pillanatban a tárolóban lévő összes fájl nevét. A pillanatkép időpontjában a program nem másol tényleges adatokat. A pillanatképek a HDFS-ben tárolt adatok megváltoztathatatlan jellegétől függenek, ahol a frissítések, a törlések és a beszúrások új adatként jelennek meg. Visszaállíthatja (*klónozhatja*) a pillanatképet ugyanazon a fürtön, vagy exportálhatja a pillanatképet egy másik fürtbe.

Pillanatkép létrehozásához az SSH a HDInsight HBase-fürt fő `hbase` csomópontjára lép, és indítsa el a rendszerhéjat:

    hbase shell

A hbase rendszerhéjon belül használja a pillanatkép parancsot a tábla és a pillanatkép nevével:

    snapshot '<tableName>', '<snapshotName>'

Ha név szerint szeretne `hbase` visszaállítani egy pillanatképet a rendszerhéjon belül, először tiltsa le a táblát, majd állítsa vissza a pillanatképet, és engedélyezze újra a táblát:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Ha új táblára szeretne pillanatképet visszaállítani, használja clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Ha egy pillanatképet szeretne exportálni a HDFS-be egy másik fürt általi használatra, először hozza létre a pillanatképet a korábban leírtak szerint, majd használja az ExportSnapshot segédprogramot. Futtassa ezt a segédprogramot az SSH munkameneten `hbase` belül a főcsomópontra, ne a rendszerhéjon belül:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

A `<hdfsHBaseLocation>` lehet a forrásfürt számára elérhető tárolóhelyek bármelyike, és a célfürt által használt hbase mappára kell mutatnia. Ha például egy másodlagos Azure Storage-fiók van csatlakoztatva a forrásfürthöz, és ez a fiók hozzáférést biztosít a célfürt alapértelmezett tárolója által használt tárolóhoz, ezt a parancsot használhatja:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

A pillanatkép exportálása után az SSH a célfürt fő csomópontjába, és a korábban leírt restore_snapshot parancs használatával visszaállítja a pillanatképet.

A pillanatképek teljes biztonsági másolatot nyújtanak `snapshot` egy tábláról a parancs időpontjában. A pillanatképek nem biztosítják a növekményes pillanatképek időbeli végrehajtásának lehetőségét, és nem határozzák meg az oszlopok családjainak a pillanatképbe foglalandó részhalmazait.

## <a name="replication"></a>Replikáció

[A HBase replikáció](https://hbase.apache.org/book.html#_cluster_replication) automatikusan lelöki a tranzakciókat a forrásfürtből a célfürtbe, a forrásfürtre minimális terhelést biztosító aszinkron mechanizmus használatával. A HDInsightban beállíthatja a fürtök közötti replikációt, ahol:

* A forrás- és célfürtök ugyanabban a virtuális hálózatban találhatók.
* A forrás- és célcsoportok különböző virtuális hálózatokban találhatók, amelyeket egy VPN-átjáró köt össze, de mindkét fürt ugyanazon a földrajzi helyen található.
* A forrásfürt- és célcsoportok különböző virtuális hálózatokban találhatók, amelyeket egy VPN-átjáró köt össze, és minden fürt más földrajzi helyen létezik.

A replikáció beállításának általános lépései a következők:

1. A forrásfürtön hozza létre a táblákat, és népesítse be az adatokat.
2. A célfürtön hozzon létre üres céltáblákat a forrástábla sémájával.
3. Regisztrálja a célfürtöt a forrásfürt társaként.
4. Engedélyezze a replikációt a kívánt forrástáblákon.
5. Másolja a meglévő adatokat a forrástáblákból a céltáblákba.
6. A replikáció automatikusan átmásolja az új adatmódosításokat a forrástáblákba a céltáblákba.

A HDInsight replikációjának engedélyezéséhez alkalmazzon parancsfájlműveletet a futó forrás HDInsight-fürtre. A fürt replikációjának engedélyezésére vonatkozó forgatókönyv, illetve az Azure Resource Management sablonokkal a virtuális hálózatokban létrehozott mintafürtökön történő replikációval való kísérletezésről az [Apache HBase replikáció konfigurálása](apache-hbase-replication.md)című témakörben olvashat. Ez a cikk a Phoenix metaadatok replikációjának engedélyezésére vonatkozó utasításokat is tartalmaz.

## <a name="next-steps"></a>További lépések

* [Apache HBase replikáció konfigurálása](apache-hbase-replication.md)
* [A HBase importálási és exportálási segédprogramhasználata](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
