---
title: "Testreszabása: Az Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó-testreszabási beállítások visszaállítása"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Az önkiszolgáló jelszóváltoztatás az Azure AD funkciók testre szabása

Informatikai szakemberek számára, akik az önkiszolgáló jelszó-változtatási (SSPR) telepíteni kívánja az Azure Active directory (Azure AD) a felhasználói élmény a felhasználók igényeinek megfelelően testre szabhatja.

## <a name="customize-the-contact-your-administrator-link"></a>"Forduljon a rendszergazdához" hivatkozásra testreszabása

Akkor is, ha az önkiszolgáló jelszó-Változtatási nincs engedélyezve, a felhasználók továbbra is fennáll "Forduljon a rendszergazdához" hivatkozásra kattintva a jelszó-visszaállítási portál. Ha a felhasználó megadja a hivatkozás akkor vagy:
   * E-mailt küld a rendszergazdák, és megkéri, ha segítségre van szüksége a jelszó módosítása. 
   * A felhasználóknak küld egy URL-címet, adja meg, ha segítségre van szüksége. 

Azt javasoljuk, hogy ez a kapcsolat egy például a felhasználók által már használt támogatási kérdéseket tesz fel e-mail cím vagy webhely.

![Ügyfél][Contact]

Az ügyfél e-mailt küld a következő címzettek, az alábbi sorrendben:

1. Ha a **jelszókezelő** szerepkör hozzá van rendelve, ezt a szerepkört a rendszergazda értesítést kap.
2. Ha nincs jelszókezelők jelszavát állíthatják rendelt, majd a rendszergazda a **felhasználó rendszergazda** szerepkör értesítést kap.
3. Ha az előző szerepkörök egyike sem van hozzárendelve, akkor a **globális rendszergazdák** értesítés jelenik meg.

Minden esetben értesítést kap egy legfeljebb 100 címzetteknek.

A másik, rendszergazdai szerepkörök és rendeljen hozzájuk kapcsolatban további információért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

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
    * Úgy, hogy a *whr* paraméter a jelszót alaphelyzetbe állítani a lap, például a "https://login.microsoftonline.com/?whr=contoso.com"
    * Úgy, hogy a *felhasználónév* paramétert a jelszó alaphelyzetbe állítása lap, például "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Grafikus részletek

A következő beállítások segítségével módosíthatja a bejelentkezési oldal visual jellemzőit. Ugrás a **Azure Active Directory** > **vállalati arculat** > **Szerkesztés vállalati arculat megjelenítése**:

* A bejelentkezési oldal lemezképet egy .png vagy .jpg formátumú fájlnak kell lennie 1420 x 1200 képpont, és nem lehet nagyobb 500 KB-nál. A legjobb eredmények elérése érdekében javasoljuk, hogy folyamatosan körülbelül 200 KB.
* A bejelentkezési oldal háttérszíne használatakor a nagy késleltetésű kapcsolat, és hexadecimális formátumú RGB kell lennie.
* A szalagcím lemezképet egy .png vagy .jpg fájlt, 60 x 280 képpont legyen, és lehet nagyobb, mint 10 KB.
* A négyszögletű embléma (normál és a sötét téma) .png vagy .jpg formátumú fájlnak, 240 x 240 (méretezhető) képpont, és nem lehet 10 KB-nál nagyobb kell lennie.

### <a name="sign-in-text-options"></a>Bejelentkezési beállítások

A következő beállítások segítségével szöveg hozzáadása a bejelentkezési oldal, amely a szervezet számára fontos. Ugrás a **Azure Active Directory** > **vállalati arculat** > **Szerkesztés vállalati arculat megjelenítése**:

* **Felhasználói név mutató**: Példa szövegét a felváltja  *someone@example.com*  lépésnél a felhasználók jobban megfelelő. Azt javasoljuk, hogy hagyja az alapértelmezett mutatót, amikor belső és külső felhasználók támogatására.
* **Bejelentkezési oldal szövege**: legfeljebb 256 karakter hosszúságú lehet. Ez a szöveg tetszőleges helyre a felhasználói bejelentkezés online és az Azure AD munkahelyi csatlakoztatás élményt nyújt a Windows 10 jelenik meg. Használja a következő szöveget a feltételek használatát, a utasításokat és a felhasználók számára. 

   >[!IMPORTANT]
   >Bárki, aki láthatja a bejelentkezési oldal, így bizalmas információk itt nem ad meg.
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>A "Me bejelentkezve megtartása letiltva" beállítás

Az a **bejelentkezve szeretnék maradni tiltva** lehetőségnél felhasználók maradjanak az aláírt zárja be és nyissa meg a böngészőablakban. Ez a beállítás nincs hatással a munkamenetek élettartamát. Ugrás a **Azure Active Directory** > **vállalati arculat** > **Szerkesztés vállalati arculat megjelenítése**.

Office 2010 és SharePoint Online néhány funkciójának jelölje be a jelölőnégyzetet, hogy a felhasználók egy függőségi rendelkezik. Ha elrejti ezt a beállítást, a felhasználók további és váratlan bejelentkezési kérések kérheti le.

### <a name="directory-name"></a>Könyvtár neve

Módosíthatja a name attribútuma alatt **Azure Active Directory** > **tulajdonságok**. Egy rövid szervezet neve, amely látható a portálon, és az automatizált kommunikáció jelenítheti meg. Ez a beállítás esetén a legjobban az automatikus e-mailek, az alábbi formában látható:

* Az e-mailben, például "Microsoft nevében CONTOSO bemutató" rövid neve
* Az e-mailben, például "CONTOSO bemutató fiók e-mail ellenőrző kód" tárgyát

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
* [Licencelési kérdése van?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "A rendszergazda kérjen segítséget a jelszó e-mail példa alaphelyzetbe állítása"
