---
title: Azure AD PowerShell-parancsmagok jelentéskészítéshez | Microsoft Docs
description: Az Azure AD PowerShell-parancsmagok referenciája a jelentéskészítéshez.
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
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a198a63d633573ad683a3f8e8215b2975721bc9
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794931"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Az Azure AD PowerShell-parancsmagjai jelentéskészítéshez

> [!NOTE] 
> Ezek a PowerShell-parancsmagok jelenleg csak az [Azure ad Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) modullal működnek. Vegye figyelembe, hogy az előzetes verziójú modul éles használatra nem ajánlott. 

A nyilvános előzetes kiadás telepítéséhez használja az alábbi parancsot. 

```powershell
Install-module AzureADPreview
```

Ha többet szeretne megtudni arról, hogyan csatlakozhat az Azure AD-hez a PowerShell használatával, tekintse meg az [Azure ad PowerShell a graphhoz](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)című cikket.  

A Azure Active Directory-(Azure AD-) jelentésekkel részletes információkat kaphat az összes írási műveletről (napló) és a hitelesítési adatokról (bejelentkezési naplók). Bár az adatok az MS Graph API használatával érhetők el, mostantól az Azure AD PowerShell-parancsmagokkal is lekérheti ugyanezeket az adatokat a jelentéskészítéshez.

Ez a cikk áttekintést nyújt a naplókhoz és a bejelentkezési naplókhoz használandó PowerShell-parancsmagokról.

## <a name="audit-logs"></a>Naplók

A [naplók nyomon követést biztosítanak](concept-audit-logs.md) az Azure ad különböző funkciói által végzett összes módosítás naplójában. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

A naplókhoz a Get-AzureADAuditDirectoryLogs parancsmag használatával férhet hozzá.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Kategória                      | Get-AzureADAuditDirectoryLogs szűrő "Category EQ" ApplicationManagement "" |
| Tevékenység dátumának időpontja            | Get-AzureADAuditDirectoryLogs – "activityDateTime gt 2019-04-18" szűrő |
| A fentiek mindegyike              | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "és category EQ" ApplicationManagement "és activityDateTime gt 2019-04-18"|


Az alábbi képen egy példa látható erre a parancsra. 

![Az "adatösszegzés" gomb](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Bejelentkezési naplók

A [bejelentkezési](concept-sign-ins.md) naplók a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról nyújtanak információt.

A bejelentkezési naplókhoz a Get-AzureADAuditSignInLogs parancsmag használatával férhet hozzá.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Felhasználó megjelenítendő neve             | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Timothy Perkins "" |
| Létrehozás dátuma és időpontja              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (mindent a 4/18-as 5:30-kor) |
| status                        | Get-AzureADAuditSignInLogs-Filter "Status/errorCode EQ 50105" |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditSignInLogs-Filter "appDisplayName EQ" StoreFrontStudio [wsfed engedélyezve] "" |
| A fentiek mindegyike              | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Timothy Perkins "és status/errorCode ne 0 és appDisplayName EQ" StoreFrontStudio [wsfed enabled] "" |


Az alábbi képen egy példa látható erre a parancsra. 

![Az "adatösszegzés" gomb](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Következő lépések

- [Azure ad-jelentések – áttekintés](overview-reports.md).
- [Naplózott jelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
