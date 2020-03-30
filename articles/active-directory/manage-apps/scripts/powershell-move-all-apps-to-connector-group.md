---
title: PowerShell-minta – Alkalmazásproxy-alkalmazások áthelyezése egy másik csoportba
description: Az Azure Active Directory (Azure AD) alkalmazásproxy-PowerShell-példa az összekötőcsoporthoz jelenleg hozzárendelt összes alkalmazás áthelyezése egy másik összekötő csoportba.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481983"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Az összekötőcsoporthoz rendelt összes alkalmazás áthelyezése egy másik összekötőcsoportba

Ez a PowerShell-parancsfájl-példa az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, amely jelenleg egy másik összekötő csoporthoz van rendelve.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ez a minta megköveteli az [AzureAD V2 PowerShell graph modul](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) vagy az [AzureAD V2 PowerShell graph modul előzetes verziója](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Egy szolgáltatásvezetőt kap. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Beszerzi az Azure AD-alkalmazást. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Az összes összekötőcsoport listáját lekéri, vagy ha meg van adva, a megadott összekötőcsoport adatait. |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | Az adott összekötőcsoportot egy adott alkalmazáshoz rendeli hozzá.|

## <a name="next-steps"></a>További lépések

Az Azure AD PowerShell-modulról az [Azure AD PowerShell-modul áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

További PowerShell-példák az alkalmazásproxyhoz, lásd: [Azure AD PowerShell példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)