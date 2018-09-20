---
title: Sablonok a parancssor az Azure Stack üzembe helyezése |} A Microsoft Docs
description: Útmutató a sablonok az Azure Stack üzembe helyezése a többplatformos parancssori felület (CLI) használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365434"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Az Azure Stack a parancssor használatával sablonok üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A parancssor használatával üzembe helyezése Azure Resource Manager-sablonok az Azure Stack Development Kit környezetben. Az Azure Resource Manager-sablonok üzembe helyezése, és az alkalmazás egyetlen, koordinált műveletben szereplő összes erőforrás kiépítéséhez.

## <a name="before-you-begin"></a>Előkészületek

 - [Telepítése és csatlakozás](azure-stack-version-profiles-azurecli2.md) az Azure Stackhez az Azure CLI használatával.
 - Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* származó a [hozzon létre a fiók példa tárolósablonját](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Sablon üzembe helyezése

Lépjen abba a mappába, amelybe a rendszer letölti a fájlokat, és futtassa a következő parancsot a sablon üzembe helyezéséhez:

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

Ez a parancs üzembe helyezi a sablont az erőforráscsoport **cliRG** az Azure Stack POC alapértelmezett helyen.

## <a name="validate-template-deployment"></a>A sablon telepítésének ellenőrzése

Az erőforrás és a tárfiókja fiók megtekintéséhez használja a következő parancsokat:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>További lépések

Sablonok telepítésével kapcsolatos további tudnivalókért lásd:

[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)

