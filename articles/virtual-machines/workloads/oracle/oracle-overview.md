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
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100025"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Az Azure-beli Oracle-alkalmazások és-megoldások áttekintése

Ez a cikk az Oracle-megoldások Azure-infrastruktúra használatával történő futtatásának lehetőségeit mutatja be. Tekintse meg az Azure Marketplace-en elérhető [Oracle VM](oracle-vm-solutions.md) -rendszerképek részletes bevezetését, valamint az [Azure-t az Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md)segítségével.

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-adatbázisok az Azure-infrastruktúrában

Oracle-adatbázisok futtatása az Azure-infrastruktúrában az Azure Marketplace-en elérhető Linux-rendszerképek használatával:

* Oracle Database 12,1, 12,2 és 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 és 18,3 Standard Edition 

Azt is megteheti, hogy a megoldás alapja az Azure-ban létrehozott egyéni rendszerkép, vagy egy egyéni rendszerkép feltöltése a helyszíni környezetből.

Opcionálisan konfigurálhat több csatlakoztatott lemezzel, és javíthatja az adatbázis teljesítményét az Oracle automatizált Storage Management (ASM) telepítésével.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Alkalmazások Oracle Linux-és WebLogic-kiszolgálón

Vállalati alkalmazások futtatása az Azure-ban támogatott Oracle operációs rendszereken. Az Azure Marketplace-en az alábbi rendszerképek érhetők el:

* Oracle WebLogic-kiszolgáló 12.1.2

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 és 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Magas rendelkezésre állás és vész-helyreállítási lehetőségek

* Az Oracle-adatvédelmet, az aktív adatvédelmet vagy az Azure-infrastruktúra GoldenGate az [Availability Zones](../../../availability-zones/az-overview.md) a magas rendelkezésre állás érdekében.

* A [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) segítségével összehangolhatja és kezelheti az Azure-beli és a helyszíni vagy fizikai kiszolgálókon futó Oracle Linux virtuális gépek vész-helyreállítását. 

* Az Oracle Real Application Clusters (RAC) engedélyezése az Azure-ban a [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)használatával.

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

Az Azure-ban futó Oracle-alkalmazásokkal együtt Oracle Database Cloud Services (autonóm adatbázis, RAC, Exadata, DBaaS, Single node) használható. További információ a [OCI adatbázis](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)-beállításairól. 
 

## <a name="licensing"></a>Licencelés

Az Oracle-alkalmazások Azure-beli üzembe helyezése a "saját licenc használata" modellen alapul. Feltételezi, hogy Ön megfelelő licenccel rendelkezik az Oracle-szoftverek használatához, valamint arról, hogy az Oracle-vel meglévő támogatási szerződése van érvényben. Az Oracle garantálta a helyszíni Azure-ba történő licenc-mobilitást. Tekintse meg az Oracle-Azure [GYIK](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)-et.

## <a name="next-steps"></a>További lépések

* További információ az [Oracle VM](oracle-vm-solutions.md) -rendszerképek Azure-infrastruktúrában történő üzembe helyezéséről.

* További információ az [Azure OCI-vel](oracle-oci-overview.md)való összekapcsolódásáról.