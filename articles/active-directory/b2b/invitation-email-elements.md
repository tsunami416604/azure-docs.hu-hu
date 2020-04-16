---
title: A B2B meghívó e-mail elemei - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B együttműködési meghívó e-mail sablonja
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407220"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>A B2B együttműködési meghívó e-mail elemei – Azure Active Directory

Meghívó e-mailek kritikus eleme, hogy a partnerek a fedélzeten, mint B2B együttműködési felhasználók az Azure AD-ben. Bár nem [szükséges e-mailt küldeni e-mailt, hogy meghívjon valakit a B2B együttműködés használatával,](add-user-without-invite.md)ezzel a felhasználónak minden szükséges információt megad a felhasználónak ahhoz, hogy döntést hozzon arról, hogy elfogadja-e a meghívást. Ez is ad nekik egy linket, hogy mindig hivatkozhat a jövőben, amikor vissza kell térniük a forrásokat.

![Képernyőkép a B2B meghívó e-mailcímről](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Ez az új e-mail sablon még mindig az összes bérlő számára elérhető, így egyes bérlők továbbra is egy régebbi design. 2020. május végéig az összes bérlő től érkező meghívók ezt a sablont fogják használni.

## <a name="explaining-the-email"></a>Az e-mail magyarázata

Nézzünk meg néhány elemet az e-mail, így tudja, hogyan lehet a legjobban használni a képességeiket.

### <a name="subject"></a>Tárgy

Az e-mail tárgya ezt a mintát követi:

&lt;felhasználónév&gt; arra kérte, hogy hozzáférjen a szervezeten belüli alkalmazásokhoz.

### <a name="from-address"></a>Cím

LinkedIn-szerű mintát használunk a From címhez. Ez a minta egyértelművé kell invites@microsoft.comtennie, hogy bár az e-mail származik, a meghívás egy másik szervezet. A formátum a következő: Microsoft <invites@microsoft.com> Invitations &lt;vagy&gt; <invites@microsoft.com>Microsoft invitations a tenantname nevében. 

### <a name="reply-to"></a>Válasz

A válasz-e-mail a meghívó e-mailcímére van beállítva, ha elérhető, így az e-mailre adott válasz e-mailt küld a meghívónak.

### <a name="phishing-warning"></a>Adathalászatra vonatkozó figyelmeztetés

Az e-mail egy rövid figyelmeztetéssel kezdődik a felhasználó számára az adathalászatról, és figyelmezteti őket, hogy csak a várt meghívókat fogadják el. Ez jó gyakorlat, hogy megbizonyosodjon arról, hogy a partnerek meghívja nem lesz meglepve a meghívást megemlíti, hogy nekik előre.

![Az adathalászatra vonatkozó figyelmeztetés képe az e-mailben](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>A meghívó adatai

Az e-mail információkat tartalmaz a meghívóról és arról a szervezetről, amelytől a meghívót küldik. Ez magában foglalja a feladó nevét és e-mail címét, valamint a szervezethez társított nevet és elsődleges tartományt. Mindezek az információk segítik a meghívottat abban, hogy megalapozott döntést hozzanak a meghívás elfogadásáról.

![A meghívó információinak képe az e-mailben](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Meghívó üzenet

Ha a meghívó a meghívó részeként tartalmaz üzenetet, amikor [vendégfelhasználót hív meg a címtárba, csoportba vagy alkalmazásba,](add-users-administrator.md) vagy amikor [a meghívó API-t használja,](customize-invitation-api.md)az üzenet az e-mail fő részében lesz kiemelve. A meghívó neve és profilképe is benne van, ha beállított egyet. Maga az üzenet egy szövegterület, ezért biztonsági okokból nem dolgozza fel a HTML-címkéket.

![A meghívóüzenet képe az e-mailben](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Az Elfogadás gomb és az URL átirányítása

Az e-mail következő része információkat tartalmaz arról, hogy a meghívott hol kerül sor a meghívás elfogadása után, valamint egy gombot.  A jövőben a meghívott mindig használhatja ezt a hivatkozást, hogy visszatérjen az erőforrásokhoz közvetlenül.

![Az elfogadás gomb képe és az URL átirányítása az e-mailben](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Lábléc szakasz

A lábléc további információt tartalmaz az elküldött meghívóról. Mindig van egy lehetőség a meghívott, hogy blokkolja a jövőbeli meghívókat. Ha a szervezet [adatvédelmi nyilatkozatot állított be,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)a nyilatkozatra mutató hivatkozás itt jelenik meg.  Ellenkező esetben a megjegyzés azt jelzi, hogy a szervezet nem állított be adatvédelmi nyilatkozatot.

![Az e-mail láblécszakaszának képe](media/invitation-email-elements/footer-section.png)
 
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
