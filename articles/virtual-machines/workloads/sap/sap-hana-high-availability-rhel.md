---
title: Az Azure-beli virtuális gépek (VM) SAP HANA-Rendszerreplikálást beállítása |} A Microsoft Docs
description: Magas rendelkezésre állás az SAP Hana az Azure-beli virtuális gépek (VM) hoz létre.
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
ms.openlocfilehash: 1be3c411a208a2a9da1a4f6a319fdf37cc8aa2dd
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669044"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Magas rendelkezésre állás az SAP HANA, Red Hat Enterprise Linux-alapú Azure virtuális gépeken

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

A helyszíni fejlesztési vagy a HANA-Rendszerreplikálást használja, vagy megosztott tárterületet használnak, a SAP HANA magas rendelkezésre állás érdekében.
Az Azure-beli virtuális gépek (VM) HANA Rendszerreplikáció az Azure-on jelenleg az egyetlen támogatott magas rendelkezésre állású függvény.
SAP HANA-replikáció egy elsődleges csomópontot és legalább egy másodlagos csomópont áll. Az adatok az elsődleges csomópont változásai replikálódnak a másodlagos csomópontra szinkron vagy aszinkron módon.

Ez a cikk bemutatja, hogyan telepíthet és a virtuális gép konfigurálása, telepítse a fürt-keretrendszert, és telepítse és konfigurálja az SAP HANA-Rendszerreplikálást.
A példa konfigurációk esetén a telepítési parancsokat, a példányok száma **03**, és a HANA rendszer-azonosító **HN1** szolgálnak.

Először olvassa el az alábbi SAP-megjegyzések és tanulmányok:

* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * A lista SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek.
  * Fontos kapacitási adatainak Azure Virtuálisgép-méretet.
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk.
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure-ban.
* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott szoftverek központi telepítéséhez szükséges előfeltételeket ismerteti.
* SAP-Jegyzetnek [2002167] javasolt a Red Hat Enterprise Linux operációs rendszer beállításai
* SAP-Jegyzetnek [2009879] Red Hat Enterprise Linux for SAP HANA-irányelvek rendelkezik
* SAP-Jegyzetnek [2178632] részletes jelentett az Azure-beli SAP-figyelési metrikákat kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzések Linux rendszeren.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP, Linux rendszeren][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux (Ez a cikk)][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [SAP HANA rendszerreplikáció támasztja fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású bővítmény áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású kiegészítő felügyeleti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmény referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure egyedi RHEL dokumentációja:
  * [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek](https://access.redhat.com/articles/3131341)
  * [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása](https://access.redhat.com/articles/3252491)
  * [Az SAP HANA telepítése Red Hat Enterprise Linux rendszeren a Microsoft Azure-beli használatra](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Áttekintés

SAP HANA magas rendelkezésre állás, telepítve van a két virtuális gépen. Az adatok replikálódnak a HANA-Rendszerreplikálást.

![SAP HANA magas rendelkezésre állás – Áttekintés](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA System Replication setup uses a dedicated virtual hostname and virtual IP addresses. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztó konfigurációját jeleníti meg:

* Előtér-konfiguráció: IP-cím 10.0.0.13 hn1-DB-hez készült
* Háttér-konfiguráció: HANA-Rendszerreplikálást részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik
* Mintavételi Port: Port 62503
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezése Linux rendszeren

Az Azure Marketplace-en Red Hat Enterprise Linux 7.4 egy képet, amely az új virtuális gépek telepítéséhez használhatja az SAP Hana tartalmazza.

### <a name="deploy-with-a-template"></a>Üzembe helyezés egy sablon használatával

A gyorsindítási sablonok, amelyek a Githubon a szükséges erőforrások üzembe helyezéséhez használhatja. A sablon üzembe helyezi a virtuális gépek, a load balancer, a rendelkezésre állási csoport, és így tovább.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [adatbázis sablon] [ template-multisid-db] az Azure Portalon.
1. Adja meg a következő paraméterekkel:
    * **Rendszer-azonosító SAP**: Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások előtagjaként is szolgál.
    * **Operációs rendszer típusa**: Válasszon ki egy Linux-disztribúció. Ebben a példában válassza **RHEL 7**.
    * **Adatbázistípus**: Válassza ki **HANA**.
    * **SAP-rendszer mérete**: Adja meg az SAP, amelyet az új rendszerre, adja meg. Ha nem biztos a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
    * **Rendszer rendelkezésre állását**: Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
    * **Rendszergazdai felhasználónév, a rendszergazdai jelszó vagy SSH-kulcs**: Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a gépre.
    * **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában tűnik **/subscriptions/\<előfizetés-azonosító > /resourceGroups/\<erőforráscsoport-név > /providers/Microsoft.Network/virtualNetworks/\<virtuális hálózat neve > /subnets/ \<alhálózat neve >**. Hagyja üresen, ha szeretne egy új virtuális hálózat létrehozása

### <a name="manual-deployment"></a>Manuális telepítés

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási csoportot.  
   Állítsa be a maximális frissítési tartományt.
1. Load balancer létrehozása (belső).
   * Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. 1 virtuális gép létrehozása.  
   Legalább Red Hat Enterprise Linux 7.4 használja az SAP Hana-hoz. Ebben a példában a Red Hat Enterprise Linux 7.4 SAP HANA-lemezképhez <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> válassza ki a rendelkezésre állási csoportot a 3. lépésben létrehozott.
1. 2 virtuális gép létrehozása.  
   Legalább Red Hat Enterprise Linux 7.4 használja az SAP Hana-hoz. Ebben a példában a Red Hat Enterprise Linux 7.4 SAP HANA-lemezképhez <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> válassza ki a rendelkezésre állási csoportot a 3. lépésben létrehozott.
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
> Lásd még az SAP Megjegyzés [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:

* **[A]**: A lépés minden csomópont számára vonatkozik.
* **[1]**: A lépés csak 1 csomópont vonatkozik.
* **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

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

1. **[A]**  RHEL HANA-konfiguráció

   Konfigurálja az RHEL, a SAP-Jegyzetnek leírtak szerint [2292690] és [2455582] és <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Az SAP HANA telepítése

   Az SAP HANA-Rendszerreplikálást telepítéséhez kövesse az <https://access.redhat.com/articles/3004101>.

   * Futtassa a **hdblcm** program HANA DVD-ről. Írja be a parancssorba a következő értékeket:
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

1. **[A]**  Tűzfal konfigurálása

   Hozzon létre az Azure load balancer mintavételi portot a tűzfalszabályt.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Az SAP HANA 2.0-s replikációs konfigurálása

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:

* **[A]**: A lépés minden csomópont számára vonatkozik.
* **[1]**: A lépés csak 1 csomópont vonatkozik.
* **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

1. **[A]**  Tűzfal konfigurálása

   HANA-Rendszerreplikálást és az ügyfél forgalmat engedélyező tűzfalszabályok létrehozása. A szükséges portok a felsorolt [TCP/IP-port minden SAP-termék](https://help.sap.com/viewer/ports). Az alábbi parancsok olyan csak egy példa adatbázis SYSTEMDB, HN1 és NW1 HANA-Rendszerreplikálást 2.0 és az ügyfél forgalom engedélyezésére.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]**  a bérlői adatbázis létrehozása.

   Az SAP HANA 2.0 vagy MDC használja, ha létrehozza az adatbázist, az SAP NetWeaver rendszernek. Cserélje le **NW1** az SAP-rendszer a biztonsági azonosítójával.

   Végrehajtási típus < hanasid\>adm a következő parancsot:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Rendszer replikáció konfigurálása első csomópontjára:

   Mint az adatbázisok biztonsági < hanasid\>adm:

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
    
   A második csomópontot, a rendszer replikáció regisztrálja. Futtassa a következő parancsot, < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]**  Replikációs állapotának ellenőrzése

   Ellenőrizheti a replikálás állapotát, és várjon, amíg az összes adatbázis szinkronban. Ha az állapot ismeretlen marad, hogy ellenőrizze a tűzfalbeállításokat.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Az SAP HANA 1.0-s replikációs konfigurálása

A jelen szakaszban ismertetett lépések használja az alábbi előtagokat:

* **[A]**: A lépés minden csomópont számára vonatkozik.
* **[1]**: A lépés csak 1 csomópont vonatkozik.
* **[2]**: A lépés fürtcsomópont 2 támasztja csak vonatkozik.

1. **[A]**  Tűzfal konfigurálása

   HANA-Rendszerreplikálást és az ügyfél forgalmat engedélyező tűzfalszabályok létrehozása. A szükséges portok a felsorolt [TCP/IP-port minden SAP-termék](https://help.sap.com/viewer/ports). Az alábbi parancsok olyan csak egy példa, hogy a HANA-Rendszerreplikálást 2.0. Mindig az SAP HANA 1.0 telepítése.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]**  Hozza létre a szükséges felhasználókat.

   Futtassa a következő parancsot a legfelső szintű. Cserélje le a félkövérrel szedett karakterláncok (HANA rendszer-azonosító **HN1** példányok száma és **03**) értékeket az SAP HANA telepítése:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Hozza létre a keystore bejegyzést.

   Futtassa a következő parancsot a legfelső szintű, hozzon létre egy új keystore:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Biztonsági másolatot az adatbázisról.

   Adatbázisok biztonsági mentése a legfelső szintű:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha egy több-bérlős telepítést használ, is biztonsági másolatot készíteni a bérlői adatbázis:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Rendszer replikáció konfigurálása első csomópontjára.

   Az elsődleges hely létrehozása < hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **a(z) [2]**  Rendszer replikáció konfigurálása a másodlagos csomópont.

   Regisztrálja a másodlagos helyet, < hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a Red Hat Enterprise Linux az Azure-beli beállítása](high-availability-guide-rhel-pacemaker.md) a HANA-kiszolgáló alapszintű támasztja fürt létrehozásához.

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürterőforrás létrehozása

Az SAP HANA-erőforrás ügynök telepítéséhez a **minden csomópont**. Ellenőrizze, hogy engedélyezi egy adattár, amely a csomag tartalmazza.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Ezután hozza létre a HANA-topológiát. Futtassa az alábbi parancsokat a támasztja fürtcsomópontok egyike:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Ezután hozza létre a HANA-erőforrások:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Győződjön meg arról, hogy a fürt állapota rendben, és az erőforrások használatába. Nem számít mely erőforrásokat futtató csomóponton.

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

Ez a szakasz ismerteti, hogyan tesztelheti a telepítőt. Egy tesztet a Kezdés előtt ellenőrizze, hogy támasztja nem rendelkezik (a számítógépek állapota) keresztül minden sikertelen művelet, nincsenek megkötések nélkül váratlan helyét (például felesleg létrejöttének kizárását egy áttelepítési teszt), HANA azt szinkronizálási állapota, például a systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>A migrálási teszt

Erőforrás állapotának a vizsgálat megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Az SAP HANA-főcsomópont áttelepítheti a következő parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Ha `AUTOMATED_REGISTER="false"`, ezzel a paranccsal végezzen áttelepítést, a SAP HANA fő csomópontot és a csoport, amely tartalmazza a virtuális IP-cím hn1-db-1.

Miután megtörtént az áttelepítés, a "sudo számítógépek állapota" kimeneti nézhet ki

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Az SAP HANA-erőforrás hn1-db-0 le van állítva. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Az áttelepítés hoz létre a helyre vonatkozó megkötések, hogy újból törlendő:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

A HANA-erőforrás "számítógépek állapota" használatával állapotának figyelése. Miután HANA hn1-db-0 elindult, a kimeneti hasonlónak kell lennie

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Az Azure az elkerítés ügynök tesztelése

Erőforrás állapotának a vizsgálat megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Tiltsa le a hálózati adaptert a csomóponton hol futnak az SAP HANA-főkiszolgálóként tesztelheti az Azure az elkerítés ügynök beállítása.
Lásd: [Red Hat tudásbázisában cikk 79523](https://access.redhat.com/solutions/79523) hálózati hiba szimulálása a leírást. Ebben a példában használjuk a net_breaker parancsfájlt a hálózathoz való hozzáférés teljes letiltása.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A virtuális gépet kell most indítsa újra, vagy állítsa le a fürt konfigurációtól függően.
Ha a `stonith-action` beállítást kapcsolja ki, a virtuális gép leáll, és az erőforrások áttelepítése a futó virtuális géphez.

> [!NOTE]
> Akár 15 percig, amíg a virtuális gépeket újra online is igénybe vehet.

Miután újra elindítani a virtuális gépet, az SAP HANA-erőforrás indítása másodlagosként, ha sikertelen `AUTOMATED_REGISTER="false"`. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Erőforrás állapotának a vizsgálat után:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Erőforrás állapotának a vizsgálat megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A fürt leállítása a hn1-db-0 csomóponton letesztelheti manuális feladatátvételt:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

A feladatátvétel után újra elkezdheti a fürtben. Ha `AUTOMATED_REGISTER="false"`, az SAP HANA-erőforrás a hn1-db-0 csomóponton másodlagosként indítása sikertelen. Ebben az esetben konfigurálni a HANA-példány másodlagos Ez a parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Erőforrás állapotának a vizsgálat után:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md)
