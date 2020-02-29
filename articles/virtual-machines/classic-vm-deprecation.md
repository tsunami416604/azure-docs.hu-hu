---
title: A klasszikus Azure-beli virtuális gépeket a 2023-es március 1-én felhasználjuk
description: A cikk magas szintű áttekintést nyújt a virtuális gépek kivonásáról
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 9f3fd59fa040ab46a5fc4ef8272a17cba4c631b8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925773"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>A IaaS-erőforrások migrálása a Azure Resource Managerra 2023. március 1-től 

2014-én a IaaS-t elindítottuk Azure Resource Manageron, és azóta is javultak a képességek. Mivel [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) mostantól teljes körű IaaS képességekkel és egyéb fejlesztésekkel rendelkezik, a IaaS-alapú virtuális gépek felügyeletét az Service Manager Azure-on keresztül, a 2020. február 28-án, és ez a funkció teljes mértékben megszűnik, 2023. március 1-jén. 

Napjainkban a IaaS virtuális gépek 90%-a Azure Resource Manager használ. Ha az Azure Service Manager (ASM) használatával IaaS-erőforrásokat használ, kezdje el megtervezni az áttelepítést, és fejezze be azt a 2023-as számú [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/)kihasználása érdekében.

A klasszikus virtuális gépek a [modern életciklus-szabályzatot](https://support.microsoft.com/help/30881/modern-lifecycle-policy) követik az elavultak esetében.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem? 

1) A 2020. február 28-án kezdődően a IaaS virtuális gépeket nem használó ügyfelek az Azure Service Manager (ASM) használatával nem tudják létrehozni 2020 a klasszikus virtuális gépeket. 
2) 2023. március 1-én az ügyfelek többé nem tudják elindítani a IaaS virtuális gépeket az Azure Service Manager használatával, és a továbbra is fut vagy lefoglalt, és fel lesz foglalva. 
2) 2023. március 1-jén az előfizetéseket, akiket nem telepítettek át Azure Resource Managerre, a többi klasszikus virtuális gép törlésére vonatkozó határidőkkel kapcsolatban tájékoztatni fogjuk.  

Ez a nyugdíjazás **nem** érinti a következő Azure-szolgáltatásokat és-funkciókat: 
- Felhőszolgáltatások 
- A klasszikus virtuális gépek által **nem** használt Storage-fiókok 
- A klasszikus virtuális gépek **nem** használják a virtuális hálózatokat (virtuális hálózatok). 
- Egyéb klasszikus erőforrások

## <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie? 

- Kezdje el megtervezni az áttelepítés megtervezését Azure Resource Managerre, ma. 

- [További](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) információ a klasszikus [Linux](./linux/migration-classic-resource-manager-plan.md) -és Windows- [alapú](./windows/migration-classic-resource-manager-plan.md) virtuális gépek Azure Resource Manager való áttelepítéséről.

- További információkért tekintse meg a [klasszikusról Azure Resource Manager áttelepítésre vonatkozó gyakori kérdéseket](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq) .

- Technikai kérdések és problémák esetén [forduljon az ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- A gyakori kérdések és visszajelzések nem részei a következő megjegyzésekben olvashatók.
