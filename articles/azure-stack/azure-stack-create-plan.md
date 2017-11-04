---
title: "Hozzon létre egy csomagot az Azure-verem |} Microsoft Docs"
description: "A felhő rendszergazdájának hozzon létre egy tervet, amely lehetővé teszi a előfizetők kiépítése virtuális gépek."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Csomag létrehozása az Azure Stackben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A [Csomagok](azure-stack-key-features.md) egy vagy több szolgáltatás csoportjai. -Szolgáltatóként biztosítson a felhasználók terveket hozhat létre. A felhasználók, a csomagok és szolgáltatások tartoznak a ajánlatok előfizetni. Ez a példa bemutatja, hogyan hozhat létre egy tervet, amely tartalmazza a számítási, hálózati és tárolóerőforrás-szolgáltatók. Ez a séma segítségével előfizetők a virtuális gépeket.

1. Jelentkezzen be a verem Azure felügyeleti portálra (https://adminportal.local.azurestack.external). 5. lépése során létrehozott fiókot adja meg a hitelesítő adatait a [a PowerShell-parancsprogrammal](azure-stack-run-powershell-script.md) szakasz.

2. A terv és az ajánlat, amely a felhasználók előfizethetnek létrehozásához kattintson a **új** > **bérlői kínál + tervek** > **terv**.

   ![](media/azure-stack-create-plan/image01.png)
3. Az a **új megtervezése** panelen adja meg a **megjelenített név** és **erőforrásnév**. A megjelenített nevének értéke a terv valódi neve, amelyet a felhasználók. Kizárólag a rendszergazda láthatja az erőforrás nevét. A rendszergazdák az Azure Resource Manager erőforrásként tervvel működését nevét is.

   ![](media/azure-stack-create-plan/image02.png)
4. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő, a terv tárolója.

   ![](media/azure-stack-create-plan/image02a.png)
5. Kattintson a **szolgáltatások**, jelölje be **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**, majd kattintson az **Válasszon**.

   ![](media/azure-stack-create-plan/image03.png)
6. Kattintson a **kvóták**, kattintson a **Microsoft.Storage (helyi)**, és majd válasszon az alapértelmezett kvótát, vagy kattintson a **hozzon létre új kvóta** testreszabása a kvótát.

   ![](media/azure-stack-create-plan/image04.png)
7. Ha hoz létre egy új kvóta, adjon meg egy nevet a kvóta > állítsa be a kvótaértékek > kattintson **OK** > kattintson az új kvótának nevére.

   ![](media/azure-stack-create-plan/image06.png)
8. Kattintson a **Microsoft.Network (helyi)**, és majd válasszon az alapértelmezett kvótát, vagy kattintson a **hozzon létre új kvóta** testreszabása a kvótát.

    ![](media/azure-stack-create-plan/image07.png)
9. Ha egy új kvóta hoz létre, írjon be egy nevet a kvóta > állítsa be a kvótaértékek > kattintson **OK** > kattintson az új kvótának nevére.

    ![](media/azure-stack-create-plan/image08.png)
10. Kattintson a **Microsoft.Compute (helyi)**, és majd válasszon az alapértelmezett kvótát, vagy kattintson a **hozzon létre új kvóta** testreszabása a kvótát.

    ![](media/azure-stack-create-plan/image09.png)
11. Ha egy új kvóta hoz létre, írjon be egy nevet a kvóta > állítsa be a kvótaértékek > kattintson **OK** > kattintson az új kvótának nevére.

    ![](media/azure-stack-create-plan/image10.png)
12. A a **kvóták** panelen kattintson **OK**, majd a a **új tervet** panelen kattintson **létrehozása** a terv létrehozásához.

    ![](media/azure-stack-create-plan/image11.png)
13. Az új csomag megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a tervet, és kattintson a nevére.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Következő lépések
[Ajánlat létrehozása](azure-stack-create-offer.md)
