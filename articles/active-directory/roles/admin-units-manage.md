---
title: Felügyeleti egységek hozzáadása és eltávolítása – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységekkel korlátozhatja a szerepkör-engedélyek hatókörét Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe76d21d948525421790f574830da7b3a163216
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395393"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Felügyeleti egységek kezelése Azure Active Directory

A Azure Active Directory (Azure AD) részletesebb felügyeleti felügyeletéhez a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz olyan hatókörrel, amely egy vagy több felügyeleti egységre van korlátozva.

## <a name="get-started"></a>Bevezetés

1. Ha a [Graph Explorerben](https://aka.ms/ge)a következő utasítások alapján szeretne lekérdezéseket futtatni, tegye a következőket:

    a. A Azure Portal nyissa meg az Azure AD-t. 
    
    b. Az alkalmazások listában válassza a **Graph Explorer** lehetőséget.
    
    c. Az **engedélyek** panelen válassza a **rendszergazdai jóváhagyás megadása a Graph Explorerben** lehetőséget.

    ![Képernyőfelvétel: a "rendszergazdai jóváhagyás megadása a Graph Explorerhez" hivatkozás.](./media/admin-units-manage/select-graph-explorer.png)


1. Használja az Azure AD PowerShell előzetes verzióját.

## <a name="add-an-administrative-unit"></a>Felügyeleti egység hozzáadása

Felügyeleti egységet a Azure Portal vagy a PowerShell használatával adhat hozzá.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az Azure AD-t. Ezután a bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget.

    ![Képernyőkép az Azure AD-ban található "felügyeleti egységek" hivatkozásról.](./media/admin-units-manage/nav-to-admin-units.png)

1. Válassza a **Hozzáadás** gombot a panel felső részén, majd a **név** mezőben adja meg a felügyeleti egység nevét. Opcionálisan a felügyeleti egység leírását is hozzáadhatja.

    ![Képernyőfelvétel: a Hozzáadás gomb és a név mező a felügyeleti egység nevének megadásához.](./media/admin-units-manage/add-new-admin-unit.png)

1. A felügyeleti egység véglegesítéséhez kattintson a kék **Hozzáadás** gombra.

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

1. A Azure Portal nyissa meg az **Azure ad** -t, majd válassza a **felügyeleti egységek** elemet. 
1. Válassza ki a törölni kívánt felügyeleti egységet, majd válassza a **Törlés** lehetőséget. 
1. Annak megerősítéséhez, hogy törölni kívánja a felügyeleti egységet, válassza az **Igen** lehetőséget. A felügyeleti egység törölve lett.

![A felügyeleti egység törlése gomb és a megerősítési ablak képernyőképe.](./media/admin-units-manage/select-admin-unit-to-delete.png)

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

## <a name="next-steps"></a>További lépések

* [Felhasználók kezelése egy felügyeleti egységben](admin-units-add-manage-users.md)
* [Csoportok kezelése egy felügyeleti egységben](admin-units-add-manage-groups.md)
