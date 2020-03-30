---
title: PowerShell-minta – Az alkalmazásproxy-alkalmazások bővített adatainak listázása
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást az alkalmazásazonosítóval (AppId), a névvel (DisplayName), a külső URL-címmel (ExternalUrl), a belső URL-címmel (InternalUrl) és a hitelesítéstípussal ( ExternalAuthenticationType).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f58de8c081dbb0323a2d9638777e29d879172072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482139"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Az összes alkalmazásproxy-alkalmazás beszereznie és a bővített információk listázása

Ez a PowerShell-parancsfájl-példa az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásadatait tartalmazza, beleértve az alkalmazásazonosítót (AppId), a nevet (DisplayName), a külső URL-címet (ExternalUrl), a belső URL-címet (InternalUrl), valamint hitelesítés típusa (ExternalAuthenticationType).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ez a minta megköveteli az [AzureAD V2 PowerShell graph modul](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) vagy az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy szolgáltatásvezetőt kap. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beszerzi az Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Az Azure AD-ben az alkalmazásproxyhoz konfigurált alkalmazás lekérése. |

## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure AD PowerShell-modul áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

További PowerShell-példák az alkalmazásproxyhoz, lásd: [Azure AD PowerShell példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)