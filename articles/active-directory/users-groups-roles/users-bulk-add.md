---
title: Tömeges importálás a felhasználók (előzetes verzió) hozzáadásához a Azure Active Directory portálon | Microsoft Docs
description: Felhasználók tömeges hozzáadása az Azure AD felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22052117448cdb71bbc16e1df7899e6d3eb764e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901489"
---
# <a name="bulk-import-users-preview-in-azure-active-directory"></a>Felhasználók tömeges importálása (előzetes verzió) Azure Active Directory

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, a csoportos meghívást a vendégek számára, és támogatja a felhasználók, csoportok és csoporttagok letöltési listáját.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A felhasználók létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez legalább 50 000 felhasználó tömeges létrehozását teszi lehetővé.

## <a name="required-permissions"></a>Szükséges engedélyek

A felhasználók tömeges létrehozásához a felügyeleti portálon globális rendszergazdaként vagy felhasználói rendszergazdaként kell bejelentkeznie.

## <a name="to-bulk-import-users"></a>Felhasználók tömeges importálása

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure ad-ben válassza a**tömegesen létrehozott** **felhasználók** > elemet.
1. A felhasználó **tömeges létrehozása** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes, VESSZŐVEL tagolt (CSV) fájljának megjelenítéséhez, majd adja hozzá az új felhasználókat.
1. Amikor befejezte a CSV-fájl szerkesztését, vagy ha valamelyik saját feltöltésre kész, válassza ki a fájlt a **CSV-fájl feltöltéséhez** .

   ![Válassza ki azt a helyi CSV-fájlt, amelyben fel szeretné sorolni a hozzáadni kívánt felhasználókat](./media/users-bulk-add/upload-button.png)

1. A fájl tartalmának ellenőrzésekor meg kell oldania a hibákat, mielőtt elindítja a feltöltési feladatot.
1. Amikor a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az új felhasználói adatokat felvenni kívánó Azure batch-feladatok elindításához. Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a tömeges művelet eredményei lapon. A fájl az egyes hibák okát tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![Feltöltés állapotának keresése a tömeges műveletek eredményei lapon](./media/users-bulk-add/bulk-center.png)

Ezután ellenőrizze, hogy a létrehozott felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="verify-users-in-the-azure-portal"></a>Felhasználók ellenőrzése a Azure Portalban

1. [Jelentkezzen be az Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**területen válassza a **minden felhasználó** lehetőséget, és győződjön meg arról, hogy a létrehozott felhasználók szerepelnek a felsorolásban.

### <a name="verify-users-with-powershell"></a>Felhasználók ellenőrzése a PowerShell-lel

Futtassa a következő parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Látnia kell, hogy a létrehozott felhasználók szerepelnek a listáján.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
