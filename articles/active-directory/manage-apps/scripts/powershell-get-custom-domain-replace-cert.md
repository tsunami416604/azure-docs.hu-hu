---
title: PowerShell-minta – Tanúsítvány cseréje alkalmazásproxy-alkalmazásokban
description: PowerShell-példa, amely tömegesen lecseréli a tanúsítványt az Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazások között.
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481996"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Az összes alkalmazásproxy alkalmazás közzététele azonos tanúsítvánnyal, és csere

Ez a PowerShell-parancsfájl-példa lehetővé teszi, hogy a tanúsítvány túriában az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazások, amelyek közzé az azonos tanúsítvánnyal.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ez a minta megköveteli az [AzureAD V2 PowerShell graph modul](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) vagy az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy szolgáltatásvezetőt kap. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beszerzi az Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Az Azure AD-ben az alkalmazásproxyhoz konfigurált alkalmazás lekérése. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Tanúsítványt rendel egy alkalmazásproxyhoz konfigurált alkalmazáshoz az Azure AD-ben. Ez a parancs feltölti a tanúsítványt, és lehetővé teszi az alkalmazás számára az egyéni tartományok használatát. |

## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure AD PowerShell-modul áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

További PowerShell-példák az alkalmazásproxyhoz, lásd: [Azure AD PowerShell példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)