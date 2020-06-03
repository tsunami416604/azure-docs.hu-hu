---
title: Oracle-megoldások a Microsoft Azureon | Microsoft Docs
description: Ismerkedjen meg az Oracle-alkalmazások és-megoldások Microsoft Azureon történő üzembe helyezési lehetőségeivel, beleértve a teljes körű Azure-infrastruktúrán való futtatást vagy az Oracle Cloud Infrastructure (OCI) Felhőbeli kapcsolatait is.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: borisb
ms.openlocfilehash: d739f9f5c0e1be80005d5f3c6db5aa94ff6cc85d
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299842"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Az Azure-beli Oracle-alkalmazások és-megoldások áttekintése

Ez a cikk az Oracle-megoldások Azure-infrastruktúra használatával történő futtatásának lehetőségeit mutatja be. Tekintse meg a [Weblogic Server Azure-alkalmazások](oracle-weblogic.md), az Azure piactéren elérhető [Oracle VM-rendszerképek](oracle-vm-solutions.md) , valamint az [Azure és az Oracle felhőalapú infrastruktúra (OCI) közötti összekapcsolás](oracle-oci-overview.md)képességéről szóló témakört is.

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-adatbázisok az Azure-infrastruktúrában

Oracle-adatbázisok futtatása Azure-infrastruktúrán az Azure Marketplace-en elérhető Oracle Linux-lemezképek Oracle Database használatával:

* Oracle Database 12,1, 12,2 és 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 és 18,3 Standard Edition

* Oracle Database 19,3

Azt is megteheti, hogy az Azure-ban elérhető nem Oracle Linux rendszerképeken beállít egy olyan megoldást, amely a teljesen új Azure-ban létrehozott, vagy a helyszíni környezetből származó egyéni rendszerkép feltöltésével Oracle Database.

Opcionálisan konfigurálhat több csatlakoztatott lemezzel, és javíthatja az adatbázis teljesítményét az Oracle automatizált Storage Management (ASM) telepítésével.

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic-kiszolgáló és Azure-szolgáltatások integrációja

A felhőalapú utazás felgyorsításához számos WebLogic Server Azure-alkalmazás közül választhat.  Számos előre konfigurált Azure-szolgáltatás-integráció érhető el, beleértve az adatbázist, az Azure app Gatewayt és a Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Alkalmazások Oracle Linux-és WebLogic-kiszolgálón

Vállalati alkalmazások futtatása az Azure-ban támogatott Oracle operációs rendszereken. Az Azure Marketplace-en a következő virtuálisgép-rendszerképek érhetők el:

* Oracle WebLogic-kiszolgáló 12.1.2

* Oracle Linux a nem törhető Enterprise kernel (UEK) 6,8, 6,9, 6,10, 7,3, 7,7, 8,0, 8,1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Magas rendelkezésre állás és vész-helyreállítási lehetőségek

* Az [Oracle-adatvédelmet](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [, az](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) [aktív adatvédelmet az](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)Azure-infrastruktúra FSFO, horizontális Felskálázási vagy [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) szolgáltatásával együtt [Availability Zones](../../../availability-zones/az-overview.md) magas rendelkezésre állást biztosít a régióban. Ezeket a konfigurációkat több Azure-régióban is beállíthatja a rendelkezésre állás és a katasztrófa utáni helyreállítás érdekében.

* A [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) segítségével összehangolhatja és kezelheti az Azure-beli és a helyszíni vagy fizikai kiszolgálókon futó Oracle Linux virtuális gépek vész-helyreállítását. 

* Az Oracle Real Application Clusters (RAC) engedélyezése az Azure-ban az [Azure VMware megoldás](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) vagy a [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)használatával.

## <a name="backup-oracle-workloads"></a>Oracle számítási feladatok biztonsági mentése

* Oracle-alapú virtuális gépek biztonsági mentése [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) használatával

* Készítsen biztonsági másolatot a Oracle Database az Oracle Oláh Anna, és igény szerint az [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) használatával egy [nagyon redundáns Azure Blob Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-redundancy) csatlakoztathat, és további rugalmasságot is megadhat a Oláh Anna biztonsági mentéséhez.

## <a name="integration-of-azure-with-oci"></a>Az Azure és az OCI integrációja

Oracle-alkalmazások futtatása Azure-infrastruktúrában, háttér-adatbázisokhoz csatlakoztatva az Oracle Cloud Infrastructure (OCI) szolgáltatásban. Ez a megoldás a következő képességeket használja: 

* **Felhőbeli hálózatkezelés** – az Azure ExpressRoute és az Oracle FastConnect között elérhető közvetlen összekötő használatával nagy sávszélességű, privát és kis késleltetésű kapcsolat hozható létre az alkalmazás és az adatbázis réteg között.
* **Integrált identitás** – összevont identitást állíthat be az Azure ad és az Oracle IDCS között, hogy egyetlen identitási forrást hozzon létre a megoldásokhoz. Az egyszeri bejelentkezés lehetővé teszi az erőforrások kezelését a OCI és az Azure között.

### <a name="deploy-oracle-applications-on-azure"></a>Oracle-alkalmazások üzembe helyezése az Azure-ban

Az Azure-infrastruktúra beállításához és az Oracle-alkalmazások telepítéséhez használjon Terraform-sablonokat. 

Az Azure/Oracle Cloud Interconnect megoldás használata esetén a következő Oracle-alkalmazások hitelesítve vannak az Azure-ban való futtatásra: 2020

* E-Business csomag
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle kereskedelmi alkalmazások
* Oracle Hyperion pénzügyi felügyelet

Az Azure-ban olyan egyéni alkalmazásokat is üzembe helyezhet, amelyek a OCI és más Azure-szolgáltatásokhoz kapcsolódnak.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-adatbázisok beállítása a OCI-ben

Az Azure-ban futó Oracle-alkalmazásokkal együtt Oracle Database Cloud Services (autonóm adatbázis, RAC, Exadata, DBaaS, Single node) használható. További információ a [OCI adatbázis-beállításairól](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencek

Az Oracle-alkalmazások Azure-beli üzembe helyezése a "saját licenc használata" modellen alapul. Feltételezi, hogy Ön megfelelő licenccel rendelkezik az Oracle-szoftverek használatához, valamint arról, hogy az Oracle-vel meglévő támogatási szerződése van érvényben. Az Oracle garantálta a helyszíni Azure-ba történő licenc-mobilitást. Tekintse meg az Oracle-Azure [GYIK](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)-et.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [Weblogic Server Azure-alkalmazásokról](oracle-weblogic.md) és az általuk támogatott Azure-szolgáltatás-integrációról.

* További információ az [Oracle VM-rendszerképek](oracle-vm-solutions.md) Azure-infrastruktúrában történő üzembe helyezéséről.

* További információ az [Azure OCI-vel](oracle-oci-overview.md)való összekapcsolódásáról.

* Tekintse meg az Azure-beli [Oracle-áttekintést](https://myignite.techcommunity.microsoft.com/sessions/82915) az Ignite 2019-ről. 
