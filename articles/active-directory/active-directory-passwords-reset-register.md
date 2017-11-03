---
title: "Az Azure AD: SSPR regisztrációs |} Microsoft Docs"
description: "Regisztrálja az Azure AD az önkiszolgáló jelszó-hitelesítési adatok alaphelyzetbe állítása"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b884f8bc3a20052fa0cb40772deef591b69d7b10
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="register-for-self-service-password-reset"></a>Regisztráció önkiszolgáló jelszó-visszaállításra

> [!IMPORTANT]
> **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md).

Végfelhasználói új jelszót, vagy anélkül, hogy az önkiszolgáló jelszó-változtatási (SSPR) használó személy kommunikáljanak a fiók zárolását kívánja feloldani. Mielőtt használhatná ezt a funkciót, hitelesítési módszereket kell regisztrálnia, vagy jóvá kell hagynia a rendszergazda által betöltött előre definiált hitelesítési módszereket.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR hitelesítés adatok regisztrálása vagy megerősítése

1. Nyissa meg az eszközén a webböngészőt, és lépjen a [jelszó-visszaállítási regisztrációs oldalra](http://aka.ms/ssprsetup)
2. Adja meg a rendszergazdától kapott felhasználónevet és jelszót.
3. Attól függően, hogy az informatikai munkatársak hogyan dolog van konfigurálva az alábbi lehetőségek közül érhetők el, hogy beállítása és ellenőrzése. A rendszergazda is feltöltése Ez néhány meg, ha azok a engedélye az információkat használja.
    * Irodai telefon csak a rendszergazda által beállított kell tudni
    * Hitelesítéshez megadott telefonját volna rendelkezik hozzáféréssel a szöveges vagy telefonhíváson fogadhassanak mobiltelefon, például egy másik telefonszámot kell megadni.
    * Hitelesítési e-mailt kell állítani egy másodlagos e-mail címre, hogy a jelszó alaphelyzetbe kell nélkül végezheti el.
    * Biztonsági kérdések lehetővé teszi a rendszergazda jóváhagyta megválaszolására kérdések listáját. Nem használja ugyanezt a kérdést és választ egynél többször.
4. Adja meg és hagyja jóvá a rendszergazda által előírt információkat. Ha egynél több lehetőség áll rendelkezésre, javasoljuk, hogy regisztrálja többféle módszer rugalmasságot biztosít, ha valami más módon nem érhető el (például: utazik, nem érhető el az irodai telefonjára)

    ![Regisztrálja a hitelesítési módszereket, majd kattintson a Befejezés gombra][Register]

5. Miután végzett a 4. lépéssel, válassza a **Befejezés** lehetőséget, és innentől használhatja az önkiszolgáló jelszó-visszaállítás szolgáltatást, amennyiben ez bármikor szükségessé válna az elkövetkezőkben.

A Hitelesítő telefonszám és a Hitelesítő e-mail-cím mezőkben megadott adatok a globális címjegyzékben nem lesznek láthatóak. Ezeket az adatokat kizárólag Ön és a rendszergazdák láthatják. A Biztonsági kérdésekre adott válaszokat pedig kizárólag Ön láthatja.

A rendszergazda esetleg bizonyos idő elteltével kérheti, hogy erősítse meg a hitelesítési módszereit, hogy bizonyos legyen, hogy még mindig regisztrálva vannak a megfelelő módszerek.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásuk

 Az alábbiakban néhány gyakori hibák és a megoldások:

| Hiba eset| Milyen hiba látható?| Megoldás |
| --- | --- | --- |
| "Forduljon a rendszergazdához" oldal jelenik meg a felhasználói azonosító megadása után | Lépjen kapcsolatba a rendszergazdával <br> <br> A rendszer azt észlelte, hogy a felhasználói fiók jelszava nem Microsoft által felügyelt. Ennek eredményeképpen jelenleg nem lehet automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért forduljon az informatikai munkatársak kell. | Ez az üzenet azért jelent meg, mert az informatikai munkatársak kezeli a jelszót a helyszíni környezetben, és nem teszi lehetővé a jelszó a nem tudja elérni a fiókot hivatkozásra. <br> <br> A jelszó alaphelyzetbe állításához, közvetlenül segítségért lépjen kapcsolatba az informatikai munkatársak, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető.|
| A felhasználói azonosító megadása után jelenik meg a "a nem engedélyezett a jelszó alaphelyzetbe állítása" hiba | A fiókjára vonatkozóan nincs engedélyezve a jelszó alaphelyzetbe állítása <br> <br> Sajnáljuk, de az informatikai munkatársak nem állította be a fiók a szolgáltatással való használatra. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ez az üzenet azért jelent, mert az informatikai munkatársak nincs engedélyezve a jelszó alaphelyzetbe állítása a szervezete számára a nem tudja elérni a fiókot hivatkozásra, vagy még nem rendelkezik licenccel a funkció használatát. <br> <br> A jelszó alaphelyzetbe állításához hivatkozásra az ügyfél egy rendszergazda e-mailt küldhet a vállalat informatikai személyzetet tart fenn, és tájékoztassa, alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető. |
| A felhasználói azonosító megadása után jelenik meg a "nem tudtuk ellenőrizni fiókját" hiba | Nem tudtuk ellenőrizni a fiókot. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ez az üzenet azért jelent meg, mert a jelszó-visszaállításhoz engedélyezve vannak, de a szolgáltatás használatára nem regisztrált. Regisztrálhatnak a jelszóváltoztatásra, http://aka.ms/ssprsetup után lépjen a fiókjába rendelkeznie helyreállt a hozzáférést. <br> <br> A jelszó alaphelyzetbe állításához hivatkozásra az ügyfél egy rendszergazda e-mailt küldhet a vállalat informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>Következő lépések

* [Jelszó visszaállítása az önkiszolgáló jelszó-visszaállítással](active-directory-passwords-update-your-own-password.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Jelszó-visszaállítási regisztrációs oldal a regisztrált módszerekkel és a Befejezés gombbal"
