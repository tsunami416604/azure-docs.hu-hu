---
title: Az SAP NetWeaver magas rendelkezésre állása az SAP NetWeaver számára Az SLES-en | Microsoft dokumentumok
description: Magas rendelkezésre állási útmutató az SAP NetWeaver számára a SUSE Linux Enterprise Server sap-alkalmazásokhoz
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348815"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server SAP-alkalmazásokhoz szolgáltatásban

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk ismerteti, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtkeretrendszert, és hogyan telepíthet egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert.
A példakonfigurációkban telepítési parancsok stb. Ascs-példány száma 00, ERS példány száma 02 és AZ SAP System ID NW1 használják. Az erőforrások (például virtuális gépek, virtuális hálózatok) nevei feltételezik, hogy a [konvergens sablont][template-converged] az SAP-rendszerazonosító NW1-vel használta az erőforrások létrehozásához.

Olvassa el először a következő SAP-megjegyzéseket és dokumentumokat

* Az SAP Note [1928533,][1928533]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban

* Az SAP Note [2015553][2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2205917][2205917] ajánlott operációs rendszer-beállításokat a SUSE Linux Enterprise Server for SAP alkalmazásokhoz
* Az SAP Note [1944799][1944799] SAP HANA irányelvei tanoncasap Linux Enterprise Server sap alkalmazásokhoz
* Az SAP Note [2178632][2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [2191498][2191498] rendelkezik a szükséges SAP Host Agent linuxos verzióval az Azure-ban.
* Az SAP Note [2243692][2243692] információkat tartalmaz az Sap-licencelésről az Azure-ban.
* Az SAP Note [1984787][1984787] általános információkat tartalmaz a SUSE Linux Enterprise Server 12-ről.
* Az SAP Note [1999351][1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* [Az SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden szükséges SAP-jegyzetet igényel Linuxhoz.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz Linuxon][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz Linuxon][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz Linuxon][dbms-guide]
* [SUSE SAP HA gyakorlati útmutatók][suse-ha-guide] A segédvonalak tartalmazzák a Netweaver HA és az SAP HANA rendszerreplikáció helyszíni beállításához szükséges összes információt. Ezeket az útmutatókat használja általános kiindulási alapként. Sokkal részletesebb információkat nyújtanak.
* [SUSE high availability Extension 12 SP3 kiadási megjegyzések][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver nfs-kiszolgálót igényel. Az NFS-kiszolgáló egy külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse/ha-suse.png)

Az NFS-kiszolgáló, az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és az SAP HANA adatbázis virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. [A Standard terheléselosztó](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)használatát javasoljuk. Az alábbi lista az (A)SCS és ERS terheléselosztó konfigurációját mutatja be.

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


## <a name="setting-up-a-highly-available-nfs-server"></a>Magas rendelkezésre állású NFS-kiszolgáló beállítása

Az SAP NetWeaver megosztott tárolást igényel az átviteli és profilkönyvtárhoz. Olvassa el [az NFS magas rendelkezésre állását az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren,][nfs-ha] hogyan állíthat be egy NFS-kiszolgálót az SAP NetWeaver számára.

## <a name="setting-up-ascs"></a>Beállítás (A)SCS

Használhatja a GitHub Azure-sablonját az összes szükséges Azure-erőforrás üzembe helyezéséhez, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztót, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése az Azure-sablonon keresztül

Az Azure Marketplace tartalmaz egy lemezképet a SUSE Linux Enterprise Server for SAP Applications 12 számára, amely új virtuális gépek üzembe helyezéséhez használható. A piactéri lemezkép az SAP NetWeaver erőforrás-ügynökét tartalmazza.

A GitHub egyik rövid útmutató sablonjával üzembe helyezheti az összes szükséges erőforrást. A sablon telepíti a virtuális gépeket, a terheléselosztót, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [ASCS/SCS Multi SID-sablont][template-multisid-xscs] vagy a [konvergens sablont][template-converged] az Azure Portalon. 
   Az ASCS/SCS sablon csak az SAP NetWeaver ASCS/SCS és ERS (csak Linux) példányok terheléselosztási szabályait hozza létre, míg a konvergens sablon egy adatbázis (például A Microsoft SQL Server vagy az SAP HANA) terheléselosztási szabályait is létrehozza. Ha SAP NetWeaver alapú rendszert kíván telepíteni, és az adatbázist ugyanarra a gépre is telepíteni szeretné, használja a [konvergens sablont.][template-converged]
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag (csak ASCS/SCS Multi SID sablon)  
      Adja meg a használni kívánt előtagot. Az érték az üzembe helyezett erőforrások előtagjaként használatos.
   3. Sap rendszerazonosító (csak konvergens sablon)  
      Adja meg a telepíteni kívánt SAP-rendszer azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használatos.
   4. Halom típusa  
      Válassza ki az SAP NetWeaver verem típusát
   5. Os típus  
      Válassza ki az egyik Linux disztribúciók. Ebben a példában válassza az SLES 12 BYOS
   6. Adatbázis típusa  
      Hana (Hana) kiválasztása
   7. Sap rendszer mérete.  
      Az új rendszer által biztosított SAPS mennyisége. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnerét vagy a rendszerintegrátort
   8. A rendszer elérhetősége  
      Ha kiválasztása
   9. Rendszergazdai felhasználónév és rendszergazdai jelszó  
      Új felhasználó jön létre, amely a számítógépre való bejelentkezéshez használható.
   10. Alhálózati azonosító  
   Ha azt szeretné, hogy a virtuális gép egy meglévő virtuális hálózat, ahol a virtuális gép definiált alhálózat a virtuális gép kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint a /subscription/**&lt;&gt;subscription ID**/resourceGroups/**&lt;erőforráscsoport neve&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuális hálózat neve&gt;**/alhálózatok/**&lt;alhálózat neve&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux manuális üzembe helyezése az Azure Portalon keresztül

Először létre kell hoznia a virtuális gépeket ehhez az NFS-fürthöz. Ezután hozzon létre egy terheléselosztót, és használja a háttérkészletben lévő virtuális gépeket.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási készlet létrehozása  
   Maximális frissítési tartomány beállítása
1. Virtuális gép létrehozása 1  
   Legalább SLES4SAP 12 SP1 használata, ebben a példában az SLES4SAP 12 SP1 lemezképhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES SAP alkalmazásokhoz 12 SP1 használatos  
   Korábbian létrehozott elérhetőségi készlet kiválasztása  
1. Virtuális gép létrehozása 2  
   Legalább SLES4SAP 12 SP1 használata, ebben a példában az SLES4SAP 12 SP1 lemezképhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES SAP alkalmazásokhoz 12 SP1 használatos  
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
         * Ismételje meg a fenti lépéseket az ERS IP-címének létrehozásához (például **10.0.0.8** és **nw1-aers-backend)**
   1. A háttérkészlet létrehozása
      1. Nyissa meg a terheléselosztót, jelölje ki a háttérkészleteket, és kattintson a Hozzáadás gombra
      1. Adja meg az új háttérkészlet nevét (például **nw1-háttér-**)
      1. Kattintson a Virtuális gép hozzáadása gombra.
      1. Virtuális gép kiválasztása
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

Kövesse a Pacemaker beállítása az [Azure-beli SUSE Linux Enterprise Server környezetben](high-availability-guide-suse-pacemaker.md) című lépéseit egy alapvető pacemaker-fürt létrehozásához ehhez az (A)SCS-kiszolgálóhoz.

### <a name="installation"></a>Telepítés

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** SUSE-csatlakozó telepítése

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Az állomásnevekben a kötőjel használatával kapcsolatos ismert problémát az **SAP-suse-cluster-connector**csomag **3.1.1-es** verziójával javították. Győződjön meg arról, hogy legalább a csomag sap-suse-cluster-connector 3.1-es verzióját használja, ha az állomásnévben kötőjellel rendelkező fürtcsomópontokat használ. Ellenkező esetben a fürt nem fog működni. 

   Győződjön meg arról, hogy telepítette az SAP SUSE fürtösszekötő új verzióját. A régit sap_suse_cluster_connector és az újat **sap-suse-cluster-connector-nak**hívják.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** SAP-erőforrás-ügynökök frissítése  
   
   Az erőforrás-ügynökök csomaghoz javításszükséges az új konfiguráció használatához, amely et ebben a cikkben ismertetett. Ellenőrizheti, ha a javítás már telepítve van a következő paranccsal

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimenetnek hasonlónak kell lennie a

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a grep parancs nem találja a IS_ERS paramétert, telepítenie kell [a SUSE letöltési oldalán](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) felsorolt javítást

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

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

1. **[A]** Autofs konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Fájl létrehozása

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Az autofs újraindítása az új megosztások csatlakoztatásához

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
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


### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ASCS-példányhoz

   > [!IMPORTANT]
   > A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a netcat nem válaszol a kérelmekre a hátralék miatt, és csak egy kapcsolat kezelésének korlátozása miatt. A netcat erőforrás leállítja az Azure Load balancer kérelmek meghallgatását, és a lebegő IP elérhetetlenné válik.  
   > Meglévő Pacemaker klaszterek esetében korábban azt javasoljuk, hogy a netcat-et socat-ra cserélje. Jelenleg azt javasoljuk, hogy az azure-lb erőforrás-ügynök, amely része a csomag erőforrás-ügynökök, a következő csomag verziókövetelmények:
   > - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
   > - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfiguráció már megváltozott socat használatára az [Azure Load-Balancer Detection Hardening című](https://www.suse.com/support/kb/doc/?id=7024128)dokumentumban leírtak szerint, nincs szükség arra, hogy azonnal váltson az azure-lb erőforrás-ügynökre.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Telepítse az SAP NetWeaver ASCS-eket  

   Telepítse az SAP NetWeaver ASCS-t gyökérként az első csomóponton egy virtuális állomásnév használatával, amely leképezi az ASCS terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-ascs</b>, <b>10.0.0.7</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>00.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés nem sikerül almappát létrehozni a /usr/sap/**NW1**/ASCS**00**kapcsolóban, próbálja meg beadni az ASCS**00** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Hozzon létre egy virtuális IP-erőforrást és állapot-mintavételt az ERS-példányhoz

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Telepítse az SAP NetWeaver ERS-t

   Telepítse az SAP NetWeaver ERS-t gyökérként a második csomópontra egy virtuális állomásnév használatával, amely leképezi az ERS terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-aers</b>, <b>10.0.0.8</b> és a terheléselosztó mintavételéhez használt példányszámot, például <b>02.</b>

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Használja swpm SP 20 PL 05 vagy magasabb. Az alacsonyabb verziók nem megfelelően állították be az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**NW1**/ERS**02**kapcsolóban, próbálja meg beadni az ERS**02** mappa tulajdonosát és csoportját, majd próbálkozzon újra.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Az ASCS/SCS és ERS példányprofilok adaptálása
 
   * ASCS/SCS profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** A Keep Alive konfigurálása

   Az SAP NetWeaver alkalmazáskiszolgáló és az ASCS/SCS közötti kommunikáció egy szoftverterhelés-elosztón keresztül történik. A terheléselosztó konfigurálható időtúltöltés után bontja az inaktív kapcsolatokat. Ennek megakadályozása érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, és módosítania kell a Linux rendszer beállításait. További információért olvassa el az [SAP Note 1410736][1410736] megjegyzést.

   Az ENQue/encni/set_so_keepalive ASCS/SCS profilparaméter már hozzá lett adva az utolsó lépésben.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Konfigurálja az SAP-felhasználókat a telepítés után

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Adja hozzá az ASCS- és ERS SAP-szolgáltatásokat az sapservice fájlhoz

   Adja hozzá az ASCS szolgáltatásbejegyzést a második csomóponthoz, és másolja az ERS szolgáltatásbejegyzést az első csomópontra.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Az SAP-fürt erőforrásainak létrehozása

Ha enqueue server 1 architektúrát (ENSA1) használ, az erőforrásokat a következőképpen határozza meg:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Az SAP az SAP NW 7.52-es részéhez benyújtotta a 2-es kiszolgáló várólistára állításának támogatását, beleértve a replikációt is. Az ABAP Platform 1809-től kezdve a 2-es várólistára helyezett kiszolgáló alapértelmezés szerint telepítve van. A 2-es kiszolgáló várólistára állításáról az SAP [2630416 megjegyzése.](https://launchpad.support.sap.com/#/notes/2630416)
Ha enqueue server 2 architektúrát ([ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)használ, az erőforrásokat a következőképpen határozza meg:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Ha régebbi verzióról frissít, és a 2-es kiszolgáló várólistára lép, olvassa el az SAP [2641019 megjegyzését.](https://launchpad.support.sap.com/#/notes/2641019) 

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazáskiszolgáló előkészítése

Egyes adatbázisok megkövetelik, hogy az adatbázispéldány telepítése egy alkalmazáskiszolgálón kerül-e végrehajtásra. Készítse elő az alkalmazáskiszolgáló virtuális gépeit, hogy ezekben az esetekben használhassák őket.

A lépések fújtató feltételezik, hogy az alkalmazáskiszolgálótelepítése az ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra. Ellenkező esetben az alábbi lépések némelyike (például az állomásnév feloldása) nem szükséges.

1. Operációs rendszer konfigurálása

   Csökkentse a piszkos gyorsítótár méretét. További információ: [Alacsony írási teljesítmény nagy RAM memóriával rendelkező SLES 11/12 kiszolgálókon.](https://www.suse.com/support/kb/doc/?id=7010287)

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Telepítési állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban

   ```bash
   sudo vi /etc/hosts
   ```

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Autofs konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Új fájl létrehozása

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Az autofs újraindítása az új megosztások csatlakoztatásához

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. SWAP-fájl konfigurálása

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

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver telepítve van az SAP HANA.In this example, SAP NetWeaver is installed on SAP HANA. A telepítéshez minden támogatott adatbázist használhat. Az SAP HANA Azure-beli telepítéséről az [SAP HANA magas rendelkezésre állása az Azure virtuális gépeken (VM-ek)][sap-hana-ha]című témakörben talál további információt. A támogatott adatbázisok listáját az [SAP Note 1928533 megjegyzésében található.][1928533]

1. Az SAP-adatbázispéldány telepítésének futtatása

   Telepítse az SAP NetWeaver adatbázispéldányt gyökérként egy virtuális állomásnév használatával, amely leképezi az adatbázis terheléselosztó előtér-konfigurációjának IP-címét, például <b>nw1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Kövesse az SAP [NetWeaver alkalmazáskiszolgáló fenti előkészítésének](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fejezetében leírt lépéseket az alkalmazáskiszolgáló előkészítéséhez.

1. Sap NetWeaver alkalmazáskiszolgáló telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver alkalmazáskiszolgálót.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA biztonságos tároló frissítése

   Frissítse az SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikációs telepítő virtuális nevére mutasson.

   A következő parancs futtatása a bejegyzések listázásához
   <pre><code>hdbuserstore List
   </code></pre>

   Ennek fel kell sorolnia az összes bejegyzést, és
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, és nem a terheléselosztó IP-címére. Ezt a bejegyzést módosítani kell, hogy a terheléselosztó virtuális állomásnevére mutasson. Győződjön meg róla, hogy ugyanazt a portot **(30313** a fenti kimenet) és az adatbázis nevét **(HN1** a fenti kimenet)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

A következő tesztek a SUSE legjobb gyakorlatokkal kapcsolatos útmutatóiban szereplő tesztesetek másolatai. Ezek másolása az Ön kényelmét. Mindig olvassa el az ajánlott eljárásokútmutatókat, és hajtsa végre az összes további, esetleg hozzáadott tesztet.

1. HaGetFailoverConfig, HACheckConfig és HACheckFailoverConfig tesztelése

   Futtassa a \<következő parancsokat sapsid>adm néven azon a csomóponton, ahol az ASCS-példány jelenleg fut. Ha a parancsok sikertelenek: Nincs elegendő memória, azt az állomásnév kötőjelei okozhatják. Ez egy ismert probléma, és a SUSE az sap-suse-cluster-connector csomagban fogja kijavítani.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Az ASCS-példány manuális áttelepítése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Az ASCS-példány áttelepítéséhez futtassa a következő parancsokat gyökérként.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. HAFailoverToNode tesztelése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a \<következő parancsokat sapsid>adm néven az ASCS-példány áttelepítéséhez.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Csomópontösszeomlás szimulálása

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   A következő parancs futtatása gyökérként azon a csomóponton, ahol az ASCS-példány fut

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Ha SBD-t használ, a Pacemaker nem indul el automatikusan a lehalt csomóponton. A csomópont újraindítása utáni állapotnak így kell kinéznie.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   A következő parancsokkal indítsa el a pacemakert a lehalt csomóponton, tisztítsa meg az SBD-üzeneteket, és tisztítsa meg a sikertelen erőforrásokat.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Az ASCS-példány manuális újraindításának tesztelése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Hozzon létre egy enqueue lock by, például szerkeszti a felhasználót a tranzakció su01. Futtassa a \<következő parancsokat sapsid>adm néven azon a csomóponton, ahol az ASCS-példány fut. A parancsok leállítják az ASCS-példányt, és újraindítják. Ha enqueue server 1 architektúrát használ, a várólistán lévő zárolás várhatóan elveszik ebben a tesztben. Ha enqueue server 2 architektúrát használ, a várólistá megmarad. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Az ASCS-példányt most le kell tiltani a Pacemakerben

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Indítsa el újra az ASCS-példányt ugyanazon a csomóponton.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   A su01 tranzakció várólistára helyezett zárolásának el kell vesznie, és a háttérrendszert alaphelyzetbe kellett volna állítani. Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Üzenetkiszolgáló folyamatának leölése

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként az üzenetkiszolgáló folyamatának azonosításához és megöléséhez.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer öli meg az üzenetkiszolgálót, az sapstart újraindítja. Ha elég gyakran öli meg, pacemaker végül áthelyezi az ASCS-példányt a másik csomópontra. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kill enqueue szerver folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS-példány fut a várólistán lévő kiszolgáló kiiktatása érdekében.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Az ASCS-példánynak azonnal át kell adnia a másik csomópontnak. Az ERS-példánynak az ASCS-példány indítása után is át kell adnia a feladatát. Futtassa a következő parancsokat gyökérként az ASCS és az ERS-példány erőforrásállapotának karbantartásához a teszt után.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. A várólistán lévő replikációs kiszolgáló folyamatának leállítása

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, ahol az ERS-példány fut, hogy megszakítsa a várólistán lévő replikációs kiszolgáló folyamatát.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer futtatja a parancsot, az sapstart újraindítja a folyamatot. Ha elég gyakran futtatja, az sapstart nem indítja újra a folyamatot, és az erőforrás leállított állapotban lesz. Futtassa a következő parancsokat gyökérként az ERS-példány erőforrásállapotának a teszt utáni karbantartásához.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill enqueue sapstartsrv folyamat

   Erőforrás állapota a teszt megkezdése előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, ahol az ASCS fut.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   A sapstartsrv folyamatot mindig újra kell indítani a Pacemaker erőforrás-ügynök. Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>További lépések

* [HA SAP NW az Azure virtuális gépek SLES SAP-alkalmazások több SID útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
