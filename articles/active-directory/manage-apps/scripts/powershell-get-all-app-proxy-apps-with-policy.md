---
title: PowerShell-minta – az összes alkalmazásproxy-alkalmazás listázása házirenddel
description: PowerShell-példa, amely felsorolja az összes olyan Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást a címtárban, amely élettartam-jogkivonat-házirenddel rendelkezik.
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
ms.openlocfilehash: a3583f19eada83b45b2289a938ac5e15b1efb4a4
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861620"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Az összes alkalmazásproxy-alkalmazás beolvasása jogkivonat-élettartam-házirenddel

Ez a PowerShell-parancsfájl felsorolja a címtárban található összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, amely rendelkezik jogkivonat-élettartam-házirenddel, és felsorolja a szabályzat részleteit.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Házirend beolvasása az Azure AD-ben. |
|[Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Lekéri egy egyszerű szolgáltatásnév házirendjét az Azure AD-ben. |


## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
