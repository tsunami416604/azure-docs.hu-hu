---
title: "A Visual Studio Azure verem sablonok telepítése |} Microsoft Docs"
description: "Útmutató a Visual Studio Azure verem sablonok telepítése."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>A Visual Studio használatával Azure verem sablonok telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Visual Studio használatával telepítése Azure Resource Manager-sablonok az Azure-verem szoftverfejlesztői készlet.

1. [Telepítse, és csatlakozzon](azure-stack-install-visual-studio.md) a Visual Studio Azure verem.
2. Nyissa meg a Visual Studiót.
3. Kattintson a **fájl**, kattintson a **új**, majd a a **új projekt** párbeszédpanelen kattintson **Azure erőforráscsoport**.
4. Adjon meg egy **neve** az új projekt, és kattintson a **OK**.
5. Az a **Azure-sablon kiválasztása** párbeszédpanelen módosítsa a *erről a helyről sablon megjelenítése* legördülő lista **Azure verem gyors üzembe helyezés**
6. Kattintson a **101--storage-fiók létrehozása**, és kattintson a **OK**.  
7. Az új projekt, megtekintheti az elérhető sablonok listájának kibontásával a **sablonok** csomópontja a **Megoldáskezelőben** ablaktáblán.
8. A a **Megoldáskezelőben** ablaktáblában kattintson a jobb gombbal a projekt nevére, kattintson **telepítés**, majd kattintson a **új központi telepítési**.
9. Az a **telepítés erőforráscsoportra** párbeszédpanel a **előfizetés** legördülő listából válassza ki a Microsoft Azure verem előfizetést.
10. Az a **erőforráscsoport** listában, válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
11. Az a **erőforráscsoport helye** listára, válassza ki azt a helyet, majd **telepítés**.
12. Az a **paraméterek szerkesztése** párbeszédpanelen adja meg az értékeket a paraméterek (amelyek eltérőek lehetnek a sablon függően), és kattintson a **mentése**.

## <a name="next-steps"></a>További lépések
[Sablonok üzembe helyezése a parancssorral](azure-stack-deploy-template-command-line.md)

[Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)

