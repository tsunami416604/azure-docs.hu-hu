---
title: A Microsoft Azure-ban Oracle-megoldások |} A Microsoft Docs
description: Oracle-alkalmazások és megoldások a Microsoft Azure, beleértve a teljes egészében az Azure-infrastruktúrán és a több felhőre kiterjedő kapcsolat Oracle Felhőbeli infrastruktúrát (OCI) lehetőségek ismertetése.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707450"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Oracle-alkalmazásokhoz és az Azure-megoldások áttekintése

Ez a cikk az Azure-infrastruktúra használata Oracle-megoldások futtatása képességek jelennek meg. Lásd még a rendelkezésre álló részletes bemutatása [Oracle Virtuálisgép-rendszerképek](oracle-vm-solutions.md) az Azure piactéren, és az előzetes verzió képességét [Azure Oracle Felhőbeli infrastruktúrát (OCI) követelő](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-adatbázisok az Azure-infrastruktúra

Oracle-adatbázisok futtathatók az Azure Marketplace-en elérhető Linux-rendszerképek használata az Azure infrastruktúra:

* Oracle Database 12.1 12.2 és 18.3 Enterprise Edition 

* Oracle Database 12.1 12.2 és 18.3 Standard Edition 

Azt is beállíthatja a megoldás alapjául egy egyéni rendszerkép létrehozása az Azure-ban, vagy egy egyéni Rendszerkép feltöltése a helyszíni környezetből.

Igény szerint több csatlakoztatott adatlemezekkel rendelkező konfigurálása és adatbázis-teljesítmény javítása Oracle automatikus Storage szolgáltatásfelügyelet (ASM) telepítésével.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux-és WebLogic Server alkalmazásokat

Nagyvállalati alkalmazások futtatása az Azure-ban támogatott Oracle operációs rendszereken. Az alábbi lemezképek az Azure Marketplace-en érhetők el:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 és 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Magas rendelkezésre állás és vészhelyreállítás helyreállítási lehetőségek

* Oracle Data Guard, az aktív Data Guard és a GoldenGate konfigurálása az Azure-infrastruktúrán együtt [rendelkezésre állási zónák](../../../availability-zones/az-overview.md) magas rendelkezésre állás érdekében.

* Használat [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) szervezését és kezelését a vész-helyreállítási az Oracle Linux rendszerű virtuális gépek Azure-ban és a helyszíni vagy fizikai kiszolgálók. 

* Az Azure-ban Oracle valós alkalmazás fürtök (RAC) engedélyezése [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integráció az Azure-ról OCI (előzetes verzió)

Oracle-alkalmazásokat futtathat az Azure-infrastruktúra, csatlakozik a háttérbeli adatbázisokhoz Oracle Felhőbeli infrastruktúrát (OCI). Ez a megoldás a következő funkciókat használja: 

* **Felhők közötti kapcsolódás** – használja a közvetlen ezek között az Azure ExpressRoute- és Oracle FastConnect nagy sávszélességű, a magán- és a közel valós idejű, az alkalmazás és az Adatbázisréteg közötti kapcsolatokat.
* **Identitás integrált** -között az Azure AD összevont identitás beállításához és az Oracle IDCS olyan egyetlen identitás forrás megoldások létrehozásához. Engedélyezze az egyszeri bejelentkezési OCI és az Azure-erőforrások kezeléséhez.

### <a name="deploy-oracle-applications-on-azure"></a>Az Azure-ban Oracle-alkalmazások üzembe helyezése

Terraform-sablonok használatával Azure-infrastruktúra beállítása és az Oracle-alkalmazások telepítése érvényesítve, és támogatja a több felhőre kiterjedő konfiguráció futtatása:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Kiskereskedelmi Oracle-alkalmazásokhoz
* Oracle Hyperion pénzügyi adatok kezelésére

A OCI és más Azure-szolgáltatásokhoz csatlakozó Azure-ban egyéni alkalmazásokat is telepíthet.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-adatbázisok az OCI beállítása

Oracle Database Cloud Services (önálló adatbázis, jogosultságifiók-tanúsítványok, Exadata, DBaaS, egycsomópontos) együttes használata az Azure-ban futtatott Oracle-alkalmazások. Tudjon meg többet [OCI adatbázis-beállítások](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencek

Az Azure-ban Oracle-alkalmazások központi telepítéséhez a "saját licenc használata" modell alapján történik. Feltételezzük, hogy meg vannak megfelelően-licenccel rendelkező Oracle-szoftverek és, hogy már működik a az Oracle egy jelenlegi támogatási szerződés. Oracle garantálta licenchordozhatósági program a helyszínről az Azure-bA. Tekintse meg az Azure-beli Oracle [– gyakori kérdések](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>További lépések

* További információ a központi telepítésével kapcsolatos [Oracle Virtuálisgép-rendszerképek](oracle-vm-solutions.md) az Azure-infrastruktúra.

* Ismerje meg, hogyan [összekapcsolható az Azure-ban OCI](oracle-oci-overview.md).