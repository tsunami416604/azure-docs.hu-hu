---
title: Az elosztott terhelésű készletek MySQL clusterize |} Microsoft Docs
description: Állítson be egy elosztott terhelésű, a magas rendelkezésre állású, a klasszikus üzembe helyezési modellel, az Azure-on Linux MySQL-fürt létrehozása
services: virtual-machines-linux
documentationcenter: ''
author: bureado
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: e2671def47879e3d4eae000c9084cd458e29b933
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Elosztott terhelésű készletek segítségével clusterize MySQL Linux rendszeren
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A [Resource Manager-sablon](https://azure.microsoft.com/documentation/templates/mysql-replication/) érhető el, ha a MySQL-fürt üzembe kell.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk ismerteti, és helyezhetők üzembe a Microsoft Azure-böngészést a MySQL-kiszolgáló magas rendelkezésre állás mint egy ismertetése a magas rendelkezésre állású Linux-alapú szolgáltatások különböző szempontok mutatja be. Ez a megközelítés bemutató videó megtalálható [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Azt fogja szerkezeti osztott, két csomópontos, egyszeri MySQL magas rendelkezésre állású megoldás DRBD, Corosync és támasztja alapján. Csak egy csomópont MySQL fut egyszerre. A DRBD erőforrás írásakor vagy olvasásakor korlátozódik is csak egy csomópont egyszerre.

Mert fogjuk elosztott terhelésű készlet a Microsoft Azure-ban ciklikus multiplexelés funkcionalitásra vagy a végpont észlelési, eltávolítás és szabályos helyreállítása a VIP, nincs szükség például HÉ, VIP-megoldások esetén. A VIP nem egy globálisan irányíthatóvá teszi IPv4-cím, a Microsoft Azure által hozzárendelt, amikor először létre kell hoznia a felhőalapú szolgáltatáshoz.

Nincsenek elérhető, mint egy virtuális gép MySQL, beleértve a NBD fürt, Percona, Galera és több köztes megoldások, beleértve a legalább egy másik lehetséges architektúrához [VM raktár számára](http://vmdepot.msopentech.com). Mindaddig, amíg ezek a megoldások képes replikálni, amely a csoportos küldést vagy szórásos és egyedi küldéses, és nem függ a megosztott tárhelytől vagy több hálózati adapterrel, a forgatókönyvek egyszerűen telepíthető a Microsoft Azure kell lennie.

Ezek a fürtszolgáltatás architektúrák kiterjeszthető más termékek, például a PostgreSQL és OpenLDAP hasonló módon. Például a megosztás terheléselosztás eljárás sikeresen tesztelték, több főkiszolgálós OpenLDAP, és figyelheti a Channel 9 blogjában.

## <a name="get-ready"></a>Felkészülés
A következő erőforrások és képességek lesz szüksége:

  - Egy érvényes előfizetéssel, létrehozhat legalább két virtuális gépek Microsoft Azure-fiókra (ebben a példában a XS lett megadva)
  - A hálózat és alhálózat
  - Az affinitáscsoportokban
  - Rendelkezésre állási csoportok
  - Virtuális merevlemezek létrehozása ugyanabban a régióban, mint a felhőalapú szolgáltatás, és csatolja őket a Linux virtuális gépek

### <a name="tested-environment"></a>Tesztelt környezet
* Ubuntu 13.10
  * DRBD
  * MySQL-kiszolgáló
  * Corosync és támasztja

### <a name="affinity-group"></a>Affinitáscsoportok
A megoldás affinitáscsoportok létrehozása az Azure-portálon való kiválasztása **beállítások**, és egy affinitáscsoporthoz létrehozása. Újabb kiosztott erőforrásokat rendeli az affinitáscsoportban.

### <a name="networks"></a>Hálózatok
Egy új hálózatot hoznak létre, és egy alhálózat létre van hozva, a hálózaton belül. A példa 10.10.10.0/24 hálózaton belül csak egy /24 alhálózattal.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az első Ubuntu 13.10 VM Endorsed Ubuntu gyűjtemény lemezkép használatával hozták létre, és nevezik `hadb01`. Új felhőszolgáltatás az a folyamat, amely a hadb jön létre. Ez a név azt mutatja be, hogy a szolgáltatás további erőforrások hozzáadásakor megosztott, elosztott terhelésű jellegét. A létrehozását `hadb01` Eseménytelen és kész, a portál használatával. Az SSH a végpont automatikusan létrejön, és az új hálózati van kiválasztva. Most egy rendelkezésre állási készletét, a virtuális gépek is létrehozhat.

Az első virtuális gép létrehozása (technikailag, amikor a felhőalapú szolgáltatás létrehozása), akkor létre kell hoznia a második virtuális gép `hadb02`. A második virtuális gép Ubuntu 13.10 virtuális gép a gyűjteményből a portál használatával használni, de egy meglévő felhőszolgáltatáshoz `hadb.cloudapp.net`, egy új létrehozása helyett. A hálózati és a rendelkezésre állási készlet ki automatikusan. Egy SSH-végpontot rendel, túl.

Mindkét virtuális gép létrehozása után tekintse meg az SSH-port `hadb01` (22-es TCP) és `hadb02` (Ez automatikusan hozzárendelve az Azure-ban).

### <a name="attached-storage"></a>Csatlakoztatott tároló
Új lemez csatolása mindkét virtuális gépeket, és 5 GB-os létrehozásához a folyamat során. A lemezek a fő operációsrendszer-lemezek használt VHD-tároló tárolt. Miután lemezek létrehozni és csatolni, nincs szükség Linux újraindítást, mivel a kernel jelenik meg az új eszköz. Ez az eszköz megfelel általában `/dev/sdc`. Ellenőrizze `dmesg` a kimeneti oldal számára.

Minden egyes virtuális gépen, hozzon létre egy partíciót használatával `cfdisk` (elsődleges, Linux partíció) és az új partíció tábla írása. Ne hozzon létre egy fájlrendszer ezt a partíciót.

## <a name="set-up-the-cluster"></a>A fürt beállítása
APT segítségével telepítse a Corosync támasztja és DRBD mindkét Ubuntu virtuális gépeken. Ehhez a `apt-get`, futtassa a következő kódot:

    sudo apt-get install corosync pacemaker drbd8-utils.

Ne telepítse a MySQL most. Debian és Ubuntu telepítési parancsfájlokat inicializálja a MySQL adatkönyvtára a `/var/lib/mysql`, de a könyvtár a rendszer egy DRBD fájlrendszer váltotta fel, mert telepítenie kell a MySQL később.

Győződjön meg arról (használatával `/sbin/ifconfig`), hogy mindkét virtuális gépeket használ a 10.10.10.0/24 alhálózati cím és, hogy azok a ping paranccsal elérhető másik név szerint. Is `ssh-keygen` és `ssh-copy-id` való győződjön meg arról, hogy mindkét virtuális gépek kommunikálhatnak SSH-kapcsolaton keresztül anélkül, hogy a jelszót.

### <a name="set-up-drbd"></a>DRBD beállítása
Hozzon létre egy DRBD erőforrást, amely használja az alapul szolgáló `/dev/sdc1` előállításához partíciót egy `/dev/drbd1` ext3 fájlrendszerrel formázott és az elsődleges és másodlagos csomópontok használt erőforrást.

1. Nyissa meg `/etc/drbd.d/r0.res` , és másolja a következő erőforrás-definíció mindkét virtuális gépeken:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Az erőforrás inicializálása használatával `drbdadm` mindkét virtuális gépeken:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Az elsődleges virtuális gépen (`hadb01`), a DRBD erőforrás (elsődleges) tulajdonjogát kényszerítése:

        sudo drbdadm primary --force r0

Ha megvizsgálja a/proc/drbd tartalmát (`sudo cat /proc/drbd`) mindkét virtuális gépeken, megtekintheti az `Primary/Secondary` a `hadb01` és `Secondary/Primary` a `hadb02`, ezen a ponton a megoldás megfelel. Az 5-GB lemezterület az ügyfél számára díjmentesen a 10.10.10.0/24 hálózaton keresztül szinkronizálódnak.

Miután a szinkronizálás a lemez hozhatja létre a fájlrendszer `hadb01`. Tesztelési célokra ext2 használtuk, de az alábbi kód létrehoz egy ext3 fájlrendszert:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Csatlakoztassa a DRBD erőforrás
Most már készen áll a DRBD erőforrások csatolható fel a `hadb01`. Debian és származékaik használata `/var/lib/mysql` MySQL tartozó adatok könyvtár. Ha még nem telepítette a MySQL, mert a következő könyvtár létrehozásakor, és a csatlakoztatási a DRBD erőforrás. Ezt a beállítást, futtassa a következő kódot `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>MySQL beállítása
Most már készen áll a MySQL telepíthető `hadb01`:

    sudo apt-get install mysql-server

A `hadb02`, két lehetőség közül választhat. Mysql-kiszolgáló, amely /var/lib/mysql létrehozása, töltse ki az új adatok könyvtárat, és távolítsa el a tartalom telepítése. Ezt a beállítást, futtassa a következő kódot `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A második lehetőség van, így `hadb02` és telepítse a mysql-kiszolgáló létezik. Telepítési parancsfájlokat megfigyelheti a meglévő verziót, és nem touch.

Futtassa a következő kódot `hadb01`:

    sudo drbdadm secondary –force r0

Futtassa a következő kódot `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Ha nem tervezi feladatátvételi DRBD most, az első lehetőség bár késései kevésbé elegáns egyszerűbb. Beállítása után ez, megkezdheti a MySQL-adatbázis használata. Futtassa a következő kódot `hadb02` (vagy bármelyik közül a kiszolgálók aktív, DRBD szerint):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Az utolsó utasítás gyakorlatilag letiltja a hitelesítési ebben a táblázatban a gyökér szintű felhasználó számára. Ez a termelési szintű kell helyettesíteni utasításokat ADNIA és meg adva csak szemléltetési célokat szolgál.

Ha engedélyezni szeretné a virtuális gépek (Ez az útmutató célja) kívül a lekérdezések, is lehetővé teszik a MySQL hálózatkezelés kell. Mindkét virtuális gépeken, nyissa meg a `/etc/mysql/my.cnf` , és navigáljon `bind-address`. Módosítsa a címet a 127.0.0.1 0.0.0.0. A fájl mentése után adja ki a `sudo service mysql restart` a jelenlegi elsődleges.

### <a name="create-the-mysql-load-balanced-set"></a>A MySQL-az elosztott terhelésű készlet létrehozása
Lépjen vissza a portálra, írja be a `hadb01`, és válassza a **végpontok**. A végpont létrehozásához válassza MySQL (TCP 3306) a legördülő listából, majd **hozzon létre új elosztott terhelésű készlet**. Az elosztott terhelésű végpont neve `lb-mysql`. Állítsa be **idő** minimális 5 másodpercre.

Miután létrehozta a végpontot, Ugrás `hadb02`, válassza a **végpontok**, és hozzon létre egy végpontot. Válasszon `lb-mysql`, majd a legördülő listából válassza ki a MySQL. Ebben a lépésben az Azure parancssori felület is használható.

Most már rendelkezik minden a fürt kézi művelet szükséges.

### <a name="test-the-load-balanced-set"></a>Az elosztott terhelésű készlet tesztelése
Tesztek külső gépből hajtható végre a MySQL-ügyfélprogrammal, vagy bizonyos alkalmazások, például egy Azure-webhelyen fut phpMyAdmin használatával. Ebben az esetben egy másik Linux mezőben használt MySQL a parancssori eszköz:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manuális feladatátvétele
Feladatátvétel szimulálhatja MySQL leállítása, DRBD tartozó elsődleges váltás, és újból elindítani a MySQL.

Ez a feladat végrehajtásához futtassa a következő kódot hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Majd a hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Miután a rendszer átadja manuálisan, megismételheti a távoli lekérdezés, és tökéletesen működnek.

## <a name="set-up-corosync"></a>Corosync beállítása
Corosync támasztja működéséhez szükséges alapul szolgáló fürt infrastruktúra. A szívverés (és egyéb módszerek, például Ultramonkey) Corosync CRM funkciók valószínűségét, míg a funkció több hasonló szívverés támasztja marad.

A fő Corosync az Azure pedig az, hogy egyedi küldésű kommunikációs keresztül Corosync inkább az szórás keresztül csoportos küldés, de a Microsoft Azure-hálózat csak az egyedi küldéses támogatja.

Szerencsére Corosync van egy működő egyedi küldéses módot. A csak valós pedig az, hogy minden csomópont nem kommunikál egymással, mert meg kell határoznia a csomópontok a konfigurációs fájlokban, beleértve az IP-címét. A Corosync példa fájlokkal azt is megteheti, az egyedi küldés és a módosítás kötési cím, a csomópont listák és a naplózási könyvtár (Ubuntu használ `/var/log/corosync` közben a példa fájlok használata `/var/log/cluster`), és kvórum eszközök engedélyezése.

> [!NOTE]
> Használja a következő `transport: udpu` irányelv és a manuálisan megadott IP-címek mindkét csomópont.

Futtassa a következő kódot `/etc/corosync/corosync.conf` mindkét csomópontok:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Másolja a konfigurációs fájl mindkét virtuális gépeken, és mindkét csomópont Corosync elindításához:

    sudo service start corosync

Röviddel a szolgáltatás indítása, a fürt kell meghatározni az aktuális gyűrűben, és a kvórum kell kialakítani. Ez a funkció jelenleg ellenőrizheti a naplók megtekintésével, vagy futtassa a következő kódot:

    sudo corosync-quorumtool –l

Az alábbi képen hasonló kimenetet fog látni:

![corosync-quorumtool - l minta kimenet](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Támasztja beállítása
Támasztja a fürt erőforrások figyelése, határozza meg, ha az elsődleges leáll, és ezeket az erőforrásokat váltani a másodlagos adatbázist használja. Erőforrások elérhető parancsfájlokkal, illetve LSB (init-bA), parancsfájlok egyéb lehetőségek között definiálhatók.

Azt szeretnénk, ha támasztja "tulajdonosa" a DRBD erőforrás, a csatlakoztatási pont és a MySQL-szolgáltatás. Ha támasztja és DRBD bekapcsolása, csatlakoztatni és leválasztani, és majd elindíthatók és leállíthatók MySQL a megfelelő sorrendben ha valami rossz fordul elő, ha az elsődleges, a telepítés befejeződött.

Támasztja első telepítésekor a konfiguráció legyen egyszerű, elég, például:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Ellenőrizze a konfigurációs futtatásával `sudo crm configure show`.
2. Ezután hozzon létre egy fájlt (például `/tmp/cluster.conf`) az a következőket:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Töltse be a fájlt bővíti a konfigurációt. Csak kell ehhez a egy csomópont.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Győződjön meg arról, hogy támasztja kezdődik-e a mindkét csomópont indítása:

        sudo update-rc.d pacemaker defaults

5. A `sudo crm_mon –L`, győződjön meg arról, hogy a csomópontok közül a master, a fürt és az erőforrások fut-e. Csatlakozási és ps segítségével ellenőrizze, hogy fut-e az erőforrásokat.

Az alábbi képernyőfelvételen látható `crm_mon` egy csomópont leállt (Ctrl + C kiválasztásával kilépési):

![crm_mon csomópont leállítása](./media/mysql-cluster/image002.png)

Ezen a képernyőfelvételen látható csomópontok, a egy fő és egy alárendelt jeleníti meg:

![crm_mon műveleti főkiszolgáló/alárendelt](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Tesztelés
Készen áll az Automatikus feladatátvétel szimuláció. Ehhez két módja van: ideiglenes és rögzített.

A helyreállítható módja a fürt leállítás függvényt használja: ``crm_standby -U `uname -n` -v on``. Ha ez a fő használja, az alárendelt élvez. Ne felejtse el, állítsa vissza ki. Ha ezt elmulasztja, akkor crm_mon készenléti egy csomópont jelenjen meg.

A rögzített módja az elsődleges virtuális gép (hadb01) leáll, a portálon, vagy ha megváltoztatja a paraméterben megadott futtatási szint a virtuális Gépre (vagyis halt, leállítási). Ez segít Corosync és támasztja jelzés által, hogy a fő tartozó is le. Ez tesztelheti (hasznos a karbantartási időszakok), de is kényszeríthető a forgatókönyv által a virtuális gép zárolása.

## <a name="stonith"></a>STONITH
Az Azure parancssori felület helyett egy fizikai eszközt vezérlő STONITH parancsfájl segítségével a virtuális gép leállítása ki kell lennie. Használhat `/usr/lib/stonith/plugins/external/ssh` kiinduló és a fürt konfigurációjába STONITH engedélyezése. Az Azure CLI globálisan kell telepíteni, és a közzétételi beállítások és a profil a fürt felhasználói kell betölteni.

Példakód az erőforrás érhető el a [GitHub](https://github.com/bureado/aztonith). A fürt konfigurációjának módosítása a következő hozzáadásával `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> A parancsfájl nem hajtható végre fel/le ellenőrzi. Az eredeti SSH erőforrás volt a 15 ping-ellenőrzéseket, de egy Azure virtuális gép helyreállítási idő további változó lehet.

## <a name="limitations"></a>Korlátozások
A következő korlátozások vonatkoznak:

* A linbit DRBD erőforrás parancsfájl, amely kezeli a DRBD erőforrásként támasztja használja a `drbdadm down` Ha egy csomópont leáll, akkor is, ha a csomópont csak készenléti van folyamatban. Ennek oka nem ideális megoldás az alárendelt fog nem lehet szinkronizálása a DRBD erőforrás fő lekérdezi az írási műveletek során. Ha a fő nem graciously sikertelen, az alárendelt átveheti egy régebbi fájlrendszer állapota. Ez megoldására két lehetséges módja van:
  * Érvényesítési egy `drbdadm up r0` a fürt összes csomópontján keresztül egy helyi (nem clusterized)-figyelő
  * A linbit DRBD parancsfájl, ügyelve arra, hogy szerkesztési `down` nem hívják meg `/usr/lib/ocf/resource.d/linbit/drbd`
* A load balancer legalább öt másodpercenként kell válaszolnia, így az alkalmazások fürttámogató legyen, és jobban tűrik a timeout kell. Egyéb-architektúrák esetén az-app várólisták és a lekérdezés middlewares, például segíthet is.
* MySQL hangolása győződjön meg arról, hogy írás kezelhető ütemben történik, és gyorsítótárak tartalmát lemezre memória veszteség minimalizálása érdekében a lehető gyakran szükség.
* Az írási teljesítmény függő a virtuális gép kapcsolódnak össze a virtuális kapcsolón, mert ez a mechanizmus DRBD használja replikálja az eszközt.
