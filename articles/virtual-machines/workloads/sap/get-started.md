---
title: Ismerkedés az SAP Azure-beli virtuális gépeken való használatába | Microsoft Docs
description: A virtuális gépeken (VM) futó SAP-megoldások ismertetése Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/03/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0219b3e35ed82795e9c799754cabdeb5d17096d6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267409"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Az Azure használata az SAP munkaterhelés-forgatókönyvek üzemeltetéséhez és futtatásához

A Microsoft Azure használatakor megbízhatóan futtathatja a kritikus fontosságú SAP-munkaterheléseket és forgatókönyveket egy skálázható, megfelelő és nagyvállalati használatra kipróbált platformon. Az Azure méretezhetőségét, rugalmasságát és költségmegtakarítását is elérheti. A Microsoft és az SAP közötti kibővített partneri együttműködés révén az Azure-ban fejlesztési és tesztelési és éles környezetben is futtathat SAP-alkalmazásokat, és teljes mértékben támogatott. Az SAP NetWeavertól az SAP S/4HANA, a Linuxon futó SAP BI-tól a Windowshoz, és SAP HANA az SQL-hez.

Az SAP NetWeaver-forgatókönyvek az Azure-beli különböző adatbázis-kezelőn kívül más SAP-munkaterhelési forgatókönyveket is tartalmazhatnak, például az Azure-beli SAP BI-t. 

Az Azure SAP HANA-hoz való egyedisége egy olyan ajánlat, amely az Azure-t egymástól függetlenül állítja be. Ahhoz, hogy több memóriát és CPU-erőforrást igénylő SAP-forgatókönyveket lehessen üzemeltetni, amelyek SAP HANA, az Azure az ügyfél által dedikált operációs rendszer nélküli hardver használatát kínálja. Ezzel a megoldással olyan SAP HANA üzemelő példányokat futtathat, amelyek akár 24 TB (120 – TB kibővített) memóriát igényelnek az S/4HANA vagy más SAP HANA számítási feladatokhoz. 

Az Azure SAP munkaterhelés-forgatókönyvek üzemeltetése az identitás-integrációra és az egyszeri bejelentkezésre vonatkozó követelményeket is képes létrehozni. Ez a helyzet akkor fordulhat elő, ha Azure Active Directory (Azure AD) használatával csatlakozik a különböző SAP-összetevőkhöz és az SAP-szolgáltatásokhoz (SaaS) vagy a szolgáltatásként kínált platformokhoz. Az Azure AD-val és az SAP-entitásokkal kapcsolatos ilyen integrációs és egyszeri bejelentkezési forgatókönyvek listáját a "HRE SAP Identity Integration és az egyszeri bejelentkezés" című szakaszban ismertetjük és dokumentáljuk.

## <a name="changes-to-the-sap-workload-section"></a>Az SAP munkaterhelés szakasz változásai
Az SAP on Azure munkaterhelés című szakaszban szereplő dokumentumok módosításai a cikk végén találhatók. A módosítási napló bejegyzései körülbelül 180 napig tartanak.

## <a name="you-want-to-know"></a>Tudni szeretné
Ha konkrét kérdései vannak, egy adott dokumentumra vagy folyamatra fogunk rámutatni, amely a Kezdőlap ezen szakaszában található. A következőket szeretné tudni:

