---
title: "Bevezetés: Azure AD SSPR | Microsoft Docs"
description: "Tippek az Azure AD önkiszolgáló jelszóátállítás sikeres bevezetéséhez"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 5f3900aef2b432527454da72f3ff15e533543758
ms.contentlocale: hu-hu
ms.lasthandoff: 06/07/2017


---
<a id="roll-out-password-reset-for-users" class="xliff"></a>

# Jelszóátállítás bevezetése felhasználók számára

A legtöbb ügyfél követi azokat a lépéseket, amelyek biztosítják számukra az SSPR funkcióinak zökkenőmentes bevezetését.

1. [A jelszóátállítás engedélyezése a címtárban](active-directory-passwords-getting-started.md)
2. [A helyszíni AD-engedélyeket konfigurálása a jelszóvisszaíró számára](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Jelszóvisszaíró konfigurálása](active-directory-passwords-writeback.md#configuring-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához
4. [A szükséges licencek hozzárendelése és ellenőrzése](active-directory-passwords-licensing.md)
5. Ha a bevezetést fokozatosan szeretné elvégezni, korlátozhatja a jelszóátállítást a felhasználók egy csoportjára, így a funkciót lassan, apránként vezetheti be. Ehhez állítsa az **Önkiszolgáló jelszóátállítás engedélyezve** lehetőség értékét a **Mindenki** értékről **Egy csoport** értékre, majd válassza ki azt a biztonsági csoportot, amely számára engedélyezni kívánja a jelszó átállítását. E csoport tagjainak rendelkeznie kell a hozzájuk rendelt licencekkel. A [csoportalapú licencelés](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing) engedélyezésére ez kiváló megoldás.
6. Töltse fel a [Hitelesítési adatok](active-directory-passwords-data.md) minimális készletét a szabályzatának megfelelően.
7. Tanítsa meg felhasználóinak az SSPR használatát, küldjön nekik útmutatást arról, hogyan regisztrálhatnak, és hogyan állíthatják át a jelszavukat.
    > [!NOTE]
    > Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai típusú fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról további információkat a [részletes bemutatócikkünkben](active-directory-passwords-how-it-works.md) talál.

8. Bármikor dönthet úgy, hogy kényszeríti a regisztrációt, és megköveteli a felhasználóktól, hogy bizonyos idő elteltével újra erősítsék meg a hitelesítési adataikat. Ha nem szeretné, hogy a felhasználók regisztrációja kötelező legyen, válassza a [jelszóátállítás üzembe helyezése anélkül, hogy a felhasználóknak regisztrálniuk kellene](active-directory-passwords-data.md) lehetőséget.
9. Az [Azure AD által biztosított jelentés](active-directory-passwords-reporting.md) megtekintésével folyamatosan ellenőrizheti, mely felhasználók regisztráltak és vették igénybe a funkciót.

<a id="email-based-rollout" class="xliff"></a>

## E-mailes alapú bevezetés

Számos ügyfél szerint egy egyszerűen követhető útmutatással ellátott e-mailes kampánnyal lehet a legkönnyebben rávenni a felhasználókat az SSPR használatára. [Három egyszerű e-mailt hoztunk létre, amelyeket sablonként használhat a bevezetés során.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* A **Hamarosan elérhető** e-mail-sablont a bevezetést megelőző hetekben vagy napokban érdemes használni, hogy a felhasználók tisztában legyenek azzal, hogy hamarosan tenniük kell valamit.
* A **Már elérhető** e-mail-sablont az indulás napján érdemes használni, hogy a felhasználók regisztráljanak, és megerősítsék a hitelesítési adataikat. Így szükség esetén már használatba tudják venni az SSPR-t.
* A **Regisztrációs emlékeztető** e-mail-sablont pár nappal vagy héttel az üzembe helyezés után érdemes használni, hogy a felhasználók emlékeztetőt kapjanak a regisztrációról és hitelesítési adataik megerősítéséről.

<a id="creating-your-own-password-portal" class="xliff"></a>

## Saját jelszókezelő portál létrehozása

Nagyvállalati ügyfeleink közül sokan döntenek webhelyek üzemeltetése és gyökérszintű DNS-bejegyzések létrehozása mellett. Például: https://jelszo.contoso.com. Ezután feltöltik az oldalt az Azure AD jelszóátállítására, a jelszóátállítási regisztrációra, a jelszóátállítási portálokra mutató és más, az adott szervezetre jellemző hivatkozásokkal. A kiküldött e-mailekben vagy közleményekben feltüntethet egy márkaüzenettel ellátott, könnyen megjegyezhető URL-címet, amelyre a felhasználók ellátogathatnak, amikor szükségük van a szolgáltatásokra.

* Jelszóátállítási portál – https://passwordreset.microsoftonline.com
* Jelszóátállítási regisztrációs portál – http://aka.ms/ssprsetup
* Jelszómódosítási portál – https://account.activedirectory.windowsazure.com/ChangePassword.aspx

<a id="using-enforced-registration" class="xliff"></a>

## Kényszerített regisztráció használata

Ha szeretné, hogy a felhasználók a jelszóátállításra regisztráljanak, kényszerítheti őket erre, amikor bejelentkeznek az Azure AD használatával. Ezt a beállítást a címtár **Jelszóátállítás** panelén engedélyezheti úgy, hogy a **Regisztráció** lapon engedélyezi a **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** beállítást.

A rendszergazdák megkövetelhetik a felhasználóktól az újbóli regisztrációt egy bizonyos idő elteltével, ha a **Felhasználók hitelesítési adatainak ismételt megerősítésére vonatkozó kérésig hátralévő napok száma** értékét egy 0 és 730 nap közötti értékre beállítják.

Miután engedélyezi ezt a beállítást, a bejelentkező felhasználókat egy üzenet fogadja majd, miszerint a rendszergazda a hitelesítési adataik érvényesítésére kéri őket.

<a id="populate-authentication-data" class="xliff"></a>

## Hitelesítési adatok feltöltése

Ha [feltölti a felhasználóihoz tartozó hitelesítési adatokat](active-directory-passwords-data.md), akkor a felhasználóknak az SSPR használatához nem lesz kötelező regisztrálniuk a jelszóátállításhoz. Mindaddig, amíg a felhasználók hitelesítési adatai definiálva vannak, ezek pedig megfelelnek az Ön által definiált jelszóátállítási szabályzatban foglaltaknak, a felhasználók átállíthatják a jelszavaikat.

<a id="disabling-self-service-password-reset" class="xliff"></a>

## Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszó-visszaállítás letiltása egyszerű: nyissa meg Azure AD-bérlőjét, keresse meg az **Új jelszó létrehozása** elemet, válassza a **Tulajdonságok** parancsot, majd válassza a **Senki** lehetőséget az **„Önkiszolgáló jelszóátállítás engedélyezve”** alatt

<a id="next-steps" class="xliff"></a>

## Következő lépések

Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [**Gyors üzembe helyezés**](active-directory-passwords-getting-started.md) – Percek alatt üzembe helyezheti az Azure AD önkiszolgáló jelszókezelőjét. 
* [**Licencelés**](active-directory-passwords-licensing.md) – Az Azure AD licencelésének konfigurálása.
* [**Adatok**](active-directory-passwords-data.md) – A szükséges adatok megismerése, és az adatok használata a rendszer jelszókezelésre.
* [**Testreszabás**](active-directory-passwords-customize.md) – Az SSPR-felület megjelenésének és működésének testre szabása a cége számára.
* [**Szabályzat**](active-directory-passwords-policy.md) – Megismerheti és beállíthatja az Azure AD jelszószabályzatait.
* [**Jelszóvisszaíró**](active-directory-passwords-writeback.md) – Megtudhatja, hogyan használhatja a jelszóvisszaírót a helyszíni címtárával.
* [**Jelentéskészítés**](active-directory-passwords-reporting.md) – Megtudhatja, mikor és hol érik el a felhasználói az SSPR funkcióit.
* [**Részletes műszaki bemutatás**](active-directory-passwords-how-it-works.md) – Egy pillantás a függöny mögé, hogy megértse, hogyan is működik.
* [**Gyakori kérdések**](active-directory-passwords-faq.md) – Hogyan? Hogy miért? Mi? Hová? Ki? Mikor? – Válaszok olyan kérdésekre, amiket mindig is fel akart tenni
* [**Hibaelhárítás**](active-directory-passwords-troubleshoot.md) – Ismerje meg, hogyan oldhat meg általános, az SSPR működése során jelentkező hibákat.
