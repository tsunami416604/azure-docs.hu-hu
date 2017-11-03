---
title: "Testreszabás: Azure AD SSPR |} Microsoft Docs"
description: "Önkiszolgáló jelszóváltoztatás szolgáltatás az Azure AD beállítások testreszabása"
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
ms.openlocfilehash: 030fb1d87547a4fc78d54a855bca961202f28837
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Az Azure AD-funkciók testreszabása az önkiszolgáló jelszó-változtatási

Informatikai szakemberek számára kívánja telepíteni az önkiszolgáló jelszóváltoztatás testre szabhatja a felhasználói élmény a felhasználók kereséséhez.

## <a name="customize-the-contact-your-administrator-link"></a>Az ügyfél a rendszergazda hivatkozás testreszabása

Akkor is, ha az önkiszolgáló jelszó-Változtatási nincs engedélyezve a felhasználók továbbra is egy "forduljon a rendszergazdához" hivatkozásra a jelszó-visszaállítási portál.  Ez a hivatkozás e-mailt küld a rendszergazdák, ha segítségre van szüksége a módosítása a jelszó kérése, vagy a felhasználók küld egy URL-címet. Azt javasoljuk, hogy ez egy például e-mail cím vagy webhely, amely segítségével a felhasználók a támogatáshoz használja.

![Ügyfél][Contact]

Az e-mailt küld a következő címzettek, az alábbi sorrendben:

1. Ha a **jelszókezelő** szerepkör van hozzárendelve, akkor kapnak értesítést, ezzel a szerepkörrel rendelkező rendszergazdák
2. Ha nincs jelszókezelők jelszavát állíthatják rendelt, majd a rendszergazda a **felhasználó rendszergazda** szerepkör értesítést kap
3. Ha az előző szerepkörök egyike sem volt rendelve, majd **globális rendszergazdák** értesítést kap

Minden esetben értesítést kap egy legfeljebb 100 címzetteknek.

