---
title: "Bevezetés: Azure AD SSPR | Microsoft Docs"
description: "Tippek az Azure AD önkiszolgáló jelszóátállítás sikeres bevezetéséhez"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 4e2f788f4e4dfd013754925d8f6dbc3bf35b1a91
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének sikeres bevezetése

Az Azure Active Directory (Azure AD) önkiszolgáló jelszóváltoztatási (SSPR) funkciójának zökkenőmentes bevezetése érdekében a legtöbb ügyfél a következő lépéseket végzi el:

1. [Az új jelszavak kérésének engedélyezése a címtárban](active-directory-passwords-getting-started.md).
2. [A helyszíni Active Directory-engedélyek konfigurálása a jelszóvisszaíró számára](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Jelszóvisszaíró konfigurálása](active-directory-passwords-writeback.md#configure-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához.
4. [A szükséges licencek hozzárendelése és ellenőrzése](active-directory-passwords-licensing.md).
5. Annak meghatározása, hogy fokozatos bevezetést szeretne-e végezni. Ha az SSPR bevezetését fokozatosan szeretné elvégezni, a hozzáférést a felhasználók egy csoportjára korlátozhatja, így egy adott csoporttal kísérletezhet a programban. Ha egy adott csoportra szeretné korlátozni a bevezetést, állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Kiválasztva** értékre, majd válassza ki azt a biztonsági csoportot, amelynek engedélyezni szeretné az új jelszavak kérését. 
6. Töltse ki a felhasználók regisztrálásához szükséges [hitelesítési adatokat](active-directory-passwords-data.md), például az irodai telefonszámukat, a mobiltelefonszámukat és a másodlagos e-mail-címüket.
7. [Az Azure AD bejelentkezési felület testreszabása a vállalat márkajelzésének megjelenítésével](active-directory-passwords-customize.md).
8. Az SSPR használatának megtanítása a felhasználóinak. Útmutatást küldhet nekik arról, hogyan regisztrálhatnak, és hogyan állíthatják át a jelszavukat.
9. A regisztráció kényszerítésének meghatározása. Bármikor dönthet úgy, hogy kényszeríti a regisztrációt. Azt is megkövetelheti a felhasználóktól, hogy bizonyos idő elteltével újra erősítsék meg a hitelesítési adataikat.
10. A jelentéskészítő képesség használata. Áttekintheti a felhasználók regisztrációra vonatkozó és használati adatait az [Azure AD által nyújtott jelentéskészítő képességgel](active-directory-passwords-reporting.md).
11. Az új jelszó kérésének engedélyezése. Amikor elkészült, engedélyezze az új jelszó kérését az összes felhasználónak: állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Mindenki** értékre. 
12. [Az új jelszó kérésének engedélyezése a Windows 10-felhasználók számára a bejelentkezési képernyőn](active-directory-passwords-login.md).

   > [!IMPORTANT]
   > Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](active-directory-passwords-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="email-based-rollout"></a>E-mailes alapú bevezetés

Számos ügyfél szerint egy egyszerűen követhető útmutatással ellátott e-mailes kampánnyal lehet a legkönnyebben rávenni a felhasználókat az SSPR használatára. [Három egyszerű e-mailt hoztunk létre, amelyeket sablonként használhat a bevezetés során](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16):

* **Hamarosan elérhető**: Olyan e-mail-sablon, amelyet a bevezetést megelőző hetekben vagy napokban érdemes használni, hogy a felhasználók tisztában legyenek azzal, hogy hamarosan tenniük kell valamit.
* **Már elérhető**: Olyan e-mail-sablon, amelyet a program indulásának napján érdemes használni, hogy a felhasználók regisztráljanak, és megerősítsék a hitelesítési adataikat. Ha a felhasználók ilyenkor regisztrálnak, szükség esetén már használatba tudják venni az SSPR-t.
* **Regisztrációs emlékeztető**: Olyan e-mail-sablon, amelyet pár nappal vagy héttel az üzembe helyezés után érdemes használni, hogy a felhasználók emlékeztetőt kapjanak a regisztrációról és hitelesítési adataik megerősítéséről.

![E-mail][Email]

## <a name="create-your-own-password-portal"></a>Saját jelszókezelő portál létrehozása

Sok ügyfél dönt webhelyek üzemeltetése és gyökérszintű DNS-bejegyzések létrehozása mellett. Például: https://jelszo.contoso.com. Ezután feltöltik az oldalt a következő információk hivatkozásaival:

* [Azure AD új jelszó létrehozására szolgáló portál](https://aka.ms/sspr)
* [Azure AD új jelszó létrehozására szolgáló regisztrációs portál](http://aka.ms/ssprsetup)
* [Azure AD jelszóátállítási portál](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* A szervezetre jellemző egyéb információk

A kiküldött e-mailekben vagy közleményekben feltüntethet egy márkaüzenettel ellátott, könnyen megjegyezhető URL-címet, amelyre a felhasználók ellátogathatnak, amikor szükségük van a szolgáltatásokra. Létrehoztunk Önnek egy [új jelszó létrehozására szolgáló mintaoldalt](https://github.com/ajamess/password-reset-page), amelyet a szervezet igényei szerint használhat és szabhat testre.

## <a name="use-enforced-registration"></a>Kényszerített regisztráció használata

Ha azt szeretné, hogy a felhasználók regisztráljanak a jelszóátállításra, ezt megkövetelheti, amikor bejelentkeznek az Azure AD használatával. Ezt a beállítást a címtár **Jelszóátállítás** panelén engedélyezheti úgy, hogy a **Regisztráció** panelen engedélyezi a **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** beállítást.

A rendszergazdák megkövetelhetik a felhasználóktól az újbóli regisztrációt egy bizonyos idő elteltével. **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését** értéket 0 és 730 nap között állíthatják be.

Miután engedélyezi ezt a beállítást, a bejelentkező felhasználókat egy üzenet fogadja majd, miszerint a rendszergazda a hitelesítési adataik érvényesítésére kéri őket.

## <a name="populate-authentication-data"></a>Hitelesítési adatok feltöltése

Érdemes [feltöltenie a felhasználók hitelesítési adatait](active-directory-passwords-data.md). Így a felhasználóknak az SSPR használatához nem lesz kötelező regisztrálniuk a jelszóátállításra. Mindaddig, amíg a felhasználók hitelesítési adatai meg vannak adva, és megfelelnek az Ön által definiált jelszóátállítási szabályzatban foglaltaknak, a felhasználók átállíthatják a jelszavaikat.

## <a name="disable-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszóátállítás letiltása egyszerű feladat. Nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása** > **Tulajdonságok** elemet, majd válassza a **Senki** elemet az **Önkiszolgáló jelszóátállítás engedélyezve** rész alatt.

## <a name="next-steps"></a>Következő lépések

* [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "E-mail-sablonok testreszabása a szervezeti követelményeknek megfelelően"
