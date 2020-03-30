---
title: PowerShell-minta – Egyéni tartományokat használó alkalmazásproxy-alkalmazások
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazások, amelyek egyéni tartományokat és tanúsítványadatokat használó.
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
ms.openlocfilehash: 9722178011c3c0411f2e0e888dd9ab5962b933b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482048"
---
# <a name="get-all-application-proxy-apps-using-custom-domains-and-certificate-information"></a>Az összes alkalmazásproxy-alkalmazás beszerezniegyéni tartományok és tanúsítványadatok használatával

Ez a PowerShell-parancsfájl-példa felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, és felsorolja az egyéni tartományokhoz társított tanúsítványadatokat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ez a minta megköveteli az [AzureAD V2 PowerShell graph modul](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) vagy az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domains-and-certs.ps1 "Get all Application Proxy apps using custom domains and certificate information")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy szolgáltatásvezetőt kap. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beszerzi az Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Az Azure AD-ben az alkalmazásproxyhoz konfigurált alkalmazás lekérése. |

## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure AD PowerShell-modul áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

További PowerShell-példák az alkalmazásproxyhoz, lásd: [Azure AD PowerShell példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)