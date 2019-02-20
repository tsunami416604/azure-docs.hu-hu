---
title: Ismerkedés az Azure virtuális gépeken SAP |} A Microsoft Docs
description: Virtuális gépek (VM) a Microsoft Azure-ban futó SAP-megoldások tudnivalók
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
ms.date: 02/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acdd2f5cb345ca5c5462394ae5c6135ad43b2143
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415705"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Az Azure használatához és az SAP munkaterhelés-forgatókönyvek

A Microsoft Azure kiválasztásával Ön megbízhatóan futtathatja az üzletmenet szempontjából kritikus fontosságú SAP számítási feladatok és a forgatókönyvek egy skálázható, kompatibilis és vállalati környezetekben kipróbált platformon.  Használja ki az Azure kínálta skálázhatóságot, rugalmasságot és költségtakarékosságot. Között a Microsoft és az SAP kiterjesztett partneri futtatható SAP-alkalmazások az Azure - fejlesztési-tesztelési és éles környezetekben, és mindehhez teljes körű támogatást. Az SAP Netweavertől az SAP S/4HANA, SAP bi-ban, Linux, Windows, az SAP HANA, SQL, az kell, hogy kielégítünk.

Amellett, hogy a különböző DBMS Azure-beli SAP NetWeaver forgatókönyvekre üzemelteti, különböző üzemeltethető más SAP munkaterhelés-forgatókönyvek, például az SAP bi-ban az Azure-ban. 

Az egyedi-e az SAP Hana Azure-ajánlat, amely Azure verseny szereplőkkel. Ahhoz, hogy több memóriát és CPU-erőforrás erőforrás-igényes forgatókönyveket az SAP az SAP HANA, az Azure üzemeltetési az ügyfél-használatát, dedikált operációs rendszer nélküli hardver futó SAP HANA üzemelő példánya, amely akár 24 TB (120 TB horizontális felskálázás) memória szükséges céljából S/4HANA vagy egyéb SAP HANA számítási feladatot. SAP Hana az Azure-ban (nagyméretű példányok) az egyedi Azure-megoldás lehetővé teszi az alkalmazásrétegre SAP vagy a számítási feladatok szoftverek középső réteg natív Azure Virtual Machines szolgáltatásban üzemeltetett dedikált operációs rendszer nélküli hardveren SAP HANA futtatása. Ez a megoldás leírása itt található több dokumentumot a következő szakaszban: "Az SAP HANA az Azure-ban (nagyméretű példányok)."   

SAP munkaterhelés-forgatókönyvek az Azure-beli futtató is hozhat létre identitásintegráció és egyszeri bejelentkezés használata az Azure Active Directory különböző SAP-összetevők és az SAP SaaS követelményeinek vagy PaaS kínál. Az ilyen integrációt és az Azure Active Directory (AAD) és az SAP entitások Single-Sign-On forgatókönyvek listája leírt, és a szakaszban leírt "AAD SAP-Identitásintegráció és egyszeri bejelentkezést."

## <a name="latest-changes"></a>Legutóbbi módosítások

Kiadása [SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák](sap-ha-availability-zones.md)

Kiadása [SAP számítási feladatok tervezési és telepítési ellenőrzőlista](sap-deployment-checklist.md)

Használati és konfigurációja pontosítása [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) a késés kritikus fontosságú kommunikáció elérési útjaiban az SAP-rendszerek:

- [Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)



## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA az SAP HANA az Azure-ban (nagyméretű példányok)

Dokumentáció sorozat végigvezeti, az SAP HANA az Azure-ban (nagyméretű példányok), vagy a rövid nagyméretű HANA-példányokhoz. A dokumentumok a felsorolt területek HANA nagyméretű példányok terjed ki:

- [Az SAP HANA az Azure-ban (nagyméretű példányok) áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Az SAP HANA az Azure-ban (nagyméretű példányok) architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktúra és kapcsolódás az Azure-ban (nagyméretű példányok) SAP Hana-hoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Az SAP HANA az Azure-ban (nagyméretű példányok) SAP HANA telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Magas rendelkezésre állású és vész-helyreállítási SAP Hana az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Hibaelhárítási és figyelési SAP Hana az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Következő lépések:

- Olvasási [áttekintése és architektúrája az SAP Hana az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA az Azure-beli virtuális gépeken
A dokumentáció ezen szakasza az SAP Hana különböző szempontokat ismerteti. Egy előfeltétel az egyszerű szolgáltatások Azure az Azure IaaS, így elsősorban az Azure számítási, tárolási és hálózatkezelési ismerete alapvető szolgáltatásokat nyújtó tisztában kell lennie. Ezek a témakörök számos kezeli, az SAP NetWeaver kapcsolódó [Azure tervezési útmutatója](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Az Azure-beli HANA adott dokumentációjában cikkek és azok subarticles listája áll:

- [Rövid útmutató: Egypéldányos SAP HANA az Azure virtuális gépek manuális telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP S/4HANA vagy BW/4hana-t az Azure-ban üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA rendelkezésre állási egy Azure-régióban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA rendelkezésre állása az Azure-régiók](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure virtuális gépeken futó SAP Hana magas rendelkezésre állás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Biztonsági mentési útmutató az SAP Hana az Azure Virtual machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Az SAP HANA az Azure Backup a fájlok szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Tárolási pillanatképeken alapuló SAP HANA biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver üzembe helyezve az Azure Virtual machines szolgáltatásban
Ebben a szakaszban az SAP NetWeaver és az Azure-ban egy üzleti tervezési és központi telepítési dokumentációjában találja. Ebben a fejezetben a dokumentáció az Azure többnyire az alapokat, és az SAP számítási feladatok nem HANA-adatbázisok használata körül összpontosít. Mivel a dokumentumok és a magas rendelkezésre ÁLLÁSÚ cikkeket a HANA magas rendelkezésre állás az Azure-ban, valamint alapját. a cikkek listája, például:

- [SAP Business One az Azure Virtual Machinesban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [SAP IDES EHP7 SP3 telepítése a SAP ERP 6.0 az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [A Microsoft Azure SUSE Linux rendszerű virtuális gépeken futó SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Egy többrétegű SAP NetWeaver alkalmazás központi telepítésének védeni a Site Recovery használatával](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Az Azure SAP LaMa összekötője](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

SAP számítási feladatok azure-ban nem HANA adatbázisokat kapcsolatban, például a dokumentumok listája:

- [Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver az SQL Server Azure virtuális gépek DBMS üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, liveCache és a webtartalom-kiszolgáló üzembe helyezés az Azure virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

SAP HANA-adatbázisok az Azure-ban ellenőrizze a szakasz az SAP HANA az Azure Virtual machines szolgáltatásban.

Magas rendelkezésre állás az SAP számítási feladatok Azure-ban a bejegyzés dokumentum van:

- [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

A bejegyzés dokumentum különböző architektúra és a forgatókönyv más dokumentumok mutat. Az utólagos forgatókönyv-dokumentumok elmagyarázza, telepítését és konfigurálását a másik magas rendelkezésre állású módszerek részletes műszaki dokumentáció mutató hivatkozások állnak rendelkezésre. A különböző dokumentumokat létrehozó és magas rendelkezésre állás az SAP NetWeaver számítási feladatok konfigurálása Linux rendszerű, valamint a Windows operációs rendszerek takarja el.


Az Azure Active Directory (AAD) és az SAP-szolgáltatások közötti integráció és Single-Sign-On, dokumentumokat például listázása:

- [Oktatóanyag: Az ügyfél az Azure Active Directory-integráció az SAP-felhővel](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Cloud platformon](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP netweaver megoldással](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Oktatóanyag: Az Azure Active Directory-integráció az SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Az S/4HANA környezet – Fiori Kezdőpanele SAML egyszeri bejelentkezés az Azure ad-vel](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

A dokumentumok listája az SAP-összetevők integrálása az Azure-szolgáltatások hasonlóan néz ki:

- [Az SAP HANA használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery és SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Az SAP BW Connector használata a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Az Azure Data Factory SAP HANA- és Business Warehouse-adatok integrációját nyújtja](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




