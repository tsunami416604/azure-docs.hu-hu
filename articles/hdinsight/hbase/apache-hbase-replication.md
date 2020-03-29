---
title: HBase fürtreplikáció virtuális hálózatokban – Azure HDInsight
description: Ismerje meg, hogyan állíthatja be a HBase replikációt egyik HDInsight-verzióról a másikra a terheléselosztáshoz, a magas rendelkezésre álláshoz, a nulla állásidejű áttelepítéshez és frissítésekhez, valamint a vészhelyreállításhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435669"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Az Apache HBase fürtreplikáció beállítása az Azure virtuális hálózataiban

Ismerje meg, hogyan állíthatja be [az Apache HBase](https://hbase.apache.org/) replikációt egy virtuális hálózaton vagy két virtuális hálózat között az Azure-ban.

A fürtreplikáció forrás-leküldéses módszert használ. A HBase-fürt lehet forrás vagy cél, vagy egyszerre mindkét szerepkört teljesítheti. A replikáció aszinkron. A replikáció célja a végleges konzisztencia. Ha a forrás szerkesztést kap egy oszlopcsaládnak, amikor a replikáció engedélyezve van, a szerkesztés az összes célfürtre lesz propagálva. Amikor az adatokat egyik fürtről a másikra replikálja, a rendszer nyomon követi a forrásfürtöt és az összes olyan fürtöt, amely már felhasználta az adatokat, hogy megakadályozza a replikációs hurkokat.

Ebben a cikkben egy forrás-cél replikációt állít be. Más fürttopológiákat az [Apache HBase referenciaútmutatójában](https://hbase.apache.org/book.html#_cluster_replication)talál.

A hbase-replikációs használati esetek egyetlen virtuális hálózat esetében a következők:

* Terheléselosztás. Futtathatja például a célfürtön a beolvasott vagy mapreduce feladatokat, és adatokat vihet be a forrásfürtön.
* Magas rendelkezésre állás hozzáadása.
* Adatok áttelepítése az egyik HBase-fürtről a másikra.
* Az Azure HDInsight-fürt frissítése egyik verzióról a másikra.

A hbase replikációs használati esetei két virtuális hálózat esetében:

* Katasztrófa utáni helyreállítás beállítása.
* Terheléselosztás és az alkalmazás particionálása.
* Magas rendelkezésre állás hozzáadása.

A [githubról](https://github.com/Azure/hbase-utils/tree/master/replication)származó [parancsfájl-műveletparancsfájlok](../hdinsight-hadoop-customize-cluster-linux.md) használatával replikálhatja a fürtöket.

## <a name="prerequisites"></a>Előfeltételek
A cikk megkezdése előtt, rendelkeznie kell egy Azure-előfizetéssel. Lásd: [Ingyenes Azure-próbaverzió beszerezni.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## <a name="set-up-the-environments"></a>A környezet beállítása

Három beállítási lehetőség közül választhat:

- Két Apache HBase-fürt egy Azure virtuális hálózatban.
- Két Apache HBase-fürt két különböző virtuális hálózatban ugyanabban a régióban.
- Két Apache HBase-fürt két különböző virtuális hálózatban, két különböző régióban (georeplikáció).

Ez a cikk a georeplikációs forgatókönyvet ismerteti.

A környezetek beállításának elősegítése érdekében létrehoztunk néhány [Azure Resource Manager-sablont.](../../azure-resource-manager/management/overview.md) Ha a környezeteket más módszerekkel szeretné beállítani, olvassa el az:

- [Apache Hadoop-fürtök létrehozása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md)
- [Apache HBase-fürtök létrehozása az Azure Virtuális Hálózatban](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Két virtuális hálózat beállítása két különböző régióban

Ha olyan sablont szeretne használni, amely két virtuális hálózatot hoz létre két különböző régióban, és a virtuális hálózatok közötti VPN-kapcsolatot, válassza ki a következő üzembe helyezés az **Azure-ba** gombot. A sablondefiníció egy [nyilvános blobtárolóban van tárolva.](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

A sablon ban lévő nehezen kódolt értékek egy része:

**Virtuális hálózat 1**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA nyugati régiója |
| Virtuális hálózat neve | &lt;ClusterNamePrevix>-vnet1 |
| Címtér előtagja | 10.1.0.0/16 |
| Alhálózat neve | 1. alhálózat |
| Alhálózati előtag | 10.1.0.0/24 |
| Alhálózat (átjáró) neve | GatewaySubnet (nem módosítható) |
| Alhálózati (átjáró) előtag | 10.1.255.0/27 |
| Átjáró neve | vnet1gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típusa | Útvonalalapú |
| Átjáró termékváltozat | Basic |
| Átjáró IP-címe | vnet1gwip |

**Virtuális hálózat 2**

| Tulajdonság | Érték |
|----------|-------|
| Hely | USA keleti régiója |
| Virtuális hálózat neve | &lt;ClusterNamePrevix>-vnet2 |
| Címtér előtagja | 10.2.0.0/16 |
| Alhálózat neve | 1. alhálózat |
| Alhálózati előtag | 10.2.0.0/24 |
| Alhálózat (átjáró) neve | GatewaySubnet (nem módosítható) |
| Alhálózati (átjáró) előtag | 10.2.255.0/27 |
| Átjáró neve | vnet2gw |
| Átjáró típusa | Vpn |
| Átjáró VPN-típusa | Útvonalalapú |
| Átjáró termékváltozat | Basic |
| Átjáró IP-címe | vnet1gwip |

## <a name="setup-dns"></a>TELEPÍTŐ DNS

Az utolsó szakaszban a sablon létrehoz egy Ubuntu virtuális gépet a két virtuális hálózat mindegyikében.  Ebben a szakaszban telepíti a Bind programot a két VIRTUÁLIS DNS-gépre, majd konfigurálja a DNS-továbbítást a két virtuális gépen.

A Bind telepítéséhez meg kell találnia a két DNS virtuális gép nyilvános IP-címét.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Nyissa meg a DNS virtuális gépet az **Erőforrások csoportok > [erőforráscsoport neve] > [vnet1DNS]** lehetőséget választva.  Az erőforráscsoport neve az utolsó eljárásban létrehozott. Az alapértelmezett VIRTUÁLIS SZÁMÍTÓGÉP-nevek *a vnet1DNS* és *a vnet2NDS.*
3. A **Tulajdonságok** lehetőséget választva nyissa meg a virtuális hálózat tulajdonságlapját.
4. Írja le a **nyilvános IP-címet**, és ellenőrizze a **privát IP-címet**is .  A privát IP-cím a vnet1DNS esetében **10.1.0.4,** a vnet2DNS esetében **pedig 10.2.0.4.**  
5. Módosítsa a DNS-kiszolgálók mindkét virtuális hálózatok alapértelmezett (Azure által biztosított) DNS-kiszolgálók, hogy a bejövő és kimenő hozzáférést a letöltési csomagok telepíteni Bind a következő lépésekben.

A Bind telepítéséhez kövesse az alábbi eljárást:

1. Az SSH segítségével csatlakozzon a DNS virtuális gép __nyilvános IP-címéhez.__ A következő példa 40.68.254.142-es sebességgel csatlakozik egy virtuális géphez:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Cserélje `sshuser` le a DNS virtuális gép létrehozásakor megadott SSH felhasználói fiókra.

    > [!NOTE]  
    > Vannak többféle ways -hoz `ssh` megkap a típus-. Linuxon, Unix-on és macOS rendszeren az operációs rendszer részeként érhető el. Windows használata esetén vegye figyelembe az alábbi lehetőségek egyikét:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash az Ubuntu-n a Windows 10-en](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. A Bind telepítéséhez használja az SSH-munkamenet következő parancsait:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurálja a Kötés beállítást úgy, hogy névfeloldási kérelmeket továbbítson a helyszíni DNS-kiszolgálónak. Ehhez használja a következő szöveget a `/etc/bind/named.conf.options` fájl tartalmaként:

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
    > Cserélje le a `goodclients` szakasz értékeit a két virtuális hálózat IP-címtartományára. Ez a szakasz azokat a címeket határozza meg, amelyekről a DNS-kiszolgáló fogadja a kéréseket.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    A fájl mentéséhez használja a __Ctrl+X,__ __Y__, majd __az Enter billentyűkombinációt.__

4. Az SSH-munkamenetből használja a következő parancsot:

    ```bash
    hostname -f
    ```

    Ez a parancs a következő höz hasonló értéket ad vissza:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a virtuális hálózat __DNS-utótagja.__ Mentse ezt az értéket, mivel később még használni fogjuk.

    A DNS-utótagot a másik DNS-kiszolgálótól is meg kell találnia. Szükséged van rá a következő lépésben.

5. Ha a Kötés beállítást a virtuális hálózaton belüli erőforrások DNS-neveinek `/etc/bind/named.conf.local` feloldására szeretné konfigurálni, használja a következő szöveget a fájl tartalmaként:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > A másik `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` virtuális hálózat DNS-utótagjára kell cserélnie. És a továbbító IP a privát IP-címét a DNS-kiszolgáló a másik virtuális hálózat.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    A fájl mentéséhez használja a __Ctrl+X,__ __Y__, majd __az Enter billentyűkombinációt.__

6. A Bind elindításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Annak ellenőrzéséhez, hogy a kötés képes-e feloldani a másik virtuális hálózat erőforrásainak nevét, használja a következő parancsokat:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Cserélje `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` le a másik hálózat BAN lévő DNS-virtuális gép teljesen minősített tartománynevére (FQDN).
    >
    > Cserélje `10.2.0.4` le a másik virtuális hálózat egyéni __DNS-kiszolgálójának belső IP-címét.__

    A válasz a következő szöveghez hasonló:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Eddig nem keresheti meg az IP-címet a másik hálózatról a megadott DNS-kiszolgáló IP-címe nélkül.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>A virtuális hálózat konfigurálása az egyéni DNS-kiszolgáló használatára

Ha úgy szeretné konfigurálni a virtuális hálózatot, hogy az Azure rekurzív feloldója helyett az egyéni DNS-kiszolgálót használja, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a virtuális hálózatot, majd válassza a __DNS-kiszolgálók__lehetőséget.

2. Válassza __az Egyéni__lehetőséget, és adja meg az egyéni DNS-kiszolgáló belső __IP-címét.__ Végül válassza a __Mentés__gombot.

6. Nyissa meg a DNS-kiszolgáló virtuális gépét a virtuális hálózat1 szolgáltatásban, és kattintson **az Újraindítás gombra.**  A DNS-konfiguráció érvénybe léptetéséhez újra kell indítania a virtuális hálózat összes virtuális gépét.
7. Ismételje meg a lépéseket konfigurálja az egyéni DNS-kiszolgáló t.

A DNS-konfiguráció teszteléséhez csatlakozhat a két DNS virtuális géphez SSH használatával, és pingelheti a másik virtuális hálózat DNS-kiszolgálóját az állomásnevével. Ha nem működik, a DNS-állapot ellenőrzéséhez használja a következő parancsot:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase-fürtök létrehozása

Hozzon létre egy [Apache HBase](https://hbase.apache.org/) fürtöt a következő konfigurációval a két virtuális hálózat mindegyikében:

- **Erőforráscsoport neve**: használja ugyanazt az erőforráscsoport nevet, mint a virtuális hálózatok létrehozása.
- **Fürt típusa**: HBase
- **Verzió:** HBase 1.1.2 (HDI 3.6)
- **Hely**: Használja ugyanazt a helyet, mint a virtuális hálózat.  Alapértelmezés szerint a vnet1 *usa-beli nyugat-amerikai,* a vnet2 pedig *USA keleti része.*
- **Tárolás**: Hozzon létre egy új tárfiókot a fürthöz.
- **Virtuális hálózat** (a portál speciális beállításaiból): Válassza ki az utolsó eljárásban létrehozott virtuális hálózat1 lehetőséget.
- **Alhálózat**: A sablonban használt alapértelmezett név az **alhálózat1**.

A környezet megfelelő konfigurálása érdekében képesnek kell lennie a fejcsomópont teljes tartományna pingelésére a két fürt között.

## <a name="load-test-data"></a>Tesztadatok betöltése

Fürt replikálásakor meg kell adnia a replikálni kívánt táblákat. Ebben a szakaszban bizonyos adatokat tölt be a forrásfürtbe. A következő szakaszban engedélyezi a replikációt a két fürt között.

**Névjegytábla** létrehozásához és néhány adat beszúrásához kövesse az [Apache HBase oktatóanyagának utasításait: Az Apache HBase használatának első lépései a HDInsightban](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Ha egyéni névtérből szeretne táblákat replikálni, gondoskodnia kell arról, hogy a célfürtön is meg legyenek adva a megfelelő egyéni névterek.
>

## <a name="enable-replication"></a>A replikáció engedélyezése

A következő lépések ismertetik, hogyan hívja meg a parancsfájl-művelet parancsfájlt az Azure Portalon. A parancsfájl-műveletek Azure PowerShell és az Azure Classic CLI használatával történő futtatásáról a [HDInsight-fürtök testreszabása parancsfájlművelettel című](../hdinsight-hadoop-customize-cluster-linux.md)témakörben talál további információt.

**HBase-replikáció engedélyezése az Azure Portalról**

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a forrás HBase fürtöt.
3. A fürt menüben válassza a **Parancsfájlműveletek lehetőséget.**
4. A lap tetején válassza az **Új küldése lehetőséget.**
5. Válassza ki vagy írja be a következő adatokat:

   1. **Név**: Írja be **a Replikáció engedélyezése**.
   2. **Bash Script**URL **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**: Írja be a .
   3. **Fej**: Győződjön meg róla, hogy ez ki van jelölve. Törölje a többi csomóponttípust.
   4. **Paraméterek**: A következő mintaparaméterek engedélyezik az összes meglévő tábla replikációját, majd a forrásfürtből származó összes adatot átmásolják a célfürtbe:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > A forrás- és a célfürt DNS-nevéhez is használjon állomásnevet a teljes tartománynév helyett.
      >
      > Ez a forgatókönyv a hn1 aktív csomópontként való feltételezését feltételezi. Ellenőrizze a fürtben az aktív főcsomópont azonosításához.

6. Kattintson a **Létrehozás** gombra. A parancsfájl futtatása eltarthat egy ideig, különösen a **-copydata** argumentum használata esetén.

Kötelező argumentumok:

|Név|Leírás|
|----|-----------|
|-s, --src-cluster | Megadja a forrás HBase fürt DNS-nevét. Például: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Megadja a cél (replika) HBase fürt DNS-nevét. Például: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-jelszó | Megadja az Ambari rendszergazdai jelszavát a Forrás HBase fürtön. |
|-dp, --dst-ambari-jelszó | Megadja az Ambari rendszergazdai jelszavát a cél HBase-fürtön.|

Választható argumentumok:

|Név|Leírás|
|----|-----------|
|-su, --src-ambari-user | Megadja az Ambari rendszergazdai felhasználónevét a Forrás HBase fürtön. Az alapértelmezett érték **admin**. |
|-du, --dst-ambari-user | Megadja az Ambari rendszergazdai felhasználónevét a cél HBase-fürtön. Az alapértelmezett érték **admin**. |
|-t, --táblázat-lista | Megadja a replikálandó táblákat. Például: --table-list="table1;table2;table3". Ha nem adja meg a táblákat, az összes meglévő HBase tábla replikálása.|
|-m, --gép | Megadja azt a főcsomópontot, ahol a parancsfájlművelet fut. Az értéket az aktív főcsomópont alapján kell kiválasztani. Akkor használja ezt a beállítást, ha a $0 parancsfájlt parancsfájlműveletként futtatja a HDInsight-portálról vagy az Azure PowerShellből.|
|-cp, -copydata | Engedélyezi a meglévő adatok áttelepítését azon táblákon, ahol a replikáció engedélyezve van. |
|-rpm, -replicate-phoenix-meta | Engedélyezi a replikációt a Phoenix rendszertáblákon. <br><br>*Óvatosan használja ezt a beállítást.* Javasoljuk, hogy a parancsfájl használata előtt hozza létre újra a Phoenix-táblákat a replikafürtökön. |
|-h, --segítség | Használati adatok megjelenítése. |

A `print_usage()` [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) szakasza részletesen ismerteti a paramétereket.

A parancsfájlművelet sikeres üzembe helyezése után az SSH segítségével csatlakozhat a cél HBase-fürthöz, majd ellenőrizheti, hogy az adatok replikálása megtörtént-e.

### <a name="replication-scenarios"></a>Replikációs forgatókönyvek

Az alábbi lista néhány általános használati esetet és azok paraméterbeállításait mutatja be:

- **Engedélyezze a replikációt a két fürt közötti összes táblán.** Ebben az esetben nincs szükség a táblákban lévő adatok másolására vagy áttelepítésére, és nem használja a Phoenix táblákat. Használja a következő paramétereket:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Engedélyezze a replikációt adott táblákon.** A 1.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Engedélyezze a replikációt adott táblákon, és másolja a meglévő adatokat.** A 1.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Engedélyezze a replikációt az összes táblán, és replikálja a Phoenix metaadatait a forrástól a célig.** A Phoenix metaadat-replikációja nem tökéletes. Óvatosan használja. Használja a következő paramétereket:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Adatok másolása és áttelepítése

A replikáció engedélyezése után két külön parancsfájlműveleti parancsfájl áll rendelkezésre az adatok másolásához vagy áttelepítéséhez:

- [Parancsfájl kis táblákhoz](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (néhány gigabájt méretű táblákhoz, és a teljes másolat várhatóan kevesebb mint egy óra alatt befejeződik)

- [Parancsfájl nagy méretű táblákhoz](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (olyan táblákhoz, amelyek másolása várhatóan egy óránál tovább tart)

A [parancsfájlművelet](#enable-replication) hívásához kövesse a replikáció engedélyezése című részben leírtakat. Használja a következő paramétereket:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A `print_usage()` [parancsfájl](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) szakasza részletes leírást tartalmaz a paraméterekről.

### <a name="scenarios"></a>Forgatókönyvek

- **Adott táblák másolása (test1, test2 és test3) az eddig szerkesztett összes sorhoz (aktuális időbélyegző):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Vagy:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Adott táblák másolása megadott időtartománysal:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>A replikálás letiltása

A replikáció letiltásához használjon egy másik parancsfájlművelet-parancsfájlt a [GitHubról.](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) A [parancsfájlművelet](#enable-replication) hívásához kövesse a replikáció engedélyezése című részben leírtakat. Használja a következő paramétereket:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A `print_usage()` [parancsfájl](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) szakasza részletesen ismerteti a paramétereket.

### <a name="scenarios"></a>Forgatókönyvek

- **A replikáció letiltása az összes táblán:**

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  vagy

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **A replikáció letiltása a megadott táblákon (table1, table2 és table3):**

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Ha törölni kívánja a célfürtöt, győződjön meg arról, hogy eltávolítja a forrásfürt társlistájáról. Ez az "1" remove_peer parancs futtatásával érhető el a forrásfürt hbase rendszerhéjánál. Ennek hiányában előfordulhat, hogy a forrásfürt nem működik megfelelően.
>

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan állíthatja be az Apache HBase replikációt egy virtuális hálózaton vagy két virtuális hálózaton belül. Ha többet szeretne megtudni a HDInsightról és az Apache HBase-ről, olvassa el az alábbi cikkeket:

* [Ismerkedés az Apache HBase-lel a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [A HDInsight Apache HBase áttekintése](./apache-hbase-overview.md)
* [Apache HBase-fürtök létrehozása az Azure Virtuális Hálózatban](./apache-hbase-provision-vnet.md)

