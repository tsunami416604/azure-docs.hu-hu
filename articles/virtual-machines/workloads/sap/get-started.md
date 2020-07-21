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
ms.date: 07/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 03d0367b3794069908b31ee7af1250fd70f1bdf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525211"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Az Azure használata az SAP munkaterhelés-forgatókönyvek üzemeltetéséhez és futtatásához

A Microsoft Azure használatakor megbízhatóan futtathatja a kritikus fontosságú SAP-munkaterheléseket és forgatókönyveket egy skálázható, megfelelő és nagyvállalati használatra kipróbált platformon. Az Azure méretezhetőségét, rugalmasságát és költségmegtakarítását is elérheti. A Microsoft és az SAP közötti kibővített partneri együttműködés révén az Azure-ban fejlesztési és tesztelési és éles környezetben is futtathat SAP-alkalmazásokat, és teljes mértékben támogatott. Az SAP NetWeavertól az SAP S/4HANA, a Linuxon futó SAP BI-tól a Windowshoz, és SAP HANA az SQL-hez.

Az SAP NetWeaver-forgatókönyvek az Azure-beli különböző adatbázis-kezelőn kívül más SAP-munkaterhelési forgatókönyveket is tartalmazhatnak, például az Azure-beli SAP BI-t. 

Az Azure SAP HANA-hoz való egyedisége egy olyan ajánlat, amely az Azure-t egymástól függetlenül állítja be. Ahhoz, hogy több memóriát és CPU-erőforrást igénylő SAP-forgatókönyveket lehessen üzemeltetni, amelyek SAP HANA, az Azure az ügyfél által dedikált operációs rendszer nélküli hardver használatát kínálja. Ezzel a megoldással olyan SAP HANA üzemelő példányokat futtathat, amelyek akár 24 TB (120 TB kibővített) memóriát igényelnek az S/4HANA vagy más SAP HANA számítási feladatokhoz. 

Az Azure SAP munkaterhelés-forgatókönyvek üzemeltetése az identitás-integrációra és az egyszeri bejelentkezésre vonatkozó követelményeket is képes létrehozni. Ez a helyzet akkor fordulhat elő, ha Azure Active Directory (Azure AD) használatával csatlakozik a különböző SAP-összetevőkhöz és az SAP-szolgáltatásokhoz (SaaS) vagy a szolgáltatásként kínált platformokhoz. Az Azure AD-val és az SAP-entitásokkal kapcsolatos ilyen integrációs és egyszeri bejelentkezési forgatókönyvek listáját a "HRE SAP Identity Integration és az egyszeri bejelentkezés" című szakaszban ismertetjük és dokumentáljuk.

## <a name="changes-to-the-sap-workload-section"></a>Az SAP munkaterhelés szakasz változásai
Az SAP on Azure munkaterhelés című szakaszban szereplő dokumentumok módosításai a cikk végén találhatók. A módosítási napló bejegyzései körülbelül 180 napig tartanak.

## <a name="you-want-to-know"></a>Tudni szeretné
Ha konkrét kérdései vannak, egy adott dokumentumra vagy folyamatra fogunk rámutatni, amely a Kezdőlap ezen szakaszában található. A következőket szeretné tudni:

- Az Azure-beli virtuális gépeket és a HANA nagyméretű példány-egységeket a SAP-szoftverek és az operációs rendszer verziószámai támogatják. Olvassa el az [Azure-beli üzembe helyezéshez támogatott SAP-szoftvereket](./sap-supported-product-on-azure.md) és az információk megkeresésének folyamatát ismertető dokumentumot.
- Milyen SAP-telepítési forgatókönyvek támogatottak az Azure-beli virtuális gépek és a HANA Large-példányok esetében. A támogatott forgatókönyvekkel kapcsolatos információk a következő dokumentumokban találhatók:
    - [SAP-tevékenységprofil az Azure-beli virtuális gépek támogatott forgatókönyveiben](./sap-planning-supported-configurations.md)
    - [A HANA nagyméretű példányának támogatott forgatókönyvei](./hana-supported-scenario.md)
- Az Azure-szolgáltatások, az Azure-beli virtuálisgép-típusok és az Azure Storage-szolgáltatások a különböző Azure-régiókban érhetők el, a helyhez tartozó [termékek pedig régiónként](https://azure.microsoft.com/global-infrastructure/services/) 
- Támogatott-e a harmadik féltől származó keret, HA a Windows és a pacemaker is működik? Tekintse meg az [SAP-támogatási Megjegyzés](https://launchpad.support.sap.com/#/notes/1928533) alsó részét #1928533

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA az Azure-ban (nagyméretű példányok)

A dokumentumok egy sorozata az Azure-ban (nagyméretű példányokon), vagy rövid, HANA nagyméretű példányokon SAP HANA. A HANA nagyméretű példányaival kapcsolatos információkért tekintse meg az [Azure-beli SAP HANA dokumentum-áttekintését és architektúráját (nagyméretű példányok)](./hana-overview-architecture.md) , és tekintse át a Hana Large instance című szakasz kapcsolódó dokumentációját.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA Azure-beli virtuális gépeken
A dokumentáció ezen szakasza a SAP HANA különböző szempontjait ismerteti. Előfeltételként ismernie kell az Azure legfontosabb szolgáltatásait, amelyek az Azure IaaS általános szolgáltatásait biztosítják. Ezért az Azure számítási, tárolási és hálózatkezelési ismereteire van szüksége. Ezeknek a témaköröknek a kezelése az SAP NetWeaver-hez kapcsolódó [Azure tervezési útmutatóban](./planning-guide.md)történik. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure-beli virtuális gépeken üzembe helyezett SAP NetWeaver
Ez a szakasz az SAP NetWeaver és az Azure-beli Business One tervezési és üzembe helyezési dokumentációját sorolja fel. A dokumentáció az Azure-beli SAP-munkaterheléssel rendelkező, nem HANA-adatbázisok alapjaira és használatára koncentrál. A magas rendelkezésre állást biztosító dokumentumok és cikkek a HANA magas rendelkezésre állásának alapja is az Azure-ban, például:

- Az [Azure tervezési útmutatója](./planning-guide.md). 
- [SAP Business One az Azure Virtual Machines szolgáltatásban](./business-one-azure.md)
- [Többplatformos SAP NetWeaver-alkalmazások telepítésének biztosítása Site Recovery használatával](../../../site-recovery/site-recovery-sap.md)
- [Az Azure SAP LaMa összekötője](./lama-installation.md)

Az Azure-beli SAP-munkaterhelések keretében nem HANA-adatbázisokkal kapcsolatos információkért lásd:

- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](./dbms_guide_general.md)
- [SQL Server Azure Virtual Machines adatbázis-kezelő rendszerbe állítás az SAP NetWeaver számára](./dbms_guide_sqlserver.md)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](./dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines adatbázis-kezelő üzembe helyezés az SAP-munkaterheléshez](./dbms_guide_ibm.md)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](./dbms_guide_sapase.md)
- [SAP-MaxDB, élő gyorsítótár és a Content Server üzembe helyezése Azure-beli virtuális gépeken](./dbms_guide_maxdb.md)

További információ az Azure-beli SAP HANA adatbázisokról: "SAP HANA az Azure Virtual Machines szolgáltatásban" című rész.

Az Azure-beli SAP-munkaterhelések magas rendelkezésre állásáról további információt a következő témakörben talál:

- [Azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver számára](./sap-high-availability-guide-start.md)

Ez a dokumentum különböző architektúra-és forgatókönyv-dokumentumokra mutat. A későbbi forgatókönyvek dokumentumaiban olyan részletes technikai dokumentumokra mutató hivatkozásokat talál, amelyek ismertetik a különböző magas rendelkezésre állású módszerek üzembe helyezését és konfigurálását. A különböző dokumentumok, amelyek bemutatják, hogyan hozhat létre és konfigurálhat magas rendelkezésre állást egy SAP NetWeaver-munkaterhelés számára a Linux és a Windows operációs rendszereken.


Az Azure Active Directory (Azure AD) és az SAP Services és az egyszeri bejelentkezés közötti integrációval kapcsolatos információkért lásd:

