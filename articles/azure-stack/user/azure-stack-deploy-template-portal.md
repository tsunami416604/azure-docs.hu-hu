---
title: A portál használatával az Azure Stackben sablonok üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Stack portálon sablonok üzembe helyezése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4013b7136fd365b1d37348cfc1f0b46cce787ce2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363123"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Az Azure Stack portálon sablonok üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A portál segítségével az Azure Stack üzembe helyezése Azure Resource Manager-sablonok.

## <a name="to-deploy-a-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a portálra, válassza ki **+ erőforrás létrehozása**, majd válassza ki **egyéni**.
2. Válassza ki **sablonalapú telepítés**.
3. Válassza ki **szerkesztési sablon**, majd illessze be a sablon JSON-kód a kód ablakba. Kattintson a **Mentés** gombra.
4. Válassza ki **paraméterek szerkesztése**, adja meg, hogy látható-e, és válassza a paraméterek értékeit **OK**.
5. Válassza ki **előfizetés**. Válassza ki az előfizetést szeretné használni, és válassza ki **OK**.
6. Válassza ki **erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat, és válassza **OK**.
7. Kattintson a **Létrehozás** gombra. Egy új csempét az irányítópulton a sablon központi telepítéséhez előrehaladását követi nyomon.

## <a name="next-steps"></a>További lépések

Sablonok telepítésével kapcsolatos további tudnivalókért lásd:

[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