- Az Azure-beli virtuális gépeket és a HANA nagyméretű példány-egységeket a SAP-szoftverek és az operációs rendszer verziószámai támogatják. Olvassa el az [Azure-beli üzembe helyezéshez támogatott SAP-szoftvereket](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) és az információk megkeresésének folyamatát ismertető dokumentumot.
- Milyen SAP-telepítési forgatókönyvek támogatottak az Azure-beli virtuális gépek és a HANA Large-példányok esetében. A támogatott forgatókönyvekkel kapcsolatos információk a következő dokumentumokban találhatók:
    - [SAP számítási feladatok az Azure-beli virtuális gépek által támogatott forgatókönyvekben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [A HANA nagyméretű példányának támogatott forgatókönyvei](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA az Azure-ban (nagyméretű példányok)

A dokumentumok egy sorozata az Azure-ban (nagyméretű példányokon), vagy rövid, HANA nagyméretű példányokon SAP HANA. A HANA nagyméretű példányaival kapcsolatos információkért tekintse meg a dokumentum [áttekintését és az Azure-beli SAP HANA architektúráját (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) , és tekintse át a nagyméretű Hana-példányok című szakasz kapcsolódó dokumentációját.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA Azure-beli virtuális gépeken
A dokumentáció ezen szakasza a SAP HANA különböző szempontjait ismerteti. Előfeltételként ismernie kell az Azure legfontosabb szolgáltatásait, amelyek az Azure IaaS általános szolgáltatásait biztosítják. Ezért az Azure számítási, tárolási és hálózatkezelési ismereteire van szüksége. Ezeknek a témaköröknek a kezelése az SAP NetWeaver-hez kapcsolódó [Azure tervezési útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)történik. 

Az Azure-beli HANA-ról további információt a következő cikkekben és alcikkeiben talál:

- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA magas rendelkezésre állás az Azure Virtual Machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Biztonsági mentési útmutató Azure-beli virtuális gépek SAP HANAához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure-beli virtuális gépeken üzembe helyezett SAP NetWeaver
Ez a szakasz az SAP NetWeaver és az Azure-beli Business One tervezési és üzembe helyezési dokumentációját sorolja fel. A dokumentáció az Azure-beli SAP-munkaterheléssel rendelkező, nem HANA-adatbázisok alapjaira és használatára koncentrál. A magas rendelkezésre állást biztosító dokumentumok és cikkek a HANA magas rendelkezésre állásának alapja is az Azure-ban, például:

- Az [Azure tervezési útmutatója](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One az Azure Virtual Machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Többplatformos SAP NetWeaver-alkalmazások telepítésének biztosítása Site Recovery használatával](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Az Azure SAP LaMa összekötője](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Az Azure-beli SAP-munkaterhelések keretében nem HANA-adatbázisokkal kapcsolatos információkért lásd:

- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines adatbázis-kezelő rendszerbe állítás az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP-MaxDB, élő gyorsítótár és a Content Server üzembe helyezése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

További információ az Azure-beli SAP HANA adatbázisokról: "SAP HANA az Azure Virtual Machines szolgáltatásban" című rész.

Az Azure-beli SAP-munkaterhelések magas rendelkezésre állásáról további információt a következő témakörben talál:

- [Azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Ez a dokumentum különböző architektúra-és forgatókönyv-dokumentumokra mutat. A későbbi forgatókönyvek dokumentumaiban olyan részletes technikai dokumentumokra mutató hivatkozásokat talál, amelyek ismertetik a különböző magas rendelkezésre állású módszerek üzembe helyezését és konfigurálását. A különböző dokumentumok, amelyek bemutatják, hogyan hozhat létre és konfigurálhat magas rendelkezésre állást egy SAP NetWeaver-munkaterhelés számára a Linux és a Windows operációs rendszereken.


Az Azure Active Directory (Azure AD) és az SAP Services és az egyszeri bejelentkezés közötti integrációval kapcsolatos információkért lásd:

- [Oktatóanyag: Azure Active Directory SAP Cloud-integráció az ügyfelek számára](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP Cloud platform Identity Authentication szolgáltatással](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP NetWeaver-vel](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP Business Bydesignnal](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció a SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Az S/4HANA-környezet: Fiori dob SAML egyszeri bejelentkezés az Azure AD-vel](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Az Azure-szolgáltatások SAP-összetevőkbe való integrálásával kapcsolatos információkért lásd:

- [Az SAP HANA használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery és SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Az SAP BW Connector használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Az Azure Data Factory SAP HANA- és Business Warehouse-adatok integrációját nyújtja](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Változásnapló
- 03/03/2020: az [SAP NW magas rendelkezésre állásának megváltozása Azure-beli virtuális gépeken a SLES-ben a ANF for SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) a hatékonyabb ANF mennyiségi elrendezésre való váltáshoz
- 03/01/2020: átdolgozott [biztonsági mentési útmutató az Azure Virtual Machines SAP HANAához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) Azure Backup szolgáltatás belefoglalásához. Csökkenthető és tömörített tartalom a [SAP HANA Azure Backup a fájl szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) , és törölte a biztonsági mentéssel foglalkozó harmadik dokumentumot a lemez pillanatképének használatával. A tartalom az Azure-beli SAP HANA biztonsági mentési útmutatójában lesz kezelve Virtual Machines 
- 02/27/2020: a magas rendelkezésre állás megváltozása az SAP NW-ben Azure-beli [virtuális gépeken a SLES for](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)SAP-alkalmazások esetében, magas rendelkezésre állás az Azure-beli virtuális gépeken a SLES-ben a [ANF for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) és [magas rendelkezésre állású SAP NetWeaver Azure-beli virtuális gépeken a SLES multi-SID útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) a "sikertelen"
- 02/26/2020: változás a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) az Azure-beli HANA-beli fájlrendszer-választás tisztázásához
- 02/26/2020: változás a [magas rendelkezésre állású architektúrában és az SAP-forgatókönyvekben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) , hogy tartalmazza az Azure-beli virtuális gépeken elérhető SAP NetWeaver-ra mutató HIVATKOZÁST a RHEL multi-SID útmutatóban
- 02/26/2020: az SAP [NW magas rendelkezésre állásának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)megváltozása az Azure-beli virtuális gépeken az SLES for SAP-alkalmazások esetében, [magas rendelkezésre állás az SAP NW számára az Azure-beli virtuális GÉPEKen a SLES-ben a ANF for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), az Azure virtuális gépek [magas rendelkezésre állása a RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) és az Azure-beli [virtuális gépek magas rendelkezésre Azure NetApp Files állásáról](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- 02/26/2020: az [SAP NetWeaver magas rendelkezésre állásának kiadása az Azure-beli virtuális gépeken a RHEL multi-SID útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) a SUSE multi-SID-fürtre mutató hivatkozás hozzáadásához
- 02/25/2020: változás a [magas rendelkezésre állású architektúrában és az SAP-forgatókönyvekben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) az újabb ha-cikkekre mutató hivatkozások hozzáadásához
- 02/25/2020: az [IBM DB2-LUW magas rendelkezésre állásának megváltozása az Azure-beli virtuális gépeken SUSE Linux Enterprise Server és a pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) használatával, amely leírja a nyilvános végponthoz való hozzáférést a standard Azure Load balancerrel
- 02/21/2020: az SAP [bevezetésének Azure Virtual Machines adatbázis-kezelő üzembe helyezése SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) című cikk teljes felülvizsgálata
- 02/21/2020: változás a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , hogy az új ajánlást képviselje a/Hana/Data, és adja hozzá az I/O-ütemező beállítását.
- 02/21/2020: a HANA nagyméretű példányainak dokumentumaiban bekövetkezett változások a S224 és a S224m újonnan hitelesített SKU-ának felelnek meg
- 02/21/2020: az Azure-beli [virtuális gépek magas rendelkezésre állásának módosítása az SAP NETWEAVER RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) és Azure-beli [virtuális gépek magas rendelkezésre ÁLLÁSa a RHEL-hez Azure NetApp Files készült SAP NetWeaver számára a](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) sorba helyezni Server Replication 2 architektúra (ENSA2) architektúrájának módosításához
- 02/20/2020: az [SAP NetWeaver magas rendelkezésre állásának változása az Azure-beli virtuális gépeken a SLES multi-SID útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) a SUSE multi-SID-fürtre mutató hivatkozás hozzáadásához
- 02/13/2020: az [Azure Virtual Machines tervezésének és megvalósításának változásai az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) számára az új dokumentumokra mutató hivatkozások megvalósításához
- 02/13/2020: új dokumentum SAP számítási feladat hozzáadva [Az Azure-beli virtuális gépek támogatott forgatókönyvéhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: új dokumentum lett hozzáadva, [mely SAP szoftvereket támogatja az Azure-beli üzembe helyezés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: az [IBM DB2-LUW magas rendelkezésre állásának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) megváltozása Red Hat Enterprise Linux-kiszolgálón lévő Azure-beli virtuális gépeken, a nyilvános végponthoz való hozzáféréshez és a standard Azure Load Balancerhez való hozzáférést leíró dokumentumhoz
- 02/13/2020: adja hozzá az új virtuálisgép-típusokat az [Microsoft Azure-on futó SAP-tanúsítványokhoz és-konfigurációkhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) .
- 02/13/2020: új SAP-támogatás megjegyzései [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: az Azure-beli [virtuális gépek magas rendelkezésre állásának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) megváltozása a RHEL és az Azure-beli virtuális gépek magas rendelkezésre állása esetén az SAP [netweaver számára a Azure NetApp Files RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) -ben a fürt erőforrásainak időtúllépése a Red Hat időtúllépési javaslataihoz
- 02/11/2020: SAP HANA kiadása az [Azure-beli nagyméretű példányok Azure-ba való áttelepítésének Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: változás a [nyilvános végponti kapcsolaton a virtuális gépekhez az Azure standard ILB használatával SAP ha-forgatókönyvekben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) a minta NSG-képernyőkép frissítése
- 02/03/2020: a magas rendelkezésre állás megváltozása az SAP NW-ben Azure-beli [virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) az SAP-alkalmazások SLES és [magas rendelkezésre ÁLLÁSa SAP NW-hez az Azure-beli virtuális gépeken a SLES-ben a ANF for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) használatával a SLES-beli fürtcsomópontok állomásneve
- 01/28/2020: a [RHEL Azure-beli virtuális gépeken a SAP HANA magas rendelkezésre állásának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) megváltozása a SAP HANA-fürt erőforrásainak a Red Hat időtúllépési javaslataihoz való igazításához
- 01/17/2020: Váltás az [Azure Proximity-elhelyezési csoportjaiban az optimális hálózati késés érdekében SAP-alkalmazásokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) a meglévő virtuális gépek áthelyezésének szakasza
- 01/17/2020: változás az [SAP munkaterhelés-konfigurációkban Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) segítségével, hogy az eljárásra mutasson, amely automatizálja a Availability Zones közötti késés mértékét.
- 01/16/2020: a [SAP HANA (nagyméretű példányok) telepítésének és konfigurálásának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) módosítása az Azure-ban az operációsrendszer-kiadások HANA IaaS-hez való telepítéséhez
- 01/16/2020: az [SAP NetWeaver magas rendelkezésre állásának változásai az Azure-beli virtuális gépeken a SLES multi-SID útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) az SAP-rendszerekre vonatkozó utasítások hozzáadásához a sorba helyezni Server 2 Architecture (ENSA2) használatával
- 01/10/2020: a [SAP HANA az Azure-beli virtuális gépek készenléti csomópontján](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) belüli, a SLES-on Azure NetApp Files és az Azure-beli virtuális gépek készenléti csomópontján [SAP HANA kibővítve, Azure NetApp Files on RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) , hogy a rendszer útmutatást adjon a `nfs4_disable_idmapping` változások végleges végrehajtásához.
- 01/10/2020: az SAP [NetWeaver magas rendelkezésre állásának változásai az Azure-beli virtuális gépeken a SLES-on Azure NetApp Files for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) és az [Azure Virtual Machines magas rendelkezésre állású SAP NetWeaver on RHEL, SAP-alkalmazások esetén pedig az Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) a Azure NetApp Files nfsv4 névleképezője-kötetek csatlakoztatásához.
- 12/23/2019: [magas rendelkezésre állású SAP NetWeaver kiadása az Azure-beli virtuális gépeken a SLES multi-SID útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: az [Azure-beli virtuális gépek készenléti csomópontjaival SAP HANA kibővíthető, Azure NetApp Files a RHEL-on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: a [SAP HANA az Azure-beli virtuális gépek készenléti csomópontjaival, az SUSE Linux Enterprise Server-on Azure NetApp Filestal való méretezéssel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) EGYSZERŰSÍTHETI az NFS-azonosítók hozzárendelésének konfigurációját, és a javasolt elsődleges hálózati adaptert az Útválasztás egyszerűsítése érdekében módosítja.
- 11/15/2019: kisebb változások történtek a [magas rendelkezésre állásban az SAP NetWeaver esetében SUSE Linux Enterprise Server az SAP-alkalmazások Azure NetApp Files](high-availability-guide-suse-netapp-files.md) , az SAP [NetWeaver magas rendelkezésre állása pedig Red Hat Enterprise Linux a Azure NetApp Files SAP-alkalmazásokhoz](high-availability-guide-rhel-netapp-files.md) , a kapacitás-készlet méretére vonatkozó korlátozások tisztázása és a csak NFSv3-verziót támogató utasítás eltávolítása.
- 11/12/2019: az [SAP NetWeaver magas rendelkezésre állásának kiadása a windowsban Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: a [SAP HANA magas rendelkezésre állásának](sap-hana-high-availability.md)változása az Azure-beli [virtuális gépeken SUSE Linux Enterprise Servereken, SAP HANA rendszerreplikáció beállítása az Azure Virtual Machines szolgáltatásban (VM)](sap-hana-high-availability-rhel.md), az [Azure Virtual Machines magas rendelkezésre állású SAP NetWeaver SUSE Linux Enterprise Server SAP-alkalmazásokhoz](high-availability-guide-suse.md), [Azure Virtual Machines magas rendelkezésre állás az SAP netweaver](high-availability-guide-suse-netapp-files.md)on SUSE Linux Enterprise Server, Azure NETAPP Files, [Azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver on Red Hat Enterprise Linux ](high-availability-guide-rhel.md), Az [Azure Virtual Machines magas rendelkezésre állást biztosít az SAP netweaver számára Red Hat Enterprise Linux a Azure NetApp Fileson](high-availability-guide-rhel-netapp-files.md), a [magas rendelkezésre állást biztosítva az NFS](high-availability-guide-suse-nfs.md)-hez az Azure-beli virtuális gépeken, SUSE Linux Enterprise Server GlusterFS az Azure-beli [virtuális gépeken az SAP NetWeaver Red Hat Enterprise Linux](high-availability-guide-rhel-glusterfs.md) az Azure standard  
- 11/08/2019: az [SAP munkaterhelés tervezési és üzembe helyezési ellenőrzőlistájának](sap-deployment-checklist.md) változásai a titkosítási javaslatok tisztázásához  
- 11/04/2019: változások az [Azure-beli SUSE Linux Enterprise Server pacemaker beállításakor](high-availability-guide-suse-pacemaker.md) a fürt közvetlen, egyedi küldési konfigurációval való létrehozásához  
- 10/29/2019: a [Virtual Machines nyilvános végponti kapcsolatának kiadása az Azure standard Load Balancer az SAP magas rendelkezésre állási forgatókönyvekben való használatával](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: változások a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , és SAP HANA az Azure-beli virtuális gépek [készenléti csomópontjának kibővítésével az Azure NetApp Files on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) a/Hana/Shared-kötet NFS-protokolljának tisztázásához
- 10/22/2019: [magas rendelkezésre állású SAP NetWeaver használata az Azure-beli virtuális gépeken SUSE Linux Enterprise Server for SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [magas rendelkezésre állás az Azure-beli virtuális gépeken futó Azure-beli virtuális SUSE Linux Enterprise Server gépeken az SAP-alkalmazásokhoz Azure NetApp Files az SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)számára, [SUSE Linux Enterprise Server magas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)rendelkezésre állás az Azure-beli virtuális gépeken az Azure [-ban SUSE Linux Enterprise Server az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), az Azure-beli [virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)elérhető Az Azure-beli [virtuális gépeken futó SAP HANA magas rendelkezésre állása a SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) Azure Load-Balancer észlelésének megerősítéséhez
- A ANF szakasz és fejléc szakaszának módosítása [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: az [Azure-beli virtuális gépek készenléti csomópontjaival SAP HANA kibővíthető, Azure NetApp Files a SLES-on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: hibás hivatkozások javítása a [biztonsági mentés és visszaállítás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore) során
- 10/16/2019: módosítsa a minimális ajánlott operációs rendszert a SLES 12 SP3 verzióról a SLES 12 SP4-re az Azure-beli [virtuális gépeken, SUSE Linux Enterprise Server a pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) használatával
- 10/11/2019: a lemezes tárolás konfigurációjának változásai és a ANF bevezetése [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: változás az [Azure Proximity-elhelyezési csoportok grafikájában az optimális hálózati késés érdekében az SAP-alkalmazásokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) , hogy világosabb legyen
- 10/01/2019: változás az [Azure-ban SAP HANA infrastruktúra-konfigurációkban és-műveletekben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) a/Hana/Shared.-alapú, magasan elérhető NFS-megosztásra vonatkozó utasítások helyesbítéséhez 
- 09/28/2019: változás a [pacemaker beállítása Red Hat Enterprise Linux az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) a SBD tisztázására, mivel a kerítési mechanizmus nem támogatott a RHEL-fürtökön  
- 09/17/2019: változás a NetWeaver tervezési és telepítési útmutatóban az SAP virtuálisgép-bővítményének használatának egységesítéséhez  
- 08/22/2019: a [pacemaker beállítása a SUSE Linux Enterprise Server az Azure-ban beállítás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) változásai az egyéni szerepkör-létrehozás URL-címeinek frissítéséhez  
- 08/16/2019: változások a [pacemaker beállítása Red Hat Enterprise Linux az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) , hogy emlékeztesse az ügyfeleket az egyéni szerepkör műveleteinek frissítésére, ha az Azure kerítési ügynök új verziójára frissít.  
- 08/15/2019: a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) változásai az ultra Disk (korábban Ultra SSD) általánosan elérhetővé tételének tükrözéséhez
- 08/01/2019: változások az [Azure-beli SUSE Linux Enterprise Server pacemaker beállításához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) , hogy integrálják a módosításokat kifejezetten a 15. SLES 


