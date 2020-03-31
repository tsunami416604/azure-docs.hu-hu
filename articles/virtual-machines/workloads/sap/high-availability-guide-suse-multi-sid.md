---
title: Az SAP NetWeaver magas rendelkezésre állású Azure-gépei az SLES multi-SID útmutatójában | Microsoft dokumentumok
description: Multi-SID magas rendelkezésre állású útmutató az SAP NetWeaver számára a SUSE Linux Enterprise Server SAP-alkalmazásokhoz
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
ms.openlocfilehash: 793851780e1154b6b6a21c88ea8cae063a277790
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350061"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Az SAP NetWeaver magas rendelkezésre állása az Azure virtuális gépein az Sap-alkalmazásokhoz készült több SID-alkalmazásokhoz szolgáltatásban

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Ez a cikk ismerteti, hogyan telepíthet több SAP NetWeaver vagy S4HANA magas rendelkezésre állású rendszerek (azaz több-SID) egy két csomópontfürt az Azure-beli virtuális gépek SUSE Linux Enterprise Server SAP-alkalmazások.  

A példakonfigurációk, telepítési parancsok, stb. három SAP NetWeaver 7.50 rendszerek egyetlen, két csomópont magas rendelkezésre állású fürt. Az SAP-rendszerek azonosítói a következők:
* **NW1**: **00** ASCS-példány száma és a virtuális állomásneve **msnw1ascs**; ERS-példány száma **02** és virtuális állomás neve **msnw1ers**.  
* **NW2**: A **10-es** SZÁMÚ ASCS-példány és **az msnw2ascs**virtuális állomásnév ; ERS-példány száma **12** és a virtuális állomás neve **msnw2ers**.  
* **NW3**: **20-as** SZÁMÚ ASCS-példány és virtual hostname **msnw3ascs**; ERS-példány száma **22** és a virtuális állomás neve **msnw3ers**.  

A cikk nem terjed ki az adatbázisrétegre és az SAP NFS-megosztások üzembe helyezésére. Ebben a cikkben szereplő példákban az NW2 NFS-megosztások hoz az NW2 NFS-megosztások és az NW3 NFS-megosztások n3-nfs virtuális neveket használunk, feltéve, hogy az NFS-fürt telepítve volt.  

Mielőtt elkezdené, először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

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
* [SUSE multi-SID fürtútmutató SLES 12 és SLES 15-höz](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]
## <a name="overview"></a>Áttekintés

A fürtben részt vevő virtuális gépeknek méretezniük kell az összes erőforrás futtatását, ha feladatátvétel történik. Minden SAP-biztonsági azonosító feladatátvételt egymással szemben a több SID magas rendelkezésre állású fürtben.  Ha SBD kerítés, az SBD-eszközök megoszthatótöbb klaszterek között.  

A magas rendelkezésre állás eléréséhez az SAP NetWeaver magas rendelkezésre állású NFS-megosztásokra van szükség. Ebben a példában feltételezzük, hogy az SAP NFS-megosztások vagy magas rendelkezésre állású [NFS fájlkiszolgálón](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)vannak, amelyet több SAP-rendszer is használhat. Vagy a megosztások az [Azure NetApp Files NFS-köteteken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)vannak telepítve.  

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Az SAP ASCS/ERS több SID-fürtözésének támogatása a SUSE Linux-szal vendég operációs rendszerként az Azure virtuális gépeken **legfeljebb öt** SAP-SID-re korlátozódik ugyanazon a fürtön. Minden új SID növeli a komplexitást. Az SAP Enqueue Replication Server 1 és az Enqueue Replication Server 2 keveréke **nem támogatott**ugyanazon a fürtön. A több SID-fürtözés több SAP ASCS/ERS-példány telepítését írja le különböző SID-kkel egy pacemaker-fürtben. Jelenleg a több SID-fürtözés csak az ASCS/ERS esetén támogatott.  

> [!TIP]
> Az SAP ASCS/ERS többSID-fürtözése nagyobb komplexitású megoldás. Ez bonyolultabb végrehajtani. Ez is magában foglalja a nagyobb felügyeleti erőfeszítéseket, karbantartási tevékenységek végrehajtásasorán (például az operációs rendszer javítása). A tényleges megvalósítás megkezdése előtt szánjon időt a központi telepítés és az összes érintett összetevő, például a virtuális gépek, az NFS-csatlakoztatások, a VIP-k, a terheléselosztó-konfigurációk és így tovább gondos tervezésére.  

Az NFS-kiszolgáló, az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és az SAP HANA adatbázis virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. [A Standard terheléselosztó](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)használatát javasoljuk.  

