---
title: PowerShell-minta – az összes alkalmazásproxy-összekötő csoport listázása
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory-(Azure AD-) alkalmazásproxy-összekötő csoportot és összekötőt a címtárban.
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482113"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Az összes összekötő-csoport és összekötő beolvasása a címtárban

Ez a PowerShell-parancsfájl például felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-összekötő csoportját és összekötőjét a címtárban.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Lekéri az összes összekötő csoport listáját, vagy ha meg van adva, a megadott összekötő-csoport részleteit. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Az egyes összekötő csoportokhoz társított összes alkalmazásproxy-összekötő beolvasása.|

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).