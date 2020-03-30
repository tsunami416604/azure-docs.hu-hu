---
title: Az SAP NW magas rendelkezésre állású Azure-gépei RHEL-en | Microsoft dokumentumok
description: Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 73b958149d9d6d907785fe1c2c56b8198bb91f70
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351105"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk ismerteti, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtkeretrendszert, és hogyan telepíthet egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert.
A példakonfigurációkban telepítési parancsok stb. Ascs-példány száma 00, ERS példány száma 02 és AZ SAP System ID NW1 használják. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) neve feltételezi, hogy az [ASCS/SCS sablont][template-multisid-xscs] az NW1 erőforrás-előtaggal használta az erőforrások létrehozásához.

Olvassa el először a következő SAP-megjegyzéseket és dokumentumokat

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban

* Az SAP Note [2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2002167] ajánlott a Red Hat Enterprise Linux operációs rendszerbeállításaihoz
* Az SAP Note [2009879] SAP HANA irányelveket készített a Red Hat Enterprise Linux-hoz
* Az SAP Note [2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz Linuxon][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [A Red Hat Gluster Storage termékdokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver a pacemaker fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS konfigurálása az SAP Netweaver számára az RHEL 7.5-ös rendszerben önálló erőforrásokkal](https://access.redhat.com/articles/3569681)
  * [Sap S/4HANA ASCS/ERS konfigurálása önálló enqueue server 2 (ENSA2) kiszolgálóval az RHEL pacemakerében](https://access.redhat.com/articles/3974941)
* Az Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver megosztott tárolást igényel. A GlusterFS egy külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS és az SAP HANA adatbázis virtuális állomásnév és virtuális IP-címek használata. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. [A Standard terheléselosztó](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)használatát javasoljuk. Az alábbi lista az (A)SCS és ERS terheléselosztó konfigurációját mutatja be.

### <a name="ascs"></a>A) a) Scs

* Előtér-konfiguráció
  * IP-cím 10.0.0.7
* Szonda port
  * Port 620<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * Standard terheléselosztó használata esetén válassza a **HA portok**
  * Alapterhelés-elosztó használata esetén hozzon létre terheléselosztási szabályokat a következő portokhoz:
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 36<strong>&lt;nr&gt; </strong> TCP
    * 39<strong>&lt;nr&gt; </strong> TCP
    * 81<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;óra&gt;</strong>16 TCP

### <a name="ers"></a>Ers

* Előtér-konfiguráció
  * IP-cím 10.0.0.8
* Szonda port
  * Port 621<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * Standard terheléselosztó használata esetén válassza a **HA portok**
  * Alapterhelés-elosztó használata esetén hozzon létre terheléselosztási szabályokat a következő portokhoz:
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 33<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;óra&gt;</strong>16 TCP

* Háttérrendszer-konfiguráció
  * Az (A)SCS/ERS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva

## <a name="setting-up-glusterfs"></a>A GlusterFS beállítása

Az SAP NetWeaver megosztott tárolást igényel az átviteli és profilkönyvtárhoz. Olvassa el [a GlusterFS-t az Azure-beli virtuális gépeken a Red Hat Enterprise Linux for SAP NetWeaver][glusterfs-ha] rendszeren a GlusterFS SAP NetWeaver hez való beállításáról.

## <a name="setting-up-ascs"></a>Beállítás (A)SCS

Használhatja a GitHub Azure-sablonját az összes szükséges Azure-erőforrás üzembe helyezéséhez, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztót, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése az Azure-sablonon keresztül

Az Azure Piactér tartalmaz egy lemezképet a Red Hat Enterprise Linux, amely segítségével új virtuális gépek üzembe helyezéséhez. A GitHub egyik rövid útmutató sablonjával üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a terheléselosztót, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [ASCS/SCS sablont][template-multisid-xscs] az Azure Portalon  
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték az üzembe helyezett erőforrások előtagjaként használatos.
   1. Halom típusa  
      Válassza ki az SAP NetWeaver verem típusát
   1. Os típus  
      Válassza ki az egyik Linux disztribúciók. Ebben a példában válassza az RHEL 7
   1. Adatbázis típusa  
      Hana (Hana) kiválasztása
   1. Sap-rendszer száma  
      A fürtben futó SAP-rendszer száma. Válassza az 1 lehetőséget.
   1. A rendszer elérhetősége  
      Ha kiválasztása
   1. Rendszergazdai felhasználónév, rendszergazdai jelszó vagy SSH-kulcs  
      Új felhasználó jön létre, amely a gépre való bejelentkezéshez használható.
   1. Alhálózati azonosító  
   Ha azt szeretné, hogy a virtuális gép egy meglévő virtuális hálózat, ahol a virtuális gép definiált alhálózat a virtuális gép kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint a /subscription/**&lt;&gt;subscription ID**/resourceGroups/**&lt;erőforráscsoport neve&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuális hálózat neve&gt;**/alhálózatok/**&lt;alhálózat neve&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux manuális üzembe helyezése az Azure Portalon keresztül

Először létre kell hoznia a fürt virtuális gépeit. Ezután hozzon létre egy terheléselosztót, és használja a háttérkészletben lévő virtuális gépeket.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási készlet létrehozása  
   Maximális frissítési tartomány beállítása
1. Virtuális gép létrehozása 1  
   Használja legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7.4 kép<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Virtuális gép létrehozása 2  
   Használja legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7.4 kép<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Legalább egy adatlemez hozzáadása mindkét virtuális géphez  
   Az adatlemezek a /usr/sap/`<SAPSID`> könyvtárhoz használatosak
1. Hozzon létre terheléselosztót (belső, standard):  
   1. Előtér-IP-címek létrehozása
      1. IP-cím 10.0.0.7 az ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
         1. Írja be az új előtér-IP-készlet nevét (például **nw1-ascs-frontend**)
         1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **10.0.0.7**)
         1. Kattintson az OK gombra
      1. IP-cím 10.0.0.8 az ASCS ERS
         * Ismételje meg a fenti lépéseket az ERS IP-címének létrehozásához (például **10.0.0.8** és **nw1-aers-frontend)**
   1. A háttérkészlet létrehozása
      1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
      1. Adja meg az új háttérkészlet nevét (például **nw1-háttér-**)
      1. Kattintson a Virtuális gép hozzáadása gombra.
      1. Válassza a Virtuális gép lehetőséget.
      1. Válassza ki az (A)SCS-fürt virtuális gépeit és IP-címeit.
      1. Kattintson az Add (Hozzáadás) parancsra
   1. Az állapotminta-szondák létrehozása
      1. 620**00-as** port ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
         1. Adja meg az új állapotérzékelő nevét (például **nw1-ascs-hp**)
         1. Válassza a TCP protokollt, a 620**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
         1. Kattintson az OK gombra
      1. 621**02-es** port ASCS ERS esetén
         * Ismételje meg a fenti lépéseket, hogy hozzon létre egy egészségügyi szonda az ERS (például 621**02** és **nw1-aers-hp**)
   1. Terheléselosztási szabályok
      1. Az ASCS terheléselosztási szabályai
         1. Nyissa meg a terheléselosztót, válassza ki a terheléselosztási szabályokat, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó szabály nevét (például **nw1-lb-ascs**)
         1. Válassza ki a korábban létrehozott előtér-IP-címet, háttérkészletet és állapotmintát (például **nw1-ascs-frontend**, **nw1-backend** és **nw1-ascs-hp**)
         1. **HA-portok** kiválasztása
         1. Az tétlen időszám növelése 30 percre
         1. **A lebegő IP engedélyezése**
         1. Kattintson az OK gombra
         * Ismételje meg a fenti lépéseket az ERS terheléselosztási szabályainak létrehozásához (például **nw1-lb-ers**)
1. Másik lehetőségként, ha a forgatókönyv alapvető terheléselosztót (belső) igényel, kövesse az alábbi lépéseket:  
   1. Előtér-IP-címek létrehozása
      1. IP-cím 10.0.0.7 az ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza az előtétes IP-készletet, és kattintson a Hozzáadás gombra
         1. Írja be az új előtér-IP-készlet nevét (például **nw1-ascs-frontend**)
         1. Állítsa a hozzárendelést Statikusra, és adja meg az IP-címet (például **10.0.0.7**)
         1. Kattintson az OK gombra
      1. IP-cím 10.0.0.8 az ASCS ERS
         * Ismételje meg a fenti lépéseket az ERS IP-címének létrehozásához (például **10.0.0.8** és **nw1-aers-frontend)**
   1. A háttérkészlet létrehozása
      1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
      1. Adja meg az új háttérkészlet nevét (például **nw1-háttér-**)
      1. Kattintson a Virtuális gép hozzáadása gombra.
      1. A korábban létrehozott elérhetőségi készlet kiválasztása
      1. Válassza ki az (A)SCS-fürt virtuális gépeit
      1. Kattintson az OK gombra
   1. Az állapotminta-szondák létrehozása
      1. 620**00-as** port ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza ki az állapotmintakat, és kattintson a Hozzáadás gombra
         1. Adja meg az új állapotérzékelő nevét (például **nw1-ascs-hp**)
         1. Válassza a TCP protokollt, a 620**00-as**portot, az 5- es intervallumot és a nem megfelelő küszöbértéket 2
         1. Kattintson az OK gombra
      1. 621**02-es** port ASCS ERS esetén
         * Ismételje meg a fenti lépéseket, hogy hozzon létre egy egészségügyi szonda az ERS (például 621**02** és **nw1-aers-hp**)
   1. Terheléselosztási szabályok
      1. 32**00** TCP ASCS-hez
         1. Nyissa meg a terheléselosztót, válassza ki a terheléselosztási szabályokat, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó szabály nevét (például **nw1-lb-3200**)
         1. Válassza ki a korábban létrehozott előtér-IP-címet, háttérkészletet és állapotmintát (például **nw1-ascs-frontend**)
         1. TCP protokoll **megtartása**, adja meg a **3200-as portot**
         1. Az tétlen időszám növelése 30 percre
         1. **A lebegő IP engedélyezése**
         1. Kattintson az OK gombra
      1. További portok az ASCS-hez
         * Ismételje meg a fenti lépéseket a 36**00,** 39**00,** 81**00**, 5**00**13, 5**00**14, 5**00**16 és a TCP portok esetében az ASCS esetében
      1. További portok az ASCS ERS-hez
         * Ismételje meg a fenti lépéseket a 33**02,** 5**02**13, 5**02**14, 5**02**16 és tcp portok esetében az ASCS ERS esetében

> [!Note]
> Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolat eléréséről további információt a [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az SAP magas rendelkezésre állású forgatókönyvekben használó virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapotminta sikertelensítését eredményezi. Állítsa a **net.ipv4.tcp_timestamps** paramétert **0-ra**. További részletek: [Terheléselosztó állapotminta.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

### <a name="create-pacemaker-cluster"></a>Szívritmus-szabályozó fürt létrehozása

Kövesse a Pacemaker beállítása a Red Hat Enterprise Linux az [Azure-ban,](high-availability-guide-rhel-pacemaker.md) hogy hozzon létre egy alapvető Pacemaker cluster ehhez (A)SCS-kiszolgálóhoz.

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** A megosztott könyvtárak létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Telepítse glusterFS kliens és egyéb követelmények

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Az erőforrás-ügynökök-sap verziójának ellenőrzése

   Győződjön meg arról, hogy a telepített erőforrás-ügynökök-sap csomag verziója legalább 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** Csatlakoztatási bejegyzések hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Az új megosztások csatlakoztatása

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** SWAP-fájl konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   A módosítás aktiválásához indítsa újra az ügynököt

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL konfiguráció

   Az RHEL konfigurálása az SAP [2002167] megjegyzésében leírtak szerint

### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ASCS-példányhoz

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Telepítse az SAP NetWeaver ASCS-eket  

   Telepítse az SAP NetWeaver ASCS-t gyökérként az első csomóponton egy virtuális állomásnév használatával, amely leképezi az ASCS terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-ascs</b>, <b>10.0.0.7</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>00.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés nem sikerül almappát létrehozni a /usr/sap/**NW1**/ASCS**00**kapcsolóban, próbálja meg beadni az ASCS**00** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ERS-példányhoz

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** Telepítse az SAP NetWeaver ERS-t  

   Telepítse az SAP NetWeaver ERS-t gyökérként a második csomópontra egy virtuális állomásnév használatával, amely leképezi az ERS terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-aers</b>, <b>10.0.0.8</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>02.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**NW1**/ERS**02**kapcsolóban, próbálja meg beadni az ERS**02** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** Az ASCS/SCS és ERS példányprofilok adaptálása

   * ASCS/SCS profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** A Keep Alive konfigurálása

   Az SAP NetWeaver alkalmazáskiszolgáló és az ASCS/SCS közötti kommunikáció egy szoftverterhelés-elosztón keresztül történik. A terheléselosztó konfigurálható időtúltöltés után bontja az inaktív kapcsolatokat. Ennek megakadályozása érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, és módosítania kell a Linux rendszer beállításait. További információért olvassa el az [SAP Note 1410736][1410736] megjegyzést.

   Az ENQue/encni/set_so_keepalive ASCS/SCS profilparaméter már hozzá lett adva az utolsó lépésben.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Frissítse a /usr/sap/sapservices fájlt

   Annak érdekében, hogy a sapinit indítási parancsfájl megakadályozza a példányok kezdetét, a Pacemaker által kezelt összes példányt a /usr/sap/sapservices fájlból kell kommentálni. Ne fűzzön megjegyzést az SAP HANA-példányhoz, ha hana sr-rel lesz használva.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** Az SAP-fürt erőforrásainak létrehozása

  Ha enqueue server 1 architektúrát (ENSA1) használ, az erőforrásokat a következőképpen határozza meg:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Az SAP az SAP NW 7.52-es részéhez benyújtotta a 2-es kiszolgáló várólistára állításának támogatását, beleértve a replikációt is. Az ABAP Platform 1809-től kezdve a 2-es várólistára helyezett kiszolgáló alapértelmezés szerint telepítve van. A 2-es kiszolgáló várólistára állításáról az SAP [2630416 megjegyzése.](https://launchpad.support.sap.com/#/notes/2630416)
   Az enqueue server 2 architektúra ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használata esetén telepítse az erőforrás-ügynök erőforrás-ügynökeit sap-4.1.1-12.el7.x86_64 vagy újabb, és határozza meg az erőforrásokat az alábbiak szerint:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Ha régebbi verzióról frissít, és a 2-es kiszolgáló várólistára lép, olvassa el az SAP [2641322 megjegyzését.](https://launchpad.support.sap.com/#/notes/2641322) 

   > [!NOTE]
   > A fenti konfigurációban az időtúltöltések csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani. 

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** Tűzfalszabályok hozzáadása az ASCS-hez és az ERS-hez mindkét csomóponton

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazáskiszolgáló előkészítése

Egyes adatbázisok megkövetelik, hogy az adatbázispéldány telepítése egy alkalmazáskiszolgálón kerül-e végrehajtásra. Készítse elő az alkalmazáskiszolgáló virtuális gépeit, hogy ezekben az esetekben használhassák őket.

A lépések fújtató feltételezik, hogy az alkalmazáskiszolgálótelepítése az ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra. Ellenkező esetben az alábbi lépések némelyike (például az állomásnév feloldása) nem szükséges.

1. Telepítési állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. A GlusterFS ügyfél és egyéb követelmények telepítése

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Csatlakoztatási bejegyzések hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Az új megosztások csatlakoztatása

   <pre><code>sudo mount -a
   </code></pre>

1. SWAP-fájl konfigurálása
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   A módosítás aktiválásához indítsa újra az ügynököt

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver telepítve van az SAP HANA.In this example, SAP NetWeaver is installed on SAP HANA. A telepítéshez minden támogatott adatbázist használhat. Az SAP HANA Azure-beli telepítéséről az [SAP HANA magas rendelkezésre állású az Azure-beli virtuális gépeken a Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]on című témakörben talál további információt.

1. Az SAP-adatbázispéldány telepítésének futtatása

   Telepítse az SAP NetWeaver adatbázispéldányt gyökérként egy virtuális állomásnév használatával, amely leképezi az adatbázis terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Kövesse az SAP [NetWeaver alkalmazáskiszolgáló fenti előkészítésének](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fejezetében leírt lépéseket az alkalmazáskiszolgáló előkészítéséhez.

1. Sap NetWeaver alkalmazáskiszolgáló telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver alkalmazáskiszolgálót.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA biztonságos tároló frissítése

   Frissítse az SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikációs telepítő virtuális nevére mutasson.

   A következő parancs futtatásával \<sapsid>adm néven listázható.

   <pre><code>hdbuserstore List
   </code></pre>

   Ennek fel kell sorolnia az összes bejegyzést, és
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, és nem a terheléselosztó IP-címére. Ezt a bejegyzést módosítani kell, hogy a terheléselosztó virtuális állomásnevére mutasson. Győződjön meg róla, hogy ugyanazt a portot **(30313** a fenti kimenet) és az adatbázis nevét **(HN1** a fenti kimenet)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

1. Az ASCS-példány manuális áttelepítése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Az ASCS-példány áttelepítéséhez futtassa a következő parancsokat gyökérként.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Csomópontösszeomlás szimulálása

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   A következő parancs futtatása gyökérként azon a csomóponton, ahol az ASCS-példány fut

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   A csomópont újraindítása utáni állapotnak így kell kinéznie.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   A következő paranccsal tisztítsa meg a meghibásodott erőforrásokat.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Üzenetkiszolgáló folyamatának leölése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként az üzenetkiszolgáló folyamatának azonosításához és megöléséhez.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer öli meg az üzenetkiszolgálót, a program újraindítja. `sapstart` Ha elég gyakran öli meg, pacemaker végül áthelyezi az ASCS-példányt a másik csomópontra. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill enqueue szerver folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS-példány fut a várólistán lévő kiszolgáló kiiktatása érdekében.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Az ASCS-példánynak azonnal át kell adnia a másik csomópontnak. Az ERS-példánynak az ASCS-példány indítása után is át kell adnia a feladatát. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. A várólistán lévő replikációs kiszolgáló folyamatának leállítása

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, ahol az ERS-példány fut, hogy megszakítsa a várólistán lévő replikációs kiszolgáló folyamatát.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer futtatja `sapstart` a parancsot, újraindítja a folyamatot. Ha elég gyakran futtatja, nem indítja újra a folyamatot, `sapstart` és az erőforrás leállított állapotban lesz. Futtassa a következő parancsokat gyökérként az ERS-példány erőforrásállapotának a teszt utáni karbantartásához.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kill enqueue sapstartsrv folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS fut.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   A sapstartsrv folyamat mindig újra kell indítani a Pacemaker erőforrás ügynök a figyelés részeként. Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>További lépések

* [HA SAP NW az Azure-beli virtuális gépek RHEL SAP-alkalmazások több SID útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), tekintse meg az [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
