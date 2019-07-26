---
title: Jelentések és naplók megtekintése az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan tekintheti meg a felhasználó-hozzárendelési jelentést és a naplókat a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489061"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Jelentések és naplók megtekintése az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Azon erőforrások megtekintése, amelyekhez a felhasználónak hozzáférése van

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás**elemre.

1. A bal oldali menüben kattintson a **felhasználói hozzárendelések jelentés**elemre.

1. Kattintson a **felhasználók kiválasztása** lehetőségre a felhasználók kiválasztása ablaktábla megnyitásához.

1. Keresse meg azt a felhasználót a listában, amelyben meg szeretné tekinteni azokat az erőforrásokat, amelyekhez hozzáférnek.

1. Kattintson a felhasználóra, majd a **kiválasztás**elemre.

    Megjelenik azon erőforrások listája, amelyekhez a felhasználó hozzáfér. Magában foglalja a hozzáférési csomagot, a szabályzatot és a dátumokat.

    ![Felhasználó-hozzárendelések jelentése](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Felhasználói kérés állapotának meghatározása

Ha további részleteket szeretne megtudni arról, hogy a felhasználó hogyan kért és kapott hozzáférést egy hozzáférési csomaghoz, használhatja az Azure AD naplót. A `EntitlementManagement` és`UserManagement` a kategóriákban található naplóbejegyzések használatával további részleteket tudhat meg az egyes kérelmek feldolgozási lépéseiről.  

1. Kattintson a **Azure Active Directory** , majd a **naplók**elemre.

1. A felső részen módosítsa a **kategóriát** a `EntitlementManagement` vagy `UserManagement`a értékre a keresett naplózási rekordtól függően.  

1. Kattintson az **Alkalmaz** gombra.

1. A naplók letöltéséhez kattintson a **Download (Letöltés**) gombra.

Amikor az Azure ad új kérést kap, egy naplózási rekordot ír, amelyben a **Kategória** szerepel `EntitlementManagement` , és a **tevékenység** általában `User requests access package assignment`.  A Azure Portalban létrehozott közvetlen hozzárendelés esetén a naplózási rekord `Administrator directly assigns user to access package` **tevékenység** mezőjében a **ActorUserPrincipalName**azonosítja a hozzárendelést végrehajtó felhasználót.

Az Azure AD további naplózási rekordokat fog írni, amíg a kérelem folyamatban van, beleértve a következőket:

| Category | Tevékenység | Kérés állapota |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | A kérelem nem igényel jóváhagyást |
| `UserManagement` | `Create request approval` | A kérelem jóváhagyást igényel |
| `UserManagement` | `Add approver to request approval` | A kérelem jóváhagyást igényel |
| `EntitlementManagement` | `Approve access package assignment request` | Kérelem jóváhagyva |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |A kérelem jóváhagyva vagy nem igényel jóváhagyást |

Amikor egy felhasználóhoz hozzáférési hozzáférés van rendelve, az Azure ad egy naplózási `EntitlementManagement` rekordot ír a kategóriába a **tevékenységgel** `Fulfill access package assignment`.  Az **ActorUserPrincipalName** mező azonosítja a hozzáférést fogadó felhasználót.

Ha a hozzáférés nem lett hozzárendelve, akkor az Azure ad egy naplózási `EntitlementManagement` rekordot ír  a tevékenység `Deny access package assignment request`kategóriába, ha a kérést egy jóváhagyó megtagadta `Access package assignment request timed out (no approver action taken)`, vagy ha a kérelem túllépte az időkorlátot a jóváhagyó jóváhagyása előtt.

Ha a felhasználó hozzáférési csomagjának hozzárendelése lejár, a felhasználó megszakította vagy eltávolítja a rendszergazdát, akkor az Azure ad naplózási rekordot ír a `EntitlementManagement` **tevékenységgel** `Remove access package assignment`rendelkező kategóriához.

## <a name="next-steps"></a>További lépések

- [Az Azure AD-jogosultságok kezelése – problémamegoldás](entitlement-management-troubleshoot.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
