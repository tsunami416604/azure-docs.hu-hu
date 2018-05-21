---
title: A portál használata az Azure-készletben sablonok telepítése |} Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure-verem portált sablonok telepítése.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 278f15271d3a5443102f5e387d3db1adb53fe7db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Üzembe helyezheti a sablonokat a verem Azure portál használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A portál központi telepítése Azure Resource Manager-sablonok Azure verem használható.

A sablon telepítéséhez:

1. Jelentkezzen be a portálra, jelölje be **új**, majd válassza ki **egyéni**.
2. Válassza ki **sablon-üzembehelyezés**.
3. Válassza ki **Szerkesztés sablon**, majd illessze be a sablon JSON-kódot a kód ablakba. Kattintson a **Mentés** gombra.
4. Válassza ki **paraméterek szerkesztése**, adjon meg értékeket a paraméterek jelennek meg, majd válassza ki, **OK**.
5. Válassza ki **előfizetés**. Válassza ki a kívánt előfizetést, és válassza **OK**.
6. Válassza ki **erőforráscsoport**. Válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, és válassza **OK**.
7. Kattintson a **Létrehozás** gombra. Egy új csempe az irányítópulton a sablon-üzembehelyezés előrehaladását követi nyomon.

## <a name="next-steps"></a>További lépések

[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
