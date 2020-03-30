---
title: PowerShell-minta – Az összes alkalmazásproxy-alkalmazás listázása házirenddel
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazások a címtárban, amelyek egy életre szóló jogkivonat-szabályzatot.
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
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482126"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Az összes alkalmazásproxy-alkalmazás beszereznie egy jogkivonat-élettartam-szabályzattal

Ez a PowerShell-parancsfájl-példa felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást a címtárban, amelyek egy token élettartam-szabályzattal rendelkeznek, és a szabályzat részleteit tartalmazza.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ez a minta megköveteli az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy szolgáltatásvezetőt kap. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beszerzi az Azure AD-alkalmazást. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Beszerzi a szabályzatot az Azure AD-ben. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Egy egyszerű szolgáltatás szabályzata az Azure AD-ben. |


## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure AD PowerShell-modul áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

További PowerShell-példák az alkalmazásproxyhoz, lásd: [Azure AD PowerShell példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)