---
title: Az SAP NW magas rendelkezésre állású Azure-gépei az RHEL multi-SID útmutatóban | Microsoft dokumentumok
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
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247435"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linux sap-alkalmazásokhoz több SID-útmutatóban

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Ez a cikk ismerteti, hogyan telepíthet több SAP NetWeaver magas rendelkezésre állású rendszerek (azaz több-SID) egy két csomópont-fürt az Azure-beli virtuális gépek red hat enterprise Linux SAP-alkalmazások.  

A példakonfigurációk, telepítési parancsok, stb. három SAP NetWeaver 7.50 rendszerek egyetlen, két csomópont magas rendelkezésre állású fürt. Az SAP-rendszerek azonosítói a következők:
* **NW1**: **00** ASCS-példány száma és a virtuális állomásneve **msnw1ascs**; ERS-példány száma **02** és virtuális állomás neve **msnw1ers**.  
* **NW2**: A **10-es** SZÁMÚ ASCS-példány és **az msnw2ascs**virtuális állomásnév ; ERS-példány száma **12** és a virtuális állomás neve **msnw2ers**.  
* **NW3**: **20-as** SZÁMÚ ASCS-példány és virtual hostname **msnw3ascs**; ERS-példány száma **22** és a virtuális állomás neve **msnw3ers**.  

A cikk nem terjed ki az adatbázisrétegre és az SAP NFS-megosztások üzembe helyezésére. Ebben a cikkben példákban az [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) kötet **sapMSID** az NFS-megosztások, feltételezve, hogy a kötet már telepítve van. Azt is feltételezzük, hogy az Azure NetApp Files kötet nfsv3 protokollal van telepítve, és hogy a következő fájlelérési utak léteznek az NW1, NW2 és NW3 SAP-rendszerek ASCS- és ERS-példányai számára:  

