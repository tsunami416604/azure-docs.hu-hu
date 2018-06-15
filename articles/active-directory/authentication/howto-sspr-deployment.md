---
title: Üzembe helyezési útmutató új jelszó kérésére vonatkozó önkiszolgáló folyamathoz – Azure Active Directory
description: Tippek az Azure AD önkiszolgáló jelszóátállítás sikeres bevezetéséhez
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 4d3e07c6c395645ef34b1707f33a4e37a20bf05d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866983"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének sikeres bevezetése

Az Azure Active Directory (Azure AD) önkiszolgáló jelszóváltoztatási (SSPR) funkciójának zökkenőmentes bevezetése érdekében a legtöbb ügyfél a következő lépéseket végzi el:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. [Az új jelszavak kérésének engedélyezése a címtárban](quickstart-sspr.md).
2. [A helyszíni Active Directory-engedélyek konfigurálása a jelszóvisszaíró számára](howto-sspr-writeback.md#active-directory-permissions).
3. [Jelszóvisszaíró konfigurálása](howto-sspr-writeback.md#configure-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához.
4. [A szükséges licencek hozzárendelése és ellenőrzése](concept-sspr-licensing.md).
5. Annak meghatározása, hogy fokozatos bevezetést szeretne-e végezni. Ha az SSPR bevezetését fokozatosan szeretné elvégezni, a hozzáférést a felhasználók egy csoportjára korlátozhatja, így egy adott csoporttal kísérletezhet a programban. Ha egy adott csoportra szeretné korlátozni a bevezetést, állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Kiválasztva** értékre, majd válassza ki azt a biztonsági csoportot, amelynek engedélyezni szeretné az új jelszavak kérését.  Ebben az esetben támogatott a biztonsági csoportok beágyazása.
6. Töltse ki a felhasználók regisztrálásához szükséges [hitelesítési adatokat](howto-sspr-authenticationdata.md), például az irodai telefonszámukat, a mobiltelefonszámukat és a másodlagos e-mail-címüket.
7. [Az Azure AD bejelentkezési felület testreszabása a vállalat márkajelzésének megjelenítésével](concept-sspr-customization.md).
8. Az SSPR használatának megtanítása a felhasználóinak. Útmutatást küldhet nekik arról, hogyan regisztrálhatnak, és hogyan állíthatják át a jelszavukat.
9. A regisztráció kényszerítésének meghatározása. Bármikor dönthet úgy, hogy kényszeríti a regisztrációt. Azt is megkövetelheti a felhasználóktól, hogy bizonyos idő elteltével újra erősítsék meg a hitelesítési adataikat.
10. A jelentéskészítő képesség használata. Áttekintheti a felhasználók regisztrációra vonatkozó és használati adatait az [Azure AD által nyújtott jelentéskészítő képességgel](howto-sspr-reporting.md).
11. Az új jelszó kérésének engedélyezése. Amikor elkészült, engedélyezze az új jelszó kérését az összes felhasználónak: állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Mindenki** értékre. 

   > [!NOTE]
   > Ha a beállítást egy kiválasztott csoportról mindenkire módosítja, a felhasználók által egy tesztcsoport tagjaként regisztrált meglévő hitelesítési adatok nem vesztik érvényüket. A konfigurált felhasználók, akik érvényes hitelesítési adatokat regisztráltak, továbbra is működni fognak.

12. [Az új jelszó kérésének engedélyezése a Windows 10-felhasználók számára a bejelentkezési képernyőn](tutorial-sspr-windows.md).

   > [!IMPORTANT]
   > Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](concept-sspr-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="email-based-rollout"></a>E-mailes alapú bevezetés

Számos ügyfél szerint egy egyszerűen követhető útmutatással ellátott e-mailes kampánnyal lehet a legkönnyebben rávenni a felhasználókat az SSPR használatára. [Három egyszerű e-mailt hoztunk létre, amelyeket sablonként használhat a bevezetés során](https://www.microsoft.com/download/details.aspx?id=56768):

* **Hamarosan elérhető**: Olyan e-mail-sablon, amelyet a bevezetést megelőző hetekben vagy napokban érdemes használni, hogy a felhasználók tisztában legyenek azzal, hogy hamarosan tenniük kell valamit.
* **Már elérhető**: Olyan e-mail-sablon, amelyet a program indulásának napján érdemes használni, hogy a felhasználók regisztráljanak, és megerősítsék a hitelesítési adataikat. Ha a felhasználók ilyenkor regisztrálnak, szükség esetén már használatba tudják venni az SSPR-t.
* **Regisztrációs emlékeztető**: Olyan e-mail-sablon, amelyet pár nappal vagy héttel az üzembe helyezés után érdemes használni, hogy a felhasználók emlékeztetőt kapjanak a regisztrációról és hitelesítési adataik megerősítéséről.

![E-mail][Email]

## <a name="create-your-own-password-portal"></a>Saját jelszókezelő portál létrehozása

Sok ügyfél dönt webhelyek üzemeltetése és gyökérszintű DNS-bejegyzések létrehozása mellett. Például: https://passwords.contoso.com. Ezután feltöltik az oldalt a következő információk hivatkozásaival:

* [Azure AD új jelszó létrehozására szolgáló portál – https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD új jelszó létrehozására szolgáló regisztrációs portál – https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Azure AD jelszómódosítási portál – https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* A szervezetre jellemző egyéb információk

A kiküldött e-mailekben vagy közleményekben feltüntethet egy márkaüzenettel ellátott, könnyen megjegyezhető URL-címet, amelyre a felhasználók ellátogathatnak, amikor szükségük van a szolgáltatásokra. Létrehoztunk Önnek egy [új jelszó létrehozására szolgáló mintaoldalt](https://github.com/ajamess/password-reset-page), amelyet a szervezet igényei szerint használhat és szabhat testre.

## <a name="step-by-step-deployment-plan"></a>Részletes üzembehelyezési terv

Az Azure Active Directory termékcsoport egy olyan [részletes üzembehelyezési tervet](https://aka.ms/SSPRDeploymentPlan) hozott létre, amelyet a cégek az ezen az oldalon található dokumentációval együtt használva létrehozhatnak egy üzleti tervet, és megtervezhetik egy önkiszolgáló jelszó-visszaállítási szolgáltatás üzembe helyezését.

## <a name="use-enforced-registration"></a>Kényszerített regisztráció használata

Ha azt szeretné, hogy a felhasználók regisztráljanak a jelszóátállításra, ezt megkövetelheti, amikor bejelentkeznek az Azure AD használatával. Ezt a beállítást a címtár **Jelszóátállítás** panelén engedélyezheti úgy, hogy a **Regisztráció** panelen engedélyezi a **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** beállítást.

A rendszergazdák megkövetelhetik a felhasználóktól az újbóli regisztrációt egy bizonyos idő elteltével. **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését** értéket 0 és 730 nap között állíthatják be.

Miután engedélyezi ezt a beállítást, a bejelentkező felhasználókat egy üzenet fogadja majd, miszerint a rendszergazda a hitelesítési adataik érvényesítésére kéri őket.

## <a name="populate-authentication-data"></a>Hitelesítési adatok feltöltése

Érdemes [feltöltenie a felhasználók hitelesítési adatait](howto-sspr-authenticationdata.md). Így a felhasználóknak az SSPR használatához nem lesz kötelező regisztrálniuk a jelszóátállításra. Mindaddig, amíg a felhasználók hitelesítési adatai meg vannak adva, és megfelelnek az Ön által definiált jelszóátállítási szabályzatban foglaltaknak, a felhasználók átállíthatják a jelszavaikat.

## <a name="disable-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszóátállítás letiltása egyszerű feladat. Nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása** > **Tulajdonságok** elemet, majd válassza a **Senki** elemet az **Önkiszolgáló jelszóátállítás engedélyezve** rész alatt.

## <a name="next-steps"></a>További lépések

* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "E-mail-sablonok testreszabása a szervezeti követelményeknek megfelelően"
