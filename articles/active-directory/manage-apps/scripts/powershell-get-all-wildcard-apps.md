---
title: PowerShell minta – alkalmazásproxy-alkalmazások listázása helyettesítő karakterek használatával
description: PowerShell-példa, amely a helyettesítő karaktereket használó összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást felsorolja.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8968f97d64fe66ac203796654a8e243b1b2e31be
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858841"
---
# <a name="get-all-application-proxy-apps-using-wildcard-publishing"></a>Az összes alkalmazásproxy-alkalmazás beolvasása a helyettesítő karakteres közzététel használatával

Ez a PowerShell-parancsfájl a helyettesítő karakterek közzétételét használó összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást listázza.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-wildcard-apps.ps1 "Get all Application Proxy apps using wildcards")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Az Application proxyhoz konfigurált alkalmazást kérdezi le az Azure AD-ben. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
