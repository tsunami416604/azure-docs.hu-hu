---
title: Felügyeleti egységek hozzáadása és eltávolítása (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: Felügyeleti egységek használata az Azure Active Directory szerepkör-engedélyeinek hatókörének korlátozására
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428159"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése az Azure Active Directoryban

Részletesebb felügyeleti vezérlés az Azure Active Directoryban (Azure AD), a felhasználók hozzárendelhető egy Azure AD-szerepkör hatóköre egy vagy több felügyeleti egység (AUs) használatával.

## <a name="getting-started"></a>Első lépések

1. Ha a következő utasításokból szeretne lekérdezéseket futtatni a [Graph Explorer](https://aka.ms/ge)en keresztül, kérjük, győződjön meg a következőkről:

    1. Nyissa meg az Azure AD-t a portálon, majd az alkalmazásokban válassza a Graph Explorer elemet, és adja meg a rendszergazdai jóváhagyást a Graph Explorerhez.

        ![válassza a Graph Explorer lehetőséget, és adja meg a rendszergazdai jóváhagyást ezen az oldalon](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. Győződjön meg arról, hogy a Graph Explorerben kiválasztja a bétaverziót.

        ![válassza ki a béta verziót a POST művelet előtt](./media/roles-admin-units-manage/select-beta-version.png)

1. Kérjük, használja az Azure AD PowerShell előzetes verzióját. Részletes utasításokat itt.

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg az Active Directory t a portálon, és válassza a felügyeleti egységek lehetőséget a bal oldali panelen.

    ![navigálás az Azure Active Directory felügyeleti egységei között](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Válassza **a Add*** lehetőséget, és adja meg a felügyeleti egység nevét, és tetszés szerint hozzáadhatja a felügyeleti egység leírását.

    ![válassza a Hozzáadás lehetőséget, majd adja meg a felügyeleti egység nevét](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. A felügyeleti egység véglegesítéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="powershell"></a>PowerShell

Telepítse az Azure AD PowerShellt (előzetes verzió), mielőtt megpróbálna végrehajtani az alábbi műveleteket:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

A fent kiemelt értékek szükség szerint módosíthatók.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Felügyeleti egység eltávolítása

Az Azure Active Directoryban (Azure AD) eltávolíthatja a felügyeleti szerepkörök hatókörének egy adott ségét.

### <a name="azure-portal"></a>Azure Portal

Nyissa meg az **Azure AD > felügyeleti egységek** a portálon. Jelölje ki a törölni kívánt felügyeleti egységet, majd válassza a **Törlés**lehetőséget. Az **Igen**megerősítése után a felügyeleti egység törlődik.

![A törölni kívánt felügyeleti egység kiválasztása](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

A kiemelt szakasz az adott környezetre vonatkozóan szükség szerint módosítható.

### <a name="graph-api"></a>Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>További lépések

[Felhasználók kezelése a felügyeleti egységben](roles-admin-units-add-manage-users.md)
[Csoportok kezelése a felügyeleti egységben](roles-admin-units-add-manage-groups.md)
