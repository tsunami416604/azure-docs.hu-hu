---
title: "A portál Azure verem sablonok telepítése |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja az Azure-verem portált sablonok telepítése."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 30c077747685ad7617148e9130926120420b098b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Üzembe helyezheti a sablonokat a verem Azure portál használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A portál használatával telepítése Azure Resource Manager-sablonok az Azure-verem szoftverfejlesztői készlet.

Resource Manager-sablonok telepítése, és jogosultságok kiosztása egyetlen, koordinált műveletben az alkalmazás összes erőforrást.

1. Jelentkezzen be a portálon, kattintson a **új**, kattintson a **egyéni**, és kattintson a **sablon-üzembehelyezés**.
2. Kattintson a **Szerkesztés sablon**, majd a JSON-sablon kódot illessze be a panelt, és kattintson a **mentése**.
3. Kattintson a **paraméterek szerkesztése**, írja be a felsorolt paraméterek értékeit, és kattintson a **OK**.
4. Kattintson a **előfizetés**, válassza ki az előfizetést szeretné használni, és kattintson **OK**.
5. Kattintson a **erőforráscsoport**, válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, és kattintson **OK**.
6. Kattintson a **Create** (Létrehozás) gombra. Egy új csempe az irányítópulton a sablon-üzembehelyezés előrehaladását követi nyomon.

## <a name="next-steps"></a>További lépések
[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)

