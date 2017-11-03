---
title: "Telepítse újra az Azure verem |} Microsoft Docs"
description: "Telepítse újra az Azure verem."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Telepítse újra az Azure verem
Telepítse újra az Azure-vermet, el kell indítania keresztül teljesen az alább ismertetett.

## <a name="steps-to-redeploy-azure-stack"></a>Telepítse újra az Azure-verem lépései
1. A development kit gazdagépen nyissa meg egy rendszergazda jogú PowerShell-konzol > keresse meg a asdk-installer.ps1 parancsfájl > futtatáshoz > kattintson **újraindítás**.
2. Válassza ki az operációs rendszer (nem **Azure verem**), majd **következő**.
3. A development kit fogadó újraindul, miután törli a CloudBuilder.vhdx fájlt, amely a korábbi központi telepítés részeként lett megadva.
4. [A csomag telepítése](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Következő lépések
[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

