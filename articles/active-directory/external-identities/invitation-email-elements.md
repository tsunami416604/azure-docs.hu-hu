---
title: A B2B meghívó e-mail-Azure Active Directory elemei | Microsoft Docs
description: Azure Active Directory B2B Collaboration meghívó e-mail sablon
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
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909335"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>A B2B együttműködés meghívójának e-mail-Azure Active Directory elemei

A meghívó e-mail-címe kritikus fontosságú összetevő, amely VÁLLALATKÖZI együttműködési felhasználókként szolgál az Azure AD-ben. Habár [nem szükséges, hogy e-mailt küldjön a B2B-együttműködéssel való meghívás meghívására](add-user-without-invite.md), így a felhasználónak minden szükséges információt meg kell hoznia arról, hogy elfogadják-e a meghívót. Egy hivatkozást is biztosít nekik, hogy a jövőben mindig hivatkozhatnak arra, amikor vissza kell térniük az erőforrásokhoz.

![A B2B meghívót tartalmazó e-mailt bemutató képernyőfelvétel](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Ez az új e-mail-sablon továbbra is minden bérlő számára elérhető, így egyes bérlők továbbra is régebbi kialakítást használnak. A 2020. május végére az összes bérlő meghívója ezt a sablont fogja használni.

## <a name="explaining-the-email"></a>Az e-mail ismertetése

Tekintsük át az e-mailek néhány elemét, hogy tudja, hogyan használják legjobban a képességeiket.

### <a name="subject"></a>Tárgy

Az e-mail tárgya a következő mintát követi:

&lt;a Felhasználónév &gt; meghívja Önt a szervezeten belüli alkalmazások elérésére.

### <a name="from-address"></a>Feladó címe

A feladó címe esetében a LinkedIn-hez hasonló mintát használjuk. Ennek a mintának egyértelművé kell tennie, hogy bár az e-mail származik invites@microsoft.com , a meghívás egy másik szervezettől származik. A formátum a következő: Microsoft meghívások  <invites@microsoft.com> vagy Microsoft meghívások a tenantname nevében &lt; &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Válasz címzettje

Ha elérhető, a Válaszcím e-mail-címére van beállítva, hogy az e-mailben küldött e-mailt küldjön vissza a meghívónak.

### <a name="phishing-warning"></a>Adatlopási figyelmeztetés

Az e-mail rövid figyelmeztetést küld a felhasználónak az adathalászatról, figyelmezteti őket, hogy csak a várt meghívókat fogadják el. Célszerű meggyőződni arról, hogy a meghívott partnereknek nem kell megemlíteniük a meghívót, ha előre megemlíti őket.

![Az e-mailben szereplő adathalászati figyelmeztetés képe](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Meghívó adatai

Az e-mail tartalmazza a meghívót és a meghívót küldő szervezet adatait. Ide tartozik a küldő neve és e-mail-címe, valamint a szervezethez társított név és elsődleges tartomány. Az összes információnak segítenie kell a meghívást, hogy tájékozott döntést hozzon a meghívó elfogadásáról.

![A meghívó információinak képe az e-mailben](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Meghívó üzenet

Ha a meghívó tartalmaz egy üzenetet a meghívásuk részeként, amikor [vendég felhasználót nyit meg a címtárban, a csoportban vagy az alkalmazásban](add-users-administrator.md) , vagy amikor a meghívó API-t [használják](customize-invitation-api.md), az üzenet kiemelve jelenik meg az e-mail fő részében. Emellett a meghívó neve és a profil képe is szerepel, ha már be lettek állítva. Az üzenet maga egy szöveges felület, ezért biztonsági okokból nem dolgozza fel a HTML-címkéket.

![A meghívót tartalmazó üzenet képe az e-mailben](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Elfogadás gomb és átirányítási URL-cím

Az e-mail következő szakasza információt tartalmaz arról, hogy a meghívást Mikor fogadja el a meghívót, valamint egy gombot.  A jövőben a meghívás mindig használhatja ezt a hivatkozást, hogy közvetlenül visszatérjen az erőforrásaihoz.

![Az elfogadás gomb és az átirányítási URL-cím ábrázolása az e-mailben](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Lábléc szakasz

A lábléc további információkat tartalmaz az elküldött meghívóról. Mindig lehetőség van a meghívásra, hogy letiltsa a jövőbeli meghívókat. Ha a szervezet [adatvédelmi nyilatkozatot állított be](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), itt jelennek meg az utasításra mutató hivatkozás.  Ellenkező esetben egy megjegyzés azt jelzi, hogy a szervezet nem állított be adatvédelmi nyilatkozatot.

![Az e-mail lábléc szakaszának képe](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>A nyelv meghatározása

A meghívó e-mailben a vendég felhasználó számára megadott nyelvet a következő beállítások határozzák meg. Ezek a Beállítások elsőbbségi sorrendben vannak felsorolva. Ha a beállítás nincs konfigurálva, a listában a következő beállítás határozza meg a nyelvet.

- A [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) objektum **messageLanguage** tulajdonsága, ha a Create meghívás API használatban van
-   A vendég [felhasználói objektumában](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) megadott **preferredLanguage** tulajdonság
-   A vendég felhasználó otthoni bérlője (csak az Azure AD-bérlők) tulajdonságaiban beállított **értesítési nyelv**
-   Az erőforrás-bérlő tulajdonságaiban beállított **értesítési nyelv**

Ha a beállítások egyike sincs konfigurálva, a nyelv alapértelmezett értéke az angol (US).

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Hogyan vehetik Azure Active Directory a rendszergazdák a B2B együttműködési felhasználókat?](add-users-administrator.md)
- [Hogyan vehetik fel az Information Worker a B2B együttműködési felhasználókat?](add-users-information-worker.md)
- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
- [B2B Collaboration-felhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)
