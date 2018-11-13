---
title: Kvóták és korlátozások méretezése az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Labor skálázása az Azure DevTest Labs szolgáltatásban
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
ms.openlocfilehash: 00f64deaa5e02f9bb74baaf86bfdf71690a3345d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248248"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Kvóták és korlátozások méretezése a DevTest Labs szolgáltatásban
DevTest Labs szolgáltatásban létrehozott munka során Észreveheti, hogy nincsenek-e az egyes Azure-erőforrások, amelyek hatással lehetnek a DevTest Labs szolgáltatás egyes alapértelmezett korlátok. Ezek a korlátok nevezzük **kvóták**.

> [!NOTE]
> A DevTest Labs szolgáltatás nem ír elő a kvóták. Kvóták, léphetnek fel a teljes Azure-előfizetés alapértelmezett korlátozásokban.

Egyes Azure-erőforrások is használhatja, amíg el nem éri a kvótát. Minden előfizetés külön kvótával rendelkezik, és a használati előfizetésenként követi nyomon.

Ha például minden előfizetési csomaghoz tartozik egy alapértelmezett kvóta 20 magot. Tehát ha a laborban négy processzormaggal rendelkező virtuális gépeket hoz létre, majd csak hozhat létre öt virtuális. 

[Az Azure előfizetés- és Szolgáltatáskorlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits) a leggyakoribb kvóták az Azure-erőforrások közül sorol fel néhányat. Leggyakrabban használt erőforrásokat egy tesztkörnyezetben, és amely találkozhat a kvótákat, tartalmazza az Virtuálisgép-magokra, nyilvános IP-címek, hálózati adapter, felügyelt lemezeket, az RBAC szerepkör-hozzárendelés és ExpressRoute-Kapcsolatcsoportok.

## <a name="view-your-usage-and-quotas"></a>A használat és kvóták megtekintése
Ezek a lépések bemutatják, hogyan az aktuális kvótái megtekintheti az adott Azure-erőforrásokhoz az előfizetésben, és tekintse meg az egyes kvóta százaléka használja.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **további szolgáltatások**, majd válassza ki **számlázási** a listából.
1. A számlázás panelen válasszon ki egy előfizetést.
4. Válassza ki **használat + kvóták**.

   ![Használat és kvóták gomb](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A használat + kvóták panel jelenik meg, ezt az előfizetést és a kvóta erőforrásonként használt aránya a elérhető különböző erőforrások listázása.

   ![Kvóták és használat](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>További erőforrások az előfizetésében kérése
Ha eléri a kvótakorlátot, egy adott erőforrás az előfizetéshez az alapértelmezett korlát növelhető a maximális korlátig leírtak szerint [Azure előfizetés- és Szolgáltatáskorlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Ezek a lépések bemutatják, hogyan kérje egy kvótájának növelését, keresztül a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **további szolgáltatások**válassza **számlázási**, majd válassza ki **használat + kvóták**.
1. A használat + kvóták panelen válassza ki a **kérelem növelése** gombra.

   ![Növelje kérésgombhoz](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Végezze el, és küldje el a kérelmet, töltse ki a szükséges adatokat, minden három lapon a **új támogatási kérelem** űrlap.

   ![Növelje űrlapja](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Ismertetése az Azure korlátai és nő](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) további információkat talál az Azure támogatási kérje egy kvótájának növelését.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>További lépések
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
