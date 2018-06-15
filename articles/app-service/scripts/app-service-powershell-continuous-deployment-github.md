---
title: Azure PowerShell-példaszkript – Webalkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel | Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8185edf1176f5215065b8154e584703069d371a2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31586391"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Webalkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd beállítja a folyamatos üzembe helyezést a GitHub-adattárból. A folyamatos üzembe helyezés nélküli GitHubos üzembe helyezésről lásd: [Webalkalmazás létrehozása és kód üzembe helyezése a GitHubról](app-service-powershell-deploy-github.md).

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. Emellett győződjön meg a következőkről is:

- Ki lett építve kapcsolat az Azure-ral az `az login` parancs használatával.
- Az alkalmazás kódja egy, az Ön tulajdonában álló nyilvános vagy privát GitHub-adattárban található.
- [Létrehozott egy hozzáférési jogkivonatot a GitHub-fiókjában](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Webalkalmazást hoz létre. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Módosít egy erőforrást egy erőforráscsoportban. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
