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
ms.date: 10/21/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b857ea5380fb13f8c8b1ff30b9dd9d8b6c723c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809675"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Az Azure használata az SAP munkaterhelés-forgatókönyvek üzemeltetéséhez és futtatásához

A Microsoft Azure használatakor megbízhatóan futtathatja a kritikus fontosságú SAP-munkaterheléseket és forgatókönyveket egy skálázható, megfelelő és nagyvállalati használatra kipróbált platformon. Az Azure méretezhetőségét, rugalmasságát és költségmegtakarítását is elérheti. A Microsoft és az SAP közötti kibővített partneri együttműködés révén az Azure-ban fejlesztési és tesztelési és éles környezetben is futtathat SAP-alkalmazásokat, és teljes mértékben támogatott. Az SAP NetWeavertól az SAP S/4HANA, a Linuxon futó SAP BI-tól a Windowshoz, és SAP HANA az SQL-hez.

Az SAP NetWeaver-forgatókönyvek az Azure-beli különböző adatbázis-kezelőn kívül más SAP-munkaterhelési forgatókönyveket is tartalmazhatnak, például az Azure-beli SAP BI-t. 

Az Azure SAP HANA-hoz való egyedisége egy olyan ajánlat, amely az Azure-t egymástól függetlenül állítja be. Ahhoz, hogy több memóriát és CPU-erőforrást igénylő SAP-forgatókönyveket lehessen üzemeltetni, amelyek SAP HANA, az Azure az ügyfél által dedikált operációs rendszer nélküli hardver használatát kínálja. Ezzel a megoldással olyan SAP HANA üzemelő példányokat futtathat, amelyek akár 24 TB (120 – TB kibővített) memóriát igényelnek az S/4HANA vagy más SAP HANA számítási feladatokhoz. 

Az Azure SAP munkaterhelés-forgatókönyvek üzemeltetése az identitás-integrációra és az egyszeri bejelentkezésre vonatkozó követelményeket is képes létrehozni. Ez a helyzet akkor fordulhat elő, ha Azure Active Directory (Azure AD) használatával csatlakozik a különböző SAP-összetevőkhöz és az SAP-szolgáltatásokhoz (SaaS) vagy a szolgáltatásként kínált platformokhoz. Az Azure AD-val és az SAP-entitásokkal kapcsolatos ilyen integrációs és egyszeri bejelentkezési forgatókönyvek listáját a "HRE SAP Identity Integration és az egyszeri bejelentkezés" című szakaszban ismertetjük és dokumentáljuk.

## <a name="changes-to-the-sap-workload-section"></a>Az SAP munkaterhelés szakasz változásai
Az SAP on Azure munkaterhelés című szakaszban szereplő dokumentumok módosításai a cikk végén találhatók.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA az Azure-ban (nagyméretű példányok)

A dokumentumok egy sorozata az Azure-ban (nagyméretű példányokon), vagy rövid, HANA nagyméretű példányokon SAP HANA. A HANA Large instances következő területeivel kapcsolatos információkért lásd:

- [Az Azure-beli SAP HANA áttekintése (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [A SAP HANA architektúrája az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktúra és kapcsolódás az Azure-SAP HANAhoz (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [SAP HANA telepítése az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [A SAP HANA magas rendelkezésre állása és vész-helyreállítása az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Az Azure-beli SAP HANA hibakeresése és figyelése (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Következő lépések:

- [A SAP HANA áttekintése és architektúrája az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA Azure-beli virtuális gépeken
A dokumentáció ezen szakasza a SAP HANA különböző szempontjait ismerteti. Előfeltételként ismernie kell az Azure legfontosabb szolgáltatásait, amelyek az Azure IaaS általános szolgáltatásait biztosítják. Ezért az Azure számítási, tárolási és hálózatkezelési ismereteire van szüksége. Ezeknek a témaköröknek a kezelése az SAP NetWeaver-hez kapcsolódó [Azure tervezési útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)történik. 

Az Azure-beli HANA-ról további információt a következő cikkekben és alcikkeiben talál:

- [Gyors útmutató: egypéldányos SAP HANA manuális telepítése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP S/4HANA vagy BW/4HANA üzembe helyezése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA magas rendelkezésre állás az Azure Virtual Machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA rendelkezésre állás egy Azure-régión belül](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA rendelkezésre állás Azure-régiók között](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Biztonsági mentési útmutató Azure-beli virtuális gépek SAP HANAához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup a fájl szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA biztonsági mentés tárolási Pillanatképek alapján](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure-beli virtuális gépeken üzembe helyezett SAP NetWeaver
Ez a szakasz az SAP NetWeaver és az Azure-beli Business One tervezési és üzembe helyezési dokumentációját sorolja fel. A dokumentáció az Azure-beli SAP-munkaterheléssel rendelkező, nem HANA-adatbázisok alapjaira és használatára koncentrál. A magas rendelkezésre állást biztosító dokumentumok és cikkek a HANA magas rendelkezésre állásának alapja is az Azure-ban, például:

- [SAP Business One az Azure Virtual Machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [SAP ide EHP7 SP3 telepítése SAP ERP 6,0-hez az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Az SAP NetWeaver futtatása Microsoft Azure SUSE Linux rendszerű virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
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


## <a name="change-log"></a>Módosítási napló
- 10/22/2019: az SAP [NetWeaver magas rendelkezésre állásának megváltozása az Azure-beli virtuális gépeken SUSE Linux Enterprise Server for SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)esetében, [magas rendelkezésre állás az Azure-beli virtuális SUSE Linux Enterprise Server gépeken futó SAP NetWeaver számára az SAP-alkalmazásokhoz Azure NetApp Files ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Magas rendelkezésre állás a SUSE Linux Enterprise Server Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), a [pacemaker beállítása az Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)-beli SUSE Linux Enterprise Server számára, [magas rendelkezésre állás az Azure-beli virtuális GÉPEKen elérhető IBM DB2-LUW, SUSE Linux Enterprise Server a pacemakerrel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) Az Azure-beli [virtuális gépeken futó SAP HANA magas rendelkezésre állása a SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) Azure Load-Balancer észlelésének megerősítéséhez
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
- 07/23/2019: változások a [fürtben egy SAP ASCS/SCS-példány egy Windows feladatátvevő fürtön egy Azure-beli fájlmegosztás használatával](sap-high-availability-guide-wsfc-file-share.md) , amely a közvetlen tárolóhelyek Azure site Recovery szolgáltatások általi támogatását tükrözi
- 07/14/2019: [Azure Proximity-elhelyezési csoportok kiadása az optimális hálózati késés érdekében SAP-alkalmazásokkal](sap-proximity-placement-scenarios.md)
- 07/11/2019: a HANA nagy példányokra kiterjedő különböző dokumentumok változásai a HANA nagy példányok 4. változatának fedezésére
- 07/09/2019: az IBM DB2 HADR új útmutatójának kiadása a [Red Hat Enterprise Serveren](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: az [SAP NetWeaver magas rendelkezésre állásának kiadása Red Hat Enterprise Linux Azure NETAPP Files SAP-alkalmazásokhoz](high-availability-guide-rhel-netapp-files.md)