* kötet sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* kötet sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* kötet sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* kötet sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Mielőtt elkezdené, először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* Az SAP Note [1928533,]amely:
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuális gépméretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió Windows és Linux rendszeren a Microsoft Azure-ban
* [Az Azure NetApp Files dokumentációja][anf-azure-doc]
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
* [SAP Netweaver a pacemaker fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású bővítmény – áttekintés](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású bővítmény felügyelete](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítményhivatkozás](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS konfigurálása az SAP Netweaver számára az RHEL 7.5-ös rendszerben önálló erőforrásokkal](https://access.redhat.com/articles/3569681)
  * [Sap S/4HANA ASCS/ERS konfigurálása önálló enqueue server 2 (ENSA2) kiszolgálóval az RHEL pacemakerében](https://access.redhat.com/articles/3974941)
* Azure-specifikus RHEL dokumentáció:
  * [Az RHEL magas rendelkezésre állású fürtjeinek támogatási szabályzatai – Microsoft Azure virtuális gépek fürttagként](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7.4 (és újabb) magas rendelkezésre állású fürt telepítése és konfigurálása a Microsoft Azure-ban](https://access.redhat.com/articles/3252491)
* [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

A fürtben részt vevő virtuális gépeknek méretezniük kell az összes erőforrás futtatását, ha feladatátvétel történik. Minden SAP-biztonsági azonosító feladatátvételt egymással szemben a több SID magas rendelkezésre állású fürtben.  

A magas rendelkezésre állás eléréséhez az SAP NetWeaver magas rendelkezésre állású megosztásokat igényel. Ebben a dokumentációban bemutatjuk a példákat az [Azure NetApp Files NFS-köteteken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)üzembe helyezett SAP-megosztásokkal. Lehetőség van a megosztások üzemeltetésére is a magas rendelkezésre állású [GlusterFS-fürtön,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)amelyet több SAP-rendszer is használhat.  

![SAP NetWeaver magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Az SAP ASCS/ERS red hat Linux-szal való több-SID-fürtözésének támogatása az Azure-beli virtuális gépekvendég operációs rendszereként **legfeljebb öt** SAP-SID-re korlátozódik ugyanazon a fürtön. Minden új SID növeli a komplexitást. Az SAP Enqueue Replication Server 1 és az Enqueue Replication Server 2 keveréke **nem támogatott**ugyanazon a fürtön. A több SID-fürtözés több SAP ASCS/ERS-példány telepítését írja le különböző SID-kkel egy pacemaker-fürtben. Jelenleg a több SID-fürtözés csak az ASCS/ERS esetén támogatott.  

> [!TIP]
> Az SAP ASCS/ERS többSID-fürtözése nagyobb komplexitású megoldás. Ez bonyolultabb végrehajtani. Ez is magában foglalja a nagyobb felügyeleti erőfeszítéseket, karbantartási tevékenységek végrehajtásasorán (például az operációs rendszer javítása). A tényleges megvalósítás megkezdése előtt szánjon időt a központi telepítés és az összes érintett összetevő, például a virtuális gépek, az NFS-csatlakoztatások, a VIP-k, a terheléselosztó-konfigurációk és így tovább gondos tervezésére.  

Az SAP NetWeaver ASCS, az SAP NetWeaver SCS és az SAP NetWeaver ERS virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban egy terheléselosztó szükséges a virtuális IP-cím használatához. [A Standard terheléselosztó](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)használatát javasoljuk.  

Az alábbi lista az (A)SCS és ERS terheléselosztó konfigurációját mutatja be ehhez a több SID-fürthöz, három SAP-rendszerrel. Külön előtér-IP-cím, állapot-mintavételek és terheléselosztási szabályok minden EGYES ASCS és ERS-példány minden egyes biztonsági azonosítók. Rendelje hozzá az ÖSSZES virtuális gépet, amelyek az ASCS/ASCS-fürt részét képezik egyetlen ILB-kiszolgáló egy háttérkészletéhez.  

### <a name="ascs"></a>A) a) Scs

* Előtér-konfiguráció
  * AZ NW1 IP-címe: 10.3.1.50
  * AZ NW2 IP-címe: 10.3.1.52
  * AZ NW3 IP-címe: 10.3.1.54

* Mintavételi portok
  * 620<strong>&lt;nr&gt;</strong>port , ezért a 620**00,** 620**10** és 620**20-as** NW1, NW2 és NW3 szondaportokhoz
* Terheléselosztási szabályok – hozzon létre egyet minden példányhoz, azaz NW1/ASCS, NW2/ASCS és NW3/ASCS.
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
  * AZ NW1 10.3.1.51 IP-címe
  * AZ NW2 10.3.1.53 IP-címe
  * AZ NW3 10.3.1.55 IP-címe

* Szonda port
  * A 621<strong>&lt;&gt;nr</strong>port tehát NW1, NW2 és N3 621**02,** 621**12** és 621**22** szondaportesetén
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

## <a name="sap-shares"></a>SAP-megosztások

Az SAP NetWeaver megosztott tárolást igényel az átvitelhez, a profilkönyvtárhoz és így tovább. A magas rendelkezésre állású SAP-rendszer, fontos, hogy magas rendelkezésre állású részvények. El kell döntenie az SAP-megosztások architektúráját. Az egyik lehetőség a megosztások üzembe helyezése az [Azure NetApp Files NFS-köteteken.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  Az Azure NetApp-fájlokkal az SAP NFS-megosztások beépített magas rendelkezésre állását kapja.

Egy másik lehetőség a [GlusterFS létrehozása az Azure-beli virtuális gépeken a Red Hat Enterprise Linux SAP NetWeaver,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)amely több SAP-rendszerek között osztható meg. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Az első SAP-rendszer telepítése a fürtben

Most, hogy úgy döntött, az ARCHitektúra az SAP-megosztások, telepítse az első SAP-rendszer a fürtben, a megfelelő dokumentációt követve.

* Ha Azure NetApp Files NFS-köteteket használ, kövesse az [SAP NetWeaver magas rendelkezésre állását a Red Hat Enterprise Linux on Az Azure NetApp Files sap-alkalmazásokhoz szolgáltatással rendelkező Sap-vm-ek magas rendelkezésre állását](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* GlusterFS-fürt használata esetén kövesse [a GlusterFS szolgáltatást az Azure-beli virtuális gépeken a Red Hat Enterprise Linux on SAP NetWeaver rendszeren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)  

A fent felsorolt dokumentumok végigvezeti a szükséges infrastruktúra előkészítéséhez, a fürt létrehozásához, az operációs rendszer sap-alkalmazás futtatásához történő előkészítéséhez szükséges lépéseken.  

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
* A fürt feladatátvételi funkciója tesztelve lett.  
* Az Összes SAP-rendszer NFS-megosztások üzembe helyezése.  

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

1. Adja hozzá az újonnan üzembe helyezett rendszer (azaz **NW2,** **NW3)** konfigurációját a meglévő Azure Load Balancerhez, az [Azure Load Balancer manuális üzembe helyezésére](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)vonatkozó utasításokat követve az Azure Portalon keresztül. Állítsa be az IP-címeket, az állapotmintaportokat, a terheléselosztási szabályokat a konfigurációhoz.  

2. **[A]** További SAP-rendszerek névfeloldása. Használhatja a DNS-kiszolgálót, vagy módosíthatja `/etc/hosts` az összes csomópontot. Ez a példa a `/etc/hosts` fájl használatát mutatja be.  Igazítsa az IP-címeket és az állomásneveket a környezetéhez. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
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

4. **[A]** Adja hozzá a /sapmnt/SID és a /usr/sap/SID/SYS fájlrendszerek csatlakoztatási bejegyzéseit a fürtbe üzembe helyezett további SAP-rendszerekhez. Ebben a példában **NW2** és **NW3**.  

   Frissítse `/etc/fstab` a fájlt a fürtre telepített további SAP-rendszerek fájlrendszereivel.  

   * Ha Azure NetApp Files-t használ, kövesse az [alábbi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation) utasításokat  
   * GlusterFS cluster használata esetén kövesse az [alábbi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation) utasításokat  

### <a name="install-ascs--ers"></a>AsCS / ERS telepítése

1. Hozza létre a virtuális IP-és állapotminta fürt erőforrásait a fürtbe üzembe helyezett további SAP-rendszerek ASCS-példányaihoz. Az itt látható példa az **NW2** és **NW3** ASCS, NFS használatával az Azure NetApp Files kötetek NFSv3 protokollal.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.  

2. **[1]** Telepítse az SAP NetWeaver ASCS-eket  

   Telepítse az SAP NetWeaver ASCS-t gyökérként egy virtuális állomásnév használatával, amely leképezi az ASCS terheléselosztó előtér-konfigurációjának IP-címét. Az **NW2**rendszer esetében például a virtuális állomásnév <b>az msnw2ascs</b>, <b>10.3.1.52</b> és a terheléselosztó mintavételéhez használt példányszám , például <b>10.</b> Az **NW3**rendszer esetében a virtuális állomásnév <b>az msnw3ascs</b>, <b>10.3.1.54</b> és a terheléselosztó mintavételéhez használt példányszám , például <b>20</b>. Jegyezze fel, hogy melyik fürtcsomóponton telepítette az ASCS-t az egyes SAP-biztonsági azonosítókhoz.  

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz. A paraméter SAPINST_USE_HOSTNAME segítségével telepítheti az SAP-t a virtuális állomásnév használatával.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**SID**/ASCS**Instance# kapcsolóban,** próbálja meg a tulajdonost az ASCS-példány**sapsys-ra** és újrapróbálkozásra. **sid**

3. **[1]** Hozzon létre egy virtuális IP-és állapot-mintavételes fürt erőforrásait a fürtbe üzembe helyezett további SAP-rendszer ERS-példányához. Az itt látható példa az **NW2** és **AZ NW3** ERS, nfs használatával az Azure NetApp Files kötetek NFSv3 protokollal.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult.  

   Ezután győződjön meg arról, hogy az újonnan létrehozott ERS-csoport erőforrásai a fürtcsomóponton futnak, szemben azzal a fürtcsomóval, ahol ugyanahhoz az SAP-rendszerhez tartozó ASCS-példány telepítve volt.  Ha például az NW2 ASCS telepítve van a rendszeren, `rhelmsscl1` `rhelmsscl2`akkor győződjön meg arról, hogy az NW2 ERS csoport fut.  Az NW2 ERS-csoportot `rhelmsscl2` a következő parancs futtatásával telepítheti a csoport egyik fürterőforrására vonatkozóan: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Telepítse az SAP NetWeaver ERS-t

   Telepítse az SAP NetWeaver ERS-t gyökérként a másik csomóponton, egy virtuális állomásnév használatával, amely leképezi az ERS terheléselosztó előtér-konfigurációjának IP-címét. Az **NW2**rendszer esetében például a virtuális állomás neve <b>msnw2ers</b>, <b>10.3.1.53</b> lesz, és a terheléselosztó mintavételéhez használt példányszám , például <b>12</b>. Az **NW3**rendszer esetében a virtuális állomás neve <b>msnw3ers</b>, <b>10.3.1.55</b> és a terheléselosztó mintavételéhez használt példányszám (például <b>22</b>). 

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER segítségével engedélyezheti, hogy egy nem root felhasználó csatlakozzon a sapinsthoz. A paraméter SAPINST_USE_HOSTNAME segítségével telepítheti az SAP-t a virtuális állomásnév használatával.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Használja swpm SP 20 PL 05 vagy magasabb. Az alacsonyabb verziók nem megfelelően állították be az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem tud almappát létrehozni a /usr/sap/**NW2**/ERS**instance# kapcsolóban,** próbálja meg a tulajdonost **sid**adm-re, a csoportot pedig az ERS**instance#** mappa sapsys-ére, majd próbálkozzon újra.

   Ha szükséges volt az újonnan üzembe helyezett SAP-rendszer ERS-csoportjának áttelepítése egy másik fürtcsomópontra, ne felejtse el eltávolítani az ERS-csoport helymegkötését. A megkötést a következő parancs futtatásával távolíthatja el (a példa az **NW2** és **NW3**SAP rendszerekre van megadva). Győződjön meg arról, hogy eltávolítja az ers fürtcsoport áthelyezéséhez használt erőforrás ideiglenes korlátait.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Igazítsa az ASCS/SCS és ERS példányprofilokat az újonnan telepített SAP-rendszer(ek)hez. Az alábbi példa az NW2-re mutat. Az ASCS/SCS és ERS-profilokat a fürthöz hozzáadott összes SAP-példányhoz módosítania kell.  
 
   * ASCS/SCS profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS profil

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Frissítse a /usr/sap/sapservices fájlt

   Annak érdekében, hogy a sapinit indítási parancsfájl megakadályozza a példányok kezdetét, a Pacemaker által kezelt összes példányt ki kell mondani a fájlból. `/usr/sap/sapservices`  Az alábbi példa az **NW2** és NW3 SAP rendszerekre **mutat.**  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Hozza létre az SAP-fürt erőforrásait az újonnan telepített SAP-rendszerhez.  

   Ha enqueue server 1 architektúrát (ENSA1) használ, az **SAP-rendszerek NW2** és **NW3** erőforrásait az alábbiak szerint határozza meg:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Az SAP az SAP NW 7.52-es részéhez benyújtotta a 2-es kiszolgáló várólistára állításának támogatását, beleértve a replikációt is. Az ABAP Platform 1809-től kezdve a 2-es várólistára helyezett kiszolgáló alapértelmezés szerint telepítve van. A 2-es kiszolgáló várólistára állításáról az SAP [2630416 megjegyzése.](https://launchpad.support.sap.com/#/notes/2630416)
   Ha enqueue server 2 architektúrát ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használ, az **SAP-rendszerek NW2** és **NW3** erőforrásait az alábbiak szerint határozza meg:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Ha régebbi verzióról frissít, és a 2-es kiszolgáló várólistára lép, olvassa el az SAP [2641019 megjegyzését.](https://launchpad.support.sap.com/#/notes/2641019) 

   > [!NOTE]
   > A fenti konfigurációban az időtúltöltések csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani. 

   Győződjön meg arról, hogy a fürt állapota rendben van, és hogy minden erőforrás elindult. Nem fontos, hogy melyik csomóponton futnak az erőforrások.
   A következő példa a fürt erőforrásainak állapotát mutatja be, miután az **NW2** és **NW3** SAP-rendszerek hozzáadódnak a fürthöz. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Adja hozzá az ASCS és az ERS tűzfalszabályait mindkét csomóponton.  Az alábbi példa az NW2 és **AZ NW3** SAP rendszerek tűzfalszabályait **mutatja be.**  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Folytassa az SAP telepítésével 

Az SAP telepítésének befejezése:

* [Az SAP NetWeaver alkalmazáskiszolgálók előkészítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS-példány telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Elsődleges SAP-alkalmazáskiszolgáló telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Egy vagy több további SAP-alkalmazáspéldány telepítése

## <a name="test-the-multi-sid-cluster-setup"></a>A több SID-fürt beállításának tesztelése

A következő tesztek a Red Hat legjobb gyakorlatokkal kapcsolatos útmutatóiban szereplő tesztesetek egy részét képezik. Ezek az ön kényelme érdekében benne vannak. A fürttesztek teljes listájához a következő dokumentációban tájékozatja a következőket:

* Ha Az Azure NetApp Files NFS-kötetek használata esetén kövesse az [SAP NetWeaver magas rendelkezésre állását az SAP NetWeaver számára az SAP-alkalmazásokhoz készült Azure NetApp-fájlok és az SAP-fájlok szolgáltatása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) esetén
* Ha magas `GlusterFS`rendelkezésre állású, kövesse [az Azure virtuális gépek magas rendelkezésre állású SAP NetWeaver RHEL SAP-alkalmazások.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)  

Mindig olvassa el a Red Hat gyakorlati útmutatókat, és végezze el az összes további tesztet, amely et hozzáadhatta.  
A bemutatott tesztek egy két csomópont, több SID-fürt három SAP-rendszer telepítve.  

1. Manuálisan telepítse át az ASCS-példányt. A példa bemutatja az SAP-rendszer NW3 ASCS-példányának áttelepítését.

   Erőforrás állapota a teszt megkezdése előtt:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Az NW3 ASCS-példány áttelepítéséhez futtassa a következő parancsokat gyökérként.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Erőforrás állapota a teszt után:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Csomópontösszeomlás szimulálása

   Erőforrás állapota a teszt megkezdése előtt:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Futtassa a következő parancsot gyökérként egy csomóponton, ahol legalább egy ASCS-példány fut. Ebben a példában a `rhelmsscl1`parancsot a ,, ahol az ASCS-példányok NW1, NW2 és NW3 futnak.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   A teszt utáni állapotnak és a lezuhanásnak a lezuhanása után újra kell kinéznie.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Ha vannak üzenetek a sikertelen erőforrásokhoz, tisztítsa meg a sikertelen erőforrások állapotát. Példa:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
