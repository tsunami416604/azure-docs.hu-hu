---
title: Felügyeleti egységek hozzáadása és eltávolítása (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységekkel korlátozhatja a szerepkör-engedélyek hatókörét Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00ed10ece042446e941832b27c58b0032f852ad9
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732259"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése Azure Active Directory

A Azure Active Directory (Azure AD) részletesebb felügyeleti felügyeletéhez a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz olyan hatókörrel, amely egy vagy több felügyeleti egységre (AUs) korlátozódik.

## <a name="get-started"></a>Bevezetés

1. Ha a [Graph Explorerben](https://aka.ms/ge)a következő utasítások alapján szeretne lekérdezéseket futtatni, tegye a következőket:

    a. A Azure Portal nyissa meg az Azure AD-t. Az alkalmazások listában válassza a **Graph Explorer**lehetőséget, majd válassza a **rendszergazdai jóváhagyás megadása a Graph Explorerben**lehetőséget.

    ![A "rendszergazdai jóváhagyás megadása" hivatkozást ábrázoló képernyőkép](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. A Graph Explorerben válassza ki a **bétaverzió** verzióját.

    ![A kiválasztott béta-verziót bemutató képernyőkép](./media/roles-admin-units-manage/select-beta-version.png)

1. Használja az Azure AD PowerShell előzetes verzióját.

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portalban nyissa meg az Azure AD-t, majd a bal oldali panelen válassza a **felügyeleti egységek**elemet.

    ![Képernyőkép a felügyeleti egységek (előzetes verzió) hivatkozásáról az Azure AD-ben](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Válassza a **Hozzáadás** lehetőséget, majd adja meg a felügyeleti egység nevét. Opcionálisan a felügyeleti egység leírását is hozzáadhatja.

    ![Képernyőkép az Add (Hozzáadás) gombra, és a szövegmezőbe írja be a felügyeleti egység nevét.](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. A felügyeleti egység véglegesítéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="use-powershell"></a>A PowerShell használata

Telepítse az Azure AD PowerShellt (előzetes verzió), mielőtt megpróbálja futtatni a következő parancsokat:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Az idézőjelek közé foglalt értékeket igény szerint módosíthatja.

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Felügyeleti egység eltávolítása

Az Azure ad-ben eltávolíthatja azt a felügyeleti egységet, amelyre már nincs szüksége a felügyeleti szerepkörök hatókörének egysége.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az **Azure ad**  >  **felügyeleti egységeit**. 
1. Válassza ki a törölni kívánt felügyeleti egységet, majd válassza a **Törlés**lehetőséget. 
1. Annak megerősítéséhez, hogy törölni kívánja a felügyeleti egységet, válassza az **Igen**lehetőséget. A felügyeleti egység törölve lett.

![A felügyeleti egység törlése gomb és a megerősítési ablak képernyőképe](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>A PowerShell használata

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Az idézőjelek közé foglalt értékeket módosíthatja az adott környezethez szükséges módon.

### <a name="use-the-graph-api"></a>A Graph API használata

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>További lépések

* [Felhasználók kezelése egy felügyeleti egységben](roles-admin-units-add-manage-users.md)
* [Csoportok kezelése egy felügyeleti egységben](roles-admin-units-add-manage-groups.md)
