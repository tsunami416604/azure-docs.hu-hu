---
title: Felhasználók tömeges törlése (előzetes verzió) a Azure Active Directory portálon | Microsoft Docs
description: A felhasználók tömeges törlése az Azure felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146433"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Felhasználók tömeges törlése (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával számos tagot eltávolíthat egy csoportba egy vesszővel tagolt (CSV) fájl használatával a felhasználók tömeges törléséhez.

## <a name="to-bulk-delete-users"></a>Felhasználók tömeges törlése

1. Jelentkezzen be az Azure AD-szervezetbe egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure ad-ben válassza a **felhasználók** > **tömeges törlés**lehetőséget.
1. A **felhasználó tömeges törlése** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes CSV-fájljának fogadásához, majd adja hozzá a törölni kívánt felhasználókat.

   ![A CSV-fájl a törölni kívánt felhasználók nevét és azonosítóit tartalmazza](./media/users-bulk-delete/delete-csv-file.png)

1. Amikor befejezte a CSV-fájl szerkesztését, válassza ki a fájlt az érvényesíteni kívánt **CSV-fájl feltöltéséhez** .

   ![Válassza ki azt a helyi CSV-fájlt, amelyben meg szeretné jeleníteni a törölni kívánt felhasználókat](./media/users-bulk-delete/bulk-delete.png)

1. A fájl tartalmának ellenőrzésekor a feladatok elküldése előtt meg kell oldania a hibákat.
1. Amikor a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget a felhasználókat törlő Azure batch-feladatok elindításához. Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a tömeges művelet eredményei lapon. A fájl az egyes hibák okát tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![Feltöltés állapotának keresése a tömeges műveletek eredményei lapon](./media/users-bulk-delete/bulk-center.png)

Ezután ellenőrizze, hogy a törölt felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="verify-deleted-users-in-the-azure-portal"></a>A törölt felhasználók ellenőrzése a Azure Portal

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**területen jelölje ki a csak az **összes felhasználó** elemet, és ellenőrizze, hogy a törölt felhasználók már nem szerepelnek-e a listáján.

### <a name="verify-deleted-users-with-powershell"></a>Törölt felhasználók ellenőrzése a PowerShell-lel

Futtassa a következő parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Győződjön meg arról, hogy a törölt felhasználók már nem szerepelnek a felsorolásban.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
