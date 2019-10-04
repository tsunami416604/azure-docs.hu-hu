---
title: Az Azure AD önkiszolgáló jelszó-visszaállítás testreszabása – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítás testreszabási lehetőségei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527dd99f122ec70cc47305947a5cbce3207b9664
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666303"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Az Azure AD funkcióinak testreszabása az önkiszolgáló jelszó-visszaállításhoz

Az önkiszolgáló jelszó-visszaállítást (SSPR) az Azure Active Directoryban (Azure AD) használó informatikai szakemberek testre szabhatják a felhasználói igényeknek megfelelő élményt.

## <a name="customize-the-contact-your-administrator-link"></a>A "Kapcsolatfelvétel a rendszergazdával" hivatkozás testreszabása

Az önkiszolgáló jelszó-visszaállítási felhasználóknak a jelszó-visszaállítási portálon elérhető "Kapcsolatfelvétel a rendszergazdával" hivatkozással. Ha a felhasználó kiválasztja ezt a hivatkozást, akkor a következő két dolog egyike lesz:

* Ha az alapértelmezett állapotban marad:
   * A rendszer e-mailt küld a rendszergazdának, és kéri, hogy nyújtson segítséget a felhasználó jelszavának módosításához. Tekintse meg a [minta e-mail-](#sample-email) címét.
* Ha testre van szabva:
   * Elküldi a felhasználónak egy weboldalt vagy e-mail-címet, amelyet a rendszergazda megad a segítségért.

> [!TIP]
> Ha ezt testreszabja, azt javasoljuk, hogy ezt a beállítást olyan felhasználók számára ajánljuk, akik már ismerik a támogatást

> [!WARNING]
> Ha ezt a beállítást egy olyan e-mail-címmel és fiókkal testreszabja, amelynek jelszó-visszaállításra van szüksége, akkor előfordulhat, hogy a felhasználó nem tud segítséget kérni.

### <a name="sample-email"></a>Minta e-mail

![Példa a rendszergazdának küldött e-mailek visszaállítására][Contact]

A kapcsolattartási e-mail küldése a következő címzetteknek történik a következő sorrendben:

1. Ha a **jelszó-rendszergazdai** szerepkör hozzá van rendelve, a rendszer értesítést küld az ezzel a szerepkörrel rendelkező rendszergazdáknak.
2. Ha nincs jelszó-rendszergazda társítva, a rendszer értesítést küld a rendszergazdáknak a **felhasználó rendszergazdai** szerepkörével.
3. Ha az előző szerepkörök egyike sincs hozzárendelve, akkor a **globális rendszergazdák** értesítést kapnak.

Minden esetben legfeljebb 100 címzett kap értesítést.

Ha többet szeretne megtudni a különböző rendszergazdai szerepkörökről és azok hozzárendeléséről, tekintse meg a [rendszergazdai szerepkörök kiosztása a Azure Active Directory-ben](../users-groups-roles/directory-assign-admin-roles.md)című témakört.

### <a name="disable-contact-your-administrator-emails"></a>A "Kapcsolatfelvétel a rendszergazdával" e-mailek letiltása

Ha a szervezet nem szeretné értesíteni a rendszergazdákat a jelszó-visszaállítási kérelmekről, akkor a következő konfigurációt engedélyezheti:

* Az összes végfelhasználó számára engedélyezze az önkiszolgáló jelszó-visszaállítást. Ez a beállítás a **jelszó-visszaállítás** > **tulajdonságai**területen található. Ha nem szeretné, hogy a felhasználók a saját jelszavukat állítsa alaphelyzetbe, akkor egy üres csoporthoz is hozzáférhet. *Ez a beállítás nem ajánlott.*
* Testreszabhatja az ügyfélszolgálati hivatkozást egy webes URL-cím vagy mailto: cím megadásához, amellyel a felhasználók segítséget kérhetnek. Ez a beállítás a **jelszó-visszaállítás** > **testreszabása** > **Egyéni segélyszolgálat e-mail-címe vagy URL-címe**alatt található.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>A SSPR AD FS bejelentkezési oldalának testreszabása

Active Directory összevonási szolgáltatások (AD FS) (AD FS) a rendszergazdák hozzáadhatnak egy hivatkozást a bejelentkezési lapjához a [bejelentkezési oldal hozzáadása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) című cikkben található útmutatás alapján.

A AD FS bejelentkezési oldalára mutató hivatkozás hozzáadásához használja a következő parancsot a AD FS-kiszolgálón. A felhasználók ezt a lapot használhatják a SSPR munkafolyamat megadására.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>A bejelentkezési oldal és a hozzáférési panel megjelenésének és működésének testreszabása

A bejelentkezési oldal testreszabható. Hozzáadhat egy emblémát, amely a cég arculatának megfelelő képpel együtt jelenik meg.

A kiválasztott grafikák a következő esetekben jelennek meg:

* Miután a felhasználó beírja a felhasználónevét
* Ha a felhasználó a testreszabott URL-címet éri el:
   * A `whr` paraméternek a jelszó-visszaállítási oldalra való átadásával, például:`https://login.microsoftonline.com/?whr=contoso.com`
   * A `username` paraméternek a jelszó-visszaállítási oldalra való átadásával, például:`https://login.microsoftonline.com/?username=admin@contoso.com`

A vállalat arculatának konfigurálásáról a [vállalati arculat hozzáadása a bejelentkezési laphoz az Azure ad](../fundamentals/customize-branding.md)-ben című cikkben talál további információt.

### <a name="directory-name"></a>Címtár neve

A címtár neve attribútumot a **Azure Active Directory** > **Tulajdonságok**területen módosíthatja. Megjelenítheti a Portálon és az automatikus kommunikációban látható, felhasználóbarát szervezet nevét. Ez a lehetőség a következő űrlapokon látható automatikus e-mailek közül a legkönnyebben látható:

* A felhasználóbarát név az e-mailben, például "a Microsoft neve a CONTOSO demo nevében"
* Az e-mailben szereplő tárgyi sor, például "CONTOSO demo-fiók e-mail-ellenőrző kódja"

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Segítségért forduljon a rendszergazdához, és kérje a jelszó-e-mail-cím alaphelyzetbe állítását"
