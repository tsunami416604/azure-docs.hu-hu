---
title: A HBase fürt replikálása az Azure virtuális hálózat beállítása |} Microsoft Docs
description: 'Útmutató: a HBase-replikálás egy HDInsight-verzióról a másikra terheléselosztás, magas rendelkezésre állású, nulla állásidő áttelepítési és a frissítések és vész-helyreállítási beállítása.'
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: c28c48b5842deec9d9c3898c5742c3d4d473094e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>A HBase fürt replikálása az Azure virtuális hálózat beállítása

Ismerje meg, hogyan állíthat be a virtuális hálózaton belül, vagy az Azure-ban két virtuális hálózatok közötti HBase-replikálás.

Fürt replikáció forrás-ügyfélleküldéses módszer használ. A cél- és a HBase-fürtöt lehet, vagy teljesíteni tudja mindkét szerepkörök egyszerre. Replikáció aszinkron. A replikáció célja végleges konzisztencia. Ha a forrás egy oszlop családhoz szerkesztési kap, ha engedélyezve van a replikáció, a Szerkesztés az összes cél fürtök propagálja. Ha adat replikálódik a az egyik fürtről a másikra, a forrás fürt és az összes fürt, amely már rendelkezik felhasznált adatok nyomon követi, replikációs hurkok megelőzése érdekében.

