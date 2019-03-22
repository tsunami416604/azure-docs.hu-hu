---
title: A SUSE Linux Enterprise Server Azure virtuális gépeken SAP Hana magas rendelkezésre állású |} A Microsoft Docs
description: A SUSE Linux Enterprise Server Azure virtuális gépeken SAP Hana magas rendelkezésre állás
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: f0bac9d50e73ed703905545261e86796ede214e2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180840"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>A SUSE Linux Enterprise Server Azure virtuális gépeken SAP Hana magas rendelkezésre állás

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

A helyszíni fejlesztési vagy a HANA-Rendszerreplikálást használja, vagy megosztott tárterületet használnak, a SAP HANA magas rendelkezésre állás érdekében.
Az Azure-beli virtuális gépek (VM) HANA Rendszerreplikáció az Azure-on jelenleg az egyetlen támogatott magas rendelkezésre állású függvény. SAP HANA-replikáció egy elsődleges csomópontot és legalább egy másodlagos csomópont áll. Az adatok az elsődleges csomópont változásai replikálódnak a másodlagos csomópontra szinkron vagy aszinkron módon.

Ez a cikk bemutatja, hogyan telepíthet és a virtuális gép konfigurálása, telepítse a fürt-keretrendszert, és telepítse és konfigurálja az SAP HANA-Rendszerreplikálást.
A példa konfigurációk esetén a telepítési parancsokat, a példányok száma **03**, és a HANA rendszer-azonosító **HN1** szolgálnak.

Először olvassa el az alábbi SAP-megjegyzések és tanulmányok:

* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * A lista SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek.
  * Fontos kapacitási adatainak Azure Virtuálisgép-méretet.
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk.
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure-ban.
* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott központi szoftvertelepítést előfeltételeit ismerteti.
* SAP-Jegyzetnek [2205917] javasoljuk az SAP-alkalmazások SUSE Linux Enterprise Server operációs rendszer beállításait.
* SAP-Jegyzetnek [1944799] rendelkezik az SAP HANA-irányelvek SUSE Linux Enterprise Server SAP-alkalmazások számára.
* SAP-Jegyzetnek [2178632] a figyelési metrikákat, az SAP az Azure-ban jelentett összes adatai olvashatók.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1984787] SUSE Linux Enterprise Server 12 vonatkozó általános információkat tartalmaz.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* SAP-Jegyzetnek [401162] elkerülésével "címet már használja" HANA Rendszerreplikáció beállításakor szóló információt tartalmaz.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges az SAP-megjegyzések Linux rendszeren.
* [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Az Azure virtuális gépek tervezése és megvalósítása a linuxon futó SAP] [ planning-guide] útmutató.
* [Az Azure virtuális gépek üzembe helyezése Linuxon futó SAP-] [ deployment-guide] (Ez a cikk).
* [Az Azure Virtual Machines DBMS üzembe helyezése Linuxon futó SAP-] [ dbms-guide] útmutató.
* [SUSE Linux Enterprise Server SAP alkalmazások 12 SP3 ajánlott eljárások][sles-for-sap-bp]
  * Az SAP HANA SR teljesítmény optimalizált infrastruktúrájának beállításával (SLES for SAP alkalmazások 12 SP1). Az útmutató összes beállítása a helyszíni fejlesztési SAP HANA-Rendszerreplikálást a szükséges információkat tartalmazza. Ez az útmutató használja kiindulópontként.
  * Az SAP HANA SR költség optimalizált infrastruktúrájának beállításával (SLES for SAP alkalmazások 12 SP1)

## <a name="overview"></a>Áttekintés

SAP HANA magas rendelkezésre állás, telepítve van a két virtuális gépen. Az adatok replikálódnak a HANA-Rendszerreplikálást.

