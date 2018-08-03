---
title: Az Azure-ban (MySQL) MariaDB-fürt futtatásához |} A Microsoft Docs
description: Hozzon létre egy MariaDB + Galera MySQL-fürt Azure-beli virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 2cdc58a827f696d62e6240b90202ee04ce371d07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426853"
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>(MySQL) MariaDB-fürt: Azure-oktatóanyag
> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén az Azure Resource Manager modellt használja.

> [!NOTE]
> MariaDB Enterprise cluster már elérhető az Azure piactéren. Az új ajánlat automatikusan telepíti a MariaDB Galera fürt az Azure Resource Managerrel. Az új ajánlat használjon [Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Ez a cikk bemutatja, hogyan hozhat létre több főkiszolgálós [Galera](http://galeracluster.com/products/) fürt [MariaDBs](https://mariadb.org/en/about/) (egy robusztus, skálázható és megbízható protokollkompatibilitását MySQL-hez) működik a magas rendelkezésre állású környezetben az Azure-ban virtuális gépek.

## <a name="architecture-overview"></a>Az architektúra áttekintése
Ez a cikk ismerteti, hogyan hajthatja végre az alábbi lépéseket:

- Hozzon létre egy három csomópontos fürt.
- Az operációsrendszer-lemezt az adatlemezek egymástól.
- Hozzon létre az adatlemezek RAID-0/csíkozott beállítás IOPS növelése érdekében.
- Használja az Azure Load Balancer a három csomópont számára a terhelés kiegyenlítése érdekében.
- Ismétlődő munkahelyi minimalizálása érdekében hozzon létre egy Virtuálisgép-rendszerképet, amely tartalmazza a MariaDB + Galera, és ezzel hozzon létre a virtuális gépek másik fürthöz.

![Rendszer-architektúra](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Ez a témakör használja a [Azure CLI-vel](../../../cli-install-nodejs.md) eszközök, ezért ügyeljen arra, hogy töltse le azokat, és csatlakoztassa őket az Azure-előfizetéshez az utasításoknak megfelelően. Ha egy hivatkozást az Azure CLI elérhető parancsai van szüksége, tekintse meg a [Azure CLI-vel parancsdokumentációja](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Is kell [hozzon létre egy SSH-kulcsot a hitelesítéshez] , és jegyezze fel a .pem fájl helyét.
>
>

## <a name="create-the-template"></a>A sablon létrehozása
### <a name="infrastructure"></a>Infrastruktúra
1. Olyan affinitáscsoportot hozzon létre az erőforrások tárolásához együtt.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
1. Hozzon létre egy virtuális hálózatot.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
1. Hozzon létre egy tárfiókot az összes lemez üzemeltetéséhez. Több mint 40 kitett lemezek nem szabad elhelyezése ugyanazt a tárfiókot, hogy elkerülje a 20 000 IOPS-tárolási fiók korlátját. Ebben az esetben Ön alatt ezt a korlátot, ezért minden ugyanahhoz a fiókhoz az egyszerűség kedvéért fogja tárolni.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
1. Keresse meg a CentOS 7 virtuális gép rendszerképének nevét.

        azure vm image list | findstr CentOS
   A kimenet lesz hasonló `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Ezt a nevet használja a következő lépésben.
1. A Virtuálisgép-sablon létrehozása és /path/to/key.pem cserélje le a tároló létrehozott .pem SSH-kulcs elérési útja.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
1. Négy 500 GB-os adatlemezeket csatlakoztathat a virtuális gép használatra a RAID-konfigurációt.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
1. Jelentkezzen be a sablon következő mariadbhatemplate.cloudapp.net:22 létrehozott virtuális gép SSH-val, és a titkos kulcs használatával csatlakoznak.

### <a name="software"></a>Szoftver
1. A legfelső szintű beolvasása.

        sudo su

1. A RAID telepítése:

    a. Telepítse a mdadm.

              yum install mdadm

    b. Hozzon létre egy EXT4 fájlrendszer RAID0/stripe konfigurációját.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Hozzon létre a csatlakoztatási pont könyvtárat.

              mkdir /mnt/data
    d. Az újonnan létrehozott RAID eszköz UUID lekéréséhez.

              blkid | grep /dev/md0
    e. /Etc/fstab szerkesztése.

              vi /etc/fstab
    f. Hozzáadja az eszközt ahhoz, hogy újraindításkor, és cserélje le az előző kapott érték az UUID automatikus csatlakoztatási **blkid** parancsot.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Csatlakoztassa az új partíciót.

              mount /mnt/data

1. Telepítse a MariaDB.

    a. Hozza létre a MariaDB.repo fájlt.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Töltse ki az adattár fájlt az alábbi tartalommal:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Az ütközések elkerülése érdekében távolítsa el a meglévő utótag és a mariadb-függvénytárak.

           yum remove postfix mariadb-libs-*
    d. Telepítse a MariaDB Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

1. Helyezze át a MySQL adatkönyvtárat a RAID blokkblob eszközre.

    a. Az aktuális MySQL könyvtár másolja be az új helyre, és távolítsa el a régi könyvtárat.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Ennek megfelelően állítsa be az új könyvtár engedélyeit.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Hozzon létre szimbolikus, amely a régi könyvtár mutat az új helyre a RAID-partíción.

           ln -s /mnt/data/mysql /var/lib/mysql

1. Mivel [SELinux zavarja a fürt működését a](http://galeracluster.com/documentation-webpages/configuration.html#selinux), tiltsa le az aktuális munkamenet szükséges. Szerkesztés `/etc/selinux/config` letiltani a későbbi újraindításhoz.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
1. Ellenőrizze a MySQL-futtatások.

   a. Indítsa el a MySQL.

           service mysql start
   b. A MySQL telepítése biztonságos, a legfelső szintű jelszó beállítása, távolítsa el a névtelen felhasználók számára, hogy tiltsa le a távoli legfelső szintű bejelentkezési és a test-adatbázis eltávolítása.

           mysql_secure_installation
   c. Hozzon létre egy felhasználót az adatbázison a fürt működését, és szükség esetén az alkalmazások számára.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Állítsa le a MySQL.

            service mysql stop
1. Hozzon létre egy konfigurációs helyőrző.

   a. A fürtbeállítások egy helyőrző létrehozása MySQL-konfiguráció szerkesztése. Ne cserélje le a **`<Variables>`** vagy állítsa vissza a most. Hogy fog történni, ha ebből a sablonból létrehozott virtuális gép.

            vi /etc/my.cnf.d/server.cnf
   b. Szerkessze a **[galera]** szakaszt, és törölje a jelölést.

   c. Szerkessze a **[mariadb]** szakaszban.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
1. Nyissa meg a szükséges portok a tűzfalon a CentOS 7 FirewallD használatával.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IZRAELI NORMÁL IDŐ SZERINT: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Töltse be újra a tűzfalon: `firewall-cmd --reload`

1. A rendszer a teljesítmény optimalizálásához. További információkért lásd: [teljesítmény-finomhangolási stratégia](optimize-mysql.md).

   a. A MySQL-konfigurációs fájl újból szerkeszteni.

            vi /etc/my.cnf.d/server.cnf
   b. Szerkessze a **[mariadb]** szakaszt, és fűzze hozzá a következő tartalommal:

   > [!NOTE]
   > Azt javasoljuk, hogy innodb\_puffer\_pool_size a virtuális memória 70 százalék. Ebben a példában azt beállított 2.45 GB, a közepes méretű Azure-beli virtuális gép 3,5 GB RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
1. Állítsa le a MySQL, tiltsa le a MySQL szolgáltatás nem fut az elkerülése érdekében a fürt megszakítása, ha csomópontot ad indítási és a gép megszüntetése.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
1. A portálon keresztül a virtuális gép rögzítése. (Jelenleg [adja ki az Azure CLI-eszközöket a #1268](https://github.com/Azure/azure-xplat-cli/issues/1268) ismerteti azt a tényt, hogy az Azure CLI-eszközök által rögzített rendszerképek nem rögzíti a csatlakoztatott adatlemezekkel.)

    a. Állítsa le a gépet a portálon keresztül.

    b. Kattintson a **rögzítése** , és adja meg a rendszerkép nevének, **mariadb-galera-image**. Adjon meg egy leírást, és ellenőrizze a "Waagent futtatnom kell."
      
      ![A virtuális gép rögzítése](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>A fürt létrehozása
Hozzon létre három virtuális gépet hozott létre, és ezután konfigurálása és a fürt elindításához a sablont.

1. Hozzon létre az első CentOS 7 virtuális gép a mariadb-galera-image lemezképből létrehozott, a következő adatok megadásával:

 - Virtuális hálózat neve: mariadbvnet
 - Alhálózat: mariadb
 - A gép méretének: Közepes
 - Felhőszolgáltatás neve: mariadbha (vagy bármilyen nevet szeretné mariadbha.cloudapp.net keresztül érhetők el)
 - Gépnév: mariadb1
 - Felhasználónév: azureuser
 - SSH-hozzáférés: engedélyezve
 - Átadása az SSH tanúsítvány .pem-fájlt, és /path/to/key.pem cserélje le a tároló létrehozott .pem SSH-kulcs elérési útja.

   > [!NOTE]
   > Az alábbi parancsokat az átláthatóság érdekében több vonalon vannak osztva, de egyes egy sorba kell megadnia.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
1. Hozzon létre két további virtuális gépek a mariadbha felhőszolgáltatáshoz való csatlakozással. Módosítsa a virtuális gép és az SSH-port nem ütközik a többi virtuális gépe ugyanazon a felhőszolgáltatáson egyedi portját.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  A MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
1. Szüksége lesz a következő lépés az egyes a három virtuális gép belső IP-címének lekéréséhez:

    ![IP-cím beolvasása](./media/mariadb-mysql-cluster/IP.png)
1. SSH-val jelentkezzen be a három virtuális gépet, és módosítsa a konfigurációs fájlt az egyes őket.

        sudo vi /etc/my.cnf.d/server.cnf

    Állítsa vissza **`wsrep_cluster_name`** és **`wsrep_cluster_address`** eltávolításával a **#** a sor elejére.
    Továbbá cserélje le **`<ServerIP>`** a **`wsrep_node_address`** és **`<NodeName>`** a **`wsrep_node_name`** -a Virtuális gép IP-cím és nevet, és, valamint azokat a sorokat, állítsa vissza.
1. MariaDB1 elindítja a fürtöt, és biztosítani, hogy az indításkor futnak.

        sudo service mysql bootstrap
        chkconfig mysql on
1. Indítsa el a MySQL MariaDB2 és MariaDB3, és biztosítani, hogy az indításkor futnak.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>A fürt terheléselosztás
A fürtözött virtuális gépek létrehozásakor hozzá azokat egy nevű clusteravset, győződjön meg arról, hogy azok különböző hibatűrési és frissítési tartományokban is helyezi, és egyszerre csak a soha nem karbantartási minden gépen, hogy az Azure rendelkezésre állási csoportba. Ez a konfiguráció megfelel a követelményeknek, az Azure szolgáltatásiszint-szerződés (SLA) támogatott.

Most már használja az Azure Load Balancer kérelmek három csomópontok közötti elosztása érdekében.

Futtassa az alábbi parancsokat a gépen az Azure parancssori felület használatával.

A parancs paraméterei struktúra a következő: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

A parancssori felület beállítása a load balancer mintavételi időköz 15 másodperc, amely egy kicsit túl hosszú lehet. Módosítsa a portál **végpontok** bármely, a virtuális gépek.

![Végpont szerkesztése](./media/mariadb-mysql-cluster/Endpoint.PNG)

Válassza ki **konfigurálja újra az elosztott terhelésű készlet**.

![Konfigurálja újra az elosztott terhelésű készlet](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Változás **mintavételi időköz** 5 másodperc és mentse a módosításokat.

![Mintavételi időköz módosítása](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>A fürt ellenőrzése
A nehezét történik. A fürt már érhető el, `mariadbha.cloudapp.net:3306`, amely eléri a load balancer és útvonal kérelmek a három virtuális gép között, zökkenőmentesen és hatékony.

A kedvenc MySQL-ügyfél segítségével csatlakozzon, vagy csatlakoztassa a virtuális gépeket, ellenőrizze, hogy működik-e a fürt egyik.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Ezután hozzon létre egy adatbázist, és néhány adatokkal való feltöltéséhez.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

A létrehozott adatbázis a következő táblázatot ad vissza:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy három csomópontos MariaDB + Galera magas rendelkezésre állású fürtöt az Azure-beli virtuális gépen futó CentOS 7. A virtuális gépek elosztott terhelésű Azure Load balancerrel.

Érdemes megtekinteni [másik módja, hogy a MySQL-fürt Linux rendszeren](mysql-cluster.md) és [optimalizálása és az Azure Linux rendszerű virtuális gépekhez a MySQL teljesítményének tesztelése](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[Hozzon létre egy SSH-kulcsot a hitelesítéshez]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
