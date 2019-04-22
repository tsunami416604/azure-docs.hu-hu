---
title: Ismerkedés az Azure virtuális gépeken SAP |} A Microsoft Docs
description: Ismerje meg a virtuális gépek (VM) a Microsoft Azure-ban futó SAP-megoldások
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698509"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Az Azure-üzemeltetése és futtatása SAP munkaterhelés-forgatókönyvek

A Microsoft Azure használatakor megbízhatóan futtathatja az üzleti szempontból alapvető fontosságú SAP számítási feladatok és forgatókönyvek egy skálázható, kompatibilis és vállalati környezetekben kipróbált platformon. Get, a skálázhatóságot, rugalmasságot és megtakarítás az Azure. Között a Microsoft és az SAP kiterjesztett partneri futtatható SAP-alkalmazások az Azure-beli fejlesztési és tesztelési és éles környezetekben, és mindehhez teljes körű támogatást. Az SAP Netweavertől az SAP S/4HANA, SAP BI, a Windows, Linux- és SAP HANA, SQL, állunk jelez.

SAP NetWeaver-forgatókönyvek az Azure-ban a különböző DBMS-üzemeltetési mellett egyéb SAP munkaterhelés-forgatókönyvek, például az SAP bi-ban az Azure-ban is üzemeltethet. 

Az egyedi-e az SAP Hana Azure-ajánlat, amely az Azure közötti. Ahhoz, hogy több memóriát és CPU erőforrás-erőforrás-igényes SAP forgatókönyvekről, az SAP HANA üzemeltető, az Azure kínál operációs rendszer nélküli ügyfél dedikált hardver használata. Használja ezt a megoldást az SAP HANA üzemelő példánya, amely akár 24 TB (120 TB-os horizontális felskálázás) memóriát igényelnek az S/4HANA vagy egyéb SAP HANA számítási feladatok futtatásához. 

SAP munkaterhelés-forgatókönyvek az Azure-beli futtató hozhat létre identitásintegráció és egyszeri bejelentkezés követelményeinek. Ez a helyzet akkor fordulhat elő, gyorsjavításainak SAP és SAP-t-a szoftverszolgáltatások (SaaS) vagy a platform--szolgáltatásként (PaaS) ajánlatok az Azure Active Directory (Azure AD) használatakor. Az ilyen integrációt és az egyszeri bejelentkezéses forgatókönyvek használatához Azure AD-vel listáját és az SAP-entitások leírt, és részletes ismertetését lásd: a szakasz "AAD SAP-identitásintegráció és egyszeri bejelentkezést."

## <a name="latest-changes"></a>Legutóbbi módosítások

- Kiadása [Azure HANA nagyméretű példányok szabályozhatja az Azure Portalon keresztül](hana-li-portal.md)

- Kiadása [magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure NetApp Files SAP alkalmazások az Azure virtuális gépeken](high-availability-guide-suse-netapp-files.md)

- A pontosítása **Linux operációs rendszer paraméter net.ipv4.tcp_timestamps** beállítások együtt egy Azure-terheléselosztó

- Kiadása [SAP számítási feladatok konfigurációk az Azure rendelkezésre állási zónák](sap-ha-availability-zones.md)

- Kiadása [SAP számítási feladatok tervezési és telepítési ellenőrzőlista](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA az Azure-ban (nagyméretű példányok)

A dokumentumok egy sorozat végigvezeti az SAP HANA az Azure-ban (nagyméretű példányok), vagy röviden, nagyméretű HANA-példányokhoz. A HANA nagyméretű példányok az alábbi területeken információkért lásd:

- [Az SAP HANA az Azure-ban (nagyméretű példányok) áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Az SAP HANA az Azure-ban (nagyméretű példányok) architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktúra és kapcsolódás az Azure-ban (nagyméretű példányok) SAP Hana-hoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Az SAP HANA telepítése az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Az Azure-ban (nagyméretű példányok) SAP HANA magas rendelkezésre állás és vészhelyreállítás helyreállítási](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Hibaelhárítás és figyelés SAP HANA az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Következő lépések:

- Olvasási [áttekintése és architektúrája az SAP HANA az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA Azure-beli virtuális gépeken
A dokumentáció ezen szakasza az SAP Hana különböző szempontokat ismerteti. Egy előfeltétel az egyszerű szolgáltatások Azure az Azure IaaS alapvető szolgáltatásokat nyújtó tisztában kell lennie. Ezért létre kell az Azure számítási, tárolási és hálózatkezelési ismerete. Az e számos kezeli az SAP NetWeaver kapcsolatos [Azure kapacitástervezési útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Az Azure-beli HANA információkért lásd: a következő cikkeket, és azok subarticles:

- [Rövid útmutató: Egypéldányos SAP HANA az Azure virtuális gépek manuális telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP S/4HANA vagy BW/4hana-t az Azure-ban üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA rendelkezésre állási egy Azure-régióban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA rendelkezésre állása az Azure-régiók](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure virtuális gépeken futó SAP Hana magas rendelkezésre állás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Az SAP HANA az Azure Backup a fájlok szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Tárolási pillanatképeken alapuló SAP HANA biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver üzembe helyezett Azure-beli virtuális gépeken
Ez a szakasz felsorolja az SAP NetWeaver és az Azure-ban egy üzleti tervezési és telepítési dokumentációját. A dokumentáció összpontosít az alapokat, és nem a HANA-adatbázisok használata az Azure-ban az SAP számítási feladatok. A dokumentumok és a magas rendelkezésre állású cikkeket is is alapját HANA magas rendelkezésre állás az Azure-ban, például:

- [SAP Business One Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [SAP IDES EHP7 SP3 telepítése a SAP ERP 6.0 az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [SAP NetWeaver futtatása Microsoft Azure SUSE Linux rendszerű virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Egy többrétegű SAP NetWeaver alkalmazás központi telepítésének védeni a Site Recovery használatával](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Az Azure SAP LaMa összekötője](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Az SAP számítási feladatok Azure-ban nem HANA adatbázisokat kapcsolatos tudnivalókat lásd:

- [Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver az SQL Server Azure virtuális gépek DBMS üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure virtuális gépeken SAP MaxDB, az élő gyorsítótárat és a webtartalom-kiszolgáló üzembe helyezés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Információ az SAP HANA-adatbázisok az Azure-ban című témakör "Az SAP HANA Azure-beli virtuális gépeken."

Információk a magas rendelkezésre állás az SAP számítási feladatok az Azure-ban::

- [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Ez a dokumentum különböző architektúra és a forgatókönyv más dokumentumok mutat. Újabb forgatókönyv-dokumentumokban mutató részletes műszaki dokumentáció, amely leírja, telepítését és konfigurálását a másik magas rendelkezésre állású módszerek állnak rendelkezésre. A különböző dokumentumokon, amelyek bemutatják a létrehozásához, és magas rendelkezésre állás az SAP NetWeaver számítási feladat konfigurálása a Linux és Windows operációs rendszerek terjed ki.


Azure Active Directory (Azure AD) és az SAP-szolgáltatások és egyszeri bejelentkezés között-integrációval kapcsolatos információkért lásd:

- [Oktatóanyag: Az ügyfél az Azure Active Directory-integráció az SAP-felhővel](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Cloud platformon](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP netweaver megoldással](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Az S/4HANA-környezetben: Fiori Kezdőpanele SAML egyszeri bejelentkezés az Azure ad-vel](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Az SAP-összetevők integrálása az Azure-szolgáltatások további információkért lásd:

- [Az SAP HANA használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery és SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Az SAP BW Connector használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Az Azure Data Factory SAP HANA- és Business Warehouse-adatok integrációját nyújtja](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