![SAP HANA magas rendelkezésre állás – Áttekintés](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA System Replication setup uses a dedicated virtual hostname and virtual IP addresses. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztó konfigurációját jeleníti meg:

* Előtér-konfiguráció: IP-cím 10.0.0.13 hn1-DB-hez készült
* Háttér-konfiguráció: HANA-Rendszerreplikálást részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik
* Mintavételi Port: Port 62503
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezése Linux rendszeren

Az erőforrás-ügynököt az SAP Hana SAP-alkalmazások a SUSE Linux Enterprise Server tartalmazza.
Az Azure Marketplace-en SUSE Linux Enterprise Server SAP alkalmazások 12, amelyek új virtuális gépek telepítéséhez használhatja a kép tartalmazza.

### <a name="deploy-with-a-template"></a>Üzembe helyezés egy sablon használatával

A gyorsindítási sablonok, amelyek a Githubon a szükséges erőforrások üzembe helyezéséhez használhatja. A sablon üzembe helyezi a virtuális gépek, a load balancer, a rendelkezésre állási csoport, és így tovább.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [adatbázis sablon] [ template-multisid-db] vagy a [sablon összevont] [ template-converged] az Azure Portalon. 
    Az adatbázis-sablon csak egy adatbázishoz tartozó terheléselosztási szabályok hoz létre. A konvergens sablon ASCS/SCS és SSZON (csak Linux) példány is a terheléselosztási szabályokat hoz létre. Ha azt tervezi, hogy az SAP NetWeaver-alapú rendszert telepíti, és szeretne az ASC/SCS példányhoz telepítse az azonos gépekre, használja a [sablon összevont][template-converged].

1. Adja meg a következő paraméterekkel:
    - **Rendszer-azonosító SAP**: Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások előtagjaként is szolgál.
    - **Írja be a verem**: (Ezt a paramétert akkor alkalmazható, csak akkor, ha a konvergens sablont használja.) Válassza ki az SAP NetWeaver a készlet típusa.
    - **Operációs rendszer típusa**: Válasszon ki egy Linux-disztribúció. Ebben a példában válassza **SLES 12**.
    - **Adatbázistípus**: Válassza ki **HANA**.
    - **SAP-rendszer mérete**: Adja meg az SAP, amelyet az új rendszerre, adja meg. Ha nem biztos a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
    - **Rendszer rendelkezésre állását**: Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
    - **Rendszergazdai felhasználónév és jelszó rendszergazdai**: Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a számítógépen.
    - **Új vagy meglévő alhálózaton**: Meghatározza, hogy egy új virtuális hálózatot és alhálózatot kell létrehozni, vagy egy meglévő alhálózatot használja. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
    - **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában tűnik **/subscriptions/\<előfizetés-azonosító > /resourceGroups/\<erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/\<virtuális hálózat neve > /subnets/ \<alhálózat neve >**.

### <a name="manual-deployment"></a>Manuális telepítés

> [!IMPORTANT]
> Győződjön meg róla, hogy az operációs rendszer választja SAP-minősítéssel rendelkező SAP Hana, az adott virtuális gépek típusai, használja a. Az SAP HANA listája tanúsított Virtuálisgép-típusok és az operációs rendszer kiadások az azokat kereshetők [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ellenőrizze, hogy a virtuális gép típusú felsorolt lekérni az SAP HANA teljes listáját a részletek megtekintéséhez kattintson az adott virtuális gép típusa támogatott operációsrendszer-kiadások
>  

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási csoportot.
   - Állítsa be a maximális frissítési tartományt.
1. Load balancer létrehozása (belső).
   - Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. 1 virtuális gép létrehozása.
   - SLES4SAP rendszerkép használata az Azure-katalógus, amely az SAP Hana-hoz, a kiválasztott VM-típus támogatott.
   - Válassza ki a 3. lépésében létrehozott rendelkezésre állási csoport.
1. 2 virtuális gép létrehozása.
   - SLES4SAP rendszerkép használata az Azure-katalógus, amely az SAP Hana-hoz, a kiválasztott VM-típus támogatott.
   - Válassza ki a 3. lépésében létrehozott rendelkezésre állási csoport. 
1. Adatlemezek hozzáadása.
1. A load balancer konfigurálása. Először hozzon létre egy előtérbeli IP-címkészlet:

   1. Válassza ki a nyissa meg a terheléselosztó **előtérbeli IP-címkészlet**, és válassza ki **Hozzáadás**.
   1. Adja meg az új előtérbeli IP-címkészlet nevét (például **hana-frontend**).
   1. Állítsa be a **hozzárendelés** való **statikus** , és adja meg az IP-címet (például **10.0.0.13**).
   1. Kattintson az **OK** gombra.
   1. Az új előtérbeli IP-címkészlet létrehozása után jegyezze fel a készlet IP-cím.

1. Ezután hozzon létre egy háttércímkészletet:

   1. Válassza ki a nyissa meg a terheléselosztó **háttérkészletek**, és válassza ki **Hozzáadás**.
   1. Adja meg az új háttérkészlet nevét (például **hana-háttérrendszer**).
   1. Válassza ki **adjon hozzá egy virtuális gépet**.
   1. Válassza ki a 3. lépésében létrehozott rendelkezésre állási csoport.
   1. Válassza ki a virtuális gépek, az SAP HANA-fürt.
   1. Kattintson az **OK** gombra.

1. Következő lépésként hozzon létre egy állapotmintát:

   1. Válassza ki a nyissa meg a terheléselosztó **állapot-mintavételei**, és válassza ki **Hozzáadás**.
   1. Adja meg az új állapotadat-mintavétel nevét (például **hana-hp**).
   1. Válassza ki **TCP** protokoll és port 625**03**. Tartsa a **időköz** értéket 5-ben és a **nem kifogástalan állapot küszöbértéke** értéke 2.
   1. Kattintson az **OK** gombra.

1. Az SAP HANA 1.0 a terheléselosztási szabályok létrehozása:

   1. Válassza ki a nyissa meg a terheléselosztó **terheléselosztási szabályok**, és válassza ki **Hozzáadás**.
   1. Adja meg az új terheléselosztó-szabályt nevét (például hana-lb-3**03**15).
   1. Válassza ki az előtérbeli IP-címet, a háttérkészlet és az állapotmintákat, amelyek a korábban létrehozott (például **hana-frontend**).
   1. Tartsa a **protokoll** beállítása **TCP**, és adja meg a 3. port**03**15.
   1. Növelje a **üresjárati időkorlát** akár 30 percig.
   1. Ügyeljen arra, hogy **Floating IP engedélyezése**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a 3. port**03**17.

1. Az SAP HANA 2.0 a rendszer adatbázishoz tartozó terheléselosztási szabályok létrehozása:

   1. Válassza ki a nyissa meg a terheléselosztó **terheléselosztási szabályok**, és válassza ki **Hozzáadás**.
   1. Adja meg az új terheléselosztó-szabályt nevét (például hana-lb-3**03**13).
   1. Válassza ki az előtérbeli IP-címet, a háttérkészlet és az állapotmintákat, amelyek a korábban létrehozott (például **hana-frontend**).
   1. Tartsa a **protokoll** beállítása **TCP**, és adja meg a 3. port**03**13.
   1. Növelje a **üresjárati időkorlát** akár 30 percig.
   1. Ügyeljen arra, hogy **Floating IP engedélyezése**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a 3. port**03**14.

1. Az SAP HANA 2.0 először hozzon létre a bérlői adatbázis vonatkozó terheléselosztási szabályok:

   1. Válassza ki a nyissa meg a terheléselosztó **terheléselosztási szabályok**, és válassza ki **Hozzáadás**.
   1. Adja meg az új terheléselosztó-szabályt nevét (például hana-lb-3**03**40).
   1. Válassza ki az előtérbeli IP-cím, a háttérkészlet és a korábban létrehozott állapotadat-mintavétel (például **hana-frontend**).
   1. Tartsa a **protokoll** beállítása **TCP**, és adja meg a 3. port**03**40.
   1. Növelje a **üresjárati időkorlát** akár 30 percig.
   1. Ügyeljen arra, hogy **Floating IP engedélyezése**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a portokhoz 3**03**41-es és 3**03**42.

Az SAP Hana-hoz a szükséges portok kapcsolatos további információkért olvassa el a fejezet [bérlői adatbázis-kapcsolatok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) a a [SAP HANA bérlői adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutató vagy [SAP Megjegyzés 2388694][2388694].

> [!IMPORTANT]
> Ne engedélyezze a TCP időbélyegeket Azure Load Balancer mögé helyezett Azure virtuális gépeken. Sikertelen állapotadat-mintavételek engedélyezése TCP időbélyegek miatt. A paramétert **net.ipv4.tcp_timestamps** való **0**. További részletekért lásd: [Load Balancer állapot-mintavételei](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).
> SAP-jegyzetnek [2382421](https://launchpad.support.sap.com/#/notes/2382421) ellentmondásos utasítás beküldte net.ipv4.tcp_timestamps állítsa 1-re is tartalmaz. Az Azure virtuális gépek Azure-terheléselosztó mögött elhelyezni, a paraméter értéke **net.ipv4.tcp_timestamps** való **0**. 

## <a name="create-a-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](high-availability-guide-suse-pacemaker.md) a HANA-kiszolgáló alapszintű támasztja fürt létrehozásához. SAP HANA és az SAP NetWeaver (A) SCS támasztja ugyanabban a fürtben is használhatja.

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:
- **[A]**: A lépés minden csomópont számára vonatkozik.
- **[1]**: A lépés csak 1 csomópont vonatkozik.
- **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

1. **[A]**  a lemez elrendezése beállítása: **A Logical Volume Manager (LVM)**.

   Azt javasoljuk, hogy a köteteket, amelyek adatokat tárolhatnak, és a naplófájlok LVM használja. Az alábbi példa azt feltételezi, hogy a virtuális gépek négy adatok csatlakoztatott lemezekkel rendelkezik, amely segítségével hozzon létre két kötetet.

   Listázza az összes elérhető lemez:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Példa a kimenetre:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Az összes használni kívánt lemez fizikai köteteket hozhat létre:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre egy kötetet az adatfájlok számára. A naplófájlok és a egy SAP Hana-megosztott könyvtár egy kötetcsoport használja:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   A logikai köteteket hozhat létre. Egy lineáris kötetet hoz létre használatakor `lvcreate` nélkül a `-i` váltani. Javasoljuk, hogy a jobb i/o-teljesítmény csíkozott kötetet hoz létre, a `-i` argumentumot kell lennie a mögöttes fizikai kötet számát. Ebben a dokumentumban két fizikai kötetre használt adatmennyiség, ezért a `-i` kapcsoló argumentum értéke **2**. Egy fizikai kötetre szolgál a naplózási kötet, így nincs `-i` explicit módon kapcsolót. Használja a `-i` váltson, és állítsa be az alapul szolgáló fizikai kötet számát, amikor egynél több fizikai kötetre használ az egyes adatok, log, vagy megosztott köteteket.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   A csatlakoztatási könyvtárak létrehozása, és másolja ki az összes logikai kötetek UUID:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Hozzon létre `fstab` bejegyzéseket a három logikai kötetek:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   A következő sort a Beszúrás a `/etc/fstab` fájlt:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Az új kötetek csatlakoztatása:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  a lemez elrendezése beállítása: **Egyszerű lemez**.

   Bemutató rendszerekhez helyezze el a HANA adathoz és naplófájlhoz egy lemezen. Hozzon létre egy partíciót a /dev/disk/azure/scsi1/lun0, és formázza xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Ez a sor beszúrása a /etc/fstab fájlban:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   A céloldali könyvtár létrehozása, és csatlakoztassa a lemezt:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása a minden gazdagép esetén.

   A DNS-kiszolgáló használatára, vagy módosítsa az összes csomóponton a Hosts fájlt. Ez a példa bemutatja, hogyan használható a Hosts fájl.
   Cserélje le az IP-cím és az állomásnevet, az alábbi parancsokban:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat a Hosts fájl. Módosítsa az IP-cím és a környezet megfelelő állomásnév:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Az SAP HANA magas rendelkezésre állású csomagok telepítéséhez:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Az SAP HANA-Rendszerreplikálást telepítéséhez hajtsa végre a fejezete 4 a [SAP HANA SR teljesítmény optimalizált Házirendforgatókönyvek útmutatója](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]**  Futtassa a **hdblcm** program HANA DVD-ről. Írja be a parancssorba a következő értékeket:
   * Válassza ki a telepítés: Adja meg **1**.
   * Válassza ki a további összetevők telepítéséhez: Adja meg **1**.
   * Adja meg a telepítési útvonal [/ hana/megosztott]: Válassza ki a adja meg.
   * Adja meg a helyi gazdagép neve [.]: Válassza ki a adja meg.
   * Biztosan további állomásokat adhat hozzá a rendszer? (y/n) [n]: Válassza ki a adja meg.
   * Adja meg az SAP HANA rendszer-azonosító: Adja meg például a HANA biztonsági azonosító: **HN1**.
   * Adja meg a [00] száma: Adja meg a HANA-példányok számát. Adja meg **03** Ha használja az Azure-sablon vagy követni a manuális központi telepítése című szakaszát.
   * Válassza ki az adatbázis mód / adja meg az Index [1]: Válassza ki a adja meg.
   * Válassza ki a rendszer terhelése / adja meg az Index [4]: Válassza ki a rendszer használati értéket.
   * Adja meg a helyet, az adatkötetek [/ hana/data/HN1]: Válassza ki a adja meg.
   * Adja meg a helyet, Log kötetek [/ hana/log/HN1]: Válassza ki a adja meg.
   * Maximális memória mennyiségét korlátozza? [n]: Válassza ki a adja meg.
   * Adja meg a tanúsítvány állomásneve gazdagép "..." [...]: Válassza ki a adja meg.
   * Enter SAP Host Agent User (sapadm) Password: Adja meg a gazdagép-ügynök felhasználói jelszót.
   * Confirm SAP Host Agent User (sapadm) Password: Adja meg a gazdagép ügynök felhasználói jelszót kétszer.
   * Adja meg a rendszergazdát (hdbadm) jelszavát: Adja meg a rendszergazda jelszavát.
   * A rendszergazda (hdbadm) jelszó megerősítése: Adja meg a rendszer rendszergazdai jelszót kétszer.
   * Adja meg a rendszer rendszergazdai kezdőkönyvtár [/ usr/sap/HN1/home]: Válassza ki a adja meg.
   * Adja meg a rendszer rendszergazdai bejelentkezési rendszerhéj [/ bin/sh]: Válassza ki a adja meg.
   * Adja meg a rendszergazda felhasználó azonosítója [1001]: Válassza ki a adja meg.
   * Adjon meg azonosító a felhasználói csoportot (sapsys) [79]: Válassza ki a adja meg.
   * Enter Database User (SYSTEM) Password: Adja meg az adatbázis felhasználói jelszót.
   * Adatbázis (rendszer) felhasználói jelszó megerősítése: Adja meg az adatbázis felhasználói jelszót kétszer.
   * Számítógép újraindítása után indítsa újra a rendszert? [n]: Válassza ki a adja meg.
   * Folytatja? (y/n): Ellenőrizze az összegzést. Adja meg **y** folytatásához.

1. **[A]**  SAP gazdagép-ügynök frissítése.

   Töltse le a legújabb SAP gazdagép-ügynök archívumot a a [SAP Software Center] [ sap-swcenter] , és futtassa a következő parancsot az ügynököt. Cserélje le a letöltött fájlra mutasson az archívum elérési útja:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Az SAP HANA 2.0-s replikációs konfigurálása

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:

* **[A]**: A lépés minden csomópont számára vonatkozik.
* **[1]**: A lépés csak 1 csomópont vonatkozik.
* **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

1. **[1]**  a bérlői adatbázis létrehozása.

   Az SAP HANA 2.0 vagy MDC használja, ha létrehozza az adatbázist, az SAP NetWeaver rendszernek. Cserélje le **NW1** az SAP-rendszer a biztonsági azonosítójával.

   Jelentkezzen be \<hanasid > adm, és hajtsa végre a következő parancsot:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Rendszer replikáció konfigurálása első csomópontjára:

   Jelentkezzen be \<hanasid > adm és az adatbázisok biztonsági mentése:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja a nyilvános kulcsokra épülő infrastruktúra fájljaiban a másodlagos hely:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Hozza létre az elsődleges hely:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **a(z) [2]**  Rendszer replikáció konfigurálása a második csomópont:
    
   A második csomópontot, a rendszer replikáció regisztrálja. Jelentkezzen be \<hanasid > adm, és futtassa a következő parancsot:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Az SAP HANA 1.0-s replikációs konfigurálása

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:

* **[A]**: A lépés minden csomópont számára vonatkozik.
* **[1]**: A lépés csak 1 csomópont vonatkozik.
* **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

1. **[1]**  Hozza létre a szükséges felhasználókat.

   Jelentkezzen be rendszergazdaként, és futtassa a következő parancsot. Cserélje le a félkövérrel szedett karakterláncok (HANA rendszer-azonosító **HN1** példányok száma és **03**) értékeket az SAP HANA telepítése:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Hozza létre a keystore bejegyzést.

   Jelentkezzen be rendszergazdaként, és a következő paranccsal hozzon létre egy új keystore:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Biztonsági másolatot az adatbázisról.

   Jelentkezzen be rendszergazdaként, és az adatbázisok biztonsági mentése:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha egy több-bérlős telepítést használ, is biztonsági másolatot készíteni a bérlői adatbázis:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Rendszer replikáció konfigurálása első csomópontjára.

   Jelentkezzen be \<hanasid > adm és az elsődleges hely létrehozásához:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **a(z) [2]**  Rendszer replikáció konfigurálása a másodlagos csomópont.

   Jelentkezzen be \<hanasid > adm és regisztrálja a másodlagos helyet:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürterőforrás létrehozása

Először hozza létre a HANA-topológiát. Futtassa az alábbi parancsokat a támasztja fürtcsomópontok egyike:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Ezután hozza létre a HANA-erőforrások:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Győződjön meg arról, hogy a fürt állapota rendben, és az erőforrások használatába. Nem számít mely erőforrásokat futtató csomóponton.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

Ez a szakasz ismerteti, hogyan tesztelheti a telepítőt. Minden teszt feltételezi, hogy a legfelső szintű áll, és az SAP HANA-főkiszolgáló fut a **hn1-db-0** virtuális gépet.

### <a name="test-the-migration"></a>A migrálási teszt

A teszt a Kezdés előtt ellenőrizze, hogy támasztja nincs minden sikertelen művelet (keresztül crm_mon - r), nincsenek megkötések nélkül váratlan helyét (például felesleg létrejöttének kizárását egy áttelepítési teszt), hogy HANA azt szinkronizálási állapota, például a SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Az SAP HANA-főcsomópont áttelepítheti a következő parancs végrehajtásával:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Ha `AUTOMATED_REGISTER="false"`, ez a parancssorozat át kell telepíteni, a SAP HANA fő csomópontot és a csoport, amely tartalmazza a virtuális IP-cím hn1-db-1.

Miután megtörtént az áttelepítés, a crm_mon - r kimenet a következőhöz hasonló

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Az SAP HANA-erőforrás hn1-db-0 másodlagosként indítása sikertelen lesz. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Az áttelepítés hoz létre a helyre vonatkozó megkötések, hogy újból törlendő:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Is szüksége lesz, a másodlagos csomópont-erőforrás állapotának karbantartásához:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Crm_mon – az r használata a HANA-erőforrás állapotának figyelése. Miután HANA hn1-db-0 elindult, a kimeneti hasonlónak kell lennie

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Az Azure az elkerítés ügynök (nem SBD) tesztelése

Tiltsa le a hálózati adaptert a hn1-db-0 csomóponton tesztelheti az Azure az elkerítés ügynök beállítása:

<pre><code>sudo ifdown eth0
</code></pre>

A virtuális gépet kell most indítsa újra, vagy állítsa le a fürt konfigurációtól függően.
Ha a `stonith-action` beállítást kapcsolja ki, a virtuális gép leáll, és az erőforrások áttelepítése a futó virtuális géphez.

Miután újra elindítani a virtuális gépet, az SAP HANA-erőforrás indítása másodlagosként, ha sikertelen `AUTOMATED_REGISTER="false"`. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Teszt SBD szintaxiskiemeléshez

SBD beállítása letesztelheti inquisitor folyamatának leállítása.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Fürt csomópont hn1-db-0 újra kell indítani. A támasztja szolgáltatás nem első ezt követően lépései. Ellenőrizze, hogy indítsa újra.

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Manuális feladatátvétel leállításával tesztelheti a `pacemaker` szolgáltatást a hn1-db-0 csomóponton:

<pre><code>service pacemaker stop
</code></pre>

A feladatátvétel után ismét elindíthatja a szolgáltatást. Ha `AUTOMATED_REGISTER="false"`, az SAP HANA-erőforrás a hn1-db-0 csomóponton másodlagosként indítása sikertelen. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tesztek

> [!IMPORTANT]
> Győződjön meg róla, hogy az operációs rendszer választja SAP-minősítéssel rendelkező SAP Hana, az adott virtuális gépek típusai, használja a. Az SAP HANA listája tanúsított Virtuálisgép-típusok és az operációs rendszer kiadások az azokat kereshetők [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ellenőrizze, hogy a virtuális gép típusú felsorolt lekérni az SAP HANA teljes listáját a részletek megtekintéséhez kattintson az adott virtuális gép típusa támogatott operációsrendszer-kiadások

Az SAP HANA SR teljesítmény optimalizált forgatókönyv vagy SAP HANA SR költség optimalizált forgatókönyv útmutatóban, attól függően, a használati eset felsorolt összes esetek futtatása. A útmutatókat találhat a [SLES for SAP ajánlott eljárások lap][sles-for-sap-bp].

A következő tesztek elvégzése a teszt leírása az SAP HANA SR teljesítmény optimalizált forgatókönyv SUSE Linux Enterprise Server – SAP alkalmazások 12 SP1 útmutató egy példányát. Naprakész verzióját mindig is a saját maga útmutatójának elolvasásához. Mindig ügyeljen arra, hogy HANA szinkronizálva. a Teszt indítása előtt, és győződjön meg arról, hogy helyesen szerepel-e a támasztja konfigurációját is.

A következő vizsgálat leírások feltételezzük PREFER_SITE_TAKEOVER = "true" és a AUTOMATED_REGISTER = "false".
MEGJEGYZÉS: A következő ellenőrzés úgy tervezték, hogy a feladatütemezési környezetben futtatható, és az előző tesztek kilépési állapotát függnek.

1. TEST 1: LEÁLLÍTÁS ELSŐDLEGES CSOMÓPONTON 1 ADATBÁZIS

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm hn1-db-0 csomópont:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Támasztja kell észleli a leállított HANA-példány és a feladatátvételt egy másik csomópontra. Miután megtörtént a feladatátvétel, a HANA-példány hn1-db-0 csomópontra le van állítva, mert támasztja nem automatikusan regisztrálja a csomópontot, a HANA másodlagos.

   Futtassa a következő parancsokat, másodlagos hn1-db-0 csomópontra, majd tisztítsa meg a hibás erőforráshoz regisztrálni.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. 2. TESZT: LEÁLLÍTÁS ELSŐDLEGES ADATBÁZIS-CSOMÓPONT 2

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Támasztja kell észleli a leállított HANA-példány és a feladatátvételt egy másik csomópontra. Miután megtörtént a feladatátvétel, a csomópont hn1-db-1 a HANA-példány leállt, mert támasztja nem automatikusan regisztrálja a csomópontot, a HANA másodlagos.

   Futtassa a következő parancsokat, mint másodlagos hn1-db-1 csomópontot és karbantartása a hibás erőforráshoz regisztráljon.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 3. TESZT: ÖSSZEOMLÁS ELSŐDLEGES ADATBÁZIS-CSOMÓPONT

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm hn1-db-0 csomópont:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Támasztja kell észlelése, a leállítva HANA-példány és a feladatátvételt egy másik csomópontra. Miután megtörtént a feladatátvétel, a HANA-példány hn1-db-0 csomópontra le van állítva, mert támasztja nem automatikusan regisztrálja a csomópontot, a HANA másodlagos.

   Futtassa a következő parancsokat, másodlagos hn1-db-0 csomópontra, majd tisztítsa meg a hibás erőforráshoz regisztrálni.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. 4. TESZT: ÖSSZEOMLÁS ELSŐDLEGES ADATBÁZIS-CSOMÓPONT 2

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Támasztja kell észlelése, a leállítva HANA-példány és a feladatátvételt egy másik csomópontra. Miután megtörtént a feladatátvétel, a csomópont hn1-db-1 a HANA-példány leállt, mert támasztja nem automatikusan regisztrálja a csomópontot, a HANA másodlagos.

   Futtassa a következő parancsokat, mint másodlagos hn1-db-1 csomópontot és karbantartása a hibás erőforráshoz regisztráljon.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: AZ ÖSSZEOMLÁSI ELSŐDLEGES HELY CSOMÓPONTOT (1. CSOMÓPONT)

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként hn1-db-0 csomópont:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Támasztja kell leállítva csomópont észlelése és a csomópont fence. Ha a csomópont az elkülönített, támasztja aktivál egy átvétele a HANA-példány. Az elkülönített csomópont újraindítása, ha támasztja nem fog automatikusan elindulni.

   Futtassa az alábbi parancsok futtatásával kezdheti támasztja, tiszta hn1-db-0 csomópont SBD állapotüzeneteiben regisztrálja hn1-db-0 csomópontra, másodlagos, és karbantartása a hibás erőforráshoz.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 6: ÖSSZEOMLÁS-MÁSODLAGOS HELY CSOMÓPONTOT (2 CSOMÓPONT)

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Támasztja kell leállítva csomópont észlelése és a csomópont fence. Ha a csomópont az elkülönített, támasztja aktivál egy átvétele a HANA-példány. Az elkülönített csomópont újraindítása, ha támasztja nem fog automatikusan elindulni.

   Futtassa az alábbi parancsok futtatásával kezdheti támasztja, tiszta hn1-db-1 csomópont SBD állapotüzeneteiben nyilvántartásba hn1-adatbázis – 1. csomóponton másodlagos, és karbantartása a hibás erőforráshoz.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 7. TESZT: ÁLLÍTSA LE A MÁSODLAGOS ADATBÁZIS-CSOMÓPONT 2

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Támasztja felismeri a leállított HANA-példány és az erőforrás az hn1-adatbázis – 1. csomóponton sikertelenként megjelölni. Támasztja automatikusan újra kell indítani a HANA-példány. Futtassa a következő parancsot a meghiúsult állapotú karbantartása.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. 8. TESZT: A MÁSODLAGOS ADATBÁZIS-CSOMÓPONT 2 ÖSSZEOMLÁS

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat, \<hanasid > adm az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Támasztja felismeri a leállítva HANA-példány és az erőforrás az hn1-adatbázis – 1. csomóponton sikertelenként megjelölni. Futtassa a következő parancsot a meghiúsult állapotú karbantartása. Támasztja majd indítsa újra automatikusan a HANA-példány.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 9: MÁSODLAGOS HELY (2 CSOMÓPONT) CSOMÓPONTON FUTÓ MÁSODLAGOS HANA-ADATBÁZIS ÖSSZEOMLÁS

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként az hn1-adatbázis – 1. csomóponton:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Támasztja kell leállítva csomópont észlelése és a csomópont fence. Az elkülönített csomópont újraindítása, ha támasztja nem fog automatikusan elindulni.

   Futtassa a következő parancsok futtatásával indítsa el a támasztja, tisztítsa meg a csomópont hn1-db-1 és karbantartása a hibás erőforráshoz SBD üzeneteket.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md)
