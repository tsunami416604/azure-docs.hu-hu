---
title: Az SAP HANA magas rendelkezésre állása az Azure virtuális gépein RHEL-en | Microsoft dokumentumok
description: Hozzon létre magas rendelkezésre állású SAP HANA az Azure virtuális gépeken (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/28/2020
ms.author: radeltch
ms.openlocfilehash: 5e3512ce86bdf96a5e6cfcf0e4459b656a5ac5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565859"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linuxon

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

A helyszíni fejlesztés, használhatja a HANA rendszerreplikáció vagy a megosztott tároló az SAP HANA magas rendelkezésre állásának létrehozásához.
Az Azure virtuális gépeken (VMs) jelenleg a HANA-rendszer replikációja az Azure-ban az egyetlen támogatott magas rendelkezésre állású függvény.
Az SAP HANA replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton lévő adatok módosításai szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk a virtuális gépek telepítését és konfigurálását, a fürtkeretrendszert, valamint az SAP HANA rendszerreplikáció telepítését és konfigurálását ismerteti.
A példakonfigurációkban telepítési parancsokat, **a 03**számú példányt és a HANA system ID **HN1-et** használják.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure virtuális gépméretek listája.
  * Fontos kapacitásadatok az Azure virtuális gép méreteihez.
  * A támogatott SAP szoftver, az operációs rendszer (OS) és az adatbázis-kombinációk.
  * A szükséges SAP kernel verzió Windows és Linux a Microsoft Azure-ban.
* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2002167] ajánlott a Red Hat Enterprise Linux operációs rendszerbeállításaihoz
* Az SAP Note [2009879] SAP HANA irányelveket készített a Red Hat Enterprise Linux-hoz
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP Linux on Linux (ez a cikk)][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [SAP HANA rendszer replikációja a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Az Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)
  * [Az SAP HANA telepítése a Red Hat Enterprise Linux ra a Microsoft Azure-ban való használatra](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás elérése érdekében az SAP HANA két virtuális gépen van telepítve. Az adatok replikálása hana rendszerreplikációval történik.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability-rhel/ha-hana.png)

Az SAP HANA rendszerreplikáció staki telepítése dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. Az alábbi lista a terheléselosztó konfigurációját mutatja be:

* Előtér-konfiguráció: 10.0.0.13 IP-cím a hn1-db-hez
* Háttérrendszer-konfiguráció: A HANA rendszerreplikáció részét kapott összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva
* Szonda port: Port 62503
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezés Linuxra

Az Azure Piactér tartalmaz egy lemezképet a Red Hat Enterprise Linux 7.4 SAP HANA, amely segítségével új virtuális gépek üzembe helyezéséhez.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablonnal

Használhatja a GitHubon található rövid útmutató sablonok egyikét az összes szükséges erőforrás üzembe helyezéséhez. A sablon telepíti a virtuális gépeket, a terheléselosztót, a rendelkezésre állási készletet és így tovább.
A sablon üzembe helyezéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [adatbázissablont][template-multisid-db] az Azure Portalon.
1. Adja meg a következő paramétereket:
    * **Sap System ID**: Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használatos.
    * **Os Type**: Válasszon egyet a Linux disztribúciók. Ebben a példában válassza **az RHEL 7**lehetőséget.
    * **Db típus**: Válassza **a HANA**lehetőséget.
    * **Sap rendszer mérete:** Adja meg az saps számát, hogy az új rendszer fog nyújtani. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
    * **A rendszer elérhetősége:** Válassza a **HA**lehetőséget.
    * **Rendszergazdai felhasználónév, rendszergazdai jelszó vagy SSH-kulcs**: Új felhasználó jön létre, amely a számítógépre való bejelentkezéshez használható.
    * **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint **\<a /subscription/ subscription ID\<>/resourceGroups/\<erőforráscsoport neve>/providers/Microsoft.Network/virtualNetworks/ virtuális hálózat neve>/alhálózatok/\<alhálózat név>**. Hagyja üresen, ha új virtuális hálózatot szeretne létrehozni

### <a name="manual-deployment"></a>Manuális üzembe helyezés

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási csoportot.  
   Állítsa be a maximális frissítési tartományt.
1. Hozzon létre egy terheléselosztó (belső). Normál [terheléselosztót](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)ajánlunk.
   * Válassza ki a 2.
1. Virtuális gép létrehozása 1.  
   Legalább Red Hat Enterprise Linux 7.4 az SAP HANA. Ebben a példában a Red Hat Enterprise Linux <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 7.4 sap HANA-lemezkép a 3.
1. Virtuális gép létrehozása 2.  
   Legalább Red Hat Enterprise Linux 7.4 az SAP HANA. Ebben a példában a Red Hat Enterprise Linux <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 7.4 sap HANA-lemezkép a 3.
1. Adatlemezek hozzáadása.
1. Normál terheléselosztó használata esetén kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előtér-IP-készletet:

      1. Nyissa meg a terheléselosztót, válassza **az előtétes IP-készletet,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új előtér-IP-készlet nevét (például **hana-előtér**).
      1. Állítsa a **hozzárendelést** **Statikus** értékre, és adja meg az IP-címet (például **10.0.0.13).**
      1. Válassza **az OK gombot.**
      1. Az új előtér-IP-készlet létrehozása után vegye figyelembe a készlet IP-címét.

   1. Ezután hozzon létre egy háttérkészletet:

      1. Nyissa meg a terheléselosztót, jelölje ki **a háttérkészleteket,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új háttérkészlet nevét (például **hana-backend**).
      1. Válassza **a Virtuális gép hozzáadása**lehetőséget.
      1. Válassza a ** Virtuális gép** lehetőséget.
      1. Válassza ki az SAP HANA-fürt virtuális gépeit és azok IP-címeit.
      1. Válassza a **Hozzáadás** lehetőséget.

   1. Ezután hozzon létre egy állapotminta:

      1. Nyissa meg a terheléselosztót, válassza **az állapotminták at,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új állapotérzékelő nevét (például **hana-hp**).
      1. Válassza a **TCP protokollt** és a 625**03-as portot.** Tartsa az **Intervallum** értéket 5-re, a **Nem kifogástalan küszöbértéket** pedig 2-re.
      1. Válassza **az OK gombot.**

   1. Ezután hozza létre a terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztót, válassza ki a **terheléselosztási szabályokat,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új terheléselosztó szabály nevét (például **hana-lb**).
      1. Válassza ki az előtér-IP-címet, a háttérkészletet és a korábban létrehozott állapotminta (például **hana-előtér,** **hana-háttér-** és **hana-HP).**
      1. Válassza a **HA portok lehetőséget**.
      1. Növelje az **tétlen időout30** percre.
      1. Győződjön meg arról, hogy **engedélyezi a lebegő IP protokollt.**
      1. Válassza **az OK gombot.**

   > [!Note]
   > Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolat eléréséről további információt a [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az SAP magas rendelkezésre állású forgatókönyvekben használó virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

1. Ha a forgatókönyv alapszintű terheléselosztót diktál, kövesse az alábbi konfigurációs lépéseket:
   1. Konfigurálja a terheléselosztót. Először hozzon létre egy előtér-IP-készletet:

      1. Nyissa meg a terheléselosztót, válassza **az előtétes IP-készletet,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új előtér-IP-készlet nevét (például **hana-előtér**).
      1. Állítsa a **hozzárendelést** **Statikus** értékre, és adja meg az IP-címet (például **10.0.0.13).**
      1. Válassza **az OK gombot.**
      1. Az új előtér-IP-készlet létrehozása után vegye figyelembe a készlet IP-címét.

   1. Ezután hozzon létre egy háttérkészletet:

      1. Nyissa meg a terheléselosztót, jelölje ki **a háttérkészleteket,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új háttérkészlet nevét (például **hana-backend**).
      1. Válassza **a Virtuális gép hozzáadása**lehetőséget.
      1. Válassza ki a 3.
      1. Válassza ki az SAP HANA-fürt virtuális gépeit.
      1. Válassza **az OK gombot.**

   1. Ezután hozzon létre egy állapotminta:

      1. Nyissa meg a terheléselosztót, válassza **az állapotminták at,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új állapotérzékelő nevét (például **hana-hp**).
      1. Válassza a **TCP protokollt** és a 625**03-as portot.** Tartsa az **Intervallum** értéket 5-re, a **Nem kifogástalan küszöbértéket** pedig 2-re.
      1. Válassza **az OK gombot.**

   1. Az SAP HANA 1.0-s létrehozása a terheléselosztási szabályok:

      1. Nyissa meg a terheléselosztót, válassza ki a **terheléselosztási szabályokat,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új terheléselosztó szabály nevét (például hana-lb-3**03**15).
      1. Válassza ki az előtér-IP-címet, a háttérkészletet és a korábban létrehozott állapotminta (például **hana-előtér).**
      1. Tartsa a **protokollt** **TCP-re**állítva, és adja meg a**303**15-ös portot.
      1. Növelje az **tétlen időout30** percre.
      1. Győződjön meg arról, hogy **engedélyezi a lebegő IP protokollt.**
      1. Válassza **az OK gombot.**
      1. Ismételje meg ezeket a lépéseket a**303**17-es porton.

   1. Az SAP HANA 2.0-s esetén hozza létre a rendszeradatbázis terheléselosztási szabályait:

      1. Nyissa meg a terheléselosztót, válassza ki a **terheléselosztási szabályokat,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új terheléselosztó szabály nevét (például hana-lb-3**03**13).
      1. Válassza ki az előtér-IP-címet, a háttérkészletet és a korábban létrehozott állapotminta (például **hana-előtér).**
      1. Tartsa a **protokollt** **TCP-re**állítva, és adja meg a**303**13-as portot.
      1. Növelje az **tétlen időout30** percre.
      1. Győződjön meg arról, hogy **engedélyezi a lebegő IP protokollt.**
      1. Válassza **az OK gombot.**
      1. Ismételje meg ezeket a lépéseket a**303**14-es porton.

   1. Az SAP HANA 2.0 esetén először hozza létre a bérlői adatbázis terheléselosztási szabályait:

      1. Nyissa meg a terheléselosztót, válassza ki a **terheléselosztási szabályokat,** és válassza **a Hozzáadás**lehetőséget.
      1. Adja meg az új terheléselosztó szabály nevét (például hana-lb-3**03**40).
      1. Válassza ki a korábban létrehozott előtér-IP-címet, háttérkészletet és állapotmintát (például **hana-előtér).**
      1. Tartsa a **protokollt** **TCP-re**állítva, és adja meg a**30340-es**portot.
      1. Növelje az **tétlen időout30** percre.
      1. Győződjön meg arról, hogy **engedélyezi a lebegő IP protokollt.**
      1. Válassza **az OK gombot.**
      1. Ismételje meg ezeket a lépéseket a**303**41 és a 3**03**42-es portesetében.

Az SAP HANA szükséges portjairól az [SAP HANA-bérlői adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az [SAP 2388694 megjegyzésében][2388694]olvassa el a [Bérlői adatbázisokhoz való kapcsolatok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) című fejezetet.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapotminta sikertelensítését eredményezi. Állítsa a **net.ipv4.tcp_timestamps** paramétert **0-ra**. További részletek: [Terheléselosztó állapotminta.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)
> Lásd még: [SAP 2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:

* **[A]**: A lépés minden csomópontra vonatkozik.
* **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

1. **[A]** Állítsa be a lemezelrendezést: **Logikai kötetkezelő (LVM).**

   Azt javasoljuk, hogy az LVM-et használja az adatokat és naplófájlokat tároló kötetekhez. A következő példa feltételezi, hogy a virtuális gépek négy adatlemezt csatoltak, amelyek két kötet létrehozásához használatosak.

   Sorolja fel az összes rendelkezésre álló lemezt:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Példa a kimenetre:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre fizikai köteteket az összes használni kívánt lemezhez:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Kötetcsoport létrehozása az adatfájlokhoz. Használjon egy kötetcsoportot a naplófájlokhoz és egyet az SAP HANA megosztott könyvtárához:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozza létre a logikai köteteket. A lineáris kötet akkor `lvcreate` jön `-i` létre, ha kapcsoló nélkül használja. Javasoljuk, hogy hozzon létre egy csíkozott kötetet `-i` a jobb I/O teljesítmény érdekében, ahol az argumentum az alapul szolgáló fizikai kötet számának kell lennie. Ebben a dokumentumban két fizikai kötet et használ `-i` az adatmennyiség, így a kapcsoló argumentum **2.** A naplókötethez egy fizikai kötetet `-i` használ, így a rendszer kifejezetten nem használ kapcsolót. Használja `-i` a kapcsolót, és állítsa be az alapul szolgáló fizikai kötet számára, ha minden adathoz, naplóhoz vagy megosztott kötethez egynél több fizikai kötetet használ.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Hozza létre a csatlakoztatási könyvtárakat, és másolja az összes logikai kötet UUID azonosítóját:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Bejegyzések `fstab` létrehozása a három logikai kötethez:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Szúrja be a `/etc/fstab` következő sort a fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Csatlakoztassa az új köteteket:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Állítsa be a lemez elrendezését: **Egyszerű lemezek**.

   A demo rendszerek, elhelyezheti a HANA adatok és naplófájlok egy lemezen. Hozzon létre egy partíciót a /dev/disk/azure/scsi1/lun0 oldalon, és formázza xfs-szel:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Szúrja be ezt a sort az /etc/fstab fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Hozza létre a célkönyvtárat, és csatlakoztassa a lemezt:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Az állomásnév feloldásának beállítása az összes állomáshoz.

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts fájlt az összes csomóponton. Ez a példa bemutatja az /etc/hosts fájl használatát.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts fájlba. Módosítsa az IP-címet és az állomásnevet a környezetnek megfelelően:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL hana konfigurációhoz

   Konfigurálja az RHEL-t az SAP [2292690] és <https://access.redhat.com/solutions/2447641> [2455582] és .

1. **[A]** Telepítse az SAP HANA

   Az SAP HANA rendszerreplikáció <https://access.redhat.com/articles/3004101>telepítéséhez kövesse a következő t.

   * Futtassa a **hdblcm** programot a HANA DVD-ről. Írja be a következő értékeket a kérdésben:
   * Válasszon telepítés: Enter **1**.
   * További összetevők kiválasztása a telepítéshez: Enter **1**.
   * Adja meg a telepítési elérési utat [/hana/shared]: Válassza az Enter lehetőséget.
   * Írja be a helyi állomásnevét [..]: Válassza az Enter lehetőséget.
   * További állomásokat szeretne hozzáadni a rendszerhez? (y/n) [n]: Válassza az Enter lehetőséget.
   * Adja meg az SAP HANA rendszerazonosítóját: Adja meg a HANA biztonsági azonosítóját, például: **HN1**.
   * Adja meg a példányszámát [00]: Adja meg a HANA-példány számát. Adja meg a **03** értéket, ha az Azure-sablont használta, vagy követte a cikk manuális központi telepítésére vonatkozó szakaszát.
   * Válassza az Adatbázis mód / Index megadása [1]: Válassza az Enter lehetőséget.
   * Válassza a Rendszerhasználat / Index megadása [4] lehetőséget: Válassza ki a rendszer használati értékét.
   * Adja meg az adatkötetek helyét [/hana/data/HN1]: Válassza az Enter lehetőséget.
   * Adja meg a naplókötetek helyét [/hana/log/HN1]: Válassza az Enter lehetőséget.
   * Korlátozza a maximális memóriafoglalást? [n]: Válassza az Enter lehetőséget.
   * Adja meg a tanúsítványállomás nevét a'...' állomáshoz. [...]: Válassza az Enter lehetőséget.
   * Írja be az SAP gazdaügynök-felhasználó (sapadm) jelszavát: Adja meg a gazdaügynök felhasználói jelszavát.
   * Erősítse meg az SAP gazdaügynök-felhasználó (sapadm) jelszavát: Adja meg újra a gazdaügynök felhasználói jelszavát a megerősítéshez.
   * Írja be a rendszergazda (hdbadm) Jelszó: Adja meg a rendszergazda jelszavát.
   * Rendszergazda (hdbadm) jelszó megerősítése: A megerősítéshez adja meg újra a rendszergazda jelszavát.
   * Írja be a rendszergazda kezdőkönyvtárát [/usr/sap/HN1/home]: Válassza az Enter lehetőséget.
   * Írja be a rendszergazdai bejelentkezési rendszerhéj [/bin/sh] mezőbe: Válassza az Enter lehetőséget.
   * Írja be a rendszergazda felhasználóazonosítóját [1001]: Válassza az Enter lehetőséget.
   * Adja meg a felhasználói csoport azonosítóját (sapsys) [79]: Válassza az Enter lehetőséget.
   * Írja be az Adatbázis felhasználója (SYSTEM) jelszavát: Adja meg az adatbázis felhasználói jelszavát.
   * Adatbázis-felhasználó (SYSTEM) jelszó megerősítése: A megerősítéshez adja meg újra az adatbázis felhasználói jelszavát.
   * Indítsa újra a rendszert a gép újraindítása után? [n]: Válassza az Enter lehetőséget.
   * Folytatja? (y/n): Érvényesítse az összefoglalót. Adja meg **az y** gombot a folytatáshoz.

1. **[A]** Frissítse az SAP gazdaügynök.

   Töltse le a legújabb SAP Host Agent archívumot az [SAP Szoftverközpontból,][sap-swcenter] és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az archívum elérési útját úgy, hogy a letöltött fájlra mutasson:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** Tűzfal konfigurálása

   Hozza létre a tűzfalszabályt az Azure terheléselosztó mintavételi portjához.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 rendszerreplikációkonfigurálása

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:

* **[A]**: A lépés minden csomópontra vonatkozik.
* **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

1. **[A]** Tűzfal konfigurálása

   Hozzon létre tűzfalszabályokat a HANA rendszerreplikáció és az ügyfélforgalom engedélyezéséhez. A szükséges portok [az összes SAP-termék TCP/IP portjain](https://help.sap.com/viewer/ports)találhatók. A következő parancsok csak egy példa, amely lehetővé teszi a HANA 2.0 rendszerreplikáció és az ügyfélforgalom adatbázis SYSTEMDB, HN1 és NW1.

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

1. **[1]** Hozza létre a bérlői adatbázist.

   Ha AZ SAP HANA 2.0 vagy MDC használatával, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer. Cserélje le az **NW1-et** az SAP-rendszer sid-jére.

   Hajtsa végre\><hanasid adm-ként a következő parancsot:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** A rendszerreplikáció konfigurálása az első csomóponton:

   Biztonsági másolatot készíteni az\>adatbázisok, mint <hanasid adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja a rendszer PKI-fájljait a másodlagos helyre:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Az elsődleges hely létrehozása:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** A rendszerreplikáció konfigurálása a második csomóponton:
    
   Regisztrálja a második csomópontot a rendszerreplikáció elindításához. Futtassa a következő parancsot <hanasid\>adm néven:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** Replikáció állapotának ellenőrzése

   Ellenőrizze a replikáció állapotát, és várja meg, amíg az összes adatbázis szinkronban van. Ha az állapot ismeretlen marad, ellenőrizze a tűzfal beállításait.

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

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 rendszerreplikációkonfigurálása

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:

* **[A]**: A lépés minden csomópontra vonatkozik.
* **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

1. **[A]** Tűzfal konfigurálása

   Hozzon létre tűzfalszabályokat a HANA rendszerreplikáció és az ügyfélforgalom engedélyezéséhez. A szükséges portok [az összes SAP-termék TCP/IP portjain](https://help.sap.com/viewer/ports)találhatók. A következő parancsok csak egy példa a HANA 2.0 rendszerreplikáció engedélyezésére. Igazítsa az SAP HANA 1.0 telepítéséhez.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Hozza létre a szükséges felhasználókat.

   Futtassa a következő parancsot gyökérként. Győződjön meg arról, hogy a félkövér karakterláncokat (HANA System ID **HN1** és **a 03**példányszám) lecseréli az SAP HANA telepítésének értékeire:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Hozza létre a kulcstároló bejegyzést.

   Új kulcstároló bejegyzés létrehozásához futtassa a következő parancsot gyökérként:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Biztonsági másolatot kell fésülni az adatbázisról.

   Az adatbázisok biztonsági mentése gyökérként:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha több-bérlős telepítést használ, a bérlői adatbázisról is biztonsági másolatot kell, hogy:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** A rendszerreplikáció konfigurálása az első csomóponton.

   Hozza létre az elsődleges helyet\><hanasid adm:Create the primary site as <hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** A rendszerreplikáció konfigurálása a másodlagos csomóponton.

   Regisztrálja a másodlagos helyet\><hanasid adm néven:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Szívritmus-szabályozó fürt létrehozása

Kövesse a Pacemaker beállítása a [Red Hat Enterprise Linux az Azure-ban](high-availability-guide-rhel-pacemaker.md) egy alapvető pacemaker fürt létrehozásához ehhez a HANA-kiszolgálóhoz.

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürterőforrások létrehozása

Telepítse az SAP HANA **erőforrásügynököket az összes csomópontra.** Győződjön meg arról, hogy engedélyezi a csomagot tartalmazó tárházat.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Ezután hozza létre a HANA topológiát. Futtassa a következő parancsokat a Pacemaker fürtcsomópontok egyikén:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Ezután hozza létre a HANA-erőforrásokat:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Győződjön meg arról, hogy a fürt állapota rendben van, és hogy az összes erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

> [!NOTE]
> A fenti konfigurációban az időtúltöltések csak példák, és előfordulhat, hogy az adott HANA-beállításhoz kell igazítani. Például szükség lehet a kezdési időtúllépése, ha hosszabb időt vesz igénybe az SAP HANA-adatbázis elindításához.  

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

Ez a szakasz azt ismerteti, hogyan tesztelheti a beállításokat. A teszt megkezdése előtt győződjön meg arról, hogy a pacemaker nem rendelkezik sikertelen művelettel (a pc-k állapotán keresztül), nincsenek váratlan helykorlátozások (például egy áttelepítési teszt maradéka), és hogy a HANA szinkronizálási állapot, például a systemReplicationStatus állapottal:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Az áttelepítés tesztelése

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Az SAP HANA főcsomópontot a következő parancs végrehajtásával telepítheti át:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Ha beállítja, `AUTOMATED_REGISTER="false"`ez a parancs áttelepítése az SAP HANA főcsomópont és a csoportot, amely tartalmazza a virtuális IP-címet hn1-db-1.

Miután a migráció befejeződött, a "sudo pcs status" kimenet így néz ki

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Az SAP HANA erőforrás hn1-db-0 leáll. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Az áttelepítés olyan helykorlátozásokat hoz létre, amelyeket újra törölni kell:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

A HANA-erőforrás állapotának figyelése a "pcs status" használatával. Miután a HANA elindult a hn1-db-0-n, a kimenetnek így kell kinéznie

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Az Azure vívóügynök tesztelése

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Tesztelheti az Azure vívóügynök beállítását, ha letiltja a hálózati felületet azon a csomóponton, ahol az SAP HANA főként fut.
A [Red Hat Tudásbázis 79523- as cikkében](https://access.redhat.com/solutions/79523) a hálózati hibák szimulálásának leírását tartalmazza. Ebben a példában a net_breaker parancsfájlt használjuk a hálózathoz való összes hozzáférés letiltására.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A virtuális gépnek a fürt konfigurációjától függően újra kell indítania vagy le kell állítania.
Ha a `stonith-action` beállítást kikapcsolja, a virtuális gép leáll, és az erőforrások átkerülnek a futó virtuális gépre.

> [!NOTE]
> A virtuális gépek újra online állapotba kerülése akár 15 percet is igénybe vehet.

Miután újra indította a virtuális gépet, az SAP HANA-erőforrás nem indul el másodlagosként, ha beállítja. `AUTOMATED_REGISTER="false"` Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

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

Erőforrás állapota a teszt megkezdése előtt:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

A manuális feladatátvételt a fürt leállításával tesztelheti a hn1-db-0 csomóponton:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

A feladatátvétel után újra elindíthatja a fürtöt. Ha beállítja, `AUTOMATED_REGISTER="false"`az SAP HANA erőforrás a hn1-db-0 csomópont nem indul másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

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

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), olvassa el az [SAP HANA (nagy példányok) magas rendelkezésre állását és a vészhelyreállítást az Azure-ban](hana-overview-high-availability-disaster-recovery.md)