Ebben az oktatóanyagban beállította a forrás-cél replikációt. Más fürt topológiája esetén tekintse meg a [Apache HBase referencia-útmutató](http://hbase.apache.org/book.html#_cluster_replication).

A HBase replikációs használati esetekben egyetlen virtuális hálózat a következők:

* Terheléselosztás. Például vizsgálatok vagy MapReduce-feladatok futtatása a cél fürtön, és a kiindulási fürt adatok.
* Magas rendelkezésre állású hozzáadása.
* Adatok áttelepítése a HBase egyik fürtről a másikra.
* Azure HDInsight-fürtök egy-es verzióról a másikra.

A HBase replikációs használati esetek két virtuális hálózatok a következők:

* Vész-helyreállítási beállítása.
* Terheléselosztás és az alkalmazás particionálást.
* Magas rendelkezésre állású hozzáadása.

A fürtök replikálhatja [parancsfájl-művelet](../hdinsight-hadoop-customize-cluster-linux.md) parancsfájl [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [egy Azure ingyenes próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>A környezetek beállítása

Konfigurációs három lehetőség közül választhat:

- A HBase fürtök egy Azure virtuális hálózatban.
- Két HBase-fürtökkel a két különböző virtuális hálózatokon ugyanabban a régióban.
- Két HBase-fürtökkel a két különböző virtuális hálózatokon két különböző régiókban (georeplikáció).

Segítségével állítsa be a környezetben, néhány létrehoztunk [Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md). Ha inkább a környezetek beállítása más módszerekkel, lásd:

- [Hdinsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
- [Hozzon létre HBase-fürtökkel Azure virtuális hálózat](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Egy virtuális hálózat beállítása

Az azonos virtuális hálózatban két HBase fürtök létrehozásához válassza ki az alábbi képen. A sablon tárolódik [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Két virtuális hálózat ugyanabban a régióban beállítása

Hozzon létre két virtuális hálózat virtuális hálózati társviszony-létesítés és ugyanabban a régióban két HBase-fürtökhöz, jelölje be az alábbi képen. A sablon tárolódik [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Ebben az esetben [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md). A sablon lehetővé teszi, hogy a virtuális hálózati társviszony-létesítés.   

HBase-replikálás ZooKeeper virtuális gépek IP-címeket használ. Be kell állítania statikus IP-címek a cél HBase ZooKeeper csomópontok.

**Statikus IP-címek konfigurálása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza **erőforráscsoportok**.
3. Válassza ki az erőforráscsoportot, amely rendelkezik a cél HBase-fürtöt. Ez az az erőforráscsoport, amelyet a környezet létrehozása a Resource Manager-sablon használatakor. A szűrő segítségével szűkítheti a listát. Az erőforrások, amelyek tartalmazzák a két virtuális hálózatok listáját láthatja.
4. Válassza ki a virtuális hálózat, amely a célként megadott HBase-fürtöt tartalmaz. Válassza például **xxxx-vnet2**. Kezdetű névvel rendelkező három eszközök **nic-zookeepermode -** vannak felsorolva. Az eszközöket a három ZooKeeper virtuális gépek.
5. A ZooKeeper virtuális gépek közül.
6. Válassza ki **IP-konfigurációk**.
7. A listában válassza ki a **ipConfig1**.
8. Válassza ki **statikus**, és másolja, vagy írja le a tényleges IP-címet. Az IP-címet kell a replikáláshoz parancsfájlművelet futtatásakor.

  ![HDInsight HBase-replikálás ZooKeeper statikus IP-cím](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Ismételje meg a 6. a két ZooKeeper csomópontok a statikus IP-címének beállítása.

A kereszt-virtuális hálózat forgatókönyv kell használnia a **- ip** váltani, ha meghívja a `hdi_enable_replication.sh` parancsfájl-művelet.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Két különböző régiókban két virtuális hálózat beállítása

A két különböző régiókban, és a VPN-kapcsolatot, a Vnetek között két virtuális hálózatok létrehozásához kattintson az alábbi képen. A sablon tárolódik [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Bizonyos a sablonban változtatható értékek:

**Virtuális hálózat 1**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA nyugati régiója |
| VNet neve | &lt;ClusterNamePrevix >-vnet1 |
| Címelőtag terület | 10.1.0.0/16 |
| Alhálózat neve | 1. alhálózata |
| Alhálózati előtag | 10.1.0.0/24 |
| Alhálózat (átjáró) neve | A GatewaySubnet (nem módosítható) |
| (Átjáró) alhálózati előtag | 10.1.255.0/27 |
| Átjáró neve | vnet1gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típus | Útvonalalapú |
| Átjáró Termékváltozat | Alapszintű |
| átjáró IP | vnet1gwip |
| Fürt neve | &lt;ClusterNamePrefix > 1 |
| Fürt verziója | 3.6 |
| Fürt típusa | a hbase |
| A fürt feldolgozó csomópontok száma | 2 |


**Virtuális hálózat 2**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA keleti régiója |
| VNet neve | &lt;ClusterNamePrevix >-vnet2 |
| Címelőtag terület | 10.2.0.0/16 |
| Alhálózat neve | 1. alhálózata |
| Alhálózati előtag | 10.2.0.0/24 |
| Alhálózat (átjáró) neve | A GatewaySubnet (nem módosítható) |
| (Átjáró) alhálózati előtag | 10.2.255.0/27 |
| Átjáró neve | vnet2gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típus | Útvonalalapú |
| Átjáró Termékváltozat | Alapszintű |
| átjáró IP | vnet1gwip |
| Fürt neve | &lt;ClusterNamePrefix > 2. régiója |
| Fürt verziója | 3.6 |
| Fürt típusa | a hbase |
| A fürt feldolgozó csomópontok száma | 2 |

HBase-replikálás a ZooKeeper virtuális gépek IP-címét használja. Be kell állítania statikus IP-címek a cél HBase ZooKeeper csomópontok. Statikus IP-cím beállításához tekintse meg a [két virtuális hálózat ugyanabban a régióban beállítása](#set-up-two-virtual-networks-in-the-same-region) ebben a cikkben.

A kereszt-virtuális hálózat forgatókönyv kell használnia a **- ip** váltani, ha meghívja a `hdi_enable_replication.sh` parancsfájl-művelet.

## <a name="load-test-data"></a>Vizsgálati adatok betöltése

A replikált egy fürtöt, meg kell adnia a táblázatot, amely a replikálni kívánt. Ebben a szakaszban meg bizonyos adatok betöltése az a forrás fürt. A következő szakaszban akkor engedélyezi a fürtök közötti replikáció.

Létrehozásához egy **névjegyek** table és adatok beszúrása a táblázatban, kövesse az utasításokat, [HBase oktatóanyag: az Apache HBase hdinsightban első lépéseiben](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>A replikáció engedélyezése

A következő lépések írják le, hogyan hívhatja meg a parancsfájl parancsfájlművelet Azure-portálról. Parancsfájl műveletek futtatása az Azure PowerShell és az Azure parancssori eszköz (Azure CLI) használatával kapcsolatos információkért lásd: [testreszabása HDInsight-fürtök parancsfájlművelet használatával](../hdinsight-hadoop-customize-cluster-linux.md).

**Azure-portálról HBase-replikálás engedélyezése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a forrás HBase-fürtöt.
3. A fürt menüben válasszon ki **Parancsfájlműveletek**.
4. A lap tetején jelölje ki a **nyújt új**.
5. Válassza ki vagy adja meg a következő adatokat:

  1. **Név**: Adjon meg **engedélyezze a replikálást**.
  2. **Parancsprogram URL-Címének bash**: Adjon meg **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: Ellenőrizze, hogy ez van kijelölve. Törölje a csomóponttípusok.
  4. **Paraméterek**: A következő minta paraméterek engedélyezze a replikálást az összes meglévő táblázatban, és majd másolja a forrás fürt a fürt céltárhelyét minden adat:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Használja az állomásnevet FQDN helyett a forrás- és a fürt DNS-név.

6. Kattintson a **Létrehozás** gombra. A parancsfájl is igénybe vehet igénybe, különösen ha használja a **- copydata** argumentum.

Szükséges argumentumokkal:

|Name (Név)|Leírás|
|----|-----------|
|-s,--src-fürt | Adja meg a forrás HBase fürt DNS-nevét. Például: -s hbsrccluster, a fürt--src = hbsrccluster |
|-d, nyári időszámítás – a fürt | Megadja a cél (replika) HBase-fürtöt DNS-nevét. Például: -s dsthbcluster, a fürt--src = dsthbcluster |
|--src-ambari-jelszó - sp, | A kiindulási HBase fürt Ambari a rendszergazdai jelszót ad meg. |
|-dp, nyári időszámítás –--jelszavaknak | A HBase célfürtön Ambari a rendszergazdai jelszót ad meg.|

Választható argumentumok:

|Name (Név)|Leírás|
|----|-----------|
|--src-ambari-user - su, | Az Ambari rendszergazda felhasználónevét adja meg a kiindulási HBase fürt. Az alapértelmezett érték **admin**. |
|-du, nyári időszámítás –-ambari-felhasználó | Az Ambari rendszergazda felhasználónevét adja meg a cél HBase fürtön. Az alapértelmezett érték **admin**. |
|-t,--tábla-lista | Adja meg a táblák replikálni. Például:--tábla-lista = "table1; table2; Tábl3". Ha nem adja meg a táblák, az összes meglévő HBase táblák replikálódnak.|
|-m,--gép | Adja meg a parancsfájlművelet futtató átjárócsomópont. Az érték az az **hn1** vagy **hn0**. Mivel a **hn0** átjárócsomópont általában busier, azt javasoljuk, **hn1**. Használja ezt a beállítást, ha a számítógépén a $0 parancsfájl parancsfájl műveletként a HDInsight portálon vagy az Azure PowerShell.|
|-ip | Két virtuális hálózatok közötti replikáció engedélyezésekor van szükség. Ezt az argumentumot úgy működik, mint a statikus IP-címek ZooKeeper csomópontok replika fürtök használatára FQDN-nevek helyett egy kapcsolót. A statikus IP-címeket kell előkonfigurálásához, mielőtt engedélyezné a replikáció. |
|-cp, - copydata | Lehetővé teszi, hogy az áttelepítés a meglévő adatok a táblák, amelyben engedélyezve van a replikáció. |
|-fordulat/perces, - replikálás-phoenix-meta | Lehetővé teszi, hogy a replikációs Phoenix rendszertáblákra. <br><br>*Használja ezt a beállítást a kellő körültekintéssel járjon el.* Azt javasoljuk, hogy Ön hozza létre újból Phoenix táblák replika fürtökön használja ezt a parancsfájlt. |
|-h, – Súgó | Megjeleníti a használati adatokat. |

A `print_usage()` szakasza a [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) rendelkezik paraméterek részletes leírását.

A parancsfájlművelet sikeres telepítése után SSH segítségével csatlakozzon a cél HBase-fürtöt, és győződjön meg arról, hogy az adatok replikálása megtörtént.

### <a name="replication-scenarios"></a>A replikáció eseteire

Az alábbi listában láthatók az egyes általános használati esetek és a paraméter beállítások:

- **Minden tábla a fürtök közötti replikáció engedélyezése**. Ebben a forgatókönyvben nem szükséges a másolással vagy a tábla meglévő adatainak áttelepítése, és nem használ Phoenix táblákat. Használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Engedélyezze a replikálást a megadott táblák**. Ahhoz, hogy a replikáció table1 table2 és Tábl3, használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Engedélyezze a replikálást a megadott táblák, és másolja a meglévő adatok**. Ahhoz, hogy a replikáció table1 table2 és Tábl3, használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Minden tábla a replikáció engedélyezése és Phoenix metaadatok replikálása forrásból cél**. Phoenix metaadatok replikációs nincs tökéletes megoldás. Használja a körültekintően. Használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Másolja, majd az adatok áttelepítése

Nincsenek két külön parancsfájl művelet parancsfájlok másolása vagy adatok áttelepítése után a replikáció engedélyezett-e:

- [Kis táblák parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (méret, és a teljes másolása a néhány gigabájt táblákat várható befejezés kisebb, mint egy óra)

- [Nagy méretű táblákra vonatkozó parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (táblák, amelyek várhatóan tovább tart, mint egy órával másolása)

Az ismertetett eljárást kell követnie is [engedélyezze a replikálást](#enable-replication) a parancsfájl művelet hívására. Használja a következő paramétereket:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A `print_usage()` szakasza a [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) paraméterek részletes leírás.

### <a name="scenarios"></a>Forgatókönyvek

- **Adott táblákra (Teszt1 Teszt2 és Teszt3) másolja az összes sora szerkesztésének eddig (aktuális időbélyeg)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Vagy:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Másolja át az adott táblákon, amelyekhez egy adott időtartományt a**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>A replikálás letiltása

Tiltsa le a replikációt, hogy egy másik parancsfájl művelet parancsfájl használatát [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Az ismertetett eljárást kell követnie is [engedélyezze a replikálást](#enable-replication) a parancsfájl művelet hívására. Használja a következő paramétereket:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A `print_usage()` szakasza a [parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) rendelkezik paraméterek részletes leírását.

### <a name="scenarios"></a>Forgatókönyvek

- **Tiltsa le a replikációt minden tábla**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  vagy

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Tiltsa le a replikációt a megadott táblák (table1 table2 és Tábl3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan állíthat be a virtuális hálózaton belül, vagy két virtuális hálózatok közötti HBase-replikálás. HDInsight és HBase kapcsolatos további tudnivalókért tekintse meg az alábbi cikkek:

* [Ismerkedés az Apache HBase a hdinsight eszközben](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase áttekintése](./apache-hbase-overview.md)
* [Hozzon létre HBase-fürtökkel Azure virtuális hálózat](./apache-hbase-provision-vnet.md)

