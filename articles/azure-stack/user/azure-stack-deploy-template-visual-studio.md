---
title: Sablonok üzembe helyezése az Azure Stackben Visual studióval |} A Microsoft Docs
description: Útmutató a sablonok üzembe helyezése az Azure Stackben Visual studióval.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1d61313018b0cd16594772676172737a7d8fc2cd
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366693"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Az Azure Stack használatával a Visual Studio sablonok üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A Visual Studio használatával üzembe helyezése Azure Resource Manager-sablonok az Azure Stackhez.

## <a name="to-deploy-a-template"></a>A sablon üzembe helyezése

1. [Telepítése és csatlakozás](azure-stack-install-visual-studio.md) az Azure Stackhez a Visual Studio használatával.
2. Nyissa meg a Visual Studiót.
3. Válassza ki **fájl**, majd válassza ki **új**. A **új projekt**válassza **Azure-erőforráscsoport**.
4. Adjon meg egy **neve** az új projekt, és válassza ki a **OK**.
5. A **Azure-sablon kiválasztása**, válasszon **Azure Stack gyors üzembe helyezés** a legördülő listából.
6. Válassza ki **101--storage-fiók létrehozása**, majd válassza ki **OK**.
7. Az új projekt, bontsa ki a **sablonok** csomópontja **Megoldáskezelőben** a rendelkezésre álló sablonok megtekintéséhez.
8. A **Megoldáskezelőben**, válassza ki a projekt nevére, és válassza **telepítés**. Válassza ki **új üzembe helyezési**.
9. A **telepítés erőforráscsoportra**, használja a **előfizetés** legördülő listából válassza ki a Microsoft Azure Stack-előfizetés kiválasztására.
10. Az a **erőforráscsoport** listában válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat.
11. Az a **erőforráscsoport helye** listában válassza ki a helyet, és válassza ki **telepítés**.
12. A **paraméterek szerkesztése**, adja meg az értékeket a paraméterek (amely régiónként eltérő sablon), majd válassza ki **mentése**.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a parancssorral](azure-stack-deploy-template-command-line.md)
* [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
