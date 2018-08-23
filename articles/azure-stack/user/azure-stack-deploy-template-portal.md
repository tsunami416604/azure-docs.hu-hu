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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 931c3d8beb9f2ed12228c74f09f84bbdee1798b8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056683"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Az Azure Stack portálon sablonok üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A portál segítségével az Azure Stack üzembe helyezése Azure Resource Manager-sablonok.

## <a name="to-deploy-a-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a portálra, válassza ki **új**, majd válassza ki **egyéni**.
2. Válassza ki **sablonalapú telepítés**.
3. Válassza ki **szerkesztési sablon**, majd illessze be a sablon JSON-kód a kód ablakba. Kattintson a **Mentés** gombra.
4. Válassza ki **paraméterek szerkesztése**, adja meg, hogy látható-e, és válassza a paraméterek értékeit **OK**.
5. Válassza ki **előfizetés**. Válassza ki az előfizetést szeretné használni, és válassza ki **OK**.
6. Válassza ki **erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat, és válassza **OK**.
7. Kattintson a **Létrehozás** gombra. Egy új csempét az irányítópulton a sablon központi telepítéséhez előrehaladását követi nyomon.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
