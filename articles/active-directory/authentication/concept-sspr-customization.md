---
title: Testreszabása az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-testreszabási beállítások alaphelyzetbe állítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a77c6ce205c40b5814f9b26f9099d868d434d3ce
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316449"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Az Azure AD-funkciókról, önkiszolgáló jelszó-visszaállítás testreszabása

Informatikai szakemberek számára, akik önkiszolgáló jelszó-visszaállítás (SSPR) telepíteni szeretné az Azure Active directory (Azure AD) a élmény a felhasználók igényeinek megfelelően testre szabhatja.

## <a name="customize-the-contact-your-administrator-link"></a>A "Forduljon a rendszergazdához" hivatkozást testreszabása

Akkor is, ha az SSPR nincs engedélyezve, a felhasználók továbbra is rendelkezik egy "Forduljon a rendszergazdához" hivatkozást a jelszó-visszaállítási portál. Ha egy felhasználó kijelöli ezt a hivatkozást, vagy:

   * E-mailt küld a rendszergazdákkal, és megkéri, ha segítségre van szüksége a felhasználó jelszavának módosítása.
   * A felhasználók küld egy Ön által megadott segítségért URL-címet.

Azt javasoljuk, hogy állítsa a kapcsolattartó valami például egy e-mail címre vagy webhelyre, hogy a felhasználók már használnak.

![Contact][Contact]

Az ügyfél e-mailt küld a következő címzetteknek, a következő sorrendben:

1. Ha a **jelszókezelő** szerepkör hozzá van rendelve, ezzel a szerepkörrel rendelkező rendszergazdák értesítést kap.
2. Ha nincs jelszókezelők hozzá van rendelve, majd a rendszergazdák a **felhasználói rendszergazdája** szerepkör értesítést kap.
3. Ha az előző szerepkörök egyike sem hozzá van rendelve, akkor a **globális rendszergazdák** értesítést kap.

Minden esetben értesítést kap egy legfeljebb 100 címzettek.

Tudjon meg többet a különböző rendszergazdai szerepkörökről és való hozzárendelésének módját, tekintse meg [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>"Forduljon a rendszergazdához" e-mailek letiltása

Ha a szervezet nem szeretné, értesítse a rendszergazdákat a jelszó-átállítási kérelmet, engedélyezheti a következő konfigurációt:

* Engedélyezze az önkiszolgáló jelszó-visszaállítás összes végfelhasználók számára. Ez a beállítás akkor alatt **új jelszó kérésére vonatkozó** > **tulajdonságok**. Ha nem szeretné, hogy a felhasználók visszaállíthassák a saját jelszavukat, gazdagépcsoportjaira üres csoport elérését. *Ezt a beállítást nem ajánlott.*
* Egy webes URL-címe vagy mailto az ügyfélszolgálati hivatkozás testreszabása: címet, amelyen a felhasználók juthat. Ez a beállítás akkor alatt **új jelszó kérésére vonatkozó** > **testreszabási** > **egyéni ügyfélszolgálati e-mail cím vagy URL-cím**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Az AD FS bejelentkezési oldal testreszabása az SSPR

Az Active Directory összevonási szolgáltatások (AD FS) a rendszergazdák használatával adhat hozzá egy hivatkozást bejelentkezési oldalra az található útmutatót az [Hozzáadás bejelentkezésilap-leírást](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) cikk.

Az AD FS bejelentkezési oldalára hivatkozás hozzáadásához használja a következő parancsot az AD FS-kiszolgálóra. Felhasználók is ezen a lapon adja meg az SSPR-munkafolyamat.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Bejelentkezési oldal és a hozzáférési panel megjelenésének és testreszabása

A bejelentkezési oldal testreszabható. Hozzáadhat egy embléma jelenik meg, valamint a lemezképet, amely megfelel saját céges védjegy megadásával.

A grafikus úgy dönt, a következő körülmények között jelennek meg:

* Miután a felhasználó beírja a felhasználóneve
* Ha a felhasználó hozzáfér a testre szabott URL-címe:
    * Tartalmában való böngészéshez illessze a `whr` paramétert a jelszó-visszaállítási oldalra, mint `https://login.microsoftonline.com/?whr=contoso.com`
    * Tartalmában való böngészéshez illessze a `username` paramétert a jelszó-visszaállítási oldalra, mint `https://login.microsoftonline.com/?username=admin@contoso.com`

A konfigurálása a vállalati arculat megjelenítése a cikkben talál [vállalati arculat megjelenítése a bejelentkezési oldal az Azure ad-ben](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Címtár neve

A könyvtár neve attribútum alapján módosíthatja **Azure Active Directory** > **tulajdonságok**. Egy rövid szervezetnevet, amely látható a portálon, és az automatikus kommunikáció jelenítheti meg. Ez a lehetőség akkor a legjobban az automatikus e-mailek, a megjelenő űrlapokat látható:

* Az e-mailben, például "a Microsoft nevében a CONTOSO bemutató" rövid neve
* A Tárgy sorában az e-mailben, például "CONTOSO bemutató fiókjának e-mail ellenőrző kódja"

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

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Kérje a rendszergazda segítségét alaphelyzetbe a jelszót e-mail példa"
