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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének sikeres bevezetése

A legtöbb ügyfél követi ezeket a lépéseket, amelyek biztosítják számukra az SSPR funkcióinak zökkenőmentes kibocsátását.

1. [Az új jelszavak kérésének engedélyezése a címtárban](active-directory-passwords-getting-started.md).
2. [A helyszíni AD-engedélyek konfigurálása a jelszóvisszaíró számára](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Jelszóvisszaíró konfigurálása](active-directory-passwords-writeback.md#configuring-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához.
4. [A szükséges licencek hozzárendelése és ellenőrzése](active-directory-passwords-licensing.md).
5. Ha az SSPR bevezetését fokozatosan szeretné elvégezni, a hozzáférést a felhasználók egy csoportjára korlátozhatja, így egy adott csoporttal kísérletezhet. Ehhez állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Kiválasztva** értékre, majd válassza ki azt a biztonsági csoportot, amely számára engedélyezni kívánja az új jelszavak kérését. 
6. Töltse ki a felhasználók [Hitelesítési adatait](active-directory-passwords-data.md), például az irodai telefonszámukat, a mobiltelefonszámukat és a másodlagos e-mail-címüket.
7. [Az Azure AD bejelentkezési felület testreszabása a vállalat márkajelzésének megjelenítésével](active-directory-passwords-customize.md).
8. Tanítsa meg felhasználóinak az SSPR használatát, küldjön nekik útmutatást arról, hogyan regisztrálhatnak, és hogyan állíthatják át a jelszavukat.
9. Bármikor dönthet úgy, hogy kényszeríti a regisztrációt, és megköveteli a felhasználóktól, hogy bizonyos idő elteltével újra erősítsék meg a hitelesítési adataikat.
10. Az [Azure AD által biztosított jelentés](active-directory-passwords-reporting.md) megtekintésével folyamatosan ellenőrizheti, mely felhasználók regisztráltak és vették igénybe a funkciót.
11. Amikor elkészült, engedélyezze az új jelszó kérését az összes felhasználónak, állítsa az **Új jelszó önkiszolgáló kérése engedélyezve** beállítást a **Mindenki** értékre. 

   > [!IMPORTANT]
   > Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai típusú fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](active-directory-passwords-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="email-based-rollout"></a>E-mailes alapú bevezetés

Számos ügyfél szerint egy egyszerűen követhető útmutatással ellátott e-mailes kampánnyal lehet a legkönnyebben rávenni a felhasználókat az SSPR használatára. [Három egyszerű e-mailt hoztunk létre, amelyeket sablonként használhat a bevezetés során.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* A **Hamarosan elérhető** e-mail-sablont a bevezetést megelőző hetekben vagy napokban érdemes használni, hogy a felhasználók tisztában legyenek azzal, hogy hamarosan tenniük kell valamit.
* A **Már elérhető** e-mail-sablont az indulás napján érdemes használni, hogy a felhasználók regisztráljanak, és megerősítsék a hitelesítési adataikat. Így szükség esetén már használatba tudják venni az SSPR-t.
* A **Regisztrációs emlékeztető** e-mail-sablont pár nappal vagy héttel az üzembe helyezés után érdemes használni, hogy a felhasználók emlékeztetőt kapjanak a regisztrációról és hitelesítési adataik megerősítéséről.

![E-mail][Email]

## <a name="creating-your-own-password-portal"></a>Saját jelszókezelő portál létrehozása

Nagyvállalati ügyfeleink közül sokan döntenek webhelyek üzemeltetése és gyökérszintű DNS-bejegyzések létrehozása mellett. Például: https://jelszo.contoso.com. Ezután feltöltik az oldalt az Azure AD jelszóátállítására, a jelszóátállítási regisztrációra, a jelszóátállítási portálokra mutató és más, az adott szervezetre jellemző hivatkozásokkal. A kiküldött e-mailekben vagy közleményekben feltüntethet egy márkaüzenettel ellátott, könnyen megjegyezhető URL-címet, amelyre a felhasználók ellátogathatnak, amikor szükségük van a szolgáltatásokra.

* Új jelszó kérése portál – https://aka.ms/sspr
* Jelszóátállítási regisztrációs portál – http://aka.ms/ssprsetup
* Jelszómódosítási portál – https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Létrehoztunk Önnek egy mintaoldalt, amelyet a szervezet igényei szerint használhat és szabhat testre, és amelyet a [GitHubról](https://github.com/ajamess/password-reset-page) tölthet le.

## <a name="using-enforced-registration"></a>Kényszerített regisztráció használata

Ha szeretné, hogy a felhasználók a jelszóátállításra regisztráljanak, kényszerítheti őket erre, amikor bejelentkeznek az Azure AD használatával. Ezt a beállítást a címtár **Jelszóátállítás** panelén engedélyezheti úgy, hogy a **Regisztráció** lapon engedélyezi a **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** beállítást.

A rendszergazdák megkövetelhetik a felhasználóktól az újbóli regisztrációt egy bizonyos idő elteltével, ha a **Felhasználók hitelesítési adatainak ismételt megerősítésére vonatkozó kérésig hátralévő napok száma** értékét egy 0 és 730 nap közötti értékre beállítják.

Miután engedélyezi ezt a beállítást, a bejelentkező felhasználókat egy üzenet tájékoztatja, hogy a rendszergazda a hitelesítési adataik ellenőrzésére kéri őket.

## <a name="populate-authentication-data"></a>Hitelesítési adatok feltöltése

Ha [feltölti a felhasználóihoz tartozó hitelesítési adatokat](active-directory-passwords-data.md), akkor a felhasználóknak az SSPR használatához nem lesz kötelező regisztrálniuk a jelszóátállításhoz. Mindaddig, amíg a felhasználók hitelesítési adatai definiálva vannak, ezek pedig megfelelnek az Ön által definiált jelszóátállítási szabályzatban foglaltaknak, a felhasználók átállíthatják a jelszavaikat.

## <a name="disabling-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszó-visszaállítás letiltása egyszerű: nyissa meg Azure AD-bérlőjét, keresse meg az **Új jelszó létrehozása** elemet, válassza a **Tulajdonságok** parancsot, majd válassza a **Senki** lehetőséget az **„Önkiszolgáló jelszóátállítás engedélyezve”** alatt

## <a name="next-steps"></a>Következő lépések

* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "E-mail-sablonok testreszabása a szervezeti követelményeknek megfelelően"