---
title: Az SAP HANA magas rendelkezésre állása az SLES-en lévő Azure virtuális gépeken | Microsoft dokumentumok
description: Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server szolgáltatásban
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/31/2020
ms.author: radeltch
ms.openlocfilehash: 215cfd033a3fe8eb0ad9896c1f45f1e0f788823f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521371"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server szolgáltatásban

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

A helyszíni fejlesztés, használhatja a HANA rendszerreplikáció vagy a megosztott tároló az SAP HANA magas rendelkezésre állásának létrehozásához.
Az Azure virtuális gépeken (VMs) jelenleg a HANA-rendszer replikációja az Azure-ban az egyetlen támogatott magas rendelkezésre állású függvény. Az SAP HANA replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton lévő adatok módosításai szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk a virtuális gépek telepítését és konfigurálását, a fürtkeretrendszert, valamint az SAP HANA rendszerreplikáció telepítését és konfigurálását ismerteti.
A példakonfigurációkban telepítési parancsokat, **a 03**számú példányt és a HANA system ID **HN1-et** használják.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure virtuális gépméretek listája.
  * Fontos kapacitásadatok az Azure virtuális gép méreteihez.
  * A támogatott SAP szoftver, az operációs rendszer (OS) és az adatbázis-kombinációk.
  * A szükséges SAP kernel verzió Windows és Linux a Microsoft Azure-ban.
* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2205917] ajánlott operációs rendszer-beállításokat ajánlott a SUSE Linux Enterprise Server for SAP Applications számára.
* Az SAP Note [1944799] SAP HANA-irányelvekkel rendelkezik a SUSE Linux Enterprise Server for SAP alkalmazásokhoz.
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-beli SAP-hoz jelentett figyelési metrikákról.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1984787] általános információkat tartalmaz a SUSE Linux Enterprise Server 12-ről.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* Az SAP [401162] megjegyzése a HANA rendszerreplikáció beállításakor tartalmaz információkat arról, hogyan kerülhető el a "már használatban lévő cím".
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzésgel Linuxhoz.
* [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP Linux-útmutató.][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP Linux on][deployment-guide] (ez a cikk).
* [Az Azure Virtual Machines DBMS üzembe helyezése az SAP Linux-útmutató.][dbms-guide]
* [A SUSE Linux Enterprise Server for SAP Applications 12 SP3 gyakorlati tanácsok útmutatói][sles-for-sap-bp]
  * Egy SAP HANA SR teljesítményoptimalizált infrastruktúra (SLES SAP-alkalmazások 12 SP1 beállítása). Az útmutató tartalmazza az sap hana rendszerreplikáció helyszíni fejlesztéshez szükséges összes szükséges információt. Ezt az útmutatót használja alapvonalként.
  * SAP HANA SR költségoptimalizált infrastruktúra beállítása (SLES SAP-alkalmazásokhoz 12 SP1)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás elérése érdekében az SAP HANA két virtuális gépen van telepítve. Az adatok replikálása hana rendszerreplikációval történik.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability/ha-suse-hana.png)

Az SAP HANA rendszerreplikáció staki telepítése dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. Az alábbi lista a terheléselosztó konfigurációját mutatja be:

* Előtér-konfiguráció: 10.0.0.13 IP-cím a hn1-db-hez
* Háttérrendszer-konfiguráció: A HANA rendszerreplikáció részét kapott összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva
* Szonda port: Port 62503
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Üzembe helyezés Linuxra

Az SAP HANA erőforrás-ügynöke a SUSE Linux Enterprise Server for SAP Applications része.
Az Azure Marketplace tartalmaz egy lemezképet a SUSE Linux Enterprise Server for SAP Applications 12 számára, amely új virtuális gépek üzembe helyezéséhez használható.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablonnal

