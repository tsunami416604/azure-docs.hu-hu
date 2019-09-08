---
title: Oracle-megoldások a Microsoft Azureon | Microsoft Docs
description: Ismerkedjen meg az Oracle-alkalmazások és-megoldások Microsoft Azureon történő üzembe helyezési lehetőségeivel, beleértve a teljes körű Azure-infrastruktúrán való futtatást vagy az Oracle Cloud Infrastructure (OCI) Felhőbeli kapcsolatait is.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: d8fed68d9b830df359f8129d55f1b9911f69e8f1
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802257"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Az Azure-beli Oracle-alkalmazások és-megoldások áttekintése

Ez a cikk az Oracle-megoldások Azure-infrastruktúra használatával történő futtatásának lehetőségeit mutatja be. Tekintse meg az Azure Marketplace-en elérhető [Oracle VM-rendszerképek](oracle-vm-solutions.md) részletes bevezetését, valamint az [Azure-t az Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md)segítségével.

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-adatbázisok az Azure-infrastruktúrában

Oracle-adatbázisok futtatása Azure-infrastruktúrán az Azure Marketplace-en elérhető Oracle Linux-lemezképek Oracle Database használatával:

* Oracle Database 12,1, 12,2 és 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 és 18,3 Standard Edition 

Azt is megteheti, hogy az Azure-ban elérhető nem Oracle Linux rendszerképeken beállít egy olyan megoldást, amely a teljesen új Azure-ban létrehozott, vagy a helyszíni környezetből származó egyéni rendszerkép feltöltésével Oracle Database.

Opcionálisan konfigurálhat több csatlakoztatott lemezzel, és javíthatja az adatbázis teljesítményét az Oracle automatizált Storage Management (ASM) telepítésével.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Alkalmazások Oracle Linux-és WebLogic-kiszolgálón

Vállalati alkalmazások futtatása az Azure-ban támogatott Oracle operációs rendszereken. Az Azure Marketplace-en az alábbi rendszerképek érhetők el:

* Oracle WebLogic-kiszolgáló 12.1.2

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 és 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Magas rendelkezésre állás és vész-helyreállítási lehetőségek

* Az [Oracle-adatvédelmet](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [, az](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) [aktív adatvédelmet az](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)Azure-infrastruktúra FSFO, horizontális Felskálázási vagy [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) szolgáltatásával együtt [Availability Zones](../../../availability-zones/az-overview.md) magas rendelkezésre állást biztosít a régióban. Ezeket a konfigurációkat több Azure-régióban is beállíthatja a rendelkezésre állás és a katasztrófa utáni helyreállítás érdekében.

* A [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) segítségével összehangolhatja és kezelheti az Azure-beli és a helyszíni vagy fizikai kiszolgálókon futó Oracle Linux virtuális gépek vész-helyreállítását. 

* Az Oracle Real Application Clusters (RAC) engedélyezése az Azure-ban az [Azure VMware megoldás](https://docs.azure.cloudsimple.com/oracle-rac/) vagy a [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)használatával.

## <a name="backup-oracle-workloads"></a>Oracle számítási feladatok biztonsági mentése

* Oracle-alapú virtuális gépek biztonsági mentése [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/backup-overview) használatával

* Készítsen biztonsági másolatot a Oracle Database Oracle Oláh Anna, és opcionálisan az [Azure Blob Fuse](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux) használatával csatlakoztathat egy nagyvállalati szintű [Azure Blob Storage-fiókot](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy) , és a Oláh Anna biztonsági másolatait is megírhatja a rugalmasság érdekében.

## <a name="integration-of-azure-with-oci-preview"></a>Az Azure integrálása a OCI (előzetes verzió)

Oracle-alkalmazások futtatása Azure-infrastruktúrában, háttér-adatbázisokhoz csatlakoztatva az Oracle Cloud Infrastructure (OCI) szolgáltatásban. Ez a megoldás a következő képességeket használja: 

* **Felhőbeli hálózatkezelés** – az Azure ExpressRoute és az Oracle FastConnect között elérhető közvetlen összekötő használatával nagy sávszélességű, privát és kis késleltetésű kapcsolat hozható létre az alkalmazás és az adatbázis réteg között.
* **Integrált identitás** – összevont identitást állíthat be az Azure ad és az Oracle IDCS között, hogy egyetlen identitási forrást hozzon létre a megoldásokhoz. Az egyszeri bejelentkezés lehetővé teszi az erőforrások kezelését a OCI és az Azure között.

### <a name="deploy-oracle-applications-on-azure"></a>Oracle-alkalmazások üzembe helyezése az Azure-ban

A Terraform-sablonok használatával beállíthatja az Azure-infrastruktúrát, és telepítheti a hitelesített és támogatott Oracle-alkalmazásokat:

* E-Business csomag
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle kereskedelmi alkalmazások
* Oracle Hyperion pénzügyi felügyelet

Az Azure-ban olyan egyéni alkalmazásokat is üzembe helyezhet, amelyek a OCI és más Azure-szolgáltatásokhoz kapcsolódnak.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-adatbázisok beállítása a OCI-ben

Az Azure-ban futó Oracle-alkalmazásokkal együtt Oracle Database Cloud Services (autonóm adatbázis, RAC, Exadata, DBaaS, Single node) használható. További információ a [OCI adatbázis-beállításairól](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencelés

Az Oracle-alkalmazások Azure-beli üzembe helyezése a "saját licenc használata" modellen alapul. Feltételezi, hogy Ön megfelelő licenccel rendelkezik az Oracle-szoftverek használatához, valamint arról, hogy az Oracle-vel meglévő támogatási szerződése van érvényben. Az Oracle garantálta a helyszíni Azure-ba történő licenc-mobilitást. Tekintse meg az Oracle-Azure [GYIK](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)-et.

## <a name="next-steps"></a>További lépések

* További információ az [Oracle VM-rendszerképek](oracle-vm-solutions.md) Azure-infrastruktúrában történő üzembe helyezéséről.

* További információ az [Azure OCI-vel](oracle-oci-overview.md)való összekapcsolódásáról.
