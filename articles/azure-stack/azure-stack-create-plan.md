---
title: Hozzon létre egy csomagot az Azure-verem |} Microsoft Docs
description: A felhő rendszergazdájának hozzon létre egy tervet, amely lehetővé teszi a előfizetők kiépítése virtuális gépek.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 1fa01d23108ce92fbd7c854442c0474b19395d25
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248703"
---
# <a name="create-a-plan-in-azure-stack"></a>Csomag létrehozása az Azure Stackben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A [Csomagok](azure-stack-key-features.md) egy vagy több szolgáltatás csoportjai. -Szolgáltatóként biztosítson a felhasználók terveket hozhat létre. A felhasználók, a csomagok és szolgáltatások tartoznak a ajánlatok előfizetni. Ez a példa bemutatja, hogyan hozhat létre egy tervet, amely tartalmazza a számítási, hálózati és tárolóerőforrás-szolgáltatók. Ez a séma segítségével előfizetők a virtuális gépeket.

1. Jelentkezzen be a verem Azure felügyeleti portálra (https://adminportal.local.azurestack.external).

2. A terv és az ajánlat, amely a felhasználók előfizethetnek létrehozásához válassza **új** > **kínál + tervek** > **terv**.
  
   ![Válassza ki a tervet.](media/azure-stack-create-plan/select-plan.png)

3. A **új tervet**, adjon meg egy **megjelenített név** és egy **erőforrásnév**. A megjelenített nevének értéke a terv valódi neve, amely a felhasználók láthatják. Csak a rendszergazda tekintheti meg az erőforrás nevét, mely rendszergazdák segítségével a terv Azure Resource Manager erőforrásként dolgozhat.

   ![Adja meg a részleteket](media/azure-stack-create-plan/plan-name.png)

4. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő, a terv tárolója.

   ![Adja meg az erőforráscsoportot](media/azure-stack-create-plan/resource-group.png)

5. Válassza ki **szolgáltatások** és majd jelölje be a **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**. Ezután válasszon **válasszon** a konfiguráció mentéséhez. Jelölőnégyzetek jelennek meg minden egyes beállítás az egérrel való rámutatáskor.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services.png)

6. Válassza ki **kvóták**, **Microsoft.Storage (helyi)**, majd válassza a alapértelmezett kvótát, vagy válasszon **hozzon létre új kvóta** egyéni kvóta.
  
   ![Kvóták](media/azure-stack-create-plan/quotas.png)

7. Ha új kvótákat hoz létre, adja meg egy **neve** a kvótához > adja meg a kvóta értékét > Válassza ki **OK**. A **kvóta létrehozása** párbeszédpanel bezárul.

   ![Új kvóta](media/azure-stack-create-plan/new-quota.png)

   Ezután válassza ki a létrehozott új kvótát. Válassza ki a kvóta rendeli, majd a Kiválasztás párbeszédpanel bezárul.
  
   ![Rendelje hozzá a kvóta](media/azure-stack-create-plan/assign-quota.png)

8. Ismételje meg a 6 és 7 létrehozására és hozzárendelésére kvótái **Microsoft.Network (helyi)** és **Microsoft.Compute (helyi)**. Ha mindhárom szolgáltatást rendelt, lesz néznek ki a következő példában.

   ![Teljes kvóta hozzárendelések](media/azure-stack-create-plan/all-quotas-assigned.png)

9. A **kvóták**, válassza a **OK**, majd a **új tervet**, válassza a **létrehozása** a terv létrehozásához.

    ![A terv létrehozása](media/azure-stack-create-plan/create.png)

10. Az új csomag megtekintéséhez válasszon **összes erőforrás**, majd keresse meg a tervet, és válassza ki a nevét. Ha az erőforrások listáját hosszú, akkor **keresési** név alapján a terv kereséséhez.

   ![Ellenőrizze, hogy a terv](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>További lépések

[Ajánlat létrehozása](azure-stack-create-offer.md)