További információk a másik, rendszergazdai szerepkörök és való hozzárendelésének módját lásd: a dokumentum [rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>Tiltsa le kérje a rendszergazda e-mailek

Ha a szervezet nem szeretné a rendszergazdák, értesítse a jelszó alaphelyzetbe állítása kérelmek, a következő konfigurációs engedélyezhető-e

* Az önkiszolgáló jelszó-változtatási minden végfelhasználói engedélyezése. Ez a beállítás akkor a **jelszó-átállítási > Tulajdonságok**.
    * Ha nem szeretné a felhasználók visszaállíthassák a saját jelszavukat, inkább üres csoportot való hozzáférés hatókörét megadhatja **ezt a beállítást nem ajánlott**.
* Testre szabhatja a segélyszolgálat hivatkozás egy webes URL-címe vagy mailto: címet, amely a felhasználók segítségével kérhet segítséget. Ez a beállítás akkor a **jelszó-átállítási > Testreszabás > egyéni segélyszolgálat e-mail vagy URL-címe**.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>Sspr az AD FS bejelentkezési oldal testreszabása

Az AD FS-rendszergazdák egy hivatkozást a bejelentkezési lapon található a cikkben szereplő útmutatások segítségével adhat hozzá [bejelentkezésilap-leírás hozzáadása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Az AD FS-kiszolgálón a következő paranccsal hozzáad egy hivatkozást a az AD FS bejelentkezési oldalra, így a felhasználók az önkiszolgáló jelszó alaphelyzetbe állítása munkafolyamat közvetlenül.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Bejelentkezés és a hozzáférési panel megjelenését és működését testreszabása

Amikor a felhasználók hozzáférnek a bejelentkezési oldal, testre szabhatja az embléma a vállalati arculat megjelenítése a bejelentkezési oldal kép együtt megjelenő.

Ezek a képek jelennek meg a következő esetekben:

* A felhasználó után meg kell adnia a felhasználóneve
* Felhasználó fér hozzá az egyéni URL-címe
    * Úgy, hogy a "whr" paramétert a jelszó alaphelyzetbe állítása lap, például a "https://login.microsoftonline.com/?whr=contoso.com"
    * Úgy, hogy a "felhasználónév" paramétert a jelszó alaphelyzetbe állítása lap, például "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Grafikus részletek

A következő beállítások lehetővé teszik a bejelentkezési oldal visual jellemzőit, és alatt található **Azure Active Directory**, **vállalati arculat**, **Szerkesztés vállalati arculat megjelenítése**

* Bejelentkezési lapot a lemezkép PNG, JPG vagy fájl 1420 x 1200 képpont, és nem lehet nagyobb 500KB-nál. Azt javasoljuk, hogy a legjobb eredmények elérése érdekében körülbelül 200 KB lehet.
* Bejelentkezési oldal háttérszíne használatakor a nagy késleltetésű kapcsolat, és a RGB hexadecimális formátumban kell lennie.
* Transzparens lemezkép PNG, JPG vagy fájl 60 x 280 képpont, és nem lehet nagyobb, mint 10 KB.
* Négyzetes embléma (normál és a sötét téma) PNG vagy JPG 240 x 240 (méretezhető) nem lehet nagyobb 10 KB.

### <a name="sign-in-text-options"></a>Bejelentkezési beállítások

A következő beállítások lehetővé teszik a szöveges szeretne hozzáadni a bejelentkezési laphoz a szervezet szempontjából. Ezek a beállítások alatt található **Azure Active Directory**, **vállalati arculat**, **Szerkesztés vállalati arculat megjelenítése**

* **Felhasználói név mutató** , például szöveg someone@example.com valami jobban megfelelő, a felhasználók számára, az ajánlott alapértelmezett maradhat, ha a belső és külső felhasználók
* **Bejelentkezési oldal szövege** legfeljebb 256 karakter hosszúságú. Ez a szöveg jelenik meg tetszőleges helyre a felhasználók bejelentkezési online, és az Azure AD Join élményt nyújt a Windows 10. Használja ezt a szöveget használati feltételeinek, utasításokat, és tippek a felhasználók számára. **Bárki, aki a bejelentkezési oldal, nem adja meg a bizalmas információk itt tekintheti meg.**

### <a name="keep-me-signed-in-disabled"></a>A Bejelentkezve szeretnék maradni beállítás le van tiltva

A "Me bejelentkezés le van tiltva megőrzése" beállítással felhasználók továbbra is, ha azok zárja be és nyissa meg a böngészőablakban bejelentkezett. Ez a beállítás nem befolyásolja a munkamenet élettartama. Ez a beállítás alatt található **Azure Active Directory > Vállalati arculat > Szerkesztés Védjegyadatok**.

Néhány funkciójának Office 2010 és SharePoint online-hoz a felhasználók ezt a jelölőnégyzetet tudni függőség rendelkezik. Elrejti ezt a beállítást, ha a felhasználók kaphat további és váratlan bejelentkezési megjelenő utasításokat.

### <a name="directory-name"></a>Könyvtár neve

Módosíthatja a name attribútum alapján **Azure Active Directory > Tulajdonságok** egy rövid szervezet neve, a portál és az automatikus kommunikáció látható megjelenítéséhez. Ez a beállítás akkor legjobban látható az űrlapokon, hajtsa végre az automatikus e-mailek formájában

* E-mailben "CONTOSO bemutató nevében Microsoft" felhasználóbarát neve
* E-mailben "CONTOSO bemutató fiók e-mail ellenőrző kód" tárgy

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Licencelés kérdése van?](active-directory-passwords-licensing.md)
* [Milyen adatok SSPR használja, és milyen adatokat kell tölteni a felhasználók számára?](active-directory-passwords-data.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik a házirend-beállításokban az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-policy.md)
* [A jelszóvisszaírás és miért fontos információk?](active-directory-passwords-writeback.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "A rendszergazda kérjen segítséget a jelszó e-mail példa alaphelyzetbe állítása"