---
title: SAP HANA rendszer-replikálás beállítása Azure-beli virtuális gépeken (VM-EK) | Microsoft Docs
description: Az Azure Virtual Machines (VM) SAP HANA magas rendelkezésre állásának biztosítása.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: f51870fb8f6ed71aab2558099c2361bf6e340493
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078514"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

A helyszíni fejlesztéshez a HANA rendszerreplikációt vagy a megosztott tárterületet használhatja a SAP HANA magas rendelkezésre állásának létrehozásához.
Az Azure Virtual Machines (VM) szolgáltatásban a HANA rendszerreplikáció az Azure-ban jelenleg az egyetlen támogatott magas rendelkezésre állási funkció.
SAP HANA replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton lévő adatváltozások szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk leírja, hogyan telepítheti és konfigurálhatja a virtuális gépeket, hogyan telepítheti a fürtöt, és hogyan telepítheti és konfigurálhatja SAP HANA rendszer-replikálást.
A példában a konfigurációk, a telepítési parancsok, a példányok száma **03**és a HANA rendszerazonosító **HN1** vannak használatban.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretével kapcsolatos fontos kapacitási információk.
  * A támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk.
  * A Windows és a Linux szükséges SAP kernel-verziója Microsoft Azureon.
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [Rendszerreplikáció SAP HANA a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmények felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmények leírása](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [A Red Hat Enterprise Linux SAP HANA telepítése a Microsoft Azure-ben való használatra](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez SAP HANA két virtuális gépre van telepítve. A rendszer a HANA rendszerreplikáció használatával replikálja az adatgyűjtést.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA System Replication setup uses a dedicated virtual hostname and virtual IP addresses. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A terheléselosztó konfigurációját a következő lista tartalmazza:

* Előtér-konfiguráció: IP-10.0.0.13 a hn1-db-hez
* Háttérbeli konfiguráció: A HANA rendszer-replikáció részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
* Mintavételi port: 62503-es port
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Linux rendszeren való üzembe helyezés

Az Azure piactér tartalmaz egy rendszerképet, amely az új virtuális gépek üzembe helyezéséhez használható SAP HANA Red Hat Enterprise Linux 7,4.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablonnal

Az összes szükséges erőforrás üzembe helyezéséhez a GitHubon található egyik rövid útmutató-sablon is használható. A sablon üzembe helyezi a virtuális gépeket, a terheléselosztóot, a rendelkezésre állási készletet stb.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Azure Portal [adatbázis][template-multisid-db] -sablonját.
1. Adja meg a következő paramétereket:
    * **SAP**-rendszerazonosító: Adja meg a telepíteni kívánt SAP-System AZONOSÍTÓját. A rendszer az azonosítót használja az üzembe helyezett erőforrások előtagjaként.
    * **Operációs rendszer típusa**: Válassza ki a Linux-disztribúciók egyikét. Ebben a példában válassza a **RHEL 7**elemet.
    * **Adatbázis típusa**: Válassza a **HANA**elemet.
    * **SAP**-rendszerméret: Adja meg az új rendszer által megadható SAP-t. Ha nem biztos benne, hogy hány SAP-rendszer szükséges, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
    * A **rendszerek rendelkezésre állása**: Válassza a **Ha**lehetőséget.
    * **Rendszergazdai Felhasználónév, rendszergazdai jelszó vagy SSH-kulcs**: Létrejön egy új felhasználó, amely a gépre való bejelentkezéshez használható.
    * **ALHÁLÓZAT azonosítója**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában úgy néz ki, mint az **\</Subscriptions/\<előfizetés-azonosítója >/resourceGroups/\<erőforráscsoport neve >/Providers/Microsoft.Network/virtualNetworks/virtuális hálózat neve >/Subnets/\<alhálózat neve >** . Ha új virtuális hálózatot szeretne létrehozni, hagyja üresen.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási készletet.  
   Állítsa be a maximális frissítési tartományt.
1. Hozzon létre egy Load balancert (belső).
   * Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. Hozzon létre egy 1. virtuális gépet.  
   A SAP HANAhoz legalább Red Hat Enterprise Linux 7,4-et használjon. Ez a példa a Red Hat Enterprise Linux 7,4 for SAP HANA- <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> lemezképfájlt használja a 3. lépésben létrehozott rendelkezésre állási csoport kiválasztásával.
1. A 2. virtuális gép létrehozása  
   A SAP HANAhoz legalább Red Hat Enterprise Linux 7,4-et használjon. Ez a példa a Red Hat Enterprise Linux 7,4 for SAP HANA- <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> lemezképfájlt használja a 3. lépésben létrehozott rendelkezésre állási csoport kiválasztásával.
1. Adatlemezek hozzáadása.
1. Konfigurálja a Load balancert. Először hozzon létre egy előtér-IP-címkészletet:

   1. Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet**lehetőséget, majd kattintson a **Hozzáadás**gombra.
   1. Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
   1. Állítsa a hozzárendelést **statikus** értékre, és adja meg az IP-címet (például **10.0.0.13**).
   1. Kattintson az **OK** gombra.
   1. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.

1. Következő lépésként hozzon létre egy háttér-készletet:

   1. Nyissa meg a Load balancert, válassza a **háttérbeli készletek**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
   1. Adja meg az új háttér-készlet nevét (például **Hana-backend**).
   1. Válassza **a virtuális gép hozzáadása**lehetőséget.
   1. Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
   1. Válassza ki a SAP HANA-fürthöz tartozó virtuális gépeket.
   1. Kattintson az **OK** gombra.

1. Következő lépésként hozzon létre egy állapot-mintavételt:

   1. Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás**gombra.
   1. Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
   1. Válassza a **TCP** lehetőséget a protokoll és a625-es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
   1. Kattintson az **OK** gombra.

1. SAP HANA 1,0 esetében hozza létre a terheléselosztási szabályokat:

   1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
   1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3**03**15).
   1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
   1. Tartsa a **protokollt** **TCP**-értékre, és írja be a 3**03**15 portot.
   1. Növelje az **üresjárati** időkorlátot 30 percre.
   1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a 3**03**17-ös porton.

1. SAP HANA 2,0 esetében hozza létre a rendszeradatbázis terheléselosztási szabályait:

   1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
   1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3**03**13).
   1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
   1. Tartsa a **protokollt** **TCP**-értékre, és írja be a 3**03**13 portot.
   1. Növelje az **üresjárati** időkorlátot 30 percre.
   1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a 3.**03**. porton.

1. SAP HANA 2,0 esetében először hozza létre a bérlői adatbázishoz tartozó terheléselosztási szabályokat:

   1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
   1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3**03**40).
   1. Válassza ki a korábban létrehozott előtérbeli IP-címet, a háttér-készletet és az állapot-mintavételt (például **Hana-frontend**).
   1. Tartsa a **protokollt** **TCP**-re, és írja be a 3**03**40 portot.
   1. Növelje az **üresjárati** időkorlátot 30 percre.
   1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
   1. Kattintson az **OK** gombra.
   1. Ismételje meg ezeket a lépéseket a 3**03**41 és 3**03**42-es porton.

A SAP HANA szükséges portokkal kapcsolatos további információkért olvassa el a bérlői [adatbázisok kapcsolatai](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) című részt a [SAP HANA bérlői adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az 2388694-es [SAP][2388694]-megjegyzésben.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a **net. IPv4. TCP** paramétert **0-ra**_timestamps. Részletekért lásd: [Load Balancer Health](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)-tesztek.
> Lásd még: SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

* **[A]** : A lépés az összes csomópontra vonatkozik.
* **[1]** : A lépés csak az 1. csomópontra vonatkozik.
* **[2]** : A lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[A]** a lemez elrendezésének beállítása: **Logikai kötet kezelője (LVM)** .

   Azt javasoljuk, hogy az LVM-t használja az adatfájlok és naplófájlok tárolására használt kötetekhez. Az alábbi példa azt feltételezi, hogy a virtuális gépekhez négy adatlemez van csatlakoztatva, amelyek két kötet létrehozásához használatosak.

   Az összes rendelkezésre álló lemez felsorolása:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Példa a kimenetre:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Fizikai kötetek létrehozása a használni kívánt összes lemezhez:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre egy kötet-csoportot az adatfájlokhoz. Használjon egy kötetet a naplófájlok számára, és egyet a SAP HANA megosztott könyvtárához:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozza létre a logikai köteteket. A `lvcreate` `-i` kapcsoló használata nélkül jön létre lineáris kötet. Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O-teljesítmény érdekében `-i` , ahol az argumentumnak a mögöttes fizikai kötet számának kell lennie. Ebben a dokumentumban két fizikai kötet van használatban az adatkötethez, így a `-i` switch argumentum értéke **2**. A rendszer egy fizikai kötetet használ a naplózási kötethez `-i` , így nincs kifejezetten használatban kapcsoló. Használja a `-i` kapcsolót, és állítsa be a mögöttes fizikai kötet számára, ha több fizikai kötetet használ minden adathoz, naplóhoz vagy megosztott kötethez.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Hozza létre a csatlakoztatási könyvtárakat, és másolja a logikai kötetek UUID azonosítóját:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Hozzon létre `fstab` bejegyzéseket a három logikai kötethez:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Szúrja be a következő sort `/etc/fstab` a fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Az új kötetek csatlakoztatása:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** a lemez elrendezésének beállítása: **Egyszerű lemezek**.

   A bemutató rendszerek esetében a HANA-adatait és a naplófájlokat egy lemezen helyezheti el. Hozzon létre egy partíciót a/dev/disk/Azure/scsi1/lun0, és formázza azt a XFS:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Szúrja be ezt a sort az/etc/fstab fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Hozza létre a cél könyvtárat, és csatlakoztassa a lemezt:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** állomásnév-feloldás beállítása az összes gazdagépen.

   Használhat DNS-kiszolgálót, vagy módosíthatja az/etc/hosts fájlt az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts fájlba. Módosítsa az IP-címet és a gazdagépet úgy, hogy az megfeleljen a környezetének:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL a HANA-konfigurációhoz

   Konfigurálja a RHEL a [2292690] -es és <https://access.redhat.com/solutions/2447641> [2455582] -es SAP-megjegyzésben leírtak szerint.

1. **[A]** telepítse a SAP HANA

   SAP HANA rendszerreplikáció telepítéséhez kövesse a következőt <https://access.redhat.com/articles/3004101>:.

   * Futtassa a **hdblcm** programot a HANA DVD-ről. Adja meg a következő értékeket a parancssorban:
   * Telepítés kiválasztása: Adja meg az **1**értéket.
   * További összetevők kiválasztása a telepítéshez: Adja meg az **1**értéket.
   * Adja meg a telepítési útvonalat [/Hana/Shared]: Válassza az ENTER gombot.
   * Adja meg a helyi gazdagép nevét [..]: Válassza az ENTER gombot.
   * További gazdagépeket szeretne hozzáadni a rendszeren? (i/n) [n]: Válassza az ENTER gombot.
   * Adja meg SAP HANA rendszerazonosítóját: Adja meg a HANA biztonsági azonosítóját, például: **HN1**.
   * Adja meg a példány számát [00]: Adja meg a HANA-példány számát. Adja meg a **03** értéket, ha az Azure-sablont használta, vagy követte a jelen cikk manuális üzembe helyezés szakaszát.
   * Adatbázis mód kiválasztása/index megadása [1]: Válassza az ENTER gombot.
   * Válasszon rendszerhasználatot/adja meg a (z) [4] indexet: Válassza ki a rendszerhasználat értékét.
   * Adja meg az adatkötetek helyét [/hana/data/HN1]: Válassza az ENTER gombot.
   * Adja meg a naplózási kötetek helyét [/hana/log/HN1]: Válassza az ENTER gombot.
   * Korlátozza a memória maximális kiosztását? [n]: Válassza az ENTER gombot.
   * Adja meg a (z) "..." gazdagép nevét. [...]: Válassza az ENTER gombot.
   * Enter SAP Host Agent User (sapadm) Password: Adja meg a gazdagép-ügynök felhasználói jelszavát.
   * Az SAP Host Agent felhasználói (sapadm) jelszavának megerősítése: A megerősítéshez írja be a gazdagép-ügynök felhasználói jelszavát.
   * Adja meg a rendszergazda (hdbadm) jelszavát: Írja be a rendszergazda jelszavát.
   * Rendszergazda (hdbadm) jelszavának megerősítése: A megerősítéshez írja be újra a rendszergazda jelszavát.
   * Adja meg a rendszergazda kezdőlapját [/usr/sap/HN1/home]: Válassza az ENTER gombot.
   * Adja meg a rendszergazda bejelentkezési rendszerhéját [/bin/sh]: Válassza az ENTER gombot.
   * Adja meg a rendszergazda felhasználói AZONOSÍTÓját [1001]: Válassza az ENTER gombot.
   * Adja meg a felhasználói csoport AZONOSÍTÓját (sapsys) [79]: Válassza az ENTER gombot.
   * Adja meg az adatbázis-felhasználó (rendszer) jelszavát: Adja meg az adatbázis felhasználói jelszavát.
   * Adatbázis-felhasználó (rendszer) jelszavának megerősítése: A megerősítéshez adja meg újra az adatbázis felhasználói jelszavát.
   * Újraindítja a rendszert a gép újraindítása után? [n]: Válassza az ENTER gombot.
   * Folytatja? (i/n): Az összefoglalás ellenőrzése. A folytatáshoz adja meg az **y** értéket.

1. **[A]** frissítse az SAP-gazdagép ügynököt.

   Töltse le a legújabb SAP Host Agent-archívumot az [SAP Software Center][sap-swcenter] webhelyről, és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az Archívum elérési útját úgy, hogy az a letöltött fájlra mutasson:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** tűzfal konfigurálása

   Hozza létre az Azure Load Balancer mintavételi portjának tűzfalszabály-szabályát.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 rendszerreplikáció konfigurálása

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

* **[A]** : A lépés az összes csomópontra vonatkozik.
* **[1]** : A lépés csak az 1. csomópontra vonatkozik.
* **[2]** : A lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[A]** tűzfal konfigurálása

   Tűzfalszabályok létrehozása a HANA rendszerreplikáció és az ügyfél forgalmának engedélyezéséhez. A szükséges portok az [összes SAP-termék TCP/IP-portjain](https://help.sap.com/viewer/ports)vannak felsorolva. A következő parancsok csak példaként szolgálnak a HANA 2,0 rendszerreplikációra és az ügyfél adatforgalmára az adatbázis SYSTEMDB, a HN1 és a NW1.

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

1. **[1]** hozza létre a bérlői adatbázist.

   Ha SAP HANA 2,0-as vagy MDC-t használ, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer számára. Cserélje le az **NW1** -t az SAP-rendszere SID-azonosítójával.

   Végrehajtás < hanasid\>adm-ként a következő paranccsal:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** a rendszerreplikáció konfigurálása az első csomóponton:

   Az adatbázisok biztonsági mentése < hanasid\>adm-ként:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja a System PKI-fájlokat a másodlagos helyre:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Hozza létre az elsődleges helyet:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** a rendszer replikációjának konfigurálása a második csomóponton:
    
   Regisztrálja a második csomópontot a rendszerreplikáció elindításához. Futtassa a következő parancsot < hanasid\>adm-ként:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** a replikálás állapotának megtekintése

   Vizsgálja meg a replikálás állapotát, és várjon, amíg az összes adatbázis szinkronban van. Ha az állapot ismeretlen marad, ellenőrizze a tűzfalbeállítások beállításait.

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

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0 rendszerreplikáció konfigurálása

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

* **[A]** : A lépés az összes csomópontra vonatkozik.
* **[1]** : A lépés csak az 1. csomópontra vonatkozik.
* **[2]** : A lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[A]** tűzfal konfigurálása

   Tűzfalszabályok létrehozása a HANA rendszerreplikáció és az ügyfél forgalmának engedélyezéséhez. A szükséges portok az [összes SAP-termék TCP/IP-portjain](https://help.sap.com/viewer/ports)vannak felsorolva. A következő parancsok csak egy példát mutatnak a HANA 2,0 rendszerbeli replikáció engedélyezésére. Igazítsa a SAP HANA 1,0-es telepítéshez.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** hozza létre a szükséges felhasználókat.

   Futtassa a következő parancsot root-ként. Ügyeljen arra, hogy a félkövér sztringek (HANA rendszerazonosító **HN1** és a példány száma **03**) helyett a SAP HANA telepítésének értékeit adja meg:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** hozza létre a tároló bejegyzését.

   A következő parancs futtatásával hozzon létre egy új tároló-bejegyzést:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** az adatbázis biztonsági mentése.

   Az adatbázisok biztonsági mentése root-ként:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha több-bérlős telepítést használ, a bérlői adatbázis biztonsági mentését is elvégezheti:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** konfigurálja a rendszer replikálását az első csomóponton.

   Hozza létre az elsődleges helyet < hanasid\>adm-ként:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** a rendszer replikálásának konfigurálása a másodlagos csomóponton.

   Regisztrálja a másodlagos helyet < hanasid\>adm-ként:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez a HANA-kiszolgálóhoz, kövesse az Azure-beli [Red Hat Enterprise Linux pacemaker beállítása](high-availability-guide-rhel-pacemaker.md) című témakör lépéseit.

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürt erőforrásainak létrehozása

Telepítse a SAP HANA erőforrás-ügynököket az **összes csomópontra**. Győződjön meg arról, hogy a csomagot tartalmazó tárházat engedélyez.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Ezután hozza létre a HANA-topológiát. Futtassa az alábbi parancsokat a pacemaker-fürtcsomópontok egyik csomópontján:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Ezután hozza létre a HANA-erőforrásokat:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

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

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan lehet tesztelni a telepítőt. Mielőtt elkezdené a tesztet, győződjön meg arról, hogy a pacemaker nem rendelkezik sikertelen művelettel (a számítógépek állapotán keresztül), nincsenek váratlan helyekre vonatkozó korlátozások (például egy áttelepítési teszt maradékai), és hogy a HANA szinkronizált állapotú, például systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Az áttelepítés tesztelése

Erőforrás állapota a teszt elindítása előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A SAP HANA fő csomópontját a következő parancs végrehajtásával telepítheti át:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Ha be van `AUTOMATED_REGISTER="false"`állítva, a parancsnak át kell telepítenie a SAP HANA fő csomópontot és a hn1-db-1 virtuális IP-címet tartalmazó csoportot.

Az áttelepítés befejezése után a "sudo PC-k állapota" kimenet a következőképpen néz ki:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

A hn1-db-0 SAP HANA erőforrása le van állítva. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Az áttelepítés olyan helyekre vonatkozó korlátozásokat hoz létre, amelyeket újra törölni kell:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

A HANA-erőforrás állapotának figyelése a számítógépek állapota alapján. Miután a HANA elindult a hn1-db-0-on, a kimenetnek a következőhöz hasonlóan kell kinéznie:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Az Azure-kerítés ügynökének tesztelése

Erőforrás állapota a teszt elindítása előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Az Azure vívó-ügynök beállításának teszteléséhez tiltsa le a hálózati adaptert azon a csomóponton, ahol a SAP HANA fut főkiszolgálóként.
A hálózati hibák szimulálása érdekében lásd: [Red Hat tudásbázis 79523.](https://access.redhat.com/solutions/79523) számú cikke. Ebben a példában a net_breaker szkriptet használjuk a hálózat összes hozzáférésének letiltására.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A fürt konfigurációjától függően a virtuális gépnek újra kell indítania vagy leállítania a szolgáltatást.
Ha a `stonith-action` beállítást kikapcsolva értékre állítja, a virtuális gép leáll, és a rendszer áttelepíti az erőforrásokat a futó virtuális gépre.

> [!NOTE]
> Akár 15 percet is igénybe vehet, amíg a virtuális gépek újra online állapotba kerülnek.

A virtuális gép újraindítása után a SAP HANA erőforrás nem indul el másodlagosként, ha be van állítva `AUTOMATED_REGISTER="false"`. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Erőforrás állapota a teszt után:

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

Erőforrás állapota a teszt elindítása előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A manuális feladatátvételt a hn1-db-0 csomóponton található fürt leállításával tesztelheti:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

A feladatátvételt követően újra elindíthatja a fürtöt. Ha be van `AUTOMATED_REGISTER="false"`állítva, a hn1-db-0 csomópont SAP HANA erőforrása nem indul el másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Erőforrás állapota a teszt után:

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

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ha szeretné megtudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), SAP HANA tekintse meg [a magas rendelkezésre állást és az Azure](hana-overview-high-availability-disaster-recovery.md) -beli vész-helyreállítási
