---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux az Azure Files-NetApp |} A Microsoft Docs
description: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503577"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux for SAP-alkalmazások Azure NetApp fájlokkal

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, konfigurálja a virtuális gépek, telepítse a fürt keretrendszert és egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert, használatával [Azure NetApp fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
A példa konfigurációk esetén a telepítési parancsokat stb. ASCS példány számos 00, a szám 02 01, elsődleges alkalmazáspéldány (szolgáltatói CÍMEI) pedig az alkalmazáspéldány (AAS) 03 SSZON példány. Az SAP-rendszer azonosító QAS szolgál. 

Az adatbázis réteg nem ez a cikk részletesen ismertetett.  

Először olvassa el az alábbi SAP-megjegyzések és tanulmányok:

* [Az Azure NetApp Files dokumentációja][anf-azure-doc] 
* SAP-Jegyzetnek [1928533], amely rendelkezik:
  * SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek
  * Azure-beli Virtuálisgép-méretek esetében fontos kapacitásadatok
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure

* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott szoftverek központi telepítéséhez szükséges előfeltételeket ismerteti.
* SAP-Jegyzetnek [2002167] javasolt a Red Hat Enterprise Linux operációs rendszer beállításai
* SAP-Jegyzetnek [2009879] Red Hat Enterprise Linux for SAP HANA-irányelvek rendelkezik
* SAP-Jegyzetnek [2178632] részletes jelentett az Azure-beli SAP-figyelési metrikákat kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzések Linux rendszeren.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP, Linux rendszeren][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux rendszeren][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [SAP Netweaver támasztja fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL-dokumentáció
  * [Magas rendelkezésre állású bővítmény áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású kiegészítő felügyeleti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású bővítmény referencia](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [SAP Netweaver RHEL 7.5 önálló erőforrásokkal ASCS/SSZON konfigurálása](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/SSZON önálló sorba kiszolgáló 2 (ENSA2) az RHEL támasztja konfigurálása ](https://access.redhat.com/articles/3974941)
* Az Azure egyedi RHEL dokumentációja:
  * [RHEL magas rendelkezésre állású fürtöket – Microsoft Azure virtuális gépek a fürt tagjai terméktámogatási irányelveinek](https://access.redhat.com/articles/3131341)
  * [Telepítése és a Microsoft Azure egy Red Hat Enterprise Linux 7.4-es (és újabb verziók) magas rendelkezésre állású fürt konfigurálása](https://access.redhat.com/articles/3252491)
* [NetApp SAP alkalmazások az Azure Files-NetApp használatával a Microsoft Azure][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Magas availability(HA) SAP Netweaver központi szolgáltatásokhoz megosztott tárolóra van szükség.
Ennek érdekében javítottunk Red Hat Linux-alapú, amennyiben szükséges volt külön magas rendelkezésre állású GlusterFS fürtöt hozhat létre. 

Most Önön eléréséhez SAP Netweaver magas rendelkezésre ÁLLÁS megosztott tárolót üzembe helyezve az Azure Files-NetApp használatával lehetséges. A megosztott tároló nem kell az Azure NetApp Files használatával további [GlusterFS fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Magas rendelkezésre ÁLLÁS, az SAP Netweaver központi services(ASCS/SCS) támasztja továbbra is szükség van.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS, and the SAP HANA database use virtual hostname and virtual IP addresses. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztót a külön előtér-IP-címek (A) SCS és SSZON konfigurációját jeleníti meg.

> [!IMPORTANT]
> Több SID-vel fürtözés az SAP ASCS/SSZON Red Hat Linux rendszerű, Azure virtuális gépeken futó vendég operációs rendszer **nem támogatott**. Több SID-vel fürtszolgáltatás egy támasztja fürt eltérő SID-több SAP ASCS/SSZON-példány telepítését ismerteti.

### <a name="ascs"></a>(A)SCS

* Előtér-konfigurációjához
  * IP-cím 192.168.14.9
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 620<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfigurációjához
  * IP-cím 192.168.14.10
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 621<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Az Azure Files-NetApp infrastruktúra beállítása 

SAP NetWeaver az átvitel és a profil Directory megosztott tároló szükséges hozzá.  A Azure NetApp files-infrastruktúra telepítése előtt, ismerje meg az a [Azure NetApp fájlok dokumentáció][anf-azure-doc]. Ellenőrizze, hogy ha a kiválasztott Azure-régiót kínál Azure NetApp fájlokat. Az alábbi hivatkozásra a NetApp Azure fájlok az Azure-régióban rendelkezésre állását jeleníti meg: [Azure NetApp rendelkezésre állási fájlok az Azure-régió szerint][anf-avail-matrix].

NetApp az Azure files érhető el több [Azure-régiók](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Azure NetApp fájlok üzembe helyezése előtt kérése a NetApp Azure Files, a következő megoldás előkészítése az [regisztrálni Azure NetApp fájlok útmutatás][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>NetApp fájlokat az Azure-erőforrások üzembe helyezése  

A lépések azt feltételezik, hogy korábban már telepítettek [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Az Azure Files-NetApp erőforrásokat és a virtuális gépek, ahol a NetApp fájlokat az Azure-erőforrások lesz csatlakoztatva kell telepíteni az azonos Azure virtuális hálózatban vagy Azure virtuális Társhálózaton belüli.  

1. Ha nem tette meg már, [bevezetése az Azure Files-NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. A NetApp fiók létrehozása a kiválasztott Azure-régióban, a következő a [NetApp fiók létrehozására vonatkozó utasításokat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Állítsa be a következő Azure NetApp fájlok kapacitás-készletben a [való beállítása az Azure Files-NetApp kapacitás készlet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Az SAP Netweaver-architektúra ebben a cikkben bemutatott egyetlen Azure NetApp fájlok kapacitás készletet, prémium szintű Termékváltozatot használja. Azure NetApp fájlok prémium Termékváltozatot javasoljuk az SAP Netweaver alkalmazás számítási feladatait az Azure-ban.  
4. Azure NetApp files alhálózat delegálása leírtak szerint a [utasításokat delegálása az Azure Files-NetApp alhálózat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Az Azure Files-NetApp köteteket, a következő üzembe helyezheti a [kötet létrehozása a NetApp Azure-fájlok útmutatás](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). A köteteket a kijelölt NetApp Azure Files szolgáltatásban üzembe helyezheti [alhálózati](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Ne feledje, hogy a NetApp fájlokat az Azure-erőforrások és az Azure virtuális gépek azonos Azure virtuális hálózatban vagy Azure virtuális Társhálózaton belüli kell lennie. Ebben a példában két Azure NetApp fájlok kötet használjuk: sap<b>QAS</b> és transSAP. A Fájlelérési utak, amelyek csatlakoztatva vannak a megfelelő csatlakoztatási pontok /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, stb.  

   1. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sszon)
   5. kötet transSAP (nfs://192.168.24.4/transSAP)
   6. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>szolgáltatói címei)
   7. kötet sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Ebben a példában a NetApp fájlokat az Azure minden SAP Netweaver fájlrendszerekben bemutatásához, hogyan használható az Azure Files-NetApp használtuk. Az SAP fájlrendszereket, amelyeket nem kell csatlakoztatnia kell az NFS-n keresztül is telepíthető központilag [az Azure disk storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . Ebben a példában <b>-e</b> kell lennie az Azure Files-NetApp és <b>f-g</b> (azaz usr/sap/<b>QAS</b>/D<b>02</b>,usr/sap/<b>QAS </b>/D<b>03</b>), az Azure disk storage is telepíthető. 

### <a name="important-considerations"></a>Fontos szempontok

Az SAP Netweaver SUSE magas rendelkezésre állású architektúra az Azure NetApp fájlok kiválasztásakor vegye figyelembe az alábbi fontos szempontokat:

- A kapacitásérték-minimumot készlet használata 4 Tib-ra. A kapacitás a készlet méretét 4 Tib-ra többszörösének kell lennie.
- A minimális kötet 100 GiB
- NetApp Azure Files és az összes virtuális gép, ahol az Azure Files-NetApp kötetek lesz csatlakoztatva, kell lennie, az azonos Azure virtuális hálózatban vagy a [társviszonyban álló virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ugyanabban a régióban. Keresztül a virtuális hálózatok közötti társviszony az azonos régióban található Azure NetApp fájlok hozzáférés mostantól támogatott. Az Azure a NetApp hozzáférés globális társviszony-létesítésen keresztül még nem támogatott.
- A kiválasztott virtuális hálózat egy alhálózat Azure NetApp Files, meghatalmazott kell rendelkeznie.
- NetApp Azure Files jelenleg csak NFSv3 támogatja 
- Biztosít a NetApp Azure Files [házirend exportálása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): szabályozhatja az engedélyezett ügyfelektől, a hozzáférés típusa (Olvasás és írás, csak olvasható, stb.). 
- Azure NetApp fájlok funkció még nem ismeri a zóna. Azure NetApp fájlok szolgáltatás jelenleg nem telepített minden rendelkezésre állási zónák egy Azure-régióban. Vegye figyelembe a lehetséges késés hatással az egyes Azure-régióban. 

## <a name="setting-up-ascs"></a>(A) SCS beállítása

Ebben a példában a rendszer üzembe helyezett erőforrások manuálisan keresztül a [az Azure portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű Azure-portálon

Először meg kell az Azure Files-NetApp köteteket hozhat létre. A virtuális gépek üzembe helyezése. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Load Balancer létrehozása (belső)  
   1. Hozzon létre az előtérbeli IP-címek
      1. Az ASCS 192.168.14.9 IP-címe
         1. Nyissa meg a terheléselosztó, válassza ki az előtérbeli IP-címkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **előtérbeli. QAS. ASCS**)
         1. A hozzárendelés Static értékre, és adja meg az IP-címet (például **192.168.14.9**)
         1. Kattintson az OK gombra
      1. Az ASCS SSZON 192.168.14.10 IP-címe
         * Ismételje meg a fenti lépéseket a SSZON IP-cím létrehozása "a" alatt (például **192.168.14.10** és **előtérbeli. QAS. SSZON**)
   1. Hozzon létre a háttérkészletek
      1. Az ASCS háttérkészlet létrehozása
         1. Nyissa meg a terheléselosztó, válassza ki a háttérkészletek, és kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **háttérrendszer. QAS**)
         1. Kattintson a Hozzáadás elemre egy virtuális gépet.
         1. Válassza ki a korábban létrehozott ascs rendszerbe fut be a rendelkezésre állási csoport 
         1. Válassza ki a virtuális gépek (A) SCS-fürt
         1. Kattintson az OK gombra
   1. Az állapotminták létrehozása
      1. Port 620**00** ASCS számára
         1. Nyissa meg a terheléselosztó, válassza ki az állapotmintákat, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotadat-mintavétel nevét (például **állapotát. QAS. ASCS**)
         1. Válassza ki a TCP protokoll, port 620**00**, időköz 5 és a nem kifogástalan állapot küszöbértéke 2
         1. Kattintson az OK gombra
      1. Port 621**01** ASCS SSZON számára
            * Ismételje meg a fenti lépéseket a "c" hozzon létre egy állapotmintát az on-felhasználók számára (például 621**01** és **állapotát. QAS. SSZON**)
   1. Terheléselosztási szabályok
      1. 32**00** TCP ASCS használata
         1. Nyissa meg a terheléselosztó, terheléselosztási szabályok kiválasztása, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó-szabályt nevét (például **lb. QAS. ASCS.3200**)
         1. Válassza ki az előtérbeli IP-cím ascs rendszerbe fut be, a háttérkészlet, valamint a korábban létrehozott állapotadat-mintavétel (például **előtérbeli. QAS. ASCS**)
         1. Tartsa protokoll **TCP**, adja meg a port **3200**
         1. Üresjárati időkorlát akár 30 percig növelése
         1. **Ügyeljen arra, hogy Floating IP engedélyezése**
         1. Kattintson az OK gombra
      1. Az ASCS további portokat
         * Ismételje meg a fenti lépéseket a "d" portok 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14., 5**00**16 és a TCP az ASCS használata
      1. Az ASCS SSZON további portokat
         * Ismételje meg a fenti lépéseket a "d" portok 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 és TCP az ASCS SSZON számára


> [!IMPORTANT]
> Ne engedélyezze a TCP időbélyegeket Azure Load Balancer mögé helyezett Azure virtuális gépeken. Sikertelen állapotadat-mintavételek engedélyezése TCP időbélyegek miatt. A paramétert **net.ipv4.tcp_timestamps** való **0**. További részletekért lásd: [Load Balancer állapot-mintavételei](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a Red Hat Enterprise Linux az Azure-beli beállítása](high-availability-guide-rhel-pacemaker.md) hozzon létre egy alapszintű támasztja fürtről ehhez (A) SCS-kiszolgálón.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver-telepítés előkészítése

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

    ```
    sudo vi /etc/hosts
    ```

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  Az Azure Files-NetApp kötet létrehozása SAP könyvtárak.  
   A virtuális gépek egyik ideiglenesen az Azure NetApp fájlok Kötet csatlakoztatásához, és hozza létre az SAP-könyvtárak (Fájlelérési utak).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr + i/sapmnt/QAS sudo chattr + i /usr/sap/trans sudo chattr + i /usr/sap/QAS/SYS sudo chattr + i /usr/sap/QAS/ASCS00 sudo chattr + i /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y erőforrás-ügynökök – sap nfs-utils erőforrás-ügynökök telepítése
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Beépülő modulok betöltése: langpacks, termékazonosító, a search-le van tiltva-adattárakkal
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata 2 hét régi van. Telepítés a yum-cron? Vagy futtassa: yum makecache gyors
   # <a name="installed-packages"></a>Telepített csomagok
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Verzió: 3.9.5
   # <a name="release------124el7"></a>Verzió: 124.el7
   # <a name="size---------100-k"></a>Méret: 100 k
   # <a name="repo---------installed"></a>Adattár: telepített
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>A tárház: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Összefoglalás: SAP fürtügynökök erőforrás és az összekötő szkripttel
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL-CÍME: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licenc: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Leírás: Az SAP erőforrás ügynökök és az összekötő parancsfájl kapcsolaton
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Támasztja, hogy a fürt kell kezelni az SAP-példányok
   #          <a name="-environment"></a>: környezetben.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Adja hozzá a következő sorokat az fstab, mentése és kilépés
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, nehéz, rsize = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>A tulajdonság ResourceDisk.EnableSwap y értéke
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Létrehozhat és használhat a lapozófájl erőforrás lemezen.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Tulajdonság ResourceDisk.SwapSizeMB a lapozófájl méretének beállítása
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>A szabad erőforrás-lemez eltérő virtuálisgép-méretet. Győződjön meg arról, hogy túl nagy az értéke nincs beállítva. Ellenőrizheti, hogy a lapozóterület-parancs swapon
   # <a name="size-of-the-swapfile"></a>A lapozófájl mérete.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo szolgáltatás waagent újraindítása
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo számítógépek erőforrás-létrehozás fs_QAS_ASCS fájlrendszer device='192.168.24.5:/sapQAS/usrsapQASascs \
     könyvtár = "/ usr/sap/QAS/ASCS00" fstype = "nfs" \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     IP = 192.168.14.9 cidr_netmask = 24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo számítógépek állapota
   
   # <a name="node-anftstsapcl2-standby"></a>Node anftstsapcl2: standby
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Erőforrások teljes listája:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):      Elindított anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Elindított anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Elindított anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Elindított anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>SWPM elérését teszi lehetővé. Ez a szabály nem állandó. Ha az újraindítás, akkor futtassa újra a parancsot.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo tulajdonost qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo számítógépek csomópont unstandby anftstsapcl2 sudo számítógépek csomópont készenléti anftstsapcl1
   
   sudo számítógépek erőforrás-létrehozás fs_QAS_AERS fájlrendszer device='192.168.24.5:/sapQAS/usrsapQASers \
     könyvtár = "/ usr/sap/QAS/ERS01" fstype = "nfs" \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     IP = 192.168.14.10 cidr_netmask = 24 \
    --group g-QAS_AERS
   
   sudo számítógépek erőforrás létrehozásának nc_QAS_AERS azure-lb port = 62101 \
    --group g-QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo számítógépek állapota
   
   # <a name="node-anftstsapcl1-standby"></a>Csomópont anftstsapcl1: készenléti
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Erőforrások teljes listája:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Elindított anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Elindított anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl2 használatába <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Elindított anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Elindított anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Elindított anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Elindított anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>SWPM elérését teszi lehetővé. Ez a szabály nem állandó. Ha az újraindítás, akkor futtassa újra a parancsot.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo tulajdonost qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Az újraindítási parancsnak váltson egy indítási parancsot.
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 helyi $(_EN) pf=$(_PF) =
   Start_Program_01 helyi $(_EN) pf=$(_PF) =
   
   # <a name="add-the-keep-alive-parameter"></a>A életben tartási paraméter hozzáadása
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Az újraindítási parancsnak váltson egy indítási parancsot.
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 helyi $(_ER) pf=$(_PFL) NR=$(SCSID) =
   Start_Program_00 helyi $(_ER) pf=$(_PFL) NR=$(SCSID) =
   
   # <a name="remove-autostart-from-ers-profile"></a>Autostart eltávolítása SSZON profil
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>A Linux rendszer-konfiguráció módosítása
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>A csomóponton, amelyre telepítve van a ascs rendszerbe fut be tegye megjegyzésbe a következő sor
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ASCS00/exe: $LD_LIBRARY_PATH; Exportálás LD_LIBRARY_PATH; /usr/SAP/QAS/ASCS00/exe/sapstartsrv pf = / usr/sap/QAS/SYS/profil/QAS_ASCS00_anftstsapvh - D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>A csomóponton, amelyre telepítve van a SSZON tegye megjegyzésbe a következő sor
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: $LD_LIBRARY_PATH; Exportálás LD_LIBRARY_PATH; /usr/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/profil/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo számítógépek tulajdonságát állítsa be a karbantartási módú = true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta erőforrás-megtartásának = 5000 áttelepítési-küszöbérték = 1 \
    --group g-QAS_ASCS
   
    sudo számítógépek erőforrás-létrehozás rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo számítógépek megkötés közös elhelyezés adja hozzá a g-QAS_AERS a g-QAS_ASCS-5000 sudo számítógépek megkötés hely rsc_sap_QAS_ASCS00 szabály pontszám = 2000 runs_ers_QAS eq 1 sudo számítógépek megkötés rendelés g-QAS_ASCS majd g-QAS_AERS típusú = nem kötelező szimmetrikus = false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo számítógépek tulajdonságát állítsa be a karbantartási módú = true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta erőforrás-megtartásának = 5000 \
    --group g-QAS_ASCS
   
    sudo számítógépek erőforrás-létrehozás rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo számítógépek megkötés közös elhelyezést a g-QAS_AERS altípussal való használata a g-QAS_ASCS-5000 sudo számítógépek megkötés rendelés g-QAS_ASCS majd g-QAS_AERS hozzáadása nem kötelező szimmetrikus = = false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo számítógépek állapota
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Erőforrások teljes listája:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Elindított anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Elindított anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Elindított anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Elindított anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Elindított anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Elindított anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Elindított anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Az ASCS a mintavételi portot
   sudo tűzfal-parancs--zóna nyilvános---port = 62000/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 62000/tcp sudo tűzfal-parancs--= zóna nyilvános---port = 3200/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 3200/tcp sudo = tűzfal-parancs--zóna nyilvános---port = 3600/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = = 3600/tcp sudo tűzfal-parancs--zóna nyilvános---port = 3900/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 3900/tcp sudo = tűzfal-parancs--zóna nyilvános---port = 8100-as/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 8100-as/tcp sudo tűzfal-parancs--= zóna nyilvános---port = 50013/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 50013/tcp sudo = tűzfal-parancs--zóna nyilvános---port = 50014/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 50014/tcp sudo tűzfal-parancs--= zóna nyilvános---port = 50016/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = = 50016/tcp
   # <a name="probe-port-of-ers"></a>SSZON mintavételi Port
   sudo tűzfal-parancs--zóna nyilvános---port = 62101/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 62101/tcp sudo tűzfal-parancs--= zóna nyilvános---port = 3301/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 3301/tcp sudo = tűzfal-parancs--zóna nyilvános---port = 50113/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 50113/tcp sudo tűzfal-parancs--= zóna nyilvános---port = 50114/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = 50114/tcp sudo = tűzfal-parancs--zóna nyilvános---port = 50116/tcp--= állandó sudo tűzfal-parancs--zóna nyilvános---port = = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi/etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>A terheléselosztó előtérbeli konfigurálásához az SAP NetWeaver ASCS IP-címe
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>A terheléselosztó előtérbeli konfigurálásához az SAP NetWeaver ASCS SSZON IP-címe
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr + i/sapmnt/QAS sudo chattr + i /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y nfs-utils uuidd telepítése
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Adja hozzá a következő sorokat az fstab, mentése és kilépés
   192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr + i /usr/sap/QAS/D02
   
   sudo vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Adja hozzá a következő sort az fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3
   
   # <a name="mount"></a>Csatlakoztatás
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr + i /usr/sap/QAS/D03
   
   sudo vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Adja hozzá a következő sort az fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw, nehéz, rsize a 65536 értékű wsize = = 65536, vers = 3
   
   # <a name="mount"></a>Csatlakoztatás
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>A tulajdonság ResourceDisk.EnableSwap y értéke
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Létrehozhat és használhat a lapozófájl erőforrás lemezen.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Tulajdonság ResourceDisk.SwapSizeMB a lapozófájl méretének beállítása
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>A szabad erőforrás-lemez eltérő virtuálisgép-méretet. Győződjön meg arról, hogy túl nagy az értéke nincs beállítva. Ellenőrizheti, hogy a lapozóterület-parancs swapon
   # <a name="size-of-the-swapfile"></a>A lapozófájl mérete.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo szolgáltatás waagent újraindítása
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore listája
   ```

   This should list all entries and should look similar to
   ```
   FÁJL adat: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. DAT KULCSFÁJL: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. KULCS
   
   KULCS ALAPÉRTELMEZETT ENV: 192.168.14.4:30313   USER: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - qasadm hdbuserstore alapértelmezett qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Távolítsa el a SSZON, amely az áttelepítés részeként történt sikertelen műveletek
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl2 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl2 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] # echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Erőforrások teljes listája:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   
   Sikertelen műveletek:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl2 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl2 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Erőforráscsoport anftstsapcl1 lépései: a g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Erőforráscsoport anftstsapcl1 lépései: a g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 rsc_sap_QAS_ERS01 használatába (ocf::heartbeat:SAPInstance):   Elindított anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
