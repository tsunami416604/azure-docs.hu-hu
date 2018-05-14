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
ms.date: 05/11/2018
ms.author: jgao
ms.openlocfilehash: 56b2b5ae9d3e4a0e682ec3dd47cd5cc30ebf6d58
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
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

Ez a cikk a georeplikáció forgatókönyvet ismertet.

Segítségével állítsa be a környezetben, néhány létrehoztunk [Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md). Ha inkább a környezetek beállítása más módszerekkel, lásd:

- [Hdinsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
- [Hozzon létre HBase-fürtökkel Azure virtuális hálózat](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Két különböző régiókban két virtuális hálózat beállítása

A két különböző régiókban, és a VPN-kapcsolatot, a Vnetek között két virtuális hálózatok létrehozásához válassza az alábbi képre kattintva hozzon létre a. A sablon tárolódik a [nyilvános blob-tároló]] (https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

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

## <a name="setup-dns"></a>A telepítő DNS

Utolsó szakaszában a sablon létrehozza a tartozó Ubuntu virtuális gép minden két virtuális hálózaton.  Ebben a szakaszban Bind telepítése a két DNS virtuális gépeken, és majd adja meg a DNS-továbbítást a két virtuális gép.

Kötési telepítéséhez yon kell a nyilvános IP-címének DNS két virtuális gépet.

1. Nyissa meg az [Azure portált](https://portal.azure.com).
2. Nyissa meg a DNS-virtuális gép kiválasztásával **erőforráscsoport > [erőforráscsoport neve] > [vnet1DNS]**.  Az erőforráscsoport neve, a egy, az utolsó eljárás hoz létre. Az alapértelmezett DNS-virtuális gép nevek *vnet1DNS* és *vnet2NDS*.
3. Válassza ki **tulajdonságok** a virtuális hálózati Tulajdonságok lapjának megnyitásához.
4. Jegyezze fel a **nyilvános IP-cím**, és azt is ellenőrizze a **magánhálózati IP-cím**.  A magánhálózati IP-címet kell **10.1.0.4** a vnet1DNS és **10.2.0.4** vnet2DNS számára.  

A kötés telepítéséhez használja az alábbi eljárást:

1. SSH használatával csatlakozhat a __nyilvános IP-cím__ a DNS-virtuális gép. Az alábbi példában egy virtuális gépet, 40.68.254.142 csatlakozik:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Cserélje le `sshuser` a DNS-virtuális gép létrehozásakor megadott SSH a felhasználói fiókkal.

    > [!NOTE]
    > Nincsenek az sokféleképpen beszerzése a `ssh` segédprogram. A Linux, Unix és macOS Ez biztosítja az operációs rendszer részeként. Ha Windows használja, fontolja meg az alábbi lehetőségek közül:
    >
    > * [Azure-felhőbe rendszerhéj](../../cloud-shell/quickstart.md)
    > * [A Windows 10 Ubuntu bash](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git)https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. A kötés telepítéséhez használja az SSH-munkamenetet a következő parancsokat:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Bind továbbítani a névfeloldási kérelmeket a helyszíni DNS-kiszolgáló konfigurálásához használja a következő szöveget a tartalmát a `/etc/bind/named.conf.options` fájlt:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16 #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Cserélje le az értékeket a `goodclients` a két virtuális hálózat az IP-címtartománnyal rendelkező szakasza. Ebben a szakaszban határozza meg a címeket, DNS-kiszolgáló elfogadja az érkező kérelmeket.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

4. Az SSH-munkamenetből használja a következő parancsot:

    ```bash
    hostname -f
    ```

    Ez a parancs értéket ad vissza az alábbihoz hasonló:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a __DNS-utótag__ ehhez a virtuális hálózathoz. Mentse ezt az értéket, a rendszer később.

    Is található, a DNS-utótagot, a más DNS-kiszolgálóról. A következő lépésben van szükség.

5. Konfigurálja a DNS-nevek a virtuális hálózaton lévő erőforrások köti, használja a következő szöveg tartalmát a `/etc/bind/named.conf.local` fájlt:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Le kell cserélnie a `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` együtt a virtuális hálózat DNS-utótagját. És a továbbító IP-cím a magánhálózati IP-címét a DNS-kiszolgáló a virtuális hálózat része.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

6. Indítsa el a kötési, használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Győződjön meg arról, hogy a kötés képes névfeloldásra a másik virtuális hálózatán lévő erőforrásokat, használja a következő parancsokat:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > Cserélje le `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` , a DNS virtuális gépet a többi hálózati teljesen minősített tartománynevét (FQDN).
    >
    > Cserélje le `10.2.0.4` rendelkező a __belső IP-cím__ a egyéni DNS-kiszolgáló más virtuális hálózatban.

    A válasz megjelenik az alábbihoz hasonló:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Eddig más hálózati nélkül megadott DNS-kiszolgáló IP-cím az IP-cím nem meg.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Az egyéni DNS-kiszolgáló használatára a virtuális hálózat konfigurálása

A virtuális hálózat az egyéni DNS-kiszolgáló használata helyett az Azure rekurzív feloldó konfigurálásához használja az alábbi lépéseket:

1. Az a [Azure-portálon](https://portal.azure.com), a virtuális hálózat, majd válassza ki és __DNS-kiszolgálók__.

2. Válassza ki __egyéni__, és írja be a __belső IP-cím__ az egyéni DNS-kiszolgáló. Végül válassza ki __mentése__.

6. Nyissa meg a DNS-kiszolgáló virtuális gép vnet1, és kattintson **indítsa újra a**.  A virtuális hálózatban, hogy a DNS-konfiguráció életbe léptetéséhez újra kell indítani a virtuális gépeket.
7. Ismételje meg a lépéseket a vnet2 egyéni DNS-kiszolgáló konfigurálása.

A DNS-konfiguráció teszteléséhez az SSH használatával két DNS virtuális számítógépekhez kapcsolódni, és a virtuális hálózat DNS-kiszolgáló pingelése állomásnevének segítségével. Ha nem működik, a következő paranccsal DNS állapotának ellenőrzéséhez:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>A HBase-fürtök létrehozása

Hozzon létre HBase-fürtöt minden két virtuális hálózaton a következő beállításokkal:

- **Az erőforráscsoport neve**: létrehozta a virtuális hálózatok az azonos erőforráscsoport nevét használja.
- **Fürt típusa**: HBase
- **Verzió**: HBase 1.1.2 (HDI 3.6)
- **Hely**: ugyanazt a helyet használja a virtuális hálózatnak.  Alapértelmezés szerint vnet1 van *USA nyugati régiója*, és vnet2 *USA keleti régiója*.
- **Tárolási**: hozzon létre egy új tárfiókot, a fürt számára.
- **Virtuális hálózati** (a Speciális beállítások a portál): Jelölje ki a legutóbbi eljárásban létrehozott vnet1.
- **Alhálózati**: az alapértelmezett név a sablonban használt **Alhalozat_1**.

Győződjön meg arról, a környezet megfelelően van konfigurálva, kell tudni pingelni a headnode FQDN a fürtök közötti.

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

