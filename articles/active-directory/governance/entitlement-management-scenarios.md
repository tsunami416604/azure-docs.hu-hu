---
title: Gyakori forgatókönyvek a jogosultságkezelésben – Azure AD
description: Ismerje meg a magas szintű lépéseket kell követnie a gyakori forgatókönyvek az Azure Active Directory jogosultságkezelés.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190551"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében

A jogosultságkezelés számos módon konfigurálható a szervezet számára. Ha azonban csak most kezdi el, érdemes megérteni a rendszergazdák, a katalógustulajdonosok, a hozzáférés-csomagkezelők, a jóváhagyók és a kérelmezők gyakori forgatókönyveit.

## <a name="delegate"></a>Delegált

### <a name="administrator-delegate-management-of-resources"></a>Rendszergazda: Erőforrások kezelésének delegálása

1. [Videó megtekintése: Küldöttség az informatikai részlegvezetőnek](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Felhasználók delegálása a katalóguskészítői szerepkörbe](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalógus létrehozója: Erőforrások kezelésének delegálása

- [Új katalógus létrehozása](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalógus tulajdonosa: Erőforrások kezelésének delegálása

1. [Társtulajdonosok hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Erőforrások hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalógus tulajdonosa: Hozzáférés-csomagok kezelésének delegálása

1. [Videó megtekintése: Delegálás a katalógus tulajdonosától a csomagkezelő eléréséhez](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Felhasználók delegálása a csomagkezelői szerepkörhöz](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>A szervezet felhasználóinak hozzáférésének szabályozása

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Access csomagkezelő: A szervezet alkalmazottai nak hozzáférést kérhetnek az erőforrásokhoz

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, Teams-ek, alkalmazások vagy SharePoint-webhelyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-create.md#resource-roles)
1. [Kérési házirend hozzáadása, amely lehetővé teszi a címtár felhasználói számára a hozzáférés kérelmezéséhez](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Lejárati beállítások megadása](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Kérelmező: Hozzáférés kérése az erőforrásokhoz

1. [Bejelentkezés a Saját Hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hozzáférési csomag keresése
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: Erőforrásokra vonatkozó kérelmek jóváhagyása

1. [Kérelem megnyitása a Saját Hozzáférés portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Kérelmező: Azoknak az erőforrásoknak a megtekintése, amelyekhez már hozzáfért

1. [Bejelentkezés a Saját Hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="govern-access-for-users-outside-your-organization"></a>A szervezeten kívüli felhasználók hozzáférésének szabályozása

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Rendszergazda: Együttműködés külső partnerszervezettel

1. [Olvassa el, hogyan működik az access külső felhasználók számára](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Külső felhasználók beállításainak áttekintése](entitlement-management-external-users.md#settings-for-external-users)
1. [Kapcsolat hozzáadása a külső szervezethez](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access csomagkezelő: Együttműködés külső partnerszervezettel

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, Teams-ek, alkalmazások vagy SharePoint-webhelyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Kérési házirend hozzáadása, amely lehetővé teszi a címtárban nem lévő felhasználók számára a hozzáférés kérelmezéséhez](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Lejárati beállítások megadása](entitlement-management-access-package-create.md#lifecycle)
1. [A hivatkozás másolása a hozzáférési csomag kéréséhez](entitlement-management-access-package-settings.md)
1. A külső partnerpartnerének küldött hivatkozás elküldése a felhasználókkal való megosztáshoz

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Kérelmező: Hozzáférés kérése az erőforrásokhoz külső felhasználóként

1. A partnertől kapott hozzáférési csomag hivatkozásának megkeresése
1. [Bejelentkezés a Saját Hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: Erőforrásokra vonatkozó kérelmek jóváhagyása

1. [Kérelem megnyitása a Saját Hozzáférés portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Kérelmező: Megtekintheti azerőforrásokat, amelyekhez már hozzáfér

1. [Bejelentkezés a Saját Hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="day-to-day-management"></a>Napi irányítás

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Access csomagkezelő: Projekt erőforrásainak frissítése

1. [Videó megtekintése: Napi menedzsment: A dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. A hozzáférési csomag megnyitása
1. [Csoportok, Teams-ek, alkalmazások vagy SharePoint-webhelyek hozzáadása és eltávolítása](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Access csomagkezelő: Projekt időtartamának frissítése

1. [Videó megtekintése: Napi menedzsment: A dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. A hozzáférési csomag megnyitása
1. [Az életciklus-beállítások megnyitása](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Az elévülési beállítások frissítése](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Access csomagkezelő: A hozzáférés projekthez való jóváhagyásának frissítése

1. [Videó megtekintése: Napi menedzsment: A dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Meglévő kérelem- és jóváhagyási házirend megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [A jóváhagyási beállítások frissítése](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Access csomagkezelő: A projekt hez készült személyek frissítése

1. [Videó megtekintése: Napi menedzsment: A dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [A már nem szükséges hozzáférésre szoruló felhasználók eltávolítása](entitlement-management-access-package-assignments.md)
1. [Meglévő kérelem- és jóváhagyási házirend megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Hozzáférésre szoruló felhasználók hozzáadása](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Access csomagkezelő: Adott felhasználók közvetlen hozzárendelése egy hozzáférési csomaghoz

1. [Ha a felhasználóknak különböző életciklus-beállításokra van szükségük, adjon hozzá egy új házirendet a hozzáférési csomaghoz](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Adott felhasználók közvetlen hozzárendelése a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Hozzárendelések és jelentések

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Rendszergazda: Annak megtekintése, hogy kinek van hozzárendelése egy hozzáférési csomaghoz

1. Hozzáférési csomag megnyitása
1. [Hozzárendelések megtekintése](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Jelentések és naplók archiválása](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Rendszergazda: A felhasználókhoz rendelt erőforrások megtekintése

1. [Felhasználó hozzáférési csomagjainak megtekintése](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Felhasználó erőforrás-hozzárendeléseinek megtekintése](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Automatizált adminisztráció

A Microsoft Graph segítségével hozzáférési csomagokat, katalógusokat, házirendeket, kéréseket és hozzárendeléseket is kezelhet.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó megfelelő szerepkörben hívhatja meg a [jogosultságkezelési API-t.](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)

## <a name="next-steps"></a>További lépések

- [Delegálás és szerepkörök](entitlement-management-delegate.md)
- [Folyamat- és e-mail-értesítések kérése](entitlement-management-process.md)
