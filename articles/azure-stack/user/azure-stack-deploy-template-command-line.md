---
title: "Azure-készletben a parancssorral sablonok telepítése |} Microsoft Docs"
description: "Útmutató: Azure verem üzembe helyezheti a sablonokat a platformfüggetlen parancssori felület (CLI) használatával."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Üzembe helyezheti a sablonokat az Azure-készletben a parancssor használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A parancssor használatával telepítése Azure Resource Manager-sablonok az Azure verem szoftverfejlesztői készlet. Az Azure Resource Manager-sablonok telepítése, és jogosultságok kiosztása egyetlen, koordinált műveletben az alkalmazás összes erőforrást.

## <a name="before-you-begin"></a>Előkészületek
 - [Telepítse, és csatlakozzon](azure-stack-connect-cli.md) Azure verem Azure parancssori felülettel
 - Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* a a [tárolási fiók példa sablon létrehozása](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Sablon üzembe helyezése
Lépjen abba a mappába, ahol ezek a fájlok letöltött és a sablon telepítéséhez a következő parancsot:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Ez a parancs telepíti az erőforráscsoport a sablon **cliRG** a Azure verem Koncepció alapértelmezett helyen.

## <a name="validate-template-deployment"></a>A sablon telepítésének ellenőrzése
Az erőforrás csoport és a tárolási fiók megtekintéséhez használja a következő parancsokat:

    azure group list

    azure storage account list




