---
title: A B2B meghívó e-mail elemei - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B együttműködési meghívó e-mail sablonja
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768371"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>A B2B együttműködési meghívó e-mail elemei – Azure Active Directory

Meghívó e-mailek kritikus eleme, hogy a partnerek a fedélzeten, mint B2B együttműködési felhasználók az Azure AD-ben. Ezek segítségével növelheti a címzett bizalmát. legitimitást és közösségi bizonyítékot adhat az e-mailhez, hogy a címzett jól érezze magát az **Első lépések** gomb kiválasztásával a meghívás elfogadásához. Ez a bizalom kulcsfontosságú eszköz a megosztási súrlódás csökkentéséhez. És azt is szeretné, hogy az e-mail jól néz ki!

![Képernyőkép a B2B meghívó e-mailcímről](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Az e-mail magyarázata
Nézzünk meg néhány elemet az e-mail, így tudja, hogyan lehet a legjobban használni a képességeiket.

### <a name="subject"></a>Tárgy
Az e-mail tárgya a következő mintát követi: A &lt;bérlőnév&gt; szervezet meghívására vagyunk meghívva

### <a name="from-address"></a>Cím
LinkedIn-szerű mintát használunk a From címhez.  Tisztában kell lennie azzal, hogy ki a meghívó és melyik vállalattól, és tisztáznia kell, hogy az e-mail egy Microsoft e-mail címről érkezik. A formátum a következő: &lt;Microsoft Invitations&gt; <invites@microsoft.com> vagy&gt; Display name <invites@microsoft.com>of inviter from &lt;tenantname (via Microsoft) .

### <a name="reply-to"></a>Válasz
A válasz-e-mail a meghívó e-mailcímére van beállítva, ha elérhető, így az e-mailre adott válasz e-mailt küld a meghívónak.

### <a name="branding"></a>Védjegyezés
A bérlőtől érkező meghívóe-mailek a bérlőhöz beállított vállalati márkajelzést használják. Ha ki szeretné használni ezt a lehetőséget, az [alábbiakban](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) a konfigurálásának részleteit találja. A szalagcím embléma megjelenik az e-mailben. A legjobb eredmény [érdekében](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) kövesse a képméretre és a minőségre vonatkozó utasításokat. Ezenkívül a cégnév is megjelenik a cselekvésre való felhívásban.

### <a name="call-to-action"></a>Cselekvésre való felhívás
A cselekvésre való felhívás két részből áll: annak magyarázatából, hogy a címzett miért kapta meg az e-mailt, és hogy a címzettet miért kérik fel rá.
- A "miért" szakasz a következő mintával címezhető: Meghívást kapott &lt;a&gt; bérlőnév szervezet alkalmazásainak elérésére

- A "mit kérnek tőled" szakaszt pedig az **Első lépések** gomb jelenléte jelzi. Ha a címzettet meghívók nélkül adta hozzá, ez a gomb nem jelenik meg.

### <a name="inviters-information"></a>A meghívó adatai
A meghívó megjelenítendő neve szerepel az e-mailben. Emellett ha beállított egy profilképet az Azure AD-fiókjához, a meghívó e-mail is tartalmazni fogja ezt a képet. Mindkettő célja, hogy növelje a címzett bizalmát az e-mailben.

Ha még nem állítottad be a profilképedet, egy ikon jelenik meg, amelynek helyén a meghívó monogramja látható a kép helyett:

  ![Képernyőkép a meghívóról a meghívó monogramjával](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Törzs
A törzs tartalmazza azt az üzenetet, amelyet a meghívó akkor állít össze, amikor [vendégfelhasználót hív meg a címtárba, csoportba vagy alkalmazásba,](add-users-administrator.md) vagy [a meghívó API használatával.](customize-invitation-api.md) Ez egy szövegterület, ezért biztonsági okokból nem dolgozza fel a HTML-címkéket.

  ![Képernyőkép a meghívó e-mail törzséről](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Lábléc szakasz
A lábléc tartalmazza a Microsoft vállalati márkáját, és tudatja a címzettel, hogy az e-mailt nem figyelt aliasból küldték-e. 

Különleges esetek:

- A meghívó nem rendelkezik e-mail címmel a meghívó bérleti szerződésben

  ![Képernyőkép, ha egy meghívó nem rendelkezik e-maillel a meghívó bérleti szerződésben](media/invitation-email-elements/inviter-no-email.png)


- A címzettnek nem kell beváltania a meghívót

  ![Képernyőkép, ha a címzettnek nem kell beváltania a meghívót](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Hogyan határozzák meg a nyelvet?
A meghívó e-mailben a vendégfelhasználó számára bemutatott nyelvet a következő beállítások határozzák meg. Ezek a beállítások a lista sorrendben jelennek meg. Ha egy beállítás nincs konfigurálva, a lista következő beállítása határozza meg a nyelvet. 
- A [meghívottUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) objektum **messageLanguage** tulajdonsága, ha a Meghívás létrehozása API-t használja
-   A vendég [felhasználói objektumában](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) megadott **preferredLanguage** tulajdonság
-   Az **értesítési nyelv** a vendégfelhasználó otthoni bérlőjének tulajdonságaiban van beállítva (csak Az Azure AD-bérlők számára)
-   Az **értesítési nyelv** az erőforrás-bérlő tulajdonságaiban

Ha egyik beállítás sincs konfigurálva, a nyelv alapértelmezés szerint az angol (US) lesz.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az Azure AD B2B együttműködés](what-is-b2b.md)
- [Hogyan adhatnak hozzá Az Azure Active Directory rendszergazdái B2B együttműködési felhasználókat?](add-users-administrator.md)
- [Hogyan adják hozzá az informatikai dolgozók a B2B együttműködési felhasználókat?](add-users-information-worker.md)
- [B2B együttműködési meghívás visszaváltás](redemption-experience.md)
- [B2B együttműködési felhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)
