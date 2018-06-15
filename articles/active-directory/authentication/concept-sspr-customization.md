---
title: Az önkiszolgáló jelszó-átállítási testreszabási - Azure Active Directoryban
description: Az Azure AD az önkiszolgáló jelszó-testreszabási beállítások visszaállítása
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: ffd12d03dffb5deafc8605cc7352bd71d588d235
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866736"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Az önkiszolgáló jelszóváltoztatás az Azure AD funkciók testre szabása

Informatikai szakemberek számára, akik az önkiszolgáló jelszó-változtatási (SSPR) telepíteni kívánja az Azure Active directory (Azure AD) a felhasználói élmény a felhasználók igényeinek megfelelően testre szabhatja.

## <a name="customize-the-contact-your-administrator-link"></a>"Forduljon a rendszergazdához" hivatkozásra testreszabása

Akkor is, ha az önkiszolgáló jelszó-Változtatási nincs engedélyezve, a felhasználók továbbra is fennáll "Forduljon a rendszergazdához" hivatkozásra kattintva a jelszó-visszaállítási portál. Ha a felhasználó megadja a hivatkozás akkor vagy:
   * E-mailt küld a rendszergazdák, és megkéri, ha segítségre van szüksége a jelszó módosítása. 
   * A felhasználóknak küld egy URL-címet, adja meg, ha segítségre van szüksége. 

Azt javasoljuk, hogy ez a kapcsolat egy például a felhasználók által már használt támogatási kérdéseket tesz fel e-mail cím vagy webhely.

![Contact][Contact]

Az ügyfél e-mailt küld a következő címzettek, az alábbi sorrendben:

1. Ha a **jelszókezelő** szerepkör hozzá van rendelve, ezt a szerepkört a rendszergazda értesítést kap.
2. Ha nincs jelszókezelők jelszavát állíthatják rendelt, majd a rendszergazda a **felhasználó rendszergazda** szerepkör értesítést kap.
3. Ha az előző szerepkörök egyike sem van hozzárendelve, akkor a **globális rendszergazdák** értesítés jelenik meg.

Minden esetben értesítést kap egy legfeljebb 100 címzetteknek.

A másik, rendszergazdai szerepkörök és rendeljen hozzájuk kapcsolatban további információért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>"Forduljon a rendszergazdához" e-mailek letiltása

Ha a szervezet nem szeretné értesíteni a rendszergazdákat a jelszó alaphelyzetbe állítása a kérelmeket, engedélyezheti a következő konfigurációt:

* Az önkiszolgáló jelszó-változtatási minden végfelhasználói engedélyezése. Ez a beállítás akkor a **jelszó-átállítási** > **tulajdonságok**.
  
  Ha szeretné a felhasználók visszaállíthassák a saját jelszavukat, hatókörét megadhatja a üres csoport elérésére. *Ez a beállítás nem ajánlott.*
* Testre szabhatja a segélyszolgálat hivatkozás egy webes URL-címe vagy mailto: címet, amely a felhasználók segítségével kérhet segítséget. Ez a beállítás akkor a **jelszó-átállítási** > **testreszabási** > **egyéni segélyszolgálat e-mail vagy URL-címe**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Az AD FS bejelentkezési oldalára sspr testreszabása

Active Directory összevonási szolgáltatások (AD FS) a rendszergazdák használatával adhat hozzá egy hivatkozást a bejelentkezési oldalon található útmutatás a [bejelentkezésilap-leírás hozzáadása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) cikk.

Az AD FS bejelentkezési oldalára hivatkozás hozzáadásához használja a következő parancsot az AD FS-kiszolgálón. Felhasználók is ezen a lapon adja meg az önkiszolgáló jelszó-Változtatási munkafolyamat.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Testre szabhatja a bejelentkezési oldalon és a hozzáférési panel megjelenését és működését

A bejelentkezési oldal testreszabható. Hozzáadhat egy embléma akkor jelenik meg, amely megfelel a vállalati védjegyadatoknak a lemezképpel együtt.

A képek, úgy dönt, a következő körülmények között láthatók:

* Miután a felhasználó adja meg a felhasználónevét
* Ha a felhasználó fér hozzá az egyéni URL-cím:
    * Úgy, hogy a *whr* paramétert a jelszó alaphelyzetbe állítása lap, például "https://login.microsoftonline.com/?whr=contoso.com"
    * Úgy, hogy a *felhasználónév* paramétert a jelszó alaphelyzetbe állítása lap, például "https://login.microsoftonline.com/?username=admin@contoso.com"

Vállalati arculat megjelenítése a cikkben konfigurálásával kapcsolatos részletes keresését [vállalati arculat megjelenítése a bejelentkezési oldalra, hogy az Azure AD](../customize-branding.md).

### <a name="directory-name"></a>Címtár neve

Módosíthatja a name attribútuma alatt **Azure Active Directory** > **tulajdonságok**. Egy rövid szervezet neve, amely látható a portálon, és az automatizált kommunikáció jelenítheti meg. Ez a beállítás esetén a legjobban az automatikus e-mailek, az alábbi formában látható:

* Az e-mailben, például "Microsoft nevében CONTOSO bemutató" rövid neve
* Az e-mailben, például "CONTOSO bemutató fiók e-mail ellenőrző kód" tárgyát

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "A rendszergazda kérjen segítséget a jelszó e-mail példa alaphelyzetbe állítása"
