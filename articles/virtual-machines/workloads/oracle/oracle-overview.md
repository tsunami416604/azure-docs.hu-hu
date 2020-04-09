---
title: Oracle megoldások a Microsoft Azure-on | Microsoft dokumentumok
description: Ismerje meg az Oracle Applications és megoldások Microsoft Azure-on történő telepítésének lehetőségeit, beleértve a teljes körű Azure-infrastruktúrán való futtatást vagy a felhőközi kapcsolatot az Oracle Cloud Infrastructure (OCI) szolgáltatással.
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
ms.date: 03/16/2020
ms.author: rogirdh
ms.openlocfilehash: 101cfc9edce8f4df1ad2388c08a5bd9702dffe68
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878221"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Az Oracle alkalmazások és megoldások áttekintése az Azure-ban

Ez a cikk bemutatja az Oracle-megoldások Azure-infrastruktúrát használó futtatásának lehetőségeit. Tekintse meg az Azure Piactéren elérhető [Oracle virtuálisgép-lemezképek](oracle-vm-solutions.md) részletes bemutatkozását, valamint az Azure és az [Oracle Cloud Infrastructure (OCI) összekapcsolására való képességet.](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-adatbázisok az Azure-infrastruktúrán

Oracle-adatbázisok futtatása az Azure-infrastruktúrán az Oracle Database on Oracle Linux-lemezképek használatával, amelyek az Azure Marketplace-en érhetők el:

* Oracle Database 12.1, 12.2 és 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 és 18.3 Standard Edition 

Dönthet úgy is, hogy az Oracle Database-t az Azure-ban elérhető, nem Oracle Linux-lemezképre állítja, és a megoldást egy olyan egyéni lemezképre alapozza, amelyet az Azure-ban a semmiből hoz létre, vagy egyéni lemezképet tölt fel a helyszíni környezetből.

Az Oracle Automated Storage Management (ASM) telepítésével konfigurálhatja a több csatlakoztatott lemezt, és javíthatja az adatbázis teljesítményét.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Alkalmazások Oracle Linux és WebLogic Server rendszeren

Nagyvállalati alkalmazások futtatása az Azure-ban a támogatott Oracle operációs rendszereken. A következő képek érhetők el az Azure Marketplace-en:

* Oracle WebLogic Server 12.1.2

* Oracle Linux a Törhetetlen Enterprise Kernel (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 és 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Magas rendelkezésre állás és vészhelyreállítási lehetőségek

* Konfigurálja [az Oracle Data Guard,](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [Active Data Guard with FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) vagy [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) az Azure-infrastruktúrát a [rendelkezésre állási zónákkal](../../../availability-zones/az-overview.md) együtt a régióban rendelkezésre álló magas rendelkezésre állás érdekében. Ezeket a konfigurációkat több Azure-régióban is beállíthatja a további rendelkezésre állás és a vészhelyreállítás érdekében.

* Az [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) használatával az Azure-beli Oracle Linux-beli virtuális gépek vészutáni helyreállítását vezényelheti és kezelheti. 

* Engedélyezze az Oracle Real Application Clusters (RAC) szolgáltatást az Azure-ban az [Azure VMWare-megoldásvagy](https://docs.azure.cloudsimple.com/oracle-rac/) a [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)használatával.

## <a name="backup-oracle-workloads"></a>Oracle-munkaterhelések biztonsági mentése

* Készítsen biztonsági másolatot oracle virtuális gépeiről az [Azure Backup használatával](https://docs.microsoft.com/azure/backup/backup-overview)

* Készítsen biztonsági másolatot oracle-adatbázisairól az Oracle RMAN használatával, és az [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) használatával csatlakoztatjon egy [erősen redudant Azure Blob Storage-fiókot,](https://docs.microsoft.com/azure/storage/common/storage-redundancy) és írja meg rman biztonsági másolatait a további rugalmasság érdekében.

## <a name="integration-of-azure-with-oci"></a>Az Azure integrációja az OCI-vel

Oracle-alkalmazások futtatása az Azure-infrastruktúrában, háttér-adatbázisokhoz csatlakoztatva az Oracle Cloud Infrastructure (OCI) alkalmazásban. Ez a megoldás a következő képességeket használja: 

* **Felhőközi hálózat –** Az Azure ExpressRoute és az Oracle FastConnect között elérhető közvetlen kapcsolattal nagy sávszélességű, privát és alacsony késésű kapcsolatokat létesíthet az alkalmazás és az adatbázisréteg között.
* **Integrált identitás** – Az Azure AD és az Oracle IDCS közötti összevont identitás beállítása a megoldások egyetlen identitásforrásának létrehozásához. Egyszeri bejelentkezés engedélyezése az erőforrások oci és az Azure-ban való kezeléséhez.

### <a name="deploy-oracle-applications-on-azure"></a>Oracle-alkalmazások telepítése az Azure-ban

Terraform-sablonokkal állíthatja be az Azure-infrastruktúrát, és telepítheti az Oracle Applications alkalmazást. 

> [!IMPORTANT]
> Az Oracle tanúsítja, hogy ezek az alkalmazások 2020 májusig futnak az Azure/Oracle Cloud interconnect megoldás használatával.

* E-Business lakosztály
* JD Edwards Vállalat
* Peoplesoft
* Oracle kiskereskedelmi alkalmazások
* Oracle Hyperion pénzügyi menedzsment

Egyéni alkalmazásokat is üzembe helyezhet az Azure-ban, amelyek OCI-vel és más Azure-szolgáltatásokkal kapcsolódnak.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-adatbázisok beállítása oci-ben

Az Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) és az Azure-ban futó Oracle-alkalmazások használata. További információ az [OCI adatbázis beállításairól](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencek

Az Oracle Applications azure-beli telepítése a "saját licenccel" modellen alapul. Feltételezhető, hogy ön megfelelő licenccel rendelkezik az Oracle szoftver használatára, és hogy jelenleg támogatási megállapodás van érvényben az Oracle-lel. Az Oracle garantált licenchordozhatósággal rendelkezik a helyszíni azure-ból. Tekintse meg az Oracle-Azure [gyakori kérdéseket.](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="next-steps"></a>További lépések

* További információ az [Oracle Virtuálisgép-lemezképek](oracle-vm-solutions.md) Azure-infrastruktúrában való üzembe helyezéséről.

* További információ az Azure és az [OCI összekapcsolásáról.](oracle-oci-overview.md)

* Tekintse meg az [Oracle on Azure áttekintő munkamenetét](https://myignite.techcommunity.microsoft.com/sessions/82915) az Ignite 2019-ből. 