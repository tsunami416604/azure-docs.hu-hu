---
title: 2023. március 1-jén nyugdíjba vonulunk az Azure Classic virtuális gépektől
description: Cikk nyújt magas szintű áttekintést a klasszikus virtuális gép nyugdíjba vonulás
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668803"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023. március 1-ig áttelepítheti IaaS-erőforrásait az Azure Resource Managerbe 

2014-ben elindítottuk az IaaS-t az Azure Resource Manageren, és azóta is fejlesztjük a képességeket. Mivel [az Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) most már teljes IaaS-képességekkel és egyéb fejlesztésekkel rendelkezik, 2020. 

Ma az IaaS virtuális gépek mintegy 90%-a használja az Azure Resource Managert. Ha az Azure Service Manageren (ASM) keresztül használja [az](https://docs.microsoft.com/azure/azure-resource-manager/management/)IaaS-erőforrásokat, kezdje el megtervezni az áttelepítést most, és 2023.

A klasszikus virtuális gépek a [modern életciklus-szabályzatot](https://support.microsoft.com/help/30881/modern-lifecycle-policy) követik a megszüntetéshez.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem? 

1) 2020. február 28-tól azok az ügyfelek, akik 2020 februárjában nem használták az IaaS-virtuális gépeket az Azure Service Manageren (ASM) keresztül, a továbbiakban nem hozhatnak létre klasszikus virtuális gépeket. 
2) 2023. március 1-jén az ügyfelek már nem indíthatják el az IaaS-virtuális gépeket az Azure Service Manager használatával, és a még futó vagy lefoglalt szolgáltatások leállnak és feloldhatók. 
2) 2023. március 1-jén az Azure Resource Managerbe nem áttelepített előfizetések tájékoztatást kapnak a fennmaradó klasszikus virtuális gépek törlésének idővonalairól.  

Ez a megszüntetés **nem** érinti a következő Azure-szolgáltatásokat és funkciókat: 
- Cloud Services 
- A klasszikus virtuális gépek **által nem** használt tárfiókok 
- A klasszikus virtuális gépek **által nem** használt virtuális hálózatok (Virtuális hálózatok). 
- Egyéb klasszikus források

## <a name="what-actions-should-i-take"></a>Milyen lépéseket kell tetsbennem? 

- Még ma megtervezheti az Azure Resource Managerbe való migrálást. 

- [További információ](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) a klasszikus [Linux-](./linux/migration-classic-resource-manager-plan.md) és [Windows-virtuális](./windows/migration-classic-resource-manager-plan.md) gépek Azure Resource Managerbe való áttelepítéséről.

- További információkért tekintse meg az [Azure Resource Manager áttelepítésének klasszikusával kapcsolatos gyakori kérdéseivel kapcsolatos gyakori kérdéseket.](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Technikai kérdések, problémák és előfizetésengedélyezési [kapcsolat felvételével forduljon az ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Egyéb kérdések nem része a GYIK és a visszajelzés, megjegyzést alább.
