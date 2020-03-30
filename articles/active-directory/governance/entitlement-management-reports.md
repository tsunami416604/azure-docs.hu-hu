---
title: Jelentések & naplók megtekintése a jogosultságkezelésben - Azure AD
description: Megtudhatja, hogyan tekintheti meg a felhasználói hozzárendelések jelentését és a naplónaplókat az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128423"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Jelentések és naplók megtekintése az Azure AD-jogosultságok kezelésében

Az Azure AD jogosultságkezelési jelentések és az Azure AD naplózási napló további részleteket tartalmaz arról, hogy a felhasználók milyen erőforrásokhoz férhetnek hozzá. Rendszergazdaként megtekintheti a felhasználó hozzáférési csomagjait és erőforrás-hozzárendeléseit, és megtekintheti a kérelemnaplókat naplózási célokra vagy a felhasználó kérésének állapotának meghatározásához. Ez a cikk ismerteti, hogyan használhatja a jogosultságkezelési jelentések és az Azure AD naplózási naplók.

Az alábbi videóból megtudhatja, hogyan tekintheti meg, hogy a felhasználók milyen erőforrásokhoz férhetnek hozzá a jogosultságkezelésben:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Felhasználó hozzáférési csomagjainak megtekintése

Ez a jelentés lehetővé teszi a felhasználó által igényelhető összes hozzáférési csomag és a felhasználóhoz jelenleg hozzárendelt hozzáférési csomagok listáját.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

1. Kattintson **az Azure Active Directory,** majd **az Identitáscégcégcég**elemre.

1. A bal oldali menüben kattintson a **Jelentések parancsra.**

1. Kattintson **a felhasználó Access-csomagjai elemre.**

1. Kattintson **a Felhasználók kijelölése** gombra a Felhasználók kiválasztása ablaktábla megnyitásához.

1. Keresse meg a felhasználót a listában, majd kattintson a **Kijelölés gombra.**

    A **Kérheti** lap megjeleníti a felhasználó által igényelhető hozzáférési csomagok listáját. Ezt a listát a hozzáférési csomagokhoz definiált [kérelemházirendek](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) határozzák meg. 

    ![Felhasználói csomagok elérése](./media/entitlement-management-reports/access-packages-report.png)

1. Ha egy hozzáférési csomaghoz egynél több erőforrásszerepkör vagy házirend tartozik, a kijelölés részleteinek megtekintéséhez kattintson az erőforrás-szerepkörökre vagy házirend-bejegyzésre.

1. A **Hozzárendelt** fülre kattintva megtekintheti a felhasználóhoz jelenleg hozzárendelt hozzáférési csomagok listáját. Ha egy hozzáférési csomag hozzá van rendelve egy felhasználóhoz, az azt jelenti, hogy a felhasználó hozzáfér a hozzáférési csomag összes erőforrásszerepköréhez.

## <a name="view-resource-assignments-for-a-user"></a>Felhasználó erőforrás-hozzárendeléseinek megtekintése

Ez a jelentés lehetővé teszi a jogosultságkezelésben a felhasználóhoz jelenleg hozzárendelt erőforrások listázását. Ne feledje, hogy ez a jelentés a jogosultságkezeléssel kezelt erőforrásokra készült. Előfordulhat, hogy a felhasználó a jogosultságkezelésen kívül más erőforrásokhoz is hozzáférhet a címtárban.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

1. Kattintson **az Azure Active Directory,** majd **az Identitáscégcégcég**elemre.

1. A bal oldali menüben kattintson a **Jelentések parancsra.**

1. Kattintson **a felhasználó erőforrás-hozzárendelései**elemre.

1. Kattintson **a Felhasználók kijelölése** gombra a Felhasználók kiválasztása ablaktábla megnyitásához.

1. Keresse meg a felhasználót a listában, majd kattintson a **Kijelölés gombra.**

    Megjelenik a felhasználóhoz jelenleg hozzárendelt erőforrások listája. A lista azt a hozzáférési csomagot és házirendet is megjeleníti, amelytől az erőforrásszerepkört kapták, valamint a hozzáférés kezdési és befejezési dátumát.
    
    Ha egy felhasználó két vagy több csomagban fér hozzá ugyanahhoz az erőforráshoz, egy nyílra kattintva megtekintheti az egyes csomagokat és házirendeket.

    ![Felhasználó erőforrás-hozzárendelései](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>A felhasználó kérésének állapotának meghatározása

Ha további részleteket szeretne megtudni arról, hogy a felhasználó hogyan kért és kapott hozzáférést egy hozzáférési csomaghoz, használhatja az Azure AD naplózási naplóját. Különösen a naplórekordok és a `EntitlementManagement` `UserManagement` kategóriák segítségével további részleteket kaphat az egyes kérelmek feldolgozási lépéseiről.  

1. Kattintson **az Azure Active Directory,** majd **a Naplók naplózása parancsra.**

1. A lap tetején módosítsa a `EntitlementManagement` `UserManagement` **Kategóriát** vagy a , a keresett naplózási rekordtól függően.  

1. Kattintson az **Alkalmaz** gombra.

1. A naplók letöltéséhez kattintson a **Letöltés gombra.**

Amikor az Azure AD új kérelmet kap, naplórekordot ír, amelyben a `User requests access package assignment` **kategória** és `EntitlementManagement` a **tevékenység** általában.  Az Azure Portalon létrehozott közvetlen hozzárendelés esetén **Activity** a naplózási rekord `Administrator directly assigns user to access package`Tevékenység mezője a , és a hozzárendelést végző felhasználót az **ActorUserPrincipalName**azonosítja.

Az Azure AD további naplózási rekordokat fog írni, amíg a kérelem folyamatban van, beleértve a következőket:

| Kategória | Tevékenység | Kérelem állapota |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | A kérelemhez nincs szükség jóváhagyásra |
| `UserManagement` | `Create request approval` | A kérelem hez jóváhagyás szükséges |
| `UserManagement` | `Add approver to request approval` | A kérelem hez jóváhagyás szükséges |
| `EntitlementManagement` | `Approve access package assignment request` | Kérelem jóváhagyva |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |A kérelem jóváhagyva, vagy nem igényel jóváhagyást |

Amikor egy felhasználóhoz hozzáférés van rendelve, az Azure `EntitlementManagement` AD naplórekordot ír a **tevékenységgel** `Fulfill access package assignment`rendelkező kategóriához.  A hozzáférést kapó felhasználót az **ActorUserPrincipalName** mező azonosítja.

Ha a hozzáférés nincs hozzárendelve, majd az Azure `EntitlementManagement` AD `Deny access package assignment request`naplórekordot ír a **kategóriához a tevékenységgel,** ha a kérelmet egy jóváhagyó megtagadta, vagy `Access package assignment request timed out (no approver action taken)`ha a kérelem időtúlodott, mielőtt a jóváhagyó jóváhagyhatja.

Amikor a felhasználó hozzáférési csomag-hozzárendelése lejár, a felhasználó megszakítja vagy egy rendszergazda eltávolítja, majd `EntitlementManagement` az Azure `Remove access package assignment`AD naplórekordot ír a **kategóriához a tevékenységgel.**

## <a name="next-steps"></a>További lépések

- [Jelentések és naplók archiválása](entitlement-management-logs-and-reporting.md)
- [Az Azure AD-jogosultságok kezelésének hibaelhárítása](entitlement-management-troubleshoot.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
