---
title: Az elemek a B2B együttműködés meghívó e-mail - Azure Active Directory |} Microsoft Docs
description: Az Azure Active Directory B2B együttműködés meghívó e-mail sablon
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e8285779154914bd09513c057d8e5ae0b6388831
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>A B2B együttműködés meghívó e-mail - Azure Active Directory elemei

Meghívót e-mailek a board partnerek átveendő az Azure AD B2B együttműködés felhasználóként kritikus összetevője. A címzett megbízhatósági növeléséhez használhatja őket. hozzáadhat érvényességét, és az e-mailt, győződjön meg arról, hogy a címzett közösségi igazolás érzi, válassza a Feladatkezelő a **Ismerkedés** gombra kattintva fogadja el a meghívást. Ebben a megbízhatósági kapcsolatban a kulcs azt jelenti, hogy megosztási súrlódás csökkentése érdekében. És is szeretne az e-mailt remekül!

![Az Azure AD B2b meghívó e-mail](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Ismertető az e-mailben
Vizsgáljuk meg az e-mailt néhány elemeinek így megtudhatja, hogyan lehet a legjobban azok képességeinek használatához.

### <a name="subject"></a>Tárgy
Az e-mail tárgyát a következő mintát követi: meghívjuk az &lt;tenantname&gt; szervezet

### <a name="from-address"></a>Feladó címe
A LinkedIn-szerű minta a feladó címe az használjuk.  Legyen törölje a jelet a meghívó, aki és amely a vállalati és is elmagyarázza, hogy az e-mailt származik-e a Microsoft e-mail cím. A formátum: &lt;megjelenített nevével meghívó&gt; a &lt;tenantname&gt; (keresztül Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Válasz címzettje
A válaszcím e-mailben a meghívó e-mail érhető el, ha van beállítva, hogy a megválaszolása az e-mailt küld egy e-mailt vissza a meghívó.

### <a name="branding"></a>Védjegyek
A meghívó e-maileket a bérlő használatát a vállalati arculat megjelenítése, amikor előfordulhat, hogy állította be a bérlő számára. Ha azt szeretné, hogy ez a funkció előnyeit [Itt](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) a konfigurálásának részletes adatai. A szalagcím emblémájának jelenik meg az e-mailben. Hajtsa végre a lemezkép mérete és minőségi utasítások [Itt](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) a legjobb eredmények elérése érdekében. Emellett a vállalat nevét is megjelennek a művelet hívása.

### <a name="call-to-action"></a>A művelet hívása
A művelet hívása két részből áll: a címzett miért kapott a levelek, és mi a címzett alatt kapcsolatba arról foglalja össze.
- A "Miért" szakaszt a következő minta használatával lehet megoldani:, hogy meghívott hozzáférés az alkalmazásokhoz a &lt;tenantname&gt; szervezet

- És a "Mi alatt megkérdezi ehhez" szakasz jelenlétét jelzi a **Ismerkedés** gombra. Ha a címzett meghívókat szükségessége nélkül hozzá lett adva, erre a gombra kattintva nem jelenik meg.

### <a name="inviters-information"></a>A meghívó a információk
A meghívó megjelenített név szerepel az e-mailt. És emellett, ha beállította az Azure AD-fiókot a profilkép, hívja fel az e-mailt fogja tartalmazni, valamint, hogy a kép. Mindkét célja, hogy növelje a címzett abban, hogy az e-mailben.

Ha még nem állított profilkép, a meghívó monogramja a kép helyett egy ikon jelenik meg:

  ![a meghívó monogramja megjelenítése](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Törzs
Az üzenet, amely a meghívó composes, vagy a meghívó API keresztül törzsében. Így azt nem dolgozza fel az HTML-címkék biztonsági okokból egy területre.

### <a name="footer-section"></a>Élőláb szakasz
A lábléc tartalmazza a Microsoft vállalatának arculatát, és lehetővé teszi, hogy a címzett tudja, ha az e-mailben küldött egy nem figyelt aliast. Bizonyos esetekben:

- A meghívó nem rendelkezik e-mail címmel hívja fel a bérlet

  ![Meghívó képe nem rendelkezik e-mail címmel hívja fel a bérlet](media/invitation-email-elements/inviter-no-email.png)


- A címzett nem szükséges a meghívó beváltása

  ![Ha a címzett meghívó beváltani nem szükséges](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés, tekintse meg a következő cikkeket:

- [Mi az az Azure AD B2B együttműködés](what-is-b2b.md)
- [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](add-users-administrator.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](add-users-information-worker.md)
- [B2B együttműködés meghívó érvényesítési](redemption-experience.md)
- [Adja hozzá a B2B együttműködés felhasználók nélkül](add-user-without-invite.md)