Az alábbi lista az (A)SCS és ERS terheléselosztó konfigurációját mutatja be ehhez a több SID-fürthöz, három SAP-rendszerrel. Külön előtér-IP-cím, állapot-mintavételek és terheléselosztási szabályok minden EGYES ASCS és ERS-példány minden egyes biztonsági azonosítók. Rendelje hozzá az ÖSSZES virtuális gépet, amelyek az ASCS/ASCS-fürt részét képezik egy háttérkészlethez.  

### <a name="ascs"></a>A) a) Scs

* Előtér-konfiguráció
  * AZ NW1 IP-címe: 10.3.1.14
  * AZ NW2 IP-címe: 10.3.1.16
  * AZ NW3 IP-címe: 10.3.1.13
* Mintavételi portok
  * 620<strong>&lt;nr&gt;</strong>port , ezért a 620**00,** 620**10** és 620**20-as** NW1, NW2 és NW3 szondaportokhoz
* Terheléselosztási szabályok - 
* hozzon létre egyet minden példányhoz, azaz NW1/ASCS, NW2/ASCS és NW3/ASCS.
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
  * AZ NW1 10.3.1.15 IP-címe
  * Az NW2 10.3.1.17 IP-címe
  * Az NW3 10.3.1.19 IP-címe
* Szonda port
  * 621<strong>&lt;nr&gt;</strong>port , ezért nw1, NW2 és N# szondaportokhoz 621**02,** 621**12** és 621**22**
* Terheléselosztási szabályok – hozzon létre egyet minden példányhoz, azaz NW1/ERS, NW2/ERS és NW3/ERS.
  * Standard terheléselosztó használata esetén válassza a **HA portok**
  * Alapterhelés-elosztó használata esetén hozzon létre terheléselosztási szabályokat a következő portokhoz:
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 33<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;óra&gt;</strong>16 TCP

* Háttérrendszer-konfiguráció
  * Az (A)SCS/ERS-fürt részét alkotó összes virtuális gép elsődleges hálózati interfészeihez csatlakoztatva


