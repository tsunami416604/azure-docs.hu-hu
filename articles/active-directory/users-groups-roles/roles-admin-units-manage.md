---
title: Felügyeleti egységek hozzáadása és eltávolítása (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: Felügyeleti egységek használatával korlátozhatja a szerepkör-engedélyek hatókörét az Azure Active Directoryban.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684915"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése az Azure Active Directoryban

Részletesebb felügyeleti vezérlés az Azure Active Directoryban (Azure AD), a felhasználók hozzárendelhető egy Azure AD-szerepkör egy hatóköre, amely egy vagy több felügyeleti egység (AUs) csak.

## <a name="get-started"></a>Bevezetés

1. Ha a következő utasításokból szeretne lekérdezéseket futtatni a [Graph Explorer en](https://aka.ms/ge)keresztül, tegye a következőket:

    a. Az Azure Portalon nyissa meg az Azure AD.In the Azure Portal, go to Azure AD. Az alkalmazások listájában válassza a **Graph Explorer**lehetőséget, majd a **Rendszergazdai hozzájárulás megadása a Graph Explorer programhoz**lehetőséget.

    ![Képernyőkép a "Rendszergazdai hozzájárulás megadása" hivatkozásra](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. A Graph Explorer **beta** ben válassza ki a bétaverziót.

    ![A kiválasztott bétaverziót bemutató képernyőkép](./media/roles-admin-units-manage/select-beta-version.png)

1. Használja az Azure AD PowerShell előzetes verzióját.

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Az Azure Portalon nyissa meg az Azure AD-t, majd a bal oldali ablaktáblában válassza a **Felügyeleti egységek**lehetőséget.

    ![Képernyőkép a felügyeleti egységek (előzetes verzió) hivatkozásáról az Azure AD-ben](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Válassza **a Hozzáadás** lehetőséget, majd írja be a felügyeleti egység nevét. Szükség esetén adja meg a felügyeleti egység leírását.

    ![Képernyőkép a Hozzáadás gombról és a felügyeleti egység nevének beírásához](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. A felügyeleti egység véglegesítéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="use-powershell"></a>A PowerShell használata

Telepítse az Azure AD PowerShellt (előzetes verziót) a következő parancsok futtatása előtt:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Szükség szerint módosíthatja az idézőjelek közé zárt értékeket.

### <a name="use-microsoft-graph"></a>A Microsoft Graph használata

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Felügyeleti egység eltávolítása

Az Azure AD-ben eltávolíthatja a felügyeleti egységek, amelyek már nincs szüksége a felügyeleti szerepkörök hatókörének egységeként.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Az Azure Portalon nyissa meg az **Azure AD** > **felügyeleti egységek.** 
1. Jelölje ki a törölni kívánt felügyeleti egységet, majd kattintson a **Törlés gombra.** 
1. A felügyeleti egység törlésének megerősítéséhez válassza az **Igen**lehetőséget. A közigazgatási egység törlődik.

![Képernyőkép a felügyeleti egység Törlése gombról és a megerősítési ablakról](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>A PowerShell használata

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Az idézőjelek közé zárt értékeket az adott környezethez szükséges módon módosíthatja.

### <a name="use-the-graph-api"></a>A Graph API használata

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>További lépések

* [Felhasználók kezelése egy felügyeleti egységben](roles-admin-units-add-manage-users.md)
* [Csoportok kezelése egy felügyeleti egységben](roles-admin-units-add-manage-groups.md)
