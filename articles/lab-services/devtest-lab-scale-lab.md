---
title: Kvóták és korlátok méretezése a laborban Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan méretezheti a labort Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 231a227cbccaa4cbf74f3b13839c0ba42895cf24
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644851"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Kvóták és korlátozások méretezése a DevTest Labs szolgáltatásban
A DevTest Labs szolgáltatásban végzett munka során észreveheti, hogy egyes Azure-erőforrások esetében bizonyos alapértelmezett korlátozások vannak érvényben, ami hatással lehet a DevTest Labs szolgáltatásra. Ezeket a korlátokat **kvótáknak**nevezzük.

> [!NOTE]
> A DevTest Labs szolgáltatás nem kényszeríti ki a kvótákat. Az esetlegesen felmerülő kvóták a teljes Azure-előfizetés alapértelmezett korlátai.

Minden Azure-erőforrást használhat, amíg el nem éri a kvótáját. Minden előfizetéshez külön kvóta tartozik, a felhasználás pedig előfizetésenként van nyilvántartva.

Például minden előfizetéshez tartozik egy 20 maggal alapértelmezett kvóta. Így ha a laborban virtuális gépeket hoz létre négy maggal, akkor csak öt virtuális gépet hozhat létre.

Az [Azure-előfizetések és-szolgáltatások korlátai](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) az Azure-erőforrások leggyakoribb kvótáit sorolja fel. A laborban leggyakrabban használt erőforrások, amelyek esetében kvóták merülhetnek fel, a virtuálisgép-magok, a nyilvános IP-címek, a hálózati adapterek, a felügyelt lemezek, a RBAC szerepkör-hozzárendelések és a ExpressRoute-áramkörök is.

## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése
Ezek a lépések bemutatják, hogyan tekintheti meg az előfizetéshez tartozó aktuális kvótákat adott Azure-erőforrásokhoz, és megtekintheti az egyes felhasznált kvóták százalékos arányát.

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **További szolgáltatások**lehetőséget, majd válassza a **számlázás** lehetőséget a listából.
1. A számlázás panelen válasszon egy előfizetést.
4. Válassza a **használat + kvóták**lehetőséget.

   ![Használat és kvóták gomb](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Megjelenik a használat + kvóták panel, amely felsorolja az előfizetésben elérhető különböző erőforrásokat, valamint az erőforrásként használt kvóta százalékos arányát.

   ![Kvóták és használat](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>További erőforrások igénylése az előfizetésben
Ha eléri a kvóta korlátját, az előfizetésben lévő erőforrások alapértelmezett korlátja az [Azure-előfizetés és a szolgáltatás korlátaiban](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)leírtak szerint legfeljebb egy maximális korlátra növelhető.

Ezek a lépések bemutatják, hogyan kérheti a kvóta növelését a [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **További szolgáltatások**lehetőséget, válassza a **számlázás**, majd a **használat + kvóták**lehetőséget.
1. A használat + kvóták panelen válassza a **kérés növelése** gombot.

   ![Kérelem növelésének gombja](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. A kérelem elvégzéséhez és elküldéséhez adja meg a szükséges információkat az **új támogatási kérelem** űrlapjának mindhárom lapján.

   ![Kérelem növelésének űrlapja](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

Az [Azure-korlátok és-növekedések megismerése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) további információkat biztosít az Azure-támogatással kapcsolatban a kvóta növelésének meghívásához.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
