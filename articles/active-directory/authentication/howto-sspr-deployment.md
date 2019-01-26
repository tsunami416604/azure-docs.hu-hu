---
title: Üzembe helyezési útmutató új jelszó kérésére vonatkozó önkiszolgáló folyamathoz – Azure Active Directory
description: Tippek az Azure AD önkiszolgáló jelszóátállítás sikeres bevezetéséhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 0111cb077afe0412d0ab807955c666b42dd1ff31
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079613"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének sikeres bevezetése

Az Azure Active Directory (Azure AD) önkiszolgáló jelszóváltoztatási (SSPR) funkciójának zökkenőmentes bevezetése érdekében a legtöbb ügyfél a következő lépéseket végzi el:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Fejezze be a szervezet egy szűk részhalmaza érhető el egy kísérleti összesítő.
   * Próbaüzem információk megtalálhatók a [oktatóanyag: Befejeződött egy Azure AD önkiszolgáló jelszó-visszaállítás próbaüzem bevezetése](tutorial-sspr-pilot.md).
1. Tájékoztassa a segélyszolgálathoz.
   * Hogyan fogja segítenek, hogy a felhasználók számára?
   * Kényszeríti a felhasználó használhatja az SSPR, és engedélyezi a felhasználók segítséget az ügyfélszolgálattól?
   * Adta nekik a regisztrációs URL-címeket és visszaállítása?
      * Regisztráció:  https://aka.ms/ssprsetup
      * Gyári beállítások visszaállítása: https://aka.ms/sspr
1. Tájékoztassa a felhasználókat.
   * Ez a dokumentum az alábbi szakaszok a haladnak át minta közötti kommunikáció, jelszó portálok, a regisztráció kényszerítése, és a hitelesítési adatok feltöltése.
   * Az Azure Active Directory termékcsoport egy olyan [részletes üzembehelyezési tervet](https://aka.ms/SSPRDeploymentPlan) hozott létre, amelyet a cégek az ezen az oldalon található dokumentációval együtt használva létrehozhatnak egy üzleti tervet, és megtervezhetik egy önkiszolgáló jelszó-visszaállítási szolgáltatás üzembe helyezését.
1. Engedélyezze az önkiszolgáló jelszó-visszaállítást a teljes cég számára.
   * Amikor elkészült, engedélyezze az új jelszó kérését az összes felhasználónak: állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Mindenki** értékre.

## <a name="sample-communication"></a>Minta kommunikáció

Számos ügyfél szerint egy egyszerűen követhető útmutatással ellátott e-mailes kampánnyal lehet a legkönnyebben rávenni a felhasználókat az SSPR használatára. [Hoztunk létre egyszerű e-mailek és más, amely sablonként használhatja a bevezetés során biztosíték](https://www.microsoft.com/download/details.aspx?id=56768):

* **Hamarosan**: Egy e-mail sablon, amelyekkel a megelőző hetekben vagy napokban a Bevezetés a felhasználóknak szükségük van valamit.
* **Rendelkezésre álló most**: A program a nap használt e-mail sablont indítsa el a felhasználók regisztráljanak, és megerősítsék a hitelesítési adataikat. Ha a felhasználók ilyenkor regisztrálnak, szükség esetén már használatba tudják venni az SSPR-t.
* **Regisztrációs emlékeztető**: Emlékeztesse a felhasználók regisztrálhatnak, és megerősítsék a hitelesítési adataikat az üzembe helyezés után néhány hét napig néhány e-mail sablont.
* **SSPR poszterek**: Poszterek testre szabhatja és a nap és a hét, akár kezdő, és a bevezetés után a szervezet köré megjelenítéséhez.
* **SSPR tábla sátrak**: Tábla kártyák elhelyezhet a helyiségben ebéd konferenciahívások, vagy arra bátorítsák a felhasználókat, hogy a regisztráció elvégzése asztalok.
* **SSPR matricákat**: Matricát sablonokat testre szabhatja és nyomtatási laptopok, monitorok, billentyűzetek vagy mobiltelefonok, ne felejtse el az SSPR eléréséhez hogyan helyezhető el.

![SSPR E-mail minták][Email]

## <a name="create-your-own-password-portal"></a>Saját jelszókezelő portál létrehozása

Sok ügyfél dönt webhelyek üzemeltetése és gyökérszintű DNS-bejegyzések létrehozása mellett. Például: https://passwords.contoso.com. Ezután feltöltik az oldalt a következő információk hivatkozásaival:

* [Azure AD új jelszó létrehozására szolgáló portál – https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD új jelszó létrehozására szolgáló regisztrációs portál – https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Azure AD jelszómódosítási portál – https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* A szervezetre jellemző egyéb információk

A kiküldött e-mailekben vagy közleményekben feltüntethet egy márkaüzenettel ellátott, könnyen megjegyezhető URL-címet, amelyre a felhasználók ellátogathatnak, amikor szükségük van a szolgáltatásokra. Létrehoztunk Önnek egy [új jelszó létrehozására szolgáló mintaoldalt](https://github.com/ajamess/password-reset-page), amelyet a szervezet igényei szerint használhat és szabhat testre.

## <a name="use-enforced-registration"></a>Kényszerített regisztráció használata

Ha azt szeretné, hogy a felhasználók regisztráljanak a jelszóátállításra, ezt megkövetelheti, amikor bejelentkeznek az Azure AD használatával. Ezt a beállítást a címtár **Jelszóátállítás** panelén engedélyezheti úgy, hogy a **Regisztráció** panelen engedélyezi a **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** beállítást.

A rendszergazdák megkövetelhetik a felhasználóktól az újbóli regisztrációt egy bizonyos idő elteltével. **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését** értéket 0 és 730 nap között állíthatják be.

Miután engedélyezi ezt a beállítást, a bejelentkező felhasználókat egy üzenet fogadja majd, miszerint a rendszergazda a hitelesítési adataik érvényesítésére kéri őket.

## <a name="populate-authentication-data"></a>Hitelesítési adatok feltöltése

Érdemes lehet [előre feltöltése az egyes hitelesítési adatokat a felhasználók számára](howto-sspr-authenticationdata.md). Így a felhasználóknak az SSPR használatához nem lesz kötelező regisztrálniuk a jelszóátállításra. Mindaddig, amíg a felhasználók hitelesítési adatai meg vannak adva, és megfelelnek az Ön által definiált jelszóátállítási szabályzatban foglaltaknak, a felhasználók átállíthatják a jelszavaikat.

## <a name="disable-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Ha a szervezet úgy dönt, hogy az önkiszolgáló jelszóátállítás letiltása egyszerű folyamat. Nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása** > **Tulajdonságok** elemet, majd válassza a **Senki** elemet az **Önkiszolgáló jelszóátállítás engedélyezve** rész alatt. Felhasználók továbbra is megőrzi a regisztrált hitelesítési módszereket későbbi használatra.

## <a name="next-steps"></a>További lépések

* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [A hiperkonvergens regisztráció az Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](concept-registration-mfa-sspr-converged.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "E-mail-sablonok testreszabása a szervezeti követelményeknek megfelelően"