Használhatja a GitHubon található rövid útmutató sablonok egyikét az összes szükséges erőforrás üzembe helyezéséhez. A sablon telepíti a virtuális gépeket, a terheléselosztót, a rendelkezésre állási készletet és így tovább.
A sablon üzembe helyezéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [adatbázissablont][template-multisid-db] vagy a [konvergens sablont][template-converged] az Azure Portalon. 
    Az adatbázissablon csak az adatbázis terheléselosztási szabályait hozza létre. A konvergens sablon egy ASCS/SCS és ERS (csak Linux) példány terheléselosztási szabályait is létrehozza. Ha SAP NetWeaver-alapú rendszert kíván telepíteni, és az ASCS/SCS-példányt ugyanarra a gépre szeretné telepíteni, használja a [konvergens sablont.][template-converged]

1. Adja meg a következő paramétereket:
    - **Sap System ID**: Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használatos.
    - **Verem típusa**: (Ez a paraméter csak akkor alkalmazható, ha a konvergens sablont használja.) Válassza ki az SAP NetWeaver verem típusát.
    - **Os Type**: Válasszon egyet a Linux disztribúciók. Ebben a példában válassza az **SLES 12**lehetőséget.
    - **Db típus**: Válassza **a HANA**lehetőséget.
    - **Sap rendszer mérete:** Adja meg az saps számát, hogy az új rendszer fog nyújtani. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
    - **A rendszer elérhetősége:** Válassza a **HA**lehetőséget.
    - **Rendszergazdai felhasználónév és rendszergazdai jelszó:** Új felhasználó jön létre, amely a számítógépre való bejelentkezéshez használható.
    - **Új vagy meglévő alhálózat:** Meghatározza, hogy új virtuális hálózatot és alhálózatot kell-e létrehozni, vagy meglévő alhálózatot kell-e használni. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **Létező**lehetőséget.
    - **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint **\<a /subscription/ subscription ID\<>/resourceGroups/\<erőforráscsoport neve>/providers/Microsoft.Network/virtualNetworks/ virtuális hálózat neve>/alhálózatok/\<alhálózat név>**.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az SAP HANA az adott virtuálisgép-típusok használata. Az SAP HANA tanúsítvánnyal rendelkező virtuálisgép-típusok és operációsrendszer-kiadások listája [az SAP HANA minősítésű IaaS-platformokon kereshető.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Győződjön meg arról, hogy kattintson a részletekre a virtuális gép típusa felsorolt a teljes listát az SAP HANA támogatott operációs rendszer kiadások az adott virtuális gép típusa
>  

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási csoportot.
   - Állítsa be a maximális frissítési tartományt.
1. Hozzon létre egy terheléselosztó (belső). Normál [terheléselosztót](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)ajánlunk.
   - Válassza ki a 2.
1. Virtuális gép létrehozása 1.
   - Használjon Egy SLES4SAP-lemezképet az Azure-galériában, amely támogatja az SAP HANA a kiválasztott virtuálisgép-típus.
   - Válassza ki a 3.
1. Virtuális gép létrehozása 2.
   - Használjon Egy SLES4SAP-lemezképet az Azure-galériában, amely támogatja az SAP HANA a kiválasztott virtuálisgép-típus.
   - Válassza ki a 3. 
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
      1. Válassza a **Virtuális hálózat lehetőséget**.
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

## <a name="create-a-pacemaker-cluster"></a>Szívritmus-szabályozó fürt létrehozása

Kövesse a Pacemaker beállítása az [Azure-beli SUSE Linux Enterprise Server pacemaker ének lépéseit](high-availability-guide-suse-pacemaker.md) egy alapvető pacemaker-fürt létrehozásához ehhez a HANA-kiszolgálóhoz. Használhatja ugyanazt a pacemaker-fürtaz SAP HANA és az SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:
- **[A]**: A lépés minden csomópontra vonatkozik.
- **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
- **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

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

   Hozza létre a logikai köteteket. A lineáris kötet akkor `lvcreate` jön `-i` létre, ha kapcsoló nélkül használja. Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O teljesítmény érdekében, és igazítsa a csíkméreteket az [SAP HANA virtuálisgép-tároló konfigurációkban dokumentált értékekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Az `-i` argumentumnak az alapul szolgáló fizikai `-I` kötetek számának, az argumentumnak pedig a csíkméretnek kell lennie. Ebben a dokumentumban két fizikai kötet et használ `-i` az adatmennyiség, így a kapcsoló argumentum **2.** Az adatmennyiség csíkmérete **256KiB**. A naplókötethez egy fizikai kötetet `-i` `-I` használ, így a naplókötet-parancsokhoz nem vagy nem használ kifejezetten kapcsolókat.  

   > [!IMPORTANT]
   > Használja `-i` a kapcsolót, és állítsa be az alapul szolgáló fizikai kötet számára, ha minden adathoz, naplóhoz vagy megosztott kötethez egynél több fizikai kötetet használ. Csíkozott `-I` kötet létrehozásakor a kapcsolóval adhatja meg a csíkméretet.  
   > Tekintse meg [az SAP HANA virtuálisgép tárolási konfigurációk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) ajánlott tárolási konfigurációk, beleértve a csíkméretek és a lemezek száma.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
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

1. **[A]** Telepítse az SAP HANA magas rendelkezésre állású csomagok:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Az SAP HANA rendszerreplikáció telepítéséhez kövesse az [SAP HANA SR teljesítményoptimalizált forgatókönyvútmutató](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)4.

1. **[A]** Futtassa a **hdblcm** programot a HANA DVD-ről. Írja be a következő értékeket a kérdésben:
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

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 rendszerreplikációkonfigurálása

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:

* **[A]**: A lépés minden csomópontra vonatkozik.
* **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

1. **[1]** Hozza létre a bérlői adatbázist.

   Ha AZ SAP HANA 2.0 vagy MDC használatával, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer. Cserélje le az **NW1-et** az SAP-rendszer sid-jére.

   Hajtsa végre a következő\>parancsot <hanasid adm néven:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** A rendszerreplikáció konfigurálása az első csomóponton:

   Biztonsági másolatot <a hanasid\>adm-<:

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

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 rendszerreplikációkonfigurálása

Az ebben a szakaszban ismertetett lépések a következő előtagokat használják:

* **[A]**: A lépés minden csomópontra vonatkozik.
* **[1]**: A lépés csak az 1- es csomópontra vonatkozik.
* **[2]**: A lépés csak a Pacemaker-fürt 2 csomópontjára vonatkozik.

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

   Hozza létre az elsődleges helyet\><hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** A rendszerreplikáció konfigurálása a másodlagos csomóponton.

   Regisztrálja a másodlagos helyet\><hanasid adm néven:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürterőforrások létrehozása

Először hozza létre a HANA topológiát. Futtassa a következő parancsokat a Pacemaker fürtcsomópontok egyikén:

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

Ezután hozza létre a HANA-erőforrásokat:

> [!IMPORTANT]
> A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a netcat nem válaszol a kérelmekre a hátralék miatt, és csak egy kapcsolat kezelésének korlátozása miatt. A netcat erőforrás leállítja az Azure Load balancer kérelmek meghallgatását, és a lebegő IP elérhetetlenné válik.  
> Meglévő Pacemaker klaszterek esetében korábban azt javasoljuk, hogy a netcat-et socat-ra cserélje. Jelenleg azt javasoljuk, hogy az azure-lb erőforrás-ügynök, amely része a csomag erőforrás-ügynökök, a következő csomag verziókövetelmények:
> - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
> - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  
>
> Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
> Meglévő pacemaker-fürtök esetén, ha a konfiguráció már megváltozott socat használatára az [Azure Load-Balancer Detection Hardening című](https://www.suse.com/support/kb/doc/?id=7024128)dokumentumban leírtak szerint, nincs szükség arra, hogy azonnal váltson az azure-lb erőforrás-ügynökre.

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

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b>

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

Győződjön meg arról, hogy a fürt állapota rendben van, és hogy az összes erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

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
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan tesztelheti a beállításokat. Minden teszt feltételezi, hogy ön root, és az SAP HANA-főkiszolgáló fut a **hn1-db-0** virtuális gépen.

### <a name="test-the-migration"></a>Az áttelepítés tesztelése

A teszt megkezdése előtt győződjön meg arról, hogy a pacemaker nem rendelkezik sikertelen művelettel (crm_mon -r) keresztül, nincsenek váratlan helykorlátozások (például egy áttelepítési teszt maradéka), és hogy a HANA szinkronizálási állapot, például az SAPHanaSR-showAttr:Before the test, make t that Pacemaker does no any failed action (via crm_mon -r), there are no unexpected location constraints (example leftovers of a migration test) and that HANA is sync state, for example with SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Az SAP HANA főcsomópontot a következő parancs végrehajtásával telepítheti át:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Ha beállítja, `AUTOMATED_REGISTER="false"`a parancsok sorozatának át kell telepítenie az SAP HANA főcsomópontot és a virtuális IP-címet tartalmazó csoportot hn1-db-1-re.

Az áttelepítés után a crm_mon -r kimenet így néz ki:

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Az SAP HANA erőforrás hn1-db-0 nem indul másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Az áttelepítés olyan helykorlátozásokat hoz létre, amelyeket újra törölni kell:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

A másodlagos csomópont-erőforrás állapotát is meg kell tisztítania:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

A HANA erőforrás állapotának figyelése crm_mon -r használatával. Miután a HANA elindult a hn1-db-0-n, a kimenetnek így kell kinéznie

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Az Azure vívóügynök tesztelése (nem SBD)

Az Azure vívóügynök beállítását a hn1-db-0 csomópont hálózati felületének letiltásával tesztelheti:

<pre><code>sudo ifdown eth0
</code></pre>

A virtuális gépnek a fürt konfigurációjától függően újra kell indítania vagy le kell állítania.
Ha a `stonith-action` beállítást kikapcsolja, a virtuális gép leáll, és az erőforrások átkerülnek a futó virtuális gépre.

Miután újra indította a virtuális gépet, az SAP HANA-erőforrás nem indul el másodlagosként, ha beállítja. `AUTOMATED_REGISTER="false"` Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Teszt SBD kerítés

Tesztelheti az SBD beállítását az inkvizítor folyamat ának megölésével.

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

A hn1-db-0 fürtcsomópontot újra kell indítani. Előfordulhat, hogy a Pacemaker szolgáltatás nem indul el később. Győződjön meg róla, hogy indítsa újra.

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

A manuális feladatátvételt a `pacemaker` hn1-db-0 csomóponton lévő szolgáltatás leállításával tesztelheti:

<pre><code>service pacemaker stop
</code></pre>

A feladatátvétel után újra indíthatja a szolgáltatást. Ha beállítja, `AUTOMATED_REGISTER="false"`az SAP HANA erőforrás a hn1-db-0 csomópont nem indul másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a parancs végrehajtásával:

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
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az SAP HANA az adott virtuálisgép-típusok használata. Az SAP HANA tanúsítvánnyal rendelkező virtuálisgép-típusok és operációsrendszer-kiadások listája [az SAP HANA minősítésű IaaS-platformokon kereshető.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Győződjön meg arról, hogy kattintson a részletekre a virtuális gép típusa felsorolt a teljes listát az SAP HANA támogatott operációs rendszer kiadások az adott virtuális gép típusa

Futtassa az összes teszt esetet, amelyek szerepelnek az SAP HANA SR teljesítmény optimalizált forgatókönyv vagy az SAP HANA SR költségoptimalizált forgatókönyv útmutató, a használati esettől függően. Az útmutatók az [SAP ajánlott eljárásokhoz való Sles oldalán][sles-for-sap-bp]találhatók.

A következő tesztek az SAP HANA SR teljesítményoptimalizált forgatókönyv SUSE Linux Enterprise Server for SAP Applications 12 SP1 útmutató tesztleírásának másolatai. A naprakész verzióért mindig olvassa el magát az útmutatót is. Mindig győződjön meg arról, hogy hana szinkronban van a teszt megkezdése előtt, és győződjön meg arról, hogy a pacemaker konfiguráció helyes.

A következő tesztleírásokban azt feltételezzük, hogy PREFER_SITE_TAKEOVER="true" és AUTOMATED_REGISTER="false".
MEGJEGYZÉS: A következő vizsgálatokat úgy tervezték, hogy egymás után fussanak, és az előző vizsgálatok kilépési állapotától függjenek.

1. 1. TESZT: ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA AZ 1- ES CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker észlelnie kell a leállított HANA-példány és feladatátvétel a másik csomópontra. Miután a feladatátvétel megtörtént, a HANA-példány a hn1-db-0 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a hn1-db-0 csomópont másodlagosként való regisztrálásához és a sikertelen erőforrás karbantartásához.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 2. TESZT: ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA A 2- ES CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker észlelnie kell a leállított HANA-példány és feladatátvétel a másik csomópontra. Miután a feladatátvétel megtörtént, a HANA-példány a hn1-db-1 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a hn1-db-1 csomópont másodlagosként való regisztrálásához és a sikertelen erőforrás karbantartásához.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 3. TESZT: ÖSSZEOMLÁS ELSŐDLEGES ADATBÁZIS A CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker észlelnie kell a megölt HANA-példány t, és feladatátvétela a másik csomópontra. Miután a feladatátvétel megtörtént, a HANA-példány a hn1-db-0 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a hn1-db-0 csomópont másodlagosként való regisztrálásához és a sikertelen erőforrás karbantartásához.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 4. TESZT: ÖSSZEOMLÁS ELSŐDLEGES ADATBÁZIS A 2-ES CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker észlelnie kell a megölt HANA-példány t, és feladatátvétela a másik csomópontra. Miután a feladatátvétel megtörtént, a HANA-példány a hn1-db-1 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a hn1-db-1 csomópont másodlagosként való regisztrálásához és a sikertelen erőforrás karbantartásához.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 5. TESZT: ÖSSZEOMLÁS ELSŐDLEGES HELYCSOMÓPONT (1. CSOMÓPONT)

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként a hn1-db-0 csomóponton:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek észlelnie kell a lehalt fürtcsomópontot, és el kell kerítenie a csomópontot. A csomópont elkerített, Pacemaker elindítja a HANA-példány átvétele. A bekerített csomópont újraindítása kor a Pacemaker nem indul el automatikusan.

   Futtassa a következő parancsokat a Pacemaker elindításához, tisztítsa meg a Hn1-db-0 csomópont SBD-üzeneteit, regisztrálja a hn1-db-0 csomópontot másodlagosként, és tisztítsa meg a sikertelen erőforrást.

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

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 6. TESZT: CRASH MÁSODLAGOS HELY CSOMÓPONT (2. CSOMÓPONT)

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek észlelnie kell a lehalt fürtcsomópontot, és el kell kerítenie a csomópontot. A csomópont elkerített, Pacemaker elindítja a HANA-példány átvétele. A bekerített csomópont újraindítása kor a Pacemaker nem indul el automatikusan.

   Futtassa a következő parancsokat a Pacemaker elindításához, tisztítsa meg a Hn1-db-1 csomópont SBD-üzeneteit, regisztrálja a hn1-db-1 csomópontot másodlagosként, és tisztítsa meg a sikertelen erőforrást.

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

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 7. TESZT: A MÁSODLAGOS ADATBÁZIS LEÁLLÍTÁSA A 2- ES CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A pacemaker észleli a leállított HANA-példányt, és a hn1-db-1 csomóponton sikertelenként jelöli meg az erőforrást. Pacemaker automatikusan újra kell indítania a HANA-példány. Futtassa a következő parancsot a sikertelen állapot megtisztításához.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 8. TESZT: A MÁSODLAGOS ADATBÁZIS ÖSSZEOMLÁSA A 2-ES CSOMÓPONTON

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat <hanasid\>adm csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   A pacemaker észleli a megölt HANA-példányt, és a hn1-db-1 csomóponton sikertelenként jelöli meg az erőforrást. Futtassa a következő parancsot a sikertelen állapot megtisztításához. Pacemaker ezután automatikusan indítsa újra a HANA-példány.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 9. TESZT: CRASH SECONDARY SITE NODE (2. CSOMÓPONT) MÁSODLAGOS HANA ADATBÁZIS FUTTATÁSÁHOZ

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek észlelnie kell a lehalt fürtcsomópontot, és el kell kerítenie a csomópontot. A bekerített csomópont újraindítása kor a Pacemaker nem indul el automatikusan.

   Futtassa a következő parancsokat a Pacemaker elindításához, tisztítsa meg a Hn1-db-1 csomópont SBD-üzeneteit, és tisztítsa meg a sikertelen erőforrást.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]

