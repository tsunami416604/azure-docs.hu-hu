---
title: Felügyeleti egységek hozzáadása és eltávolítása – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységekkel korlátozhatja a szerepkör-engedélyek hatókörét Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b47ea3cc55be26521dfa6e2b3230b477f82f442
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376113"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése Azure Active Directory

A Azure Active Directory (Azure AD) részletesebb felügyeleti felügyeletéhez a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz olyan hatókörrel, amely egy vagy több felügyeleti egységre (AUs) korlátozódik.

## <a name="get-started"></a>Bevezetés

1. Ha a [Graph Explorerben](https://aka.ms/ge)a következő utasítások alapján szeretne lekérdezéseket futtatni, tegye a következőket:

    a. A Azure Portal nyissa meg az Azure AD-t. Az alkalmazások listában válassza a **Graph Explorer**lehetőséget, majd válassza a **rendszergazdai jóváhagyás megadása a Graph Explorerben**lehetőséget.

    ![A "rendszergazdai jóváhagyás megadása" hivatkozást ábrázoló képernyőkép](./media/admin-units-manage/select-graph-explorer.png)


1. Használja az Azure AD PowerShell előzetes verzióját.

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portalban nyissa meg az Azure AD-t, majd a bal oldali panelen válassza a **felügyeleti egységek**elemet.

    ![Képernyőkép az Azure AD felügyeleti unitslink](./media/admin-units-manage/nav-to-admin-units.png)

1. Válassza a **Hozzáadás** lehetőséget, majd adja meg a felügyeleti egység nevét. Opcionálisan a felügyeleti egység leírását is hozzáadhatja.

    ![Képernyőkép az Add (Hozzáadás) gombra, és a szövegmezőbe írja be a felügyeleti egység nevét.](./media/admin-units-manage/add-new-admin-unit.png)

1. A felügyeleti egység véglegesítéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="use-powershell"></a>A PowerShell használata

Telepítse az Azure AD PowerShellt (előzetes verzió), mielőtt megpróbálja futtatni a következő parancsokat:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Az idézőjelek közé foglalt értékeket igény szerint módosíthatja.

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Felügyeleti egység eltávolítása

Az Azure ad-ben eltávolíthatja azt a felügyeleti egységet, amelyre már nincs szüksége a felügyeleti szerepkörök hatókörének egysége.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az **Azure ad**  >  **felügyeleti egységeit**. 
1. Válassza ki a törölni kívánt felügyeleti egységet, majd válassza a **Törlés**lehetőséget. 
1. Annak megerősítéséhez, hogy törölni kívánja a felügyeleti egységet, válassza az **Igen**lehetőséget. A felügyeleti egység törölve lett.

![A felügyeleti egység törlése gomb és a megerősítési ablak képernyőképe](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Az idézőjelek közé foglalt értékeket módosíthatja az adott környezethez szükséges módon.

### <a name="use-the-graph-api"></a>A Graph API használata

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Következő lépések

* [Felhasználók kezelése egy felügyeleti egységben](admin-units-add-manage-users.md)
* [Csoportok kezelése egy felügyeleti egységben](admin-units-add-manage-groups.md)