- [Oktatóanyag: Azure Active Directory SAP Cloud-integráció az ügyfelek számára](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP Cloud platform Identity Authentication szolgáltatással](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory-integráció az SAP NetWeaver megoldással](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory-integráció az SAP Business ByDesign megoldással](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Azure Active Directory integráció a SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Az S/4HANA-környezet: Fiori dob SAML egyszeri bejelentkezés az Azure AD-vel](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Az Azure-szolgáltatások SAP-összetevőkbe való integrálásával kapcsolatos információkért lásd:

- [Az SAP HANA használata a Power BI Desktopban](/power-bi/desktop-sap-hana)
- [DirectQuery és SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Az SAP BW Connector használata a Power BI Desktopban](/power-bi/desktop-sap-bw-connector) 
- [Az Azure Data Factory SAP HANA- és Business Warehouse-adatok integrációját nyújtja](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Módosítási napló

- 07/16/2020: leírja, hogyan telepítheti az SAP-hez készült új virtuálisgép-bővítményt az Azure PowerShell használatával a [telepítési útmutatóban](deployment-guide.md)
- 7/04/2020: az [Azure monitor for SAP Solutions (előzetes verzió)](./azure-monitor-overview.md) kiadása
- 07/01/2020: kevésbé költséges tárolási konfigurációra utal, amely az Azure Premium Storage burst funkcióján alapul, [SAP HANA Azure-beli virtuális gépek tárolási konfigurációinak](./hana-vm-operations-storage.md) dokumentálása 
- 06/24/2020: változás a [pacemaker beállítása a SLES az Azure-ban](./high-availability-guide-suse-pacemaker.md) az új továbbfejlesztett Azure kerítés-ügynök és az Azure kerítési ügynökön alapuló eszközök rugalmasabb STONITH-konfigurációjának kiadásához 
- 06/24/2020: változás a [pacemaker beállítása a RHEL az Azure-ban](./high-availability-guide-rhel-pacemaker.md) a rugalmasabb STONITH-konfiguráció felszabadításához
- 06/23/2020: az [azure Virtual Machines tervezésének és megvalósításának változásai az SAP NetWeaver](./planning-guide.md) útmutatóban és az [Azure Storage-típusok](./planning-guide-storage.md) bevezetése az SAP számítási feladatok útmutatójában
- 06/22/2020: telepítési lépések hozzáadása az SAP-hez készült új virtuálisgép-bővítményhez a [telepítési útmutatóban](deployment-guide.md)
- 06/16/2020: változás a [virtuális gépek nyilvános végponti kapcsolatán az Azure standard ILB SAP ha-forgatókönyvek használatával](./high-availability-guide-standard-load-balancer-outbound-connections.md) a SUSE nyilvános felhőalapú infrastruktúra 101 dokumentációjának hivatkozása 
- 06/10/2020: új HLI SKU-EK hozzáadása [elérhető SKU-HLI](./hana-available-skus.md) és [SAP HANA (nagyméretű példányok) tárolási architektúrája](./hana-storage-architecture.md)
- 05/21/2020: változás a [pacemaker beállítása a SLES az Azure](./high-availability-guide-suse-pacemaker.md) -ban és [a pacemaker beállítása az Azure-ban a RHEL-ben](./high-availability-guide-rhel-pacemaker.md) az Azure [standard ILB SAP if-forgatókönyvek használatával történő nyilvános végponti kapcsolathoz](./high-availability-guide-standard-load-balancer-outbound-connections.md) való hozzáadásához  
- 05/19/2020: fontos üzenet hozzáadása, hogy ne használja a gyökérszintű csoportot, ha az LVM-t használ a HANA-hez kapcsolódó kötetekhez [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](./hana-vm-operations-storage.md)
- 05/19/2020: új támogatott operációs rendszer hozzáadása a HANA nagyméretű példány Type II típusához a [kompatibilis, Hana nagyméretű példányokhoz](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 05/12/2020: változás a [virtuális gépek nyilvános végponti kapcsolataiban az Azure standard ILB SAP ha-forgatókönyvekben](./high-availability-guide-standard-load-balancer-outbound-connections.md) a hivatkozások frissítéséhez és a külső tűzfal konfigurálásához szükséges információk hozzáadásához
- 05/11/2020: a SLES-on futó Azure-beli [virtuális gépek SAP HANA magas rendelkezésre állásának](./sap-hana-high-availability.md) megváltozása a netcat-erőforráshoz tartozó erőforrás-stickiion beállításához 
- 05/05/2020: az [Azure Virtual Machines tervezésének és megvalósításának változásai az SAP NetWeaver](./planning-guide.md) használatával kifejezve, hogy Gen2 üzemelő példányok elérhetők a Mv1 VM-család számára
- 04/24/2020: SAP HANA az Azure-beli virtuális [gépek készenléti csomópontján](./sap-hana-scale-out-standby-netapp-files-suse.md)belüli SAP HANA, a SLES-on futó ANF változásai az Azure-beli virtuális gépek készenléti csomópontjaival, a [RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)-vel való ANF, az Azure-beli virtuális gépek [magas rendelkezésre állása](./high-availability-guide-suse-netapp-files.md) a SLES ANF és [magas rendelkezésre állással](./high-availability-guide-rhel-netapp-files.md)
- 04/22/2020: a SLES-on futó Azure-beli [virtuális gépek SAP HANA magas rendelkezésre állásának](./sap-hana-high-availability.md) megváltozása a meta attribútumnak `is-managed` az utasításokból való eltávolításához, ahogy az ütközik a fürtnek a karbantartási módba vagy kívülre helyezésével.
- 04/21/2020: a (z) SAP-(Hybris-) kereskedelmi platform 1811-es és újabb verzióiban elérhetővé tette SQL Azure DB-t az Azure-beli üzemelő [példányok](./sap-supported-product-on-azure.md) és az [SAP-tanúsítványok, valamint a Microsoft Azure](./sap-certifications.md)
- 04/16/2020: hozzáadva SAP HANA az SAP (Hybris) kereskedelmi platform támogatott adatbázis-kezelői szolgáltatásához, amely az Azure-környezetekben [támogatott SAP-szoftvereket](./sap-supported-product-on-azure.md) és az [Microsoft Azure-on futó SAP-tanúsítványokat és-konfigurációkat](./sap-certifications.md) támogatja.
- 04/13/2020: helyes az SAP bevezetési számának pontos kiosztása az SAP-beli [Azure Virtual Machines adatbázis-kezelő rendszerekben](./dbms_guide_sapase.md)
- 04/07/2020: változás a [pacemaker beállítása a SLES az Azure-ban](./high-availability-guide-suse-pacemaker.md) a Cloud-netconfig – Azure-utasítások
- 04/06/2020: a SAP HANA az Azure-beli [virtuális gépek készenléti csomópontján](./sap-hana-scale-out-standby-netapp-files-suse.md) belüli, SLES-alapú és SAP HANA kibővített Azure-beli virtuális gépekre vonatkozó, készenléti csomóponttal rendelkező Azure NetApp Files változásai a NetApp [TR-4435-](https://www.netapp.com/us/media/tr-4746.pdf) re mutató hivatkozások eltávolításához (a [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)- [Azure NetApp Files vel](./sap-hana-scale-out-standby-netapp-files-rhel.md) helyettesített)
- 03/31/2020: a [SAP HANA magas rendelkezésre állásának](./sap-hana-high-availability.md) megváltozása Azure-beli virtuális gépeken a SLES-on és a [magas rendelkezésre állású SAP HANA a RHEL](./sap-hana-high-availability-rhel.md) -on futó Azure-beli virtuális gépeken a csíkozott kötetek létrehozásakor
- 03/27/2020: az [SAP NW magas rendelkezésre állásának](./high-availability-guide-suse-netapp-files.md) változása az Azure-beli virtuális gépeken a SLES-ben a ANF for SAP-alkalmazások a fájlrendszer csatlakoztatási lehetőségeinek a NetApp TR-4746-hez (a szinkronizálási csatlakoztatási lehetőség eltávolítása)
- 03/26/2020: az [SAP NetWeaver magas rendelkezésre állásának változása az Azure-beli virtuális gépeken a SLES multi-SID útmutatóban](./high-availability-guide-suse-multi-sid.md) a NetApp TR-4746-hez való hivatkozás hozzáadásához
- 03/26/2020: az SAP [NetWeaver magas rendelkezésre állásának](./high-availability-guide-suse.md)megváltozása az Azure-beli virtuális gépeken az SLES for SAP-alkalmazásokhoz, [magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP netweaver-hez Azure NetApp Files SAP-alkalmazások](./high-availability-guide-suse-netapp-files.md)esetén, [magas rendelkezésre állás az NFS-hez az Azure-beli virtuális gépeken a SLES](./high-availability-guide-suse-nfs.md)-on, magas rendelkezésre állás [Az Azure-beli virtuális gépeken a SLES multi-SID útmutatójában,](./high-availability-guide-suse-multi-sid.md)magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára a RHEL for [SAP Applications és](./high-availability-guide-rhel.md) magas rendelkezésre [állású Azure](./high-availability-guide-rhel-netapp-files.md) -beli virtuális gépeken Azure Load Balancer Azure NetApp Files
- 03/19/2020: a dokumentum gyors változata [: egypéldányos SAP HANA manuális telepítése Azure-Virtual Machines](./hana-get-started.md) az Azure-beli [SAP HANA telepítéséhez Virtual Machines](./hana-get-started.md)
- 03/17/2020: változás a [pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](./high-availability-guide-suse-pacemaker.md) a már nem szükséges SBD-konfigurációs beállítás eltávolításához
- 03/16/2020: az Azure-beli üzemelő [példányok által támogatott SAP-szoftvereket](./sap-supported-product-on-azure.md) SAP HANA IaaS minősítésű platformon az oszlop tanúsítási forgatókönyvének pontosítása
- 03/11/2020: változás az [Azure-beli virtuális gépeken futó SAP-munkaterhelések](./sap-planning-supported-configurations.md) esetében, hogy az adatbázis-kezelő példányok támogatása több adatbázist is tisztázza
- 03/11/2020: változás az [Azure Virtual Machines az SAP NetWeaver tervezésében és megvalósításában az](./planning-guide.md) 1. és a 2. generációs virtuális gépekhez
- 03/10/2020: változás a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](./hana-vm-operations-storage.md) a ANF valós meglévő átviteli korlátinak tisztázásához
- 03/09/2020: az SAP [NetWeaver magas rendelkezésre állásának megváltozása az Azure-beli virtuális gépeken SUSE Linux Enterprise Server for](./high-availability-guide-suse.md)SAP-alkalmazások esetén, [magas rendelkezésre állás az Azure-beli virtuális SUSE Linux Enterprise Server gépeken futó SAP netweaver számára a Azure NetApp Files for SAP-alkalmazások](./high-availability-guide-suse-netapp-files.md)esetében, [magas rendelkezésre állás az NFS-en a SUSE Linux Enterprise Server Azure-beli virtuális gépeken](./high-availability-guide-suse-nfs.md), a [pacemaker beállítása az Azure](./high-availability-guide-suse-pacemaker.md)-beli SUSE Linux Enterprise Server számára, magas rendelkezésre állás az Azure-beli virtuális [gépeken SUSE Linux Enterprise Server a pacemakerrel](./dbms-guide-ha-ibm.md), magas rendelkezésre állás az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server [-](./sap-hana-high-availability.md) on és magas rendelkezésre állású Azure-beli virtuális gépeken az Azure-beli virtuális gépeken a SLES [multi-SID útmutatóban](./high-availability-guide-suse-multi-sid.md) SAP HANA 
- 03/05/2020: az Azure-régiók és Azure-beli virtuális gépek szerkezetének változásai és tartalmi változásai az Azure-ban [Virtual Machines az SAP NetWeaver tervezése és megvalósítása](./planning-guide.md)
- 03/03/2020: az [SAP NW magas rendelkezésre állásának megváltozása Azure-beli virtuális gépeken a SLES-ben a ANF for SAP-alkalmazások](./high-availability-guide-suse-netapp-files.md) a hatékonyabb ANF mennyiségi elrendezésre való váltáshoz
- 03/01/2020: átdolgozott [biztonsági mentési útmutató az Azure Virtual Machines SAP HANAához](./sap-hana-backup-guide.md) Azure Backup szolgáltatás belefoglalásához. Csökkenthető és tömörített tartalom a [SAP HANA Azure Backup a fájl szintjén](./sap-hana-backup-file-level.md) , és törölte a biztonsági mentéssel foglalkozó harmadik dokumentumot a lemez pillanatképének használatával. A tartalom az Azure-beli SAP HANA biztonsági mentési útmutatójában lesz kezelve Virtual Machines 
- Február 27., 2020: magas rendelkezésre állás megváltozása az SAP NW-ben Azure-beli [virtuális gépeken a SLES SAP-alkalmazásokhoz](./high-availability-guide-suse.md), magas rendelkezésre állás az Azure-beli virtuális gépeken elérhető SAP NW-hez a [ANF for SAP Applications](./high-availability-guide-suse-netapp-files.md) és [magas rendelkezésre állású SAP NetWeaver Azure-beli virtuális gépeken a SLES multi-SID útmutató](./high-availability-guide-suse-multi-sid.md) a "sikertelen" fürt
- Február 26., 2020: változás a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](./hana-vm-operations-storage.md) az Azure-beli HANA-beli fájlrendszer-beállítások tisztázásához
- Február 26., 2020: változás a [magas rendelkezésre állású architektúrában és az SAP-forgatókönyvekben](./sap-high-availability-architecture-scenarios.md) , hogy tartalmazza az Azure-beli virtuális gépeken elérhető SAP NetWeaver-ra mutató HIVATKOZÁST a RHEL multi-SID útmutatóban
- Február 26., 2020: magas rendelkezésre állás megváltozása az SAP NW-ben Azure-beli [virtuális gépeken az SLES for SAP-alkalmazásokhoz](./high-availability-guide-suse.md), [magas rendelkezésre állás az SAP NW számára az Azure-beli virtuális GÉPEKen a SLES-ben a ANF for SAP Applications](./high-availability-guide-suse-netapp-files.md), az [Azure virtuális gépek magas rendelkezésre állása a RHEL](./high-availability-guide-rhel.md) és az azure-beli [virtuális gépek magas Azure NetApp Files rendelkezésre](./high-availability-guide-rhel-netapp-files.md) állásáról
- Február 26., 2020: az [SAP NetWeaver magas rendelkezésre állásának kiadása az Azure-beli virtuális gépeken a RHEL multi-SID útmutatóban](./high-availability-guide-rhel-multi-sid.md) a SUSE multi-SID-fürtre mutató hivatkozás hozzáadásához
- 02/25/2020: változás a [magas rendelkezésre állású architektúrában és az SAP-forgatókönyvekben](./sap-high-availability-architecture-scenarios.md) az újabb ha-cikkekre mutató hivatkozások hozzáadásához
- Február 25.2020: az [IBM DB2-LUW magas rendelkezésre állásának megváltozása az Azure-beli virtuális gépeken SUSE Linux Enterprise Server és a pacemaker](./dbms-guide-ha-ibm.md) használatával olyan dokumentumra mutat, amely leírja a nyilvános végponthoz való hozzáférést a standard Azure Load balancerrel
- Február 21., 2020: a SAP-beli [Azure Virtual Machines adatbázis-kezelő rendszer üzembe helyezése SAP-munkaterheléshez](./dbms_guide_sapase.md) című cikk teljes felülvizsgálata
- Február 21., 2020: változás a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](./hana-vm-operations-storage.md) , hogy az új ajánlásokat képviselje a/Hana/Data és az I/O-ütemező beállításának hozzáadásakor.
- 2020. február 21.: a HANA nagyméretű példányainak dokumentumaiban történt változások a S224 és a S224m újonnan hitelesített SKU-ának felelnek meg
- Február 21., 2020: az [Azure-beli virtuális gépek magas rendelkezésre állásának](./high-availability-guide-rhel.md) megváltozása a RHEL és az Azure-beli virtuális gépek magas rendelkezésre állása az [SAP netweaver számára a Azure NetApp Files RHEL](./high-availability-guide-rhel-netapp-files.md) -ben a sorba helyezni-kiszolgáló replikációs 2 architektúrája (ENSA2) számára
- Február 20., 2020: az [SAP NetWeaver magas rendelkezésre állásának változása az Azure-beli virtuális gépeken a SLES multi-SID útmutatóban](./high-availability-guide-suse-multi-sid.md) a SUSE multi-SID-fürtre mutató hivatkozás hozzáadásához
- Február 13., 2020: az [Azure Virtual Machines tervezésének és megvalósításának változásai az SAP NetWeaver](./planning-guide.md) -hoz az új dokumentumokra mutató hivatkozások megvalósításához
- Február 13., 2020: új dokumentum SAP-számítási feladatának hozzáadása [Az Azure-beli virtuális gépek támogatott forgatókönyvében](./sap-planning-supported-configurations.md)
- Február 13., 2020: új dokumentum, amely az Azure-beli [üzembe helyezéshez támogatott SAP-szoftvert támogatja](./sap-supported-product-on-azure.md)
- Február 13., 2020: az [IBM DB2-LUW magas rendelkezésre állásának változása Red Hat Enterprise Linux-kiszolgálón lévő Azure-beli virtuális gépeken](./high-availability-guide-rhel-ibm-db2-luw.md) a nyilvános végponthoz a standard Azure Load Balancerhez való hozzáférést ismertető dokumentumra mutatva
- Február 13., 2020: az új virtuálisgép-típusok hozzáadása az [SAP-tanúsítványokhoz és a Microsoft Azureon futó konfigurációkhoz](./sap-certifications.md)
- Február 13., 2020: új SAP-támogatás megjegyzései [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](./sap-deployment-checklist.md)
- Február 13., 2020: az [Azure-beli virtuális gépek magas rendelkezésre állásának](./high-availability-guide-rhel.md) változása a RHEL és az Azure-beli virtuális gépek magas rendelkezésre állása az [SAP netweaver számára a Azure NetApp Files RHEL](./high-availability-guide-rhel-netapp-files.md) -ben a fürt erőforrásainak a Red Hat időtúllépési javaslataihoz való igazításához
- Február 11.2020: az Azure [-beli nagyméretű példányok Azure-ba való migrálása SAP HANA kiadása Virtual Machines](./hana-large-instance-virtual-machine-migration.md)
- Február 07., 2020: változás [nyilvános végponti kapcsolaton keresztül a virtuális gépekhez az Azure standard ILB SAP ha-forgatókönyvek használatával](./high-availability-guide-standard-load-balancer-outbound-connections.md) a minta NSG frissítése képernyőkép
- Február 03.2020: az SAP [NW magas rendelkezésre állásának változása az Azure-beli virtuális gépeken a SLES for SAP Applications](./high-availability-guide-suse.md) és a [magas rendelkezésre állás az SAP NW-ben Azure-beli virtuális gépeken a SLES-ben a ANF for SAP Applications](./high-availability-guide-suse-netapp-files.md) használatával a SLES-beli fürtcsomópontok állomásneve
- 2020. január 28.: az [Azure-beli virtuális gépeken SAP HANA magas rendelkezésre állásának](./sap-hana-high-availability-rhel.md) megváltozása a RHEL a SAP HANA-fürt erőforrásainak a Red Hat időtúllépési javaslataihoz való igazításához
- Január 17., 2020: változás az [Azure közelségi elhelyezési csoportjaiban az optimális hálózati késés érdekében SAP-alkalmazásokkal](./sap-proximity-placement-scenarios.md) , hogy megváltoztassa a meglévő virtuális gépek földrajzi elhelyezési csoportba való áthelyezésének szakaszát.
- Január 17., 2020: az [SAP-munkaterhelés konfigurációjának](./sap-ha-availability-zones.md) változása Azure Availability Zones használatával, amely automatizálja a Availability Zones közötti késés mértékét.
- Január 16., 2020: a [SAP HANA (nagyméretű példányok) telepítésének és konfigurálásának](./hana-installation.md) módosítása az Azure-ban az operációsrendszer-kiadások HANA IaaS-hez való telepítéséhez
- Január 16., 2020: az [SAP NetWeaver magas rendelkezésre állásának változásai az Azure-beli virtuális gépeken a SLES multi-SID útmutatóban](./high-availability-guide-suse-multi-sid.md) az SAP-rendszerekre vonatkozó utasítások hozzáadásához a sorba helyezni Server 2 Architecture használatával (ENSA2)
- 2020. január 10.: az [Azure-beli Azure NetApp Files virtuális gépek készenléti csomópontján](./sap-hana-scale-out-standby-netapp-files-suse.md) belüli, a SLES-on és a SAP HANA kibővített Azure [Azure NetApp Files-](./sap-hana-scale-out-standby-netapp-files-rhel.md) beli virtuális gépek készenléti csomóponttal való kiskálázása SAP HANA a módosítások végleges végrehajtásával kapcsolatos utasítások hozzáadásához `nfs4_disable_idmapping` .
- 2020. január 10.: a [SLES Azure-beli virtuális gépeken az SAP NetWeaver magas rendelkezésre állásának változásai a Azure NetApp Files for SAP Applications](./high-availability-guide-suse-netapp-files.md) és az [Azure Virtual Machines magas rendelkezésre állású SAP NetWeaver on RHEL, SAP-alkalmazások esetén pedig az Azure NetApp Files for SAP applications](./high-availability-guide-rhel-netapp-files.md) a Azure NetApp Files nfsv4 névleképezője-kötetek csatlakoztatására vonatkozó utasítások hozzáadásához.
- 2019. december 23.: az [Azure-beli virtuális gépeken futó SAP NetWeaver magas rendelkezésre állásának kiadása a SLES multi-SID útmutatójában](./high-availability-guide-suse-multi-sid.md)
- December 18., 2019: az [Azure-beli virtuális gépek készenléti csomópontjaival SAP HANA kibővíthető a Azure NetApp Files on RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- November 21., 2019: az [Azure-beli virtuális Azure NetApp Files gépek készenléti csomópontjának SAP HANA-méretezésének](./sap-hana-scale-out-standby-netapp-files-suse.md) változásai a SUSE Linux Enterprise Server használatával egyszerűbbé teszik az NFS-azonosítók hozzárendelésének konfigurációját, és a javasolt elsődleges hálózati adaptert az Útválasztás egyszerűsítése érdekében módosítják.
- November 15., 2019: kisebb változások történtek a [magas rendelkezésre állásban az SAP NetWeaver esetében SUSE Linux Enterprise Server Azure NetApp Files az](high-availability-guide-suse-netapp-files.md) SAP-alkalmazások és a [magas rendelkezésre állás az sap netweaver esetében Red Hat Enterprise Linux Azure NetApp Files SAP-alkalmazások](high-availability-guide-rhel-netapp-files.md) esetében, hogy tisztázza a kapacitási készlet méretére vonatkozó korlátozásokat, és eltávolítsa az utasítást, hogy csak a NFSv3 verziója támogatott.
- November 12.2019: az [SAP NetWeaver magas rendelkezésre állásának kiadása a Windows rendszeren Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- November 8., 2019: a [SAP HANA magas rendelkezésre állásának változásai a SUSE Linux Enterprise Server Azure](sap-hana-high-availability.md)-beli [virtuális gépeken, SAP HANA rendszerreplikáció beállítása az Azure Virtual Machines szolgáltatásban (VM)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines magas rendelkezésre állás for SAP NetWeaver on SUSE Linux Enterprise Server for sap Applications](high-availability-guide-suse.md), [Azure Virtual Machines magas rendelkezésre állás for SAP netweaver on SUSE Linux Enterprise Server with Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver on Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Azure Virtual Machines magas rendelkezésre állású](high-availability-guide-rhel-netapp-files.md)SAP NetWeaver on Red Hat Enterprise Linux Azure NetApp Files, magas rendelkezésre állás az NFS-en az Azure-beli [virtuális gépeken](high-availability-guide-suse-nfs.md)a SUSE Linux Enterprise Server-on, GlusterFS az Azure-beli [virtuális gépeken](high-availability-guide-rhel-glusterfs.md)  
- November 8., 2019: változások az [SAP munkaterhelés tervezési és üzembe helyezési ellenőrzőlista](sap-deployment-checklist.md) a titkosítási javaslatok tisztázásához  
- November 4., 2019: a [pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](high-availability-guide-suse-pacemaker.md) a fürt közvetlen, egyedi küldési konfigurációval való létrehozásához  