> [!Note]
> Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolat eléréséről további információt a [nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával az SAP magas rendelkezésre állású forgatókönyvekben használó virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket az Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapotminta sikertelensítését eredményezi. Állítsa a **net.ipv4.tcp_timestamps** paramétert **0-ra**. További részletek: [Terheléselosztó állapotminta.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

## <a name="sap-nfs-shares"></a>SAP NFS-megosztások

Az SAP NetWeaver megosztott tárolást igényel az átvitelhez, a profilkönyvtárhoz és így tovább. A magas rendelkezésre állású SAP-rendszer esetében fontos, hogy magas rendelkezésre állású NFS-megosztásokkal rendelkezik. El kell döntenie az SAP NFS-megosztások architektúráját. Az egyik lehetőség az, hogy [magas rendelkezésre álló NFS-fürt az Azure-beli virtuális gépeken suse Linux Enterprise Server,][nfs-ha]amely több SAP-rendszerek között osztható konkretizálható. 

Egy másik lehetőség a megosztások üzembe helyezése az [Azure NetApp Files NFS-köteteken.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  Az Azure NetApp-fájlokkal az SAP NFS-megosztások beépített magas rendelkezésre állását kapja.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Az első SAP-rendszer telepítése a fürtben

Most, hogy úgy döntött, az SAP NFS-megosztások architektúrája, telepítse az első SAP-rendszer a fürtben, a megfelelő dokumentációt követve.

* Ha magas rendelkezésre állású NFS-kiszolgálót használ, kövesse [az SAP NetWeaver magas rendelkezésre állását az Azure-beli virtuális gépeken az SUSE Linux Enterprise Server for SAP-alkalmazásokon.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)  
* Ha Azure NetApp Files NFS-köteteket használ, kövesse [az SAP NetWeaver magas rendelkezésre állását az Azure virtuális gépeken az SUSE Linux Enterprise Server en az Azure NetApp-fájlokkal az SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

A fent felsorolt dokumentumok végigvezeti a szükséges infrastruktúrák előkészítéséhez, a fürt létrehozásához, az operációs rendszer sap-alkalmazás futtatásához való előkészítéséhez szükséges lépéseken.  

> [!TIP]
> Mindig tesztelje a fürt feladatátvételi funkcióit az első rendszer üzembe helyezése után, mielőtt hozzáadná a további SAP-azonosítókat a fürthöz. Így tudni fogja, hogy a fürt funkció működik, mielőtt hozzáadná a fürthöz további SAP-rendszerek összetettségét.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>További SAP-rendszerek telepítése a fürtben

Ebben a példában feltételezzük, hogy az **NW1** rendszer már telepítve van a fürtben. Bemutatjuk, hogyan kell telepíteni a fürt SAP rendszerek **NW2** és **NW3**. 

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

### <a name="prerequisites"></a>Előfeltételek 

> [!IMPORTANT]
> Mielőtt követné a fürtben további SAP-rendszerek üzembe helyezésére vonatkozó utasításokat, kövesse az utasításokat az első SAP-rendszer központi telepítéséhez a fürtben, mivel vannak olyan lépések, amelyek csak az első rendszer üzembe helyezése során szükségesek.  

Ez a dokumentáció feltételezi, hogy:
* A Pacemaker-fürt már konfigurálva van és fut.  
* Legalább egy SAP-rendszer (ASCS / ERS-példány) már telepítve van, és fut a fürtben.  
* A fürt feladatátvételi funkcióit tesztelték.  
* Az Összes SAP-rendszer NFS-megosztások üzembe helyezése.  

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

1. Adja hozzá az újonnan üzembe helyezett rendszer (azaz **NW2,** **NW3)** konfigurációját a meglévő Azure Load Balancerhez, az [Azure Load Balancer manuális üzembe helyezésére](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal)vonatkozó utasításokat követve az Azure Portalon keresztül. Állítsa be az IP-címeket, az állapotmintaportokat, a terheléselosztási szabályokat a konfigurációhoz.  

2. **[A]** Névfeloldás beállítása a további SAP-rendszerekhez. Használhatja a DNS-kiszolgálót, vagy módosíthatja `/etc/hosts` az összes csomópontot. Ez a példa a `/etc/hosts` fájl használatát mutatja be.  Igazítsa az IP-címeket és az állomásneveket a környezetéhez. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** Hozza létre a megosztott könyvtárakat a fürtre telepített további **NW2** és **NW3** SAP-rendszerekhez. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** `autofs` Konfigurálja a /sapmnt/SID és a /usr/sap/SID/SYS fájlrendszerek csatlakoztatásához a fürtbe üzembe helyezett további SAP-rendszerekhez. Ebben a példában **NW2** és **NW3**.  

   Frissítse `/etc/auto.direct` a fájlt a fürtre telepített további SAP-rendszerek fájlrendszereivel.  

   * Ha NFS fájlkiszolgálót használ, kövesse az [alábbi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation) utasításokat
   * Ha Azure NetApp Files-t használ, kövesse az [alábbi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation) utasításokat 

   Az újonnan hozzáadott `autofs` megosztások csatlakoztatásához újra kell indítania a szolgáltatást.  

### <a name="install-ascs--ers"></a>AsCS / ERS telepítése

1. Hozza létre a virtuális IP-és állapotminta fürt erőforrásait a fürtbe üzembe helyezett további SAP-rendszer ASCS-példányához. Az itt látható példa az **NW2** és **AZ NW3** ASCS, magas rendelkezésre állású NFS-kiszolgáló használatával.  

   > [!IMPORTANT]
   > A legutóbbi tesztelés olyan helyzeteket tárt fel, amikor a netcat nem válaszol a kérelmekre a hátralék miatt, és csak egy kapcsolat kezelésének korlátozása miatt. A netcat erőforrás leállítja az Azure Load balancer kérelmek meghallgatását, és a lebegő IP elérhetetlenné válik.  
   > Meglévő Pacemaker klaszterek esetében korábban azt javasoljuk, hogy a netcat-et socat-ra cserélje. Jelenleg azt javasoljuk, hogy az azure-lb erőforrás-ügynök, amely része a csomag erőforrás-ügynökök, a következő csomag verziókövetelmények:
   > - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
   > - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfiguráció már megváltozott socat használatára az [Azure Load-Balancer Detection Hardening című](https://www.suse.com/support/kb/doc/?id=7024128)dokumentumban leírtak szerint, nincs szükség arra, hogy azonnal váltson az azure-lb erőforrás-ügynökre.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Az erőforrások létrehozásakor előfordulhat, hogy különböző fürterőforrásokhoz vannak rendelve. Csoportosításkor áttelepülnek az egyik fürtcsomópontra. Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.

2. **[1]** Telepítse az SAP NetWeaver ASCS-eket  

   Telepítse az SAP NetWeaver ASCS-t gyökérként egy virtuális állomásnév használatával, amely leképezi az ASCS terheléselosztó előtér-konfigurációjának IP-címét. Az **NW2**rendszer esetében például a virtuális állomásnév <b>az msnw2ascs</b>, <b>10.3.1.16</b> és a terheléselosztó mintavételéhez használt példányszám , például <b>10.</b> az **NW3**rendszer esetében a virtuális állomásnév <b>az msnw3ascs</b>, <b>10.3.1.13</b> és a terheléselosztó mintavételéhez használt példányszám , például <b>20</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz. A paraméter SAPINST_USE_HOSTNAME segítségével telepítheti az SAP-t a virtuális állomásnév használatával.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**SID**/ASCS**Instance# kapcsolóban,** próbálja meg a tulajdonost az ASCS-példány**sapsys-ra** és újrapróbálkozásra. **sid**

3. **[1]** Hozzon létre egy virtuális IP-és állapot-mintavételes fürt erőforrásait a fürtbe üzembe helyezett további SAP-rendszer ERS-példányához. Az itt látható példa az **NW2** és **az NW3** ERS, magas rendelkezésre állású NFS-kiszolgáló használatával. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Az erőforrások létrehozásakor előfordulhat, hogy különböző fürtcsomópontokhoz vannak rendelve. Csoportosításkor áttelepülnek az egyik fürtcsomópontra. Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult.  

   Ezután győződjön meg arról, hogy az újonnan létrehozott ERS-csoport erőforrásai a fürtcsomóponton futnak, szemben azzal a fürtcsomóval, ahol ugyanahhoz az SAP-rendszerhez tartozó ASCS-példány telepítve volt.  Ha például az NW2 ASCS telepítve van a rendszeren, `slesmsscl1` `slesmsscl2`akkor győződjön meg arról, hogy az NW2 ERS csoport fut.  Az NW2 ERS-csoportot `slesmsscl2` a következő parancs futtatásával telepítheti át: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** Telepítse az SAP NetWeaver ERS-t

   Telepítse az SAP NetWeaver ERS-t gyökérként a másik csomóponton, egy virtuális állomásnév használatával, amely leképezi az ERS terheléselosztó előtér-konfigurációjának IP-címét. Az **NW2**rendszer esetében például a virtuális állomás neve <b>msnw2ers</b>, <b>10.3.1.17</b> lesz, és a terheléselosztó mintavételéhez használt példányszám , például <b>12</b>. Az **NW3**rendszer esetében a virtuális állomás neve <b>msnw3ers</b>, <b>10.3.1.19</b> és a terheléselosztó mintavételéhez használt példányszám (például <b>22</b>). 

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz. A paraméter SAPINST_USE_HOSTNAME segítségével telepítheti az SAP-t a virtuális állomásnév használatával.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Használja swpm SP 20 PL 05 vagy magasabb. Az alacsonyabb verziók nem megfelelően állították be az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**NW2**/ERS**instance# kapcsolóban,** próbálja meg a tulajdonost **sid**adm-re, a csoportot pedig az ERS**instance#** mappa sapsys-ére, majd próbálkozzon újra.

   Ha szükséges volt az újonnan üzembe helyezett SAP-rendszer ERS-csoportjának áttelepítése egy másik fürtcsomópontra, ne felejtse el eltávolítani az ERS-csoport helymegkötését. A megkötést a következő parancs futtatásával távolíthatja el (a példa az **NW2** és **NW3**SAP rendszerekre van megadva).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** Igazítsa az ASCS/SCS és ERS példányprofilokat az újonnan telepített SAP-rendszer(ek)hez. Az alábbi példa az NW2-re mutat. Az ASCS/SCS és ERS-profilokat a fürthöz hozzáadott összes SAP-példányhoz módosítania kell.  
 
 * ASCS/SCS profil

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * ERS profil

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** Konfigurálja az SAP-felhasználók at az újonnan telepített SAP-rendszer, ebben a példában **NW2** és **NW3.** 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Adja hozzá az ASCS és ERS SAP-szolgáltatásokat `sapservice` az újonnan telepített SAP-rendszerhez. Az alábbi példa az **NW2** és NW3 SAP rendszerekre **mutat.**  

   Adja hozzá az ASCS szolgáltatásbejegyzést a második csomóponthoz, és másolja az ERS szolgáltatásbejegyzést az első csomópontra. Hajtsa végre a parancsokat az egyes SAP-rendszer a csomóponton, ahol az ASCS-példány az SAP-rendszer telepítve volt.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** Hozza létre az SAP-fürt erőforrásait az újonnan telepített SAP-rendszerhez. 

   Ha enqueue server 1 architektúrát (ENSA1) használ, az **SAP-rendszerek NW2** és **NW3** erőforrásait az alábbiak szerint határozza meg:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Az SAP az SAP NW 7.52-es részéhez benyújtotta a 2-es kiszolgáló várólistára állításának támogatását, beleértve a replikációt is. Az ABAP Platform 1809-től kezdve a 2-es várólistára helyezett kiszolgáló alapértelmezés szerint telepítve van. A 2-es kiszolgáló várólistára állításáról az SAP [2630416 megjegyzése.](https://launchpad.support.sap.com/#/notes/2630416)
   Ha enqueue server 2 architektúrát ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használ, az **SAP-rendszerek NW2** és **NW3** erőforrásait az alábbiak szerint határozza meg:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Ha régebbi verzióról frissít, és a 2-es kiszolgáló várólistára lép, olvassa el az SAP [2641019 megjegyzését.](https://launchpad.support.sap.com/#/notes/2641019) 

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.
   A következő példa a fürt erőforrásainak állapotát mutatja be, miután az **NW2** és **NW3** SAP-rendszerek hozzáadódnak a fürthöz. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Az alábbi képen látható, hogyan fog kinézni a források a HA Web Konsole(Hawk), a források SAP rendszer **NW2** bővült.  

   [![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Folytassa az SAP telepítésével 

Az SAP telepítésének befejezése:

* [Az SAP NetWeaver alkalmazáskiszolgálók előkészítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS-példány telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [Elsődleges SAP-alkalmazáskiszolgáló telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* Egy vagy több további SAP-alkalmazáspéldány telepítése

## <a name="test-the-multi-sid-cluster-setup"></a>A több SID-fürt beállításának tesztelése

A következő tesztek a SUSE legjobb gyakorlatokkal kapcsolatos útmutatóiban szereplő vizsgálati esetek egy részét képezik. Ezek az ön kényelme érdekében benne vannak. A fürttesztek teljes listájához a következő dokumentációban tájékozatja a következőket:

* Ha magas rendelkezésre állású NFS-kiszolgálót használ, kövesse [az SAP NetWeaver magas rendelkezésre állását az Azure-beli virtuális gépeken az SUSE Linux Enterprise Server for SAP-alkalmazásokon.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)  
* Ha Azure NetApp Files NFS-köteteket használ, kövesse [az SAP NetWeaver magas rendelkezésre állását az Azure virtuális gépeken az SUSE Linux Enterprise Server en az Azure NetApp-fájlokkal az SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

Mindig olvassa el a SUSE gyakorlati útmutatókat, és hajtsa végre az összes további, esetleg hozzáadott teszteket.  
A bemutatott tesztek egy két csomópont, több SID-fürt három SAP-rendszer telepítve.  

1. HaGetFailoverConfig és HACheckFailoverConfig tesztelése

   Futtassa a <sapsid>következő parancsokat adm-ként azon a csomóponton, ahol az ASCS-példány jelenleg fut. Ha a parancsok sikertelenek: Nincs elegendő memória, azt az állomásnév kötőjelei okozhatják. Ez egy ismert probléma, és a SUSE az sap-suse-cluster-connector csomagban fogja kijavítani.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Manuálisan telepítse át az ASCS-példányt. A példa bemutatja az SAP-rendszer NW2 ASCS-példányának áttelepítését.  
   Erőforrás állapota, a teszt megkezdése előtt:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Az NW2 ASCS-példány áttelepítéséhez futtassa a következő parancsokat gyökérként.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Tesztelje a HAFailoverToNode-ot. Az itt bemutatott teszt az SAP-rendszer NW2 ASCS-példányának áttelepítését mutatja.  

   Erőforrás állapota a teszt megkezdése előtt:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Futtassa a következő parancsokat **nw2**adm néven az NW2 ASCS-példány áttelepítéséhez.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Csomópontösszeomlás szimulálása

   Erőforrás állapota a teszt megkezdése előtt:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Futtassa a következő parancsot gyökérként azon a csomóponton, ahol legalább egy ASCS-példány fut. Ebben a példában a `slesmsscl2`parancsot a , , ahol az ASCS-példányok NW1 és NW3 futnak.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Ha SBD-t használ, a Pacemaker nem indul el automatikusan a lehalt csomóponton. A csomópont újraindítása utáni állapotnak így kell kinéznie.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   A következő parancsokkal indítsa el a pacemakert a lehalt csomóponton, tisztítsa meg az SBD-üzeneteket, és tisztítsa meg a sikertelen erőforrásokat.

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Erőforrás állapota a teszt után:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
