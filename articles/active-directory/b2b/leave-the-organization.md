---
title: Szervezet kilépése vendégfelhasználóként – Azure Active Directory
description: Bemutatja, hogy egy Azure AD B2B vendégfelhasználó hogyan hagyhatja el a szervezetet a Hozzáférési panel használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272498"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Kilépés a szervezetből vendégfelhasználóként

Az Azure Active Directory (Azure AD) B2B-beli b2B-vendégfelhasználó bármikor dönthet úgy, hogy kilép egy szervezetből, ha már nem kell használnia az adott szervezettől származó alkalmazásokat, vagy fenntartani a társítást. A felhasználó saját maga hagyhatja el a szervezetet anélkül, hogy kapcsolatba kellene lépnie a rendszergazdával.

> [!NOTE]
> A vendégfelhasználó nem hagyhatja el a szervezetet, ha a fiók le van tiltva az otthoni bérlő ben vagy az erőforrás-bérlőben. Ha a fiók le van tiltva, a vendégfelhasználónak kapcsolatba kell lépnie a bérlői rendszergazdával, aki törölheti a vendégfiókot, vagy engedélyezheti a vendégfiókot, hogy a felhasználó elhagyhassa a szervezetet.

## <a name="leave-an-organization"></a>Szervezet elhagyása

Ha ki szeretne lépni egy szervezetből, kövesse az alábbi lépéseket.

1. Nyissa meg a Hozzáférési panel profillapját az alábbi lépések egyikével:
   
   - Az [Azure Portalon](https://portal.azure.com)kattintson a nevére a jobb felső sarokban, és válassza a **Fiók megtekintése**lehetőséget.
   - Nyissa meg a [hozzáférési panelt,](https://myapps.microsoft.com)kattintson a nevére a jobb felső sarokban, és a **Szervezetek**csoport ban válassza a beállítások ikonját (fogaskerék).
 
   ![Képernyőkép a felhasználói beállításokról a Hozzáférési panelen](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Ha még nem jelentkezett be a kilépő szervezetbe, a **Szervezetek**csoportban kattintson a Bejelentkezés gombra a szervezet neve melletti **szervezethivatkozás elhagyásához.** Miután bejelentkezett, kattintson ismét a nevére a jobb felső sarokban és a **Szervezetek**csoportban, és válassza a beállítások ikonját (fogaskerék).

3. A **Szervezetek**csoportban keresse meg a kilépni kívánt szervezetet, és válassza a Kilépés a **szervezetből**lehetőséget.

   ![Képernyőkép a Szervezet elhagyása beállításról a felhasználói felületen](media/leave-the-organization/LeaveOrg.png)

4. Amikor a rendszer a megerősítést kéri, válassza a **Kilépés**lehetőséget. 

## <a name="account-removal"></a>Fiók eltávolítása

Amikor egy felhasználó kilép egy szervezetből, a felhasználói fiók "helyreállíthatóan törlődik" a címtárban. Alapértelmezés szerint a felhasználói objektum az Azure AD **Törölt felhasználók** területén, de nem véglegesen törlődik 30 napig. Ez a lágy törlés lehetővé teszi a rendszergazda számára a felhasználói fiók visszaállítását (beleértve a csoportokat és az engedélyeket is), ha a felhasználó a 30 napos időszakon belül kéri a fiók visszaállítását.

Ha szükséges, a bérlői rendszergazda véglegesen törölheti a fiókot a 30 napos időszak alatt bármikor. Ehhez tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Azure Active Directory**lehetőséget.
2. A **Kezelés** alatt válassza a **Felhasználókat**.
3. Válassza **a Törölt felhasználók lehetőséget**.
4. Jelölje be a törölt felhasználó melletti jelölőnégyzetet, majd a **Törlés véglegesen**lehetőséget.

Ha véglegesen töröl egy felhasználót, ez a művelet visszavonhatatlan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>További lépések

- Az Azure AD B2B áttekintését a [Mi az Azure AD B2B együttműködés?](what-is-b2b.md)



