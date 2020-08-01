---
title: A klasszikus Azure-beli virtuális gépeket a 2023-es március 1-én felhasználjuk
description: A cikk magas szintű áttekintést nyújt a virtuális gépek kivonásáról
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461204"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>A IaaS-erőforrások migrálása a Azure Resource Managerra 2023. március 1-től 

2014-én a IaaS-t elindítottuk Azure Resource Manageron, és azóta is javultak a képességek. Mivel [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) mostantól teljes körű IaaS képességekkel és egyéb fejlesztésekkel rendelkezik, a IaaS-alapú virtuális gépek felügyeletét az Service Manager Azure-on keresztül, a 2020. február 28-án, és ez a funkció teljes mértékben megszűnik, 2023. március 1-jén. 

Napjainkban a IaaS virtuális gépek 90%-a Azure Resource Manager használ. Ha az Azure Service Manager (ASM) használatával IaaS-erőforrásokat használ, kezdje el megtervezni az áttelepítést, és fejezze be azt a 2023-as számú [Azure Resource Manager](../azure-resource-manager/management/index.yml)kihasználása érdekében.

A klasszikus virtuális gépek a [modern életciklus-szabályzatot](https://support.microsoft.com/help/30881/modern-lifecycle-policy) követik a nyugdíjazáshoz.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem? 

1) A 2020. február 28-án kezdődően a IaaS virtuális gépeket nem használó ügyfelek az Azure Service Manager (ASM) használatával nem tudják létrehozni 2020 a klasszikus virtuális gépeket. 
2) 2023. március 1-én az ügyfelek többé nem tudják elindítani a IaaS virtuális gépeket az Azure Service Manager használatával, és a továbbra is fut vagy lefoglalt, és fel lesz foglalva. 
2) 2023. március 1-jén az előfizetéseket, akiket nem telepítettek át Azure Resource Managerre, a többi klasszikus virtuális gép törlésére vonatkozó határidőkkel kapcsolatban tájékoztatni fogjuk.  

Ez a nyugdíjazás **nem** érinti a következő Azure-szolgáltatásokat és-funkciókat: 
- Cloud Services 
- A klasszikus virtuális gépek által **nem** használt Storage-fiókok 
- A klasszikus virtuális gépek **nem** használják a virtuális hálózatokat (virtuális hálózatok). 
- Egyéb klasszikus erőforrások

## <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie? 

- Kezdje el megtervezni az áttelepítés megtervezését Azure Resource Managerre, ma. 

- [További](./windows/migration-classic-resource-manager-overview.md) információ a klasszikus [Linux](./linux/migration-classic-resource-manager-plan.md) -és Windows- [alapú](./windows/migration-classic-resource-manager-plan.md) virtuális gépek Azure Resource Manager való áttelepítéséről.

- További információkért tekintse meg a [klasszikusról Azure Resource Manager áttelepítésre vonatkozó gyakori kérdéseket](./windows/migration-classic-resource-manager-faq.md) .

- Technikai kérdések, problémák és előfizetések hozzáadása az engedélyezési listához [forduljon az ügyfélszolgálathoz](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Az áttelepítés során segítségért [forduljon az áttelepítés támogatásához](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}) .

- A gyakori kérdések és visszajelzések nem részei a következő megjegyzésekben olvashatók.
