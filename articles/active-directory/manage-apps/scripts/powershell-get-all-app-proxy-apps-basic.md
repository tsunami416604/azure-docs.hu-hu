---
title: PowerShell-minta – alkalmazásproxy-alkalmazások alapszintű adatainak listázása
description: PowerShell-példa, amely felsorolja Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásait, valamint az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName) és az Object ID-t (ObjId).
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
ms.openlocfilehash: 80509969a30ea3e708d2e176a87277d51fb6da3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88506903"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Az összes alkalmazásproxy-alkalmazás beolvasása és alapvető információk listázása

Ez a PowerShell-parancsfájl az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazással kapcsolatos információkat listázza, beleértve az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName) és az Object ID-t (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.
## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy egyszerű szolgáltatásnév beolvasása. |

## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).