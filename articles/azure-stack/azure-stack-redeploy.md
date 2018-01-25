---
title: "Telepítse újra az Azure verem |} Microsoft Docs"
description: "Telepítse újra az Azure verem."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Telepítse újra az Azure verem
Azure verem másikban hibaüzenetet kap, ha újból futtathatja a telepítő a következő PowerShell-paranccsal: `.\InstallAzureStackpoc.ps1 -rerun`. Ez a parancs a pont, amely korábban nem kell kezdenie a telepítőt Azure verem újraindul. Ha újra ugyanazt a telepítési hiba, a cím a problémát egy teljes újratelepítés végrehajtásához szükség lehet. 

Telepítse újra az Azure-vermet, el kell indítania keresztül teljesen az alább ismertetett.

## <a name="steps-to-redeploy-azure-stack"></a>Telepítse újra az Azure-verem lépései
1. A development kit gazdagépen nyissa meg egy rendszergazda jogú PowerShell-konzol > keresse meg a asdk-installer.ps1 parancsfájl > futtatáshoz > kattintson **újraindítás**.
2. Válassza ki az operációs rendszer (nem **Azure verem**), majd **következő**.
3. A development kit fogadó újraindul, miután törli a CloudBuilder.vhdx fájlt, amely a korábbi központi telepítés részeként lett megadva.
4. [A csomag telepítése](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>További lépések
[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

