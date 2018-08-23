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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058430"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Az Azure Stack a parancssor használatával sablonok üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A parancssor használatával az Azure Resource Manager-sablonok üzembe helyezése az Azure Stack Development Kit. Az Azure Resource Manager-sablonok üzembe helyezése, és az alkalmazás egyetlen, koordinált műveletben szereplő összes erőforrás kiépítéséhez.

## <a name="before-you-begin"></a>Előkészületek
 - [Telepítése és csatlakozás](azure-stack-version-profiles-azurecli2.md) az Azure Stackhez az Azure CLI-vel
 - Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* származó a [hozzon létre a fiók példa tárolósablonját](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Sablon üzembe helyezése
Lépjen abba a mappába, ahol ezeket a fájlokat is letöltött és a sablon üzembe helyezéséhez a következő parancsot:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Ez a parancs üzembe helyezi a sablont az erőforráscsoport **cliRG** az Azure Stack POC alapértelmezett helyen.

## <a name="validate-template-deployment"></a>A sablon telepítésének ellenőrzése
Az erőforrás és a tárfiókja fiók megtekintéséhez használja a következő parancsokat:

    azure group list

    azure storage account list




