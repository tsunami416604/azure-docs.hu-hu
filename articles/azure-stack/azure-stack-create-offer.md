---
title: "Hozzon létre egy ajánlatot Azure verem |} Microsoft Docs"
description: "Felhő rendszergazdaként megtudhatja, hogyan hozzon létre egy ajánlatot a felhasználók Azure-készletben."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>Ajánlat létrehozása az Azure Stackben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

[Kínál](azure-stack-key-features.md) olyan beállításcsoportok, egy vagy több terveket tartalmazhatnak, amelyek a felhasználóknak, hogy a megvásárolt, illetve fizethetnek elő telepített szolgáltatók. Ez a dokumentum azt ismerteti, hogyan hozzon létre egy ajánlat, amely tartalmazza a [létrehozott tervet](azure-stack-create-plan.md) az előző lépésben. Ez az ajánlat segítségével előfizetők a virtuális gépeket.

1. Jelentkezzen be a verem Azure felügyeleti portálra (https://adminportal.local.azurestack.external) > kattintson **új** > **bérlői kínál + tervek**  >   **Ajánlat**.

   ![](media/azure-stack-create-offer/image01.png)
2. Az a **új ajánlat** panelen adja meg a **megjelenített név** és **erőforrásnév**, és majd ki kell jelölnie egy új vagy meglévő **erőforráscsoport**. A megjelenített név az ajánlat rövid nevét és a felhasználók által látható, ha feliratkozik az ajánlat csak információt. Ezért ügyeljen arra, hogy egy egyszerűen elsajátítható nevet válasszon, amely segít a felhasználó milyen tartalmaz az ajánlat megismeréséhez. Kizárólag a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![](media/azure-stack-create-offer/image01a.png)
3. Kattintson az **Alapcsomagok** elemre, majd a **Csomag** panelen válassza ki azokat a csomagokat, amelyeket bele szeretne foglalni az ajánlatba, végül kattintson a **Kiválasztás** elemre. Az ajánlat létrehozásához kattintson a **Létrehozás** parancsra.

   ![](media/azure-stack-create-offer/image02.png)
4. Kattintson a **összes erőforrás**, keresse meg az új ajánlat, kattintson az új kínálnak, majd **állapotváltoztatási**, és kattintson a **nyilvános**.

   ![](media/azure-stack-create-offer/image03.png)

Ajánlatok a felhasználók számára a teljes nézet jelenik meg, ha az előfizetés közzé kell tenni. Ajánlatok lehet:

* **Nyilvános**: a felhasználók számára látható.
* **Személyes**: csak a felhő rendszergazdák számára látható. A terv vagy ajánlat létrehozásakor elkészítése során hasznos, vagy ha a felhő rendszergazdája szeretné minden előfizetés jóváhagyása.
* **Leszerelve:** Új előfizetők már nem választhatják. A felhő rendszergazdájához használható leszerelt jövőbeli előfizetések megelőzni, de a jelenlegi előfizetők változatlanul hagyja.

Az ajánlat módosításai nem azonnal láthatók a felhasználó. A módosítások megtekintéséhez, lehetséges, hogy kijelentkezik/be az új előfizetés az "előfizetés objektumválasztó" megjelenítéséhez erőforrások és az erőforrások csoportok létrehozásakor.

> [!NOTE]
>Is létrehozhat alapértelmezett ajánlatokat, a csomagok és a kvóták, tekintse meg a PowerShell használatával a [Azure verem szolgáltatás-rendszergazda információs](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Következő lépések
[Ajánlattal szolgáltatásra, és azt követően a virtuális gépek](azure-stack-subscribe-plan-provision-vm.md)
