---
title: Azure AD PowerShell-parancsmagok jelentéskészítéshez | Microsoft dokumentumok
description: Az Azure AD PowerShell-parancsmagok jelentése hivatkozása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495308"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Az Azure AD PowerShell-parancsmagjai jelentéskészítéshez

> [!NOTE] 
> Ezek a Powershell-parancsmagok jelenleg csak az [Azure AD előzetes verziómodullal](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) működnek. Kérjük, vegye figyelembe, hogy az előzetes modul nem ajánlott éles használatra. 

A nyilvános előzetes verziótelepítéséhez használja az alábbiakat. 

```powershell
Install-module AzureADPreview
```
Az Azure AD-hez a PowerShell használatával való csatlakozásról további infromációért tekintse meg az [Azure AD Powershell for Graph című cikket.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)  

Az Azure Active Directory (Azure AD) jelentésekkel részletesen megismerheti az ön irányítása alatt végzett írási műveletek (naplók) és a hitelesítési adatok (bejelentkezési naplók) tevékenységeit. Bár az információ érhető el az MS Graph API használatával, most már beolvasni ugyanazokat az adatokat az Azure AD PowerShell-parancsmagok jelentéskészítéshez.

Ez a cikk áttekintést nyújt a naplózási naplók hoz és a bejelentkezési naplókhoz használható PowerShell-parancsmagokról.

## <a name="audit-logs"></a>Naplók

[A naplónaplók](concept-audit-logs.md) az Azure AD különböző funkciói által végrehajtott összes módosítás nyomon követhetőségét biztosítják a naplókon keresztül. A naplónaplók közé tartoznak például az Azure AD-n belüli erőforrásokon végrehajtott módosítások, például felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadása vagy eltávolítása.

A naplónaplókhoz a "Get-AzureADAuditDirectoryLogs parancsmag használatával kap hozzáférést.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq "Azure AD Cloud Sync"" |
| Kategória                      | Get-AzureADAuditDirectoryLogs -Filter "category eq 'Application Management'" |
| Tevékenység dátuma            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| A fentiek közül az összes              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq "Azure AD Cloud Sync" és a kategória eq "Application Management" és activityDateTime gt 2019-04-18"|


Az alábbi képen egy példa látható erre a parancsra. 

![Az "Adatok összegzése" gomb](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Bejelentkezési naplók

A bejelentkezési naplók a felügyelt alkalmazások és a felhasználói [bejelentkezési](concept-sign-ins.md) tevékenységek használatával kapcsolatos információkat tartalmaznak.

A bejelentkezési naplók a "Get-AzureADAuditSignInLogs parancsmag használatával kap hozzáférést.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Felhasználó megjelenítendő neve             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq "Timothy Perkins"" |
| Dátum időnek létrehozása              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Minden 17:30 óta 4/18) |
| status                        | Get-AzureADAuditSignInLogs -Szűrő "status/errorCode eq 50105" |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]"" |
| A fentiek közül az összes              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq "Timothy Perkins" és status/errorCode ne 0 és appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


Az alábbi képen egy példa látható erre a parancsra. 

![Az "Adatok összegzése" gomb](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>További lépések

- [Az Azure AD-jelentések áttekintése](overview-reports.md).
- [Naplójelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
