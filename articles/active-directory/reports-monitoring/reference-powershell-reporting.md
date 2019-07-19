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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27fa3d7be5238527f86e9dfde3be70ae09259d69
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302740"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Az Azure AD PowerShell-parancsmagjai jelentéskészítéshez

A Azure Active Directory-(Azure AD-) jelentésekkel megtekintheti a környezete működésének meghatározásához szükséges információkat. A jelentések adatai a jelentéskészítéshez használható Azure AD PowerShell-parancsmagok használatával olvashatók be.

Ez a cikk áttekintést nyújt a parancsmagról.




## <a name="audit-logs"></a>Naplók

A naplók nyomon követést [biztosítanak](concept-audit-logs.md) az Azure ad különböző funkciói által végzett összes módosítás naplójában. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

A naplókhoz a Get-AzureADAuditDirectoryLogs parancsmag használatával férhet hozzá.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Category                      | Get-AzureADAuditDirectoryLogs szűrő "kategória EQ" Application Management "" |
| Tevékenység dátumának időpontja            | Get-AzureADAuditDirectoryLogs – "activityDateTime gt 2019-04-18" szűrő |
| A fentiek mindegyike              | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "és Kategória EQ" Application Management "és activityDateTime gt 2019-04-18"|


Az alábbi képen egy példa látható erre a parancsra. 

![Az "adatösszegzés" gomb](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Bejelentkezési naplók

A [bejelentkezési](concept-sign-ins.md) naplók a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról nyújtanak információt.

A bejelentkezési naplókhoz a Get-AzureADAuditSignInLogs parancsmag használatával férhet hozzá.


| Forgatókönyv                      | PowerShell-parancs |
| :--                           | :--                |
| Felhasználó megjelenítendő neve             | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Timothy Perkins "" |
| Létrehozás dátuma és időpontja              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (mindent a 4/18-as 5:30-kor) |
| Állapot                        | Get-AzureADAuditSignInLogs-Filter "Status/errorCode EQ 50105" |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditSignInLogs-Filter "appDisplayName EQ" StoreFrontStudio [wsfed engedélyezve] "" |
| A fentiek mindegyike              | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Timothy Perkins "és status/errorCode ne 0 és appDisplayName EQ" StoreFrontStudio [wsfed enabled] "" |


Az alábbi képen egy példa látható erre a parancsra. 

![Az "adatösszegzés" gomb](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>További lépések

- [Azure ad-jelentések – áttekintés](overview-reports.md).
- [Naplózott jelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
