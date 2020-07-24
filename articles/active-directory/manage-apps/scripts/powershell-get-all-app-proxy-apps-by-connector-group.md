---
title: Azure AD Application Proxy-összekötő csoportok listázása alkalmazásokhoz
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory-(Azure AD-) alkalmazásproxy-összekötő csoportot a hozzárendelt alkalmazásokkal.
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
ms.openlocfilehash: 8f381d64e06d8b7abf67b08297baa616397cc439
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019437"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Az összes alkalmazásproxy-alkalmazás és-lista lekérése összekötő csoport szerint

Ez a PowerShell-parancsfájl a hozzárendelt alkalmazásokkal rendelkező összes Azure Active Directory (Azure AD) alkalmazásproxy-összekötő csoport adatait listázza.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Az Application proxyhoz konfigurált alkalmazást kérdezi le az Azure AD-ben. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Lekéri az összes összekötő csoport listáját, vagy ha meg van adva, a megadott összekötő-csoport részleteit. |


## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).