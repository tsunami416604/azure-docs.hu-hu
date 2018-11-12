---
title: HBase-fürt replikációja az Azure virtuális hálózatok beállítása
description: Ismerje meg, hogyan lehet HBase-replikálás beállítása egy HDInsight-verzióról a másikra a terheléselosztást, magas rendelkezésre állású, üzemszünet nélküli áttelepítés és a frissítések és vész-helyreállítási.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: b978adcdcc025c24746167ef5ab92aebe94aca8b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016233"
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>HBase-fürt replikációja az Azure virtuális hálózatok beállítása

Ismerje meg, hogyan állítható be a HBase-replikáció a virtuális hálózaton belül, vagy az Azure-ban két virtuális hálózat között.

Fürt-replikáció az a forrás-küldéses metódussal. HBase-fürtöt a forrás vagy cél, vagy teljesíteni tudja mindkét szerepkört egyszerre. Replikáció az aszinkron. A replikációs célja végső konzisztenciát. Amikor a forrás és a egy oszlopcsalád szerkesztését kap, ha a replikáció engedélyezve van, a Szerkesztés az összes cél fürtök propagálja. Amikor adatok replikálódnak az egyik fürtről a másikra, a forrás fürt és az összes olyan fürtben, amely már használt fel, az adatok nyomon követi, replikációs hurkok elkerülése érdekében.

