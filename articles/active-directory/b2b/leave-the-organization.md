---
title: Egy szervezetet vendégfelhasználóként egy – Azure Active Directory |} Microsoft Docs
description: Bemutatja, hogyan egy Azure AD B2B Vendég felhasználó elhagyja a szervezetek a hozzáférési panelen.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3415989cb6cec68f95e7d317671c31ba9bd231e9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267437"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Egy szervezetet vendégfelhasználóként egy

Egy Azure Active Directory (Azure AD) B2B Vendég felhasználó dönt, hogy egy szervezet bármikor hagyja, ha már nincs szükségük használják az alkalmazásokat a szervezet vagy karbantartása van társítva. A felhasználó hagyhatja a szervezetek saját maguk, anélkül, hogy a rendszergazda segítségére.

## <a name="leave-an-organization"></a>Egy szervezetet

Egy szervezet, mint a bejelentkezett felhasználó, hogy a [hozzáférési Panel](https://myapps.microsoft.com), tegye a következőket:

1. Ha van már nincs bejelentkezve a szervezet számára, hogy elhagyja, a jobb felső sarokban válassza ki a nevét, majd kattintson a elhagyja a szervezet.
2. A jobb felső sarkában válassza ki a nevét.
3. A **szervezetek**, válassza a beállítások (fogaskerék) ikonra.
 
   ![A hozzáférési Panel felhasználói beállításait ábrázoló képernyőfelvétel](media/leave-the-organization/UserSettings.png) 

3. A **szervezetek**, szervezet, amely hagyja, és válassza ki a kívánt található **szervezetet**.

   ![A felhasználói felületen hagyja szervezet pontját megjelenítő képernyőkép](media/leave-the-organization/LeaveOrg.png)

4. Amikor a rendszer rákérdez, válassza ki a **hagyja**. 

## <a name="account-removal"></a>Fiók eltávolítása

Amikor egy felhasználó elhagyja a szervezet, a felhasználói fiók "törölték" a címtárban. Alapértelmezés szerint a user objektum áthelyezése a **törölt felhasználók** terület az Azure ad-ben azonban nincs véglegesen törölve 30 napig. A helyreállítható törlés lehetővé teszi, hogy a rendszergazdához, és állítsa vissza a felhasználói fiók (beleértve a csoportok és engedélyek), ha a felhasználó egy kérést a fiók visszaállítása a 30 napos időszakon belül.

Igény szerint egy Bérlői rendszergazda véglegesen törölheti a fiók a 30 napos időszak alatt. Ehhez tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **Azure Active Directory**.
2. A **kezelése**, jelölje be **felhasználók**.
3. Válassza ki **törölt felhasználók**.
4. A törölt felhasználói melletti jelölőnégyzetet, majd válassza ki és **véglegesen törli**.

Ha a felhasználó véglegesen törli ezt a műveletet nem módosítható.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

- Azure AD B2B áttekintését lásd: [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)



