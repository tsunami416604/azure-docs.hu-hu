---
title: Azure-készletben a parancssorral sablonok telepítése |} Microsoft Docs
description: 'Útmutató: Azure verem üzembe helyezheti a sablonokat a platformfüggetlen parancssori felület (CLI) használatával.'
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316195"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Üzembe helyezheti a sablonokat az Azure-készletben a parancssor használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A parancssor használatával telepítése Azure Resource Manager-sablonok az Azure verem szoftverfejlesztői készlet. Az Azure Resource Manager-sablonok telepítése, és jogosultságok kiosztása egyetlen, koordinált műveletben az alkalmazás összes erőforrást.

## <a name="before-you-begin"></a>Előkészületek
 - [Telepítse, és csatlakozzon](azure-stack-version-profiles-azurecli2.md) Azure verem Azure parancssori felülettel
 - Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* a a [tárolási fiók példa sablon létrehozása](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Sablon üzembe helyezése
Lépjen abba a mappába, ahol ezek a fájlok letöltött és a sablon telepítéséhez a következő parancsot:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Ez a parancs telepíti az erőforráscsoport a sablon **cliRG** a Azure verem Koncepció alapértelmezett helyen.

## <a name="validate-template-deployment"></a>A sablon telepítésének ellenőrzése
Az erőforrás csoport és a tárolási fiók megtekintéséhez használja a következő parancsokat:

    azure group list

    azure storage account list




