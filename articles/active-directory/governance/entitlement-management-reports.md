---
title: Jelentések megtekintése és az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory-naplók
description: Ismerje meg a felhasználó-hozzárendelés jelentés megtekintéséhez és a naplók az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541540"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Jelentések megtekintése és a naplókat az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>A felhasználónak hozzáférése van erőforrások megtekintése

1. Kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **hozzárendelések – felhasználói jelentés**.

1. Kattintson a **válassza ki a felhasználók** nyissa meg a kiválasztott felhasználók ablaktáblát.

1. Keresse meg a felhasználót, hogy meg szeretné tekinteni az erőforrásokat, amelyekhez hozzáféréssel rendelkeznek a listában.

1. Kattintson a felhasználót, majd **kiválasztása**.

    Erőforrásokat, amelyeket a felhasználónak hozzáférése van egy lista jelenik meg. A hozzáférés csomag, a házirend és a dátumokat tartalmazza.

    ![Felhasználó-hozzárendelések jelentése](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Egy felhasználói kérelem állapotának megállapítása

További részletekért a felhasználó a kért és kapott hozzáférést egy hozzáférés-csomagot, használhatja az Azure AD-naplókban. Különösen, használhatja a napló-rekordokat a `EntitlementManagement` és `UserManagement` kategóriák részletes további részleteket a feldolgozási lépések, az egyes kérések.  

1. Kattintson a **Azure Active Directory** majd **Auditnaplók**.

1. A képernyő felső részén módosíthatja az **kategória** egyaránt `EntitlementManagement` vagy `UserManagement`, attól függően, a keresett naplórekordot.  

1. Kattintson az **Alkalmaz** gombra.

1. A naplók letöltéséhez kattintson **letöltése**.

Azure ad-ben egy új kérelmet kap, amikor egy naplórekordot ír, amelyben a **kategória** van `EntitlementManagement` és a **tevékenység** általában `User requests access package assignment`.  Az Azure Portalon létrehozott egy közvetlen hozzárendelés esetén a **tevékenység** mező a naplórekord `Administrator directly assigns user to access package`, és a felhasználó elvégezhesse a hozzárendelés azonosíthatók a **ActorUserPrincipalName**.

Az Azure AD további naplózási rekordoknak fog kiírni, amíg folyamatban van. a kérelem többek között:

| Category | Tevékenység | Kérés állapota |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Jóváhagyási kérelem nem igényel |
| `UserManagement` | `Create request approval` | Kérelem jóváhagyása szükséges |
| `UserManagement` | `Add approver to request approval` | Kérelem jóváhagyása szükséges |
| `EntitlementManagement` | `Approve access package assignment request` | Kérelem jóváhagyva |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Kérelem jóváhagyása és nem igényel jóváhagyási |

A felhasználói hozzáférés hozzárendelése esetén a az Azure AD-ellenőrzési rekord ír a `EntitlementManagement` kategória **tevékenység** `Fulfill access package assignment`.  A felhasználó, aki megkapta a hozzáférést által azonosított **ActorUserPrincipalName** mező.

Hozzáférés nem lett hozzárendelve, akkor az Azure AD-ellenőrzési rekord ír a `EntitlementManagement` kategória **tevékenység** vagy `Deny access package assignment request`, ha a kérelem meg lett tagadva, jóváhagyó vagy `Access package assignment request timed out (no approver action taken)`, ha a kérelem túllépte az időkorlátot előtt egy jóváhagyó jóváhagyása sikerült.

Amikor a felhasználó hozzáférési csomagok hozzárendelésével lejár, a felhasználó megszakította, vagy eltávolítja a rendszergazda, majd az Azure AD-ellenőrzési rekord kiírja a `EntitlementManagement` kategória **tevékenység** , `Remove access package assignment`.

## <a name="next-steps"></a>További lépések

- [Tagjogosultság-kezelés az Azure AD hibaelhárítása](entitlement-management-troubleshoot.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