Ebben az oktatóanyagban beállította egy forrás-cél replikációs. Más fürt topológiákat, tekintse meg a [Apache HBase referencia-útmutató](http://hbase.apache.org/book.html#_cluster_replication).

A HBase replikációs használati eseteinek egyetlen virtuális hálózaton az alábbiak:

* Terheléselosztás. Például a célfürtön vizsgálatok és a MapReduce-feladatok futtatásához, és kiolvasni az adatokat a kiindulási fürt.
* Megjelenik a magas rendelkezésre állás.
* Egy HBase-fürt migrálása adatait egy másikba.
* Egy Azure HDInsight-fürt frissítése egyik verzióról a másikra.

A HBase replikációs használati eseteinek két virtuális hálózat a következők:

* Vészhelyreállítás beállítása.
* A terheléselosztást és az alkalmazás particionálás.
* Megjelenik a magas rendelkezésre állás.

Fürtök használatával replikálhatja [műveleti parancsfájl](../hdinsight-hadoop-customize-cluster-linux.md) parancsfájlokat [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [az Azure ingyenes próbaverziójára első](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>A környezetek beállítása

Három konfigurációs lehetőségek állnak rendelkezésére:

- Két HBase-fürtökhöz egy Azure virtuális hálózatot.
- Két HBase-fürtök két különböző virtuális hálózat ugyanabban a régióban.
- Két HBase-fürtök két különböző virtuális hálózat két különböző régióban (georeplikáció).

Ez a cikk foglalkozik: bemutatja a georeplikáció.

Segítséget nyújtanak a környezetek beállítása, hoztunk létre néhány [Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md). Ha inkább a környezetek beállítása az egyéb módszerek használatával, lásd:

- [A HDInsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
- [HBase-fürtök létrehozása az Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Két különböző régiókban lévő két virtuális hálózat beállítása

A sablont, amely két különböző régióban és a virtuális hálózatok közötti VPN-kapcsolatot hoz létre két virtuális hálózatot, jelölje be a következő **üzembe helyezés az Azure** gombra. A Sablondefiníció tárolja egy [nyilvános blobtárolóban](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Egyes változtatható értékek a sablonban:

**1 virtuális hálózat**

| Tulajdonság | Value (Díj) |
|----------|-------|
| Földrajzi egység | USA nyugati régiója |
| Virtuális hálózat neve | &lt;ClusterNamePrevix >-vnet1 |
| Címtér-előtagját | 10.1.0.0/16 |
| Alhálózat neve | 1. alhálózata |
| Alhálózati előtag | 10.1.0.0/24 |
| Alhálózat (átjáró) neve | Átjáró-alhálózat (nem módosítható) |
| (Átjáró) alhálózati előtag | 10.1.255.0/27 |
| Átjáró neve | vnet1gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típust | Útvonalalapú |
| Átjáró-Termékváltozatot | Alapszintű |
| Átjáró IP | vnet1gwip |

**Virtuális hálózat 2**

| Tulajdonság | Value (Díj) |
|----------|-------|
| Földrajzi egység | USA keleti régiója |
| Virtuális hálózat neve | &lt;ClusterNamePrevix >-vnet2 |
| Címtér-előtagját | 10.2.0.0/16 |
| Alhálózat neve | 1. alhálózata |
| Alhálózati előtag | 10.2.0.0/24 |
| Alhálózat (átjáró) neve | Átjáró-alhálózat (nem módosítható) |
| (Átjáró) alhálózati előtag | 10.2.255.0/27 |
| Átjáró neve | vnet2gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típust | Útvonalalapú |
| Átjáró-Termékváltozatot | Alapszintű |
| Átjáró IP | vnet1gwip |

## <a name="setup-dns"></a>A telepítő DNS

Az előző szakaszban a sablon létrehoz egy Ubuntu virtuális gép minden a két virtuális hálózat.  Ebben a szakaszban kötési telepítse a két DNS virtuális gép, és majd konfigurálja a DNS-továbbítást a két virtuális gépen.

Kötési telepítéséhez yon kell keresnie a két DNS virtuális gép nyilvános IP-címét.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Nyissa meg a DNS virtuális gép kiválasztásával **erőforráscsoportok > [erőforráscsoport neve] > [vnet1DNS]**.  Az erőforráscsoport nevét az utolsó eljárás során létrehozott egy. Az alapértelmezett DNS virtuális gép neve *vnet1DNS* és *vnet2NDS*.
3. Válassza ki **tulajdonságok** a virtuális hálózati Tulajdonságok lapjának megnyitásához.
4. Jegyezze fel a **nyilvános IP-cím**, ellenőrizze azt is, és a **magánhálózati IP-cím**.  A magánhálózati IP-címet kell **10.1.0.4** a vnet1DNS és **10.2.0.4** vnet2DNS számára.  
5. Módosítsa az alapértelmezett (Azure által biztosított) DNS-kiszolgáló Bind telepítéséhez a következő lépésekben-csomagok letöltése bejövő és kimenő hozzáférésének engedélyezéséhez használandó mindkét virtuális hálózat DNS-kiszolgálók.

Kötési telepítéséhez használja a következő eljárást:

1. Az SSH használatával csatlakozhat a __nyilvános IP-cím__ DNS virtuális gép. Az alábbi példa egy virtuális gép 40.68.254.142 csatlakozik:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Cserélje le `sshuser` a DNS virtuális gép létrehozásakor megadott SSH felhasználói fiókkal.

    > [!NOTE]
    > Számos módon juthat a `ssh` segédprogramot. A Linux, Unix és macOS nyújtja az operációs rendszer részeként. Ha Windows használ, fontolja meg az alábbi lehetőségek közül:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10-es](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git)https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Kötési telepítéséhez használja az SSH-munkamenetből a következő parancsokat:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Továbbítsa a névfeloldási kérelmeket a helyi DNS-kiszolgáló Bind konfigurálása. Ehhez használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.options` fájlt:

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
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Cserélje le az értékeket a `goodclients` szakaszt az IP-címtartományt, a két virtuális hálózat. Ez a szakasz határozza meg a címeket, a DNS-kiszolgáló fogadja a kéréseit.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

4. Az SSH-munkamenetből a következő paranccsal:

    ```bash
    hostname -f
    ```

    Ez a parancs egy értéket ad vissza az alábbi szöveghez hasonló:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a __DNS-utótag__ ehhez a virtuális hálózathoz. Mentse ezt az értéket, mivel később még használni fogjuk.

    Is található meg a DNS-utótagot a más DNS-kiszolgálóról. A következő lépésben kell.

5. Kötési feloldani a DNS-nevek a virtuális hálózaton belüli erőforrásokhoz konfigurálásához használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.local` fájlt:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Cserélje le a `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` az a virtuális hálózat DNS-utótagját. És a továbbító IP-cím a virtuális hálózat a DNS-kiszolgáló a magánhálózati IP-címe.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

6. Kötési indításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Győződjön meg arról, hogy a kötés képes névfeloldásra a másik virtuális hálózatban lévő erőforrásokra, használja a következő parancsokat:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]
    > Cserélje le `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` a teljesen minősített tartománynevét (FQDN) a DNS virtuális gép a hálózatban.
    >
    > Cserélje le `10.2.0.4` együtt a __belső IP-cím__ az egyéni DNS-kiszolgáló a virtuális hálózatban.

    A válasz jelenik meg az alábbi szöveghez hasonló:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Eddig, nem tudja ellenőrizni az IP-cím megadott DNS-kiszolgáló IP-cím nélkül más hálózati.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Egyéni DNS-kiszolgáló használata a virtuális hálózat konfigurálása

Az egyéni DNS-kiszolgáló használata helyett az Azure rekurzív feloldó virtuális hálózat konfigurálása, használja az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), a virtuális hálózat, majd válassza ki és __DNS-kiszolgálók__.

2. Válassza ki __egyéni__, és adja meg a __belső IP-cím__ az egyéni DNS-kiszolgáló. Végül válassza __mentése__.

6. Nyissa meg a DNS-kiszolgáló virtuális gépének vnet1, és kattintson a **indítsa újra a**.  A virtuális hálózatban, hogy a DNS-konfiguráció érvénybe léptetéséhez újra kell indítania a virtuális gépeket.
7. Ismételje meg a lépéseket a vnet2-től az egyéni DNS-kiszolgáló konfigurálása.

A DNS-konfiguráció teszteléséhez a két DNS virtuális gép SSH használatával csatlakozhat, és a virtuális hálózat DNS-kiszolgáló pingelése a gazdagép nevét segítségével. Ha nem működik, a következő paranccsal DNS állapotának ellenőrzéséhez:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>HBase-fürtök létrehozása

Hozzon létre egy HBase-fürt minden egyes a két virtuális hálózat a következő beállításokkal:

- **Erőforráscsoport neve**: használja ugyanazt az erőforráscsoport-nevet a virtuális hálózatok létrehozott.
- **Fürt típusa**: HBase
- **Verzió**: HBase 1.1.2 (HDI 3.6)
- **Hely**: a virtuális hálózatnak ugyanazt a helyet használja.  Alapértelmezés szerint a vnet1 *USA nyugati RÉGIÓJA*, pedig a vnet2 *USA keleti Régiójában*.
- **Tárolási**: hozzon létre egy új tárfiókot, a fürt számára.
- **Virtuális hálózat** (a Speciális beállítások a portálon): válassza ki az utolsó eljárás során létrehozott vnet1.
- **Alhálózat**: az alapértelmezett név a sablonban használt **subnet1**.

Annak érdekében, hogy a környezet megfelelően van konfigurálva, az átjárócsomópont teljesen minősített Tartományneve, a fürtök közötti pingelni kell lennie.

## <a name="load-test-data"></a>Tesztadatok betöltése

Ha egy fürt replikálja, meg kell adnia a táblákat, amelyet replikálni szeretne. Ebben a szakaszban a kiindulási fürt adatokat betölteni azt. A következő szakaszban a fürtök közötti replikáció lehetővé teszi.

Hozhat létre egy **névjegyek** táblában és adatok beszúrása a táblázatban, kövesse az utasításokat, [HBase-oktatóanyag: a HDInsight Apache HBase használatának első lépései](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>A replikáció engedélyezése

A következő lépések bemutatják, hogyan hívja a parancsfájl parancsfájlművelet az Azure Portalról. Azure PowerShell és a klasszikus Azure CLI használatával szkriptműveletet futtatásával kapcsolatos további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel használatával](../hdinsight-hadoop-customize-cluster-linux.md).

**Az Azure Portalról HBase-replikáció engedélyezése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a forrás HBase-fürtnek.
3. Válassza ki a fürt menüben **Parancsfájlműveletek**.
4. A lap tetején válassza **új küldése**.
5. Válassza ki vagy adja meg a következőket:

  1. **Név**: Adja meg **engedélyezze a replikációt**.
  2. **Bash-szkript URL-cím**: Adja meg **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **A fő**: Ellenőrizze, hogy ez van kijelölve. A csomóponttípusok törölje.
  4. **Paraméterek**: A következő minta paraméterek összes meglévő tábla a replikáció engedélyezése, és másolja az összes adat a forrás-fürtről a cél-fürthöz:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Állomásnév helyett a teljes Tartománynevet használja a forrás- és a fürt DNS-név.

6. Kattintson a **Létrehozás** gombra. A parancsfájl eltarthat egy ideig, futtatásához, különösen ha használja a **- copydata** argumentum.

Szükséges argumentumok:

|Name (Név)|Leírás|
|----|-----------|
|-s használata esetén – src-fürt | Adja meg a forrás HBase-fürt DNS-nevét. Például: -s hbsrccluster, a--src-fürt = hbsrccluster |
|-d, nyári időszámítás – a fürt | Adja meg a cél (replika) HBase-fürt DNS-nevét. Például: -s dsthbcluster, a--src-fürt = dsthbcluster |
|--src-ambari-jelszó - sp, | A kiindulási HBase-fürt Ambari megadja a rendszergazdai jelszót. |
|nyári időszámítás –-ambari-jelszó - terjesztési pont, | A cél HBase-fürt Ambari a rendszergazdai jelszót adja meg.|

Nem kötelező argumentumok:

|Name (Név)|Leírás|
|----|-----------|
|--src-ambari-user - su, | Adja meg a rendszergazdai felhasználóneve Ambari a kiindulási HBase-fürt. Az alapértelmezett érték **rendszergazdai**. |
|-du, nyári időszámítás –-ambari-felhasználó | A cél HBase-fürt Ambari rendszergazdai felhasználónevet megadása Az alapértelmezett érték **rendszergazdai**. |
|-t, a--tábla-lista | Megadja a replikálni kívánt táblák. Például:--tábla-list = "tábla1; table2; Tábl3". Táblák nem ad meg, ha a rendszer replikálja az összes meglévő HBase-táblákat.|
|-m,--gép | Adja meg a fő csomópont, ahol a parancsfájlművelet fut-e. Az érték kisebb, mint **hn1** vagy **hn0**. Mivel a **hn0** átjárócsomópont általában elfoglaltabb, javasoljuk a **hn1**. Használja ezt a beállítást, ha futtatja a 0 USD parancsfájl szkriptműveletet, a HDInsight-portál vagy az Azure PowerShell-lel.|
|-cp, - copydata | Lehetővé teszi a meglévő adatok a táblák, ahol a replikáció engedélyezve van-e az áttelepítés. |
|-rpm, - replikálás-phoenix-metaadatok | Lehetővé teszi a Phoenix rendszertáblákra replikációt. <br><br>*Használja ezt a beállítást körültekintéssel.* Azt javasoljuk, hogy újra létrehozza a Phoenix táblák replika fürtökön Ez a szkript használata előtt. |
|-h, – Súgó | Megjeleníti a használati adatokat. |

A `print_usage()` szakaszában a [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) rendelkezik paraméterekkel részletes leírását.

A szkriptműveletek sikeres telepítése után az SSH segítségével kapcsolódni a cél HBase-fürtöt, és ellenőrizze, hogy az adatok replikálása.

### <a name="replication-scenarios"></a>Replikációs forgatókönyvek

Az alábbi listában látható néhány általános használati esetek és paraméter beállítása:

- **Engedélyezze a replikációt a fürtök közötti összes tábla**. Ez a forgatókönyv nem igényel másolása vagy a táblák a meglévő adatok áttelepítése, és ne használja a Phoenix táblákat. Használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Engedélyezze a replikációt az adott táblák**. Engedélyezze a replikációt table1 table2 és Tábl3, használja a következő paraméterekkel:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Engedélyezze a replikációt az adott táblák, és másolja a meglévő adatok**. Engedélyezze a replikációt table1 table2 és Tábl3, használja a következő paraméterekkel:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Minden tábla a replikáció engedélyezése és a Phoenix metaadatok replikálása forrás célhelyre**. A Phoenix metaadatok replikációs nem tökéletes. Körültekintően használja azt. Használja a következő paramétereket:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Másolja ki és adatok áttelepítése

Érhetők el két külön parancsfájl művelet parancsfájlok másolását vagy adatok áttelepítése a replikáció engedélyezése után:

- [A kis táblák parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (kevesebb mint egy óra múlva befejeződik a méret és a teljes másolási néhány gigabájttól táblákat várható)

- [Nagy táblák esetében parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (táblákat, amelyek várhatóan másolása egy óránál hosszabb időt vesz igénybe)

Az ismertetett eljárást kell követnie is [engedélyezze a replikációt](#enable-replication) a parancsprogram-művelet meghívásához. Használja a következő paramétereket:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A `print_usage()` szakaszában a [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) paraméterek részletes leírás.

### <a name="scenarios"></a>Alkalmazási helyzetek

- **Másolja be az adott táblák (Teszt1 test2 és Teszt3) szerkesztett mostanáig minden sort (aktuális időbélyeg)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Vagy:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **A megadott időtartomány adott tábla másolása**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>A replikálás letiltása

Tiltsa le a replikációt, használja a egy másik parancsfájl parancsfájlművelet [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Az ismertetett eljárást kell követnie is [engedélyezze a replikációt](#enable-replication) a parancsprogram-művelet meghívásához. Használja a következő paramétereket:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A `print_usage()` szakaszában a [parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) rendelkezik paraméterekkel részletes leírását.

### <a name="scenarios"></a>Alkalmazási helyzetek

- **Tiltsa le az összes tábla**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  vagy

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Tiltsa le a replikációt a megadott táblák (table1 table2 és Tábl3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan állíthatja be a HBase-replikáció a virtuális hálózaton belül, vagy két virtuális hálózat között. A HDInsight és a HBase kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [A HDInsight Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase áttekintése](./apache-hbase-overview.md)
* [HBase-fürtök létrehozása az Azure Virtual Network](./apache-hbase-provision-vnet.md)

