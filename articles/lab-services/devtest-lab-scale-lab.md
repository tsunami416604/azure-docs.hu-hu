---
title: Kvóták és korlátok méretezése a laborban az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan skálázhatja a laboraz Azure DevTest Labs. Tekintse meg a használati kvótákat és korlátokat, és kérjen emelést.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761185"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Kvóták és korlátok méretezése a DevTest Labs ben
A DevTest Labs használata során észreveheti, hogy bizonyos Azure-erőforrások ra vonatkozóan vannak bizonyos alapértelmezett korlátok, amelyek hatással lehetnek a DevTest Labs szolgáltatásra. Ezeket a korlátokat **kvótáknak nevezzük**.

> [!NOTE]
> A DevTest Labs szolgáltatás nem ír elő kvótákat. A kvóták, amelyek esetleg találkoznak a teljes Azure-előfizetés alapértelmezett megkötései.

Használhatja az egyes Azure-erőforrások, amíg el nem éri a kvótát. Minden előfizetéshez külön kvóta tartozik, a felhasználás pedig előfizetésenként van nyilvántartva.

Például minden előfizetés rendelkezik egy alapértelmezett kvótát 20 magok. Így ha virtuális gépeket hoz létre a laborban négy maggal, majd csak öt virtuális gépet hozhat létre.

[Az Azure Előfizetési és Szolgáltatáskorlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) az Azure-erőforrások leggyakoribb kvótáit sorolja fel. A tesztkörnyezetben leggyakrabban használt erőforrások közé tartoznak a virtuális gépmagok, a nyilvános IP-címek, a hálózati adapter, a felügyelt lemezek, az RBAC szerepkör-hozzárendelés és az ExpressRoute-áramkörök.

## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése
Ezek a lépések bemutatják, hogyan tekintheti meg az aktuális kvótákat az előfizetésében az adott Azure-erőforrásokhoz, és megtekintheti, hogy az egyes kvóták hány százalékát használta.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza a **További szolgáltatások**lehetőséget, majd a listából a **Számlázás** lehetőséget.
1. A Számlázás panelen válasszon ki egy előfizetést.
4. Válassza **a Felhasználás + kvóták lehetőséget.**

   ![Használat és kvóták gomb](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Megjelenik a Használat + kvóták panel, amely felsorolja az adott előfizetésben elérhető különböző erőforrásokat és az erőforrásonként használt kvóta százalékos arányát.

   ![Kvóták és felhasználás](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>További erőforrások kérése az előfizetésben
Ha eléri a kvótaplafont, az előfizetésben lévő erőforrások alapértelmezett korlátja az [Azure-előfizetési és szolgáltatáskorlátokban](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)leírtak szerint maximális korlátig növelhető.

Ezek a lépések bemutatják, hogyan kérhet kvótanövelést az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)keresztül.

1. Válassza a **További szolgáltatások**lehetőséget, válassza **a Számlázás**lehetőséget, majd a Használat **+ kvóták**lehetőséget.
1. A Használat + kvóták panelen válassza a **Kérelem növelése** gombot.

   ![Emelés kérése gomb](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. A kérelem kitöltéséhez és elküldéséhez töltse ki a szükséges információkat az **Új támogatási kérelem** űrlap mindhárom lapján.

   ![Kérelemnövelő űrlap](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Az Azure limitek és -növekmények ismertetése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) további információt nyújt az Azure-támogatással való kapcsolatfelvételről a kvótanövelés kérelmezéséhez.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>További lépések
* Fedezze fel a [DevTest Labs Azure Resource Manager QuickStart sablongyűjteményét.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
