---
title: "Egy MariaDB (MySQL) fürt futtatása az Azure-on |} Microsoft Docs"
description: "Hozzon létre egy MariaDB + Galera MySQL az Azure virtuális gépek fürtön"
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 53e9bf18b26338212411ea7c4f260eb308486738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB (MySQL) fürt: Azure útmutató
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén az Azure Resource Manager modellt használja.

> [!NOTE]
> MariaDB vállalati fürt most nem áll rendelkezésre az Azure piactéren. Az új ajánlat automatikusan telepíteni fogja az Azure Resource Manager MariaDB Galera fürt. Az új ajánlat használjon [Azure piactér](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Ez a cikk bemutatja, hogyan hozzon létre egy többszörös főkiszolgáló [Galera](http://galeracluster.com/products/) fürtben [MariaDBs](https://mariadb.org/en/about/) (nagy teljesítményű, méretezhető és megbízható Esőcsepp helyettesíti a MySQL) működését a magas rendelkezésre állású környezet az Azure-on virtuális gépek.

## <a name="architecture-overview"></a>Az architektúra áttekintése
Ez a cikk ismerteti, hogyan lehet elvégezni az alábbi lépéseket:

- Hozzon létre egy három csomópontos fürtre.
- Az adatlemezek külön származó az operációsrendszer-lemezképet.
- Hozzon létre az adatlemezek RAID-0/csíkozott beállítás IOPS növelése érdekében.
- Használja az Azure terheléselosztó a három csomópontok a terhelés kiegyenlítése érdekében.
- Ismétlődő munkahelyi minimalizálása érdekében hozzon létre egy Virtuálisgép-lemezkép MariaDB + Galera tartalmazó és a virtuális gépek más fürt létrehozására használható.

![Rendszer-architektúra](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Ez a témakör használja a [Azure CLI](../../../cli-install-nodejs.md) eszközök, ezért ügyeljen arra, hogy letöltheti a fájlokat, és csatlakoztassa őket az Azure-előfizetéshez a utasításainak megfelelően. Ha egy hivatkozást az az Azure parancssori felület parancsai van szüksége, tekintse meg a [Azure CLI parancsdokumentációja](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Is kell [hitelesítéshez SSH-kulcs létrehozása] és jegyezze fel a .pem fájl helyét.
>
>

## <a name="create-the-template"></a>A sablon létrehozása
### <a name="infrastructure"></a>Infrastruktúra
1. Ahhoz, hogy az erőforrások együtt affinitáscsoport létrehozásához.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Hozzon létre egy virtuális hálózatot.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Hozzon létre egy tárfiókot, a lemezek. Legfeljebb 40 terhelésnek kitett lemezek nem szabad elhelyezése elérte-e a 20 000 IOPS fiók méretkorlátot elkerülése érdekében ugyanazt a tárfiókot. Ebben az esetben Ön alatt ezt a határértéket, így mindent az egyszerűség kedvéért ugyanazt a fiókot fogja tárolni.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Keresse meg a CentOS 7 virtuális gép lemezképe.

        azure vm image list | findstr CentOS
   A kimenet lesz hasonlót `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Használja ezt a nevet a következő lépésben.
5. A Virtuálisgép-sablon létrehozásához, és cserélje le /path/to/key.pem az elérési út a generált .pem SSH-kulcs tárolására.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Négy 500 GB-os adatlemezt csatolni a virtuális Gépet a RAID-konfigurációban való használat céljából.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Jelentkezzen be a sablon virtuális Gépet, amely létrehozta a következő mariadbhatemplate.cloudapp.net:22 az SSH segítségével, és a titkos kulcs használatával csatlakoznak.

### <a name="software"></a>Szoftver
1. A legfelső szintű beolvasása.

        sudo su

2. A RAID telepítése:

    a. Telepítse a mdadm.

              yum install mdadm

    b. Hozza létre a RAID0/paritásos konfigurációt EXT4 fájlrendszerrel.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Hozzon létre a csatlakoztatási pont könyvtárat.

              mkdir /mnt/data
    d. Az újonnan létrehozott RAID eszköz UUID beolvasása.

              blkid | grep /dev/md0
    e. /Etc/fstab szerkesztése.

              vi /etc/fstab
    f. Adja hozzá az eszközt, hogy engedélyezze az automatikus csatlakoztatását a újraindításkor UUID cseréje a során kapott érték az előző **blkid** parancsot.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Csatlakoztassa az új partíciót.

              mount /mnt/data

3. Telepítse a MariaDB.

    a. Hozza létre a MariaDB.repo fájlt.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Töltse ki a tárházban fájl a következő tartalommal:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Az ütközések elkerülése érdekében távolítsa el a meglévő utótag és mariadb-függvénytárak.

           yum remove postfix mariadb-libs-*
    d. Telepítési MariaDB Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Helyezze át a MySQL adatkönyvtár a RAID-blokk eszköz.

    a. Az aktuális MySQL könyvtár átmásolja az új helyre, és távolítsa el a régi könyvtárat.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Ennek megfelelően állítsa be az új könyvtár engedélyeket.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Hozzon létre egy symlink mutat, a régi könyvtár az új helyre a RAID-partíción.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Mivel [SELinux a fürt működését gátolja](http://galeracluster.com/documentation-webpages/configuration.html#selinux), le kell tiltani az aktuális munkamenetre szükséges. Szerkesztés `/etc/selinux/config` le kell tiltani a későbbi újraindítások.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Ellenőrizze a MySQL futtatása.

   a. Indítsa el a MySQL.

           service mysql start
   b. A MySQL-telepítés biztonságos, a legfelső szintű jelszó beállítását, távolítsa el a névtelen felhasználók számára, hogy tiltsa le a távoli legfelső szintű bejelentkezést és a test-adatbázis eltávolításához.

           mysql_secure_installation
   c. Felhasználó létrehozása az adatbázis, a fürt működését, és szükség esetén az alkalmazások számára.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Állítsa le a MySQL.

            service mysql stop
7. Hozzon létre egy konfigurációs helyőrző.

   a. A fürtbeállítások helyőrzője létrehozása a MySQL konfigurációjának a szerkesztésével. Cserélje le a  **`<Variables>`**  vagy állítsa vissza a most. Amely akkor után hozzon létre egy virtuális Gépet a sablon alapján történik.

            vi /etc/my.cnf.d/server.cnf
   b. Szerkessze a  **[galera]**  szakaszt, és törölje a jelölést.

   c. Szerkessze a **[mariadb]** szakasz.

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
8. Nyissa meg a szükséges portok a tűzfalon keresztül FirewallD CentOS 7.

   * MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA ITT:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Töltse be újra a tűzfalon:`firewall-cmd --reload`

9. A rendszer a teljesítmény optimalizálása. További információkért lásd: [teljesítményének hangolása stratégia](optimize-mysql.md).

   a. A MySQL-konfigurációs fájl újból szerkeszteni.

            vi /etc/my.cnf.d/server.cnf
   b. Szerkessze a **[mariadb]** szakaszt, és hozzáfűzése a következőket:

   > [!NOTE]
   > Azt javasoljuk, hogy innodb\_puffer\_pool_size a virtuális gép memória 70 százalék. Ebben a példában beállítása 2.45 GB a közepes 3.5-ös GB RAM-MAL rendelkező Azure virtuális gépet.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Állítsa le a MySQL, futtatását indítási elkerülése érdekében a fürt megszakítása, ha a csomópont hozzáadása a MySQL-szolgáltatás letiltása, és a gép kiosztásának megszüntetése.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Rögzítse a virtuális Gépet a portálon keresztül. (Jelenleg [adja ki az Azure CLI-eszközei #1268](https://github.com/Azure/azure-xplat-cli/issues/1268) ismerteti azt a tényt, hogy az Azure CLI-eszközök által rögzített lemezképeket nem rögzíthető lemezkép a mellékelt adatok lemezek.)

    a. Állítsa le a gépet a portálon keresztül.

    b. Kattintson a **rögzítése** , és adja meg a lemezkép neve, ahogyan **mariadb-galera-lemezkép**. Adjon meg egy leírást, és ellenőrizze a "Rendelkezik futtattam a waagent."
      
      ![A virtuális gép rögzítése](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>A fürt létrehozása
Hozzon létre három virtuális gépek sablonnal létrehozott, és ezután konfigurálása és a fürt elindításához.

1. Az első CentOS 7 virtuális gép létrehozása a mariadb-galera-lemezkép lemezképet létrehozni, a következő adatokat:

 - Virtuális hálózati név: mariadbvnet
 - Alhálózati: mariadb
 - Méret gépen: Közepes
 - Felhőszolgáltatás neve: mariadbha (vagy mariadbha.cloudapp.net elérni kívánt nevet)
 - Számítógépnév: mariadb1
 - Felhasználónév: azureuser
 - SSH-elérést: engedélyezve
 - Átadja a SSH tanúsítvány .pem fájlt, és /path/to/key.pem cseréje az elérési út a generált .pem SSH-kulcs tárolására.

   > [!NOTE]
   > A következő parancsokat az átláthatóság többsoros vannak osztva, de egyes egy sorba kell megadnia.
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
2. Hozzon létre két további virtuális gépek csatlakoztatja őket a mariadbha felhőalapú szolgáltatás. Módosítsa a virtuális gép nevét és az SSH-port nem ütközik más virtuális gépek ugyanazt a felhőszolgáltatásban található egy egyedi portot.

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
3. A három virtuális gépek mindegyikének a belső IP-cím lekérése a következő lépésben lesz szüksége:

    ![IP-cím beolvasása](./media/mariadb-mysql-cluster/IP.png)
4. SSH használatával jelentkezzen be a három virtuális gépeket, majd szerkessze a konfigurációs fájlt a hozzájuk.

        sudo vi /etc/my.cnf.d/server.cnf

    Állítsa vissza  **`wsrep_cluster_name`**  és  **`wsrep_cluster_address`**  eltávolításával a  **#**  a sor elején.
    Továbbá cserélje le  **`<ServerIP>`**  a  **`wsrep_node_address`**  és  **`<NodeName>`**  a  **`wsrep_node_name`**  rendelkező a A virtuális gép IP cím és nevet, illetve kódrészig, és azokat a sorokat.
5. Indítsa el a fürt a MariaDB1, és hagyja, hogy az indítási parancsot.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Indítsa el a MySQL MariaDB2 és MariaDB3, és hagyja, hogy az indítási parancsot.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>A fürt terheléselosztás
A fürtözött virtuális gépek létrehozásakor hozzá azokat a clusteravset annak érdekében, hogy a különböző tartalék és a frissítési tartományok fel, és egyszerre csak a soha nem nem karbantartás, minden számítógépen, hogy Azure nevű rendelkezésre állási csoportok. Ez a konfiguráció megfelel az Azure szolgáltatásiszint-szerződéssel (SLA) támogatott.

Azure Load Balancer segítségével kiegyenlítheti vonatkozó a három csomópontok között.

A következő parancsokat a számítógépen az Azure parancssori felület használatával.

A parancs paraméterei struktúra a következő:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

A parancssori felület a load balancer mintavétel gyakoriságát 15 másodpercre állítja, elképzelhető, hogy egy kicsit túl hosszú. A portál módosítása **végpontok** bármely, a virtuális gépek.

![Végpont szerkesztése](./media/mariadb-mysql-cluster/Endpoint.PNG)

Válassza ki **konfigurálja újra az elosztott terhelésű készlet**.

![Konfigurálja újra az elosztott terhelésű készlet](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Változás **mintavételi időköze** 5 másodperc és a változtatások mentéséhez.

![Mintavételi időköz módosítása](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>A fürt ellenőrzése
A rögzített munkát. A fürt elérhető kell, hogy most a `mariadbha.cloudapp.net:3306`, amely találatok, a terheléselosztó és útvonal terhelésigényét a három virtuális gépek közötti hatékony és problémamentesen.

A kedvenc MySQL-ügyfél segítségével csatlakoztassa, vagy csatlakoztassa a virtuális gépek ellenőrzése, hogy működik-e a fürt egyik.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Ezután hozzon létre egy adatbázist, és feltöltheti olyan néhány adat.

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
## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy három csomópontos MariaDB létrehozott + Galera magas rendelkezésre állású fürt Azure virtuális gépen futó CentOS 7. A virtuális gépek az elosztott terhelésű Azure terheléselosztó.

Érdemes megtekinteni [MySQL-fürt Linux rendszeren másik módja](mysql-cluster.md) és módjai [optimalizálása és MySQL az Azure Linux virtuális gépeken futó teljesítménytesztelési](optimize-mysql.md).

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
[hitelesítéshez SSH-kulcs létrehozása]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
