---
title: Önkiszolgáló jelszó-visszaállítás testreszabása – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítás testreszabási lehetőségei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979461"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Az Azure AD funkció testreszabása az önkiszolgáló jelszó-visszaállításhoz

Azok az informatikai szakemberek, akik önkiszolgáló jelszó-visszaállítást (SSPR) szeretnének telepíteni az Azure Active Directoryban (Azure AD), testreszabhatják a felhasználói élményt, hogy megfeleljenek a felhasználói knak.

## <a name="customize-the-contact-your-administrator-link"></a>A "Kapcsolatfelvétel a rendszergazdával" hivatkozás testreszabása

Az önkiszolgáló jelszó-visszaállítási felhasználók számára elérhető a jelszó-visszaállítási portálon elérhető "Kapcsolatfelvétel a rendszergazdával" hivatkozás. Ha egy felhasználó ezt a hivatkozást választja, két dolgot fog tenni:

* Ha az alapértelmezett állapotban marad:
   * A rendszer e-mailt küld a rendszergazdáknak, és megkéri őket, hogy adjanak segítséget a felhasználó jelszavának módosításához. Lásd az alábbi [minta e-mailt.](#sample-email)
* Ha testre szabták:
   * Elküldi a felhasználót a rendszergazda által megadott weboldalra vagy e-mail címre segítségért.

> [!TIP]
> Ha ezt testreszabja, javasoljuk, hogy ezt olyan ra kvanva, amit a felhasználók már ismernek a támogatás hoz.

> [!WARNING]
> Ha ezt a beállítást olyan e-mail címmel és fiókkal szabja testre, amely jelszó-visszaállítást igényel, előfordulhat, hogy a felhasználó nem tud segítséget kérni.

### <a name="sample-email"></a>Minta e-mail

![Mintakérelem a rendszergazdának küldött e-mailek alaphelyzetbe állításához][Contact]

A kapcsolattartó e-mail címe a következő címzetteknek kerül elküldésre a következő sorrendben:

1. Ha az **ügyfélszolgálati rendszergazdai** szerepkör vagy **jelszó-rendszergazdai** szerepkör van hozzárendelve, az ilyen szerepkörrel rendelkező rendszergazdák értesítést kapnak.
1. Ha nincs helpdesk rendszergazda vagy jelszó-rendszergazda hozzárendelve, akkor a **rendszergazdai** szerepkörrel rendelkező rendszergazdák értesítést kapnak.
1. Ha az előző szerepkörök egyike sincs hozzárendelve, a rendszer értesíti a **globális rendszergazdákat.**

Minden esetben legfeljebb 100 címzettet értesítenek.

Ha többet szeretne megtudni a különböző rendszergazdai szerepkörökről és hozzárendelésükről, olvassa el [a Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakört.](../users-groups-roles/directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>A "Kapcsolatfelvétel a rendszergazdával" e-mailek letiltása

Ha a szervezet nem szeretne értesítést küldeni a rendszergazdáknak a jelszó-visszaállítási kérelmekről, a következő konfigurációt engedélyezheti:

* Engedélyezze az önkiszolgáló jelszó-visszaállítást az összes végfelhasználó számára. Ez a beállítás a **Jelszó-visszaállítás** > **tulajdonságai csoportban található.** Ha nem szeretné, hogy a felhasználók alaphelyzetbe állítsák saját jelszavukat, hatókör-hozzáférést biztosíthat egy üres csoporthoz. *Nem javasoljuk ezt a lehetőséget.*
* Testreszabhatja az helpdesk hivatkozást, hogy webes URL-t vagy mailto-címet adjon meg: címet, amelyet a felhasználók a segítség hez használhatnak. Ez a beállítás a **Jelszó visszaállítása** > **testreszabás egyéni** > **helpdesk e-mail vagy URL**csoportban található.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Az AD FS bejelentkezési lapjának testreszabása az SSPR-hez

Az Active Directory összevonási szolgáltatások (AD FS) rendszergazdái a [Bejelentkezési lap leírásának hozzáadása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) című cikkben található útmutatás segítségével adhatnak hozzá hivatkozást a bejelentkezési lap hoz.

Az AD FS bejelentkezési lapra mutató hivatkozás hozzáadásához használja a következő parancsot az AD FS-kiszolgálón. A felhasználók ezen a lapon adhatnak meg az SSPR-munkafolyamatot.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>A bejelentkezési lap és a hozzáférési panel megjelenésének és megjelenésének testreszabása

Testreszabhatja a bejelentkezési lapot. Hozzáadhat egy emblémát, amely a vállalati márkajelzéshez illeszkedő képpel együtt jelenik meg.

A kiválasztott grafikák a következő körülmények között jelennek meg:

* Miután a felhasználó megadta a felhasználónevét
* Ha a felhasználó hozzáfér a testreszabott URL-címhez:
   * A paraméter `whr` nek a jelszó-visszaállítási oldalra való átadva, például`https://login.microsoftonline.com/?whr=contoso.com`
   * A paraméter `username` nek a jelszó-visszaállítási oldalra való átadva, például`https://login.microsoftonline.com/?username=admin@contoso.com`

A vállalati márkajelzés konfigurálásának részleteiről a [Vállalati márkajelzés hozzáadása a bejelentkezési laphoz az Azure AD-ben](../fundamentals/customize-branding.md)című cikkben olvashat részletesen.

### <a name="directory-name"></a>Könyvtár neve

A könyvtárnév attribútumot az **Azure Active Directory** > tulajdonságai csoportban**módosíthatja.** Megjelenítheti a portálon és az automatizált kommunikációban látható rövid szervezetnevet. Ez a beállítás a következő űrlapokon az automatikus e-mailekben a leglátványosabb:

* A rövid név az e-mailben, például "Microsoft nevében CONTOSO demo"
* Az e-mail tárgysora, például "CONTOSO demo account email verification code"

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "A jelszó-e-mail alaphelyzetbe állításával kapcsolatban kérjen segítséget a rendszergazdától"
