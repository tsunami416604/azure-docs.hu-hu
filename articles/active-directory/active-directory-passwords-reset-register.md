---
title: "Az Azure AD: SSPR regisztrációs |} Microsoft Docs"
description: "Regisztrálja az Azure AD az önkiszolgáló jelszó-hitelesítési adatok alaphelyzetbe állítása"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
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
ms.openlocfilehash: bae62762decf530521ba10ce684d5db9afa0b86c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="register-for-self-service-password-reset"></a>Regisztráció önkiszolgáló jelszó-visszaállításra

> [!IMPORTANT]
> Biztosan itt nem tud bejelentkezni, mert? Ha igen, tekintse meg a [a munkahelyi vagy iskolai jelszó](active-directory-passwords-update-your-own-password.md).

Végfelhasználói új jelszót, vagy a fiók zárolásának önállóan, Azure Active Directory (Azure AD) önkiszolgáló jelszó-változtatási (SSPR) használatakor. Ez a funkció használata előtt kell regisztrálni a hitelesítési módszereket, illetve erősítse meg az előre definiált hitelesítési módszereket, amelyek a rendszergazda fel van töltve.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR hitelesítés adatok regisztrálása vagy megerősítése

1. Nyissa meg az eszközön a webböngésző, és navigáljon a [jelszó-változtatási regisztrációs lapjához](http://aka.ms/ssprsetup).
2. Adja meg a felhasználónév és a jelszavát, amelyet a rendszergazda.
3. Attól függően, hogy az informatikai munkatársak konfigurálta dolog az alábbi lehetőségek közül érhetők el, hogy beállítása és ellenőrzése. Ha a rendszergazda a adatok engedélyt, azok töltheti fel, az adatok egy részét.
    * **Irodai telefon**: csak a rendszergazda állíthatja be ezt a beállítást.
    * **Hitelesítéshez megadott telefonját**: értéket, hogy rendelkezik-e a hozzáférést egy másik telefonszámot kell megadni. Példa: a mobiltelefon fogadhassanak egy text vagy a hívás.
    * **Hitelesítési E-mail**: állítsa be ezt a beállítást egy másodlagos e-mail címre, amely alaphelyzetbe állítja a jelszót használata nélkül végezheti el.
    * **Biztonsági kérdések**: A rendszergazda jóváhagyta a kérdések megválaszolása listája. Nem használja ugyanezt a kérdést és választ egynél többször.
4. Adja meg, és ellenőrizze, hogy a rendszergazda megköveteli az adatokat. Ha egynél több lehetőség áll rendelkezésre, javasoljuk, hogy regisztrálja többféle módszer. Ez rugalmasságot biztosít, amikor a módszerek nem érhető el. Például akkor, ha utazik, és még nem érhető el az irodai telefonjára.

    ![Hitelesítési módszerek regisztrálja, és kattintson a Befejezés][Register]

5. Válassza ki **Befejezés**. Most már használhatja a SSPR, ha szeretné a jövőben.

Ha ad meg, hogy az adatok **hitelesítéshez megadott telefonját** vagy **hitelesítési E-mail**, nem látható a globális könyvtárban. Ezeket az adatokat kizárólag Ön és a rendszergazdák láthatják. Csak tekintheti meg a biztonsági kérdésekre adott válaszokat.

A rendszergazdák szükség lehet a hitelesítési módszerek ellenőrzése után győződjön meg arról, hogy továbbra is regisztrálva megfelelő módszereket az adott időszakban.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásuk

 Az alábbiakban néhány gyakori hibák és a megoldások:

| Hiba eset| Milyen hiba látható?| Megoldás |
| --- | --- | --- |
| "Forduljon a rendszergazdához" oldal jelenik meg a felhasználói azonosító megadása után | Lépjen kapcsolatba a rendszergazdával. <br> <br> A rendszer azt észlelte, hogy a felhasználói fiók jelszava nem Microsoft által felügyelt. Ennek eredményeképpen jelenleg nem lehet automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért lépjen kapcsolatba az informatikai munkatársak. | Most azt láthatja ezt az üzenetet, mert az informatikai munkatársak kezeli a jelszót a helyszíni környezetben, és nem teszi lehetővé a jelszó a **nem fér hozzá a fiókjához** hivatkozásra. <br> <br> A jelszó alaphelyzetbe állításához, lépjen kapcsolatba az informatikai munkatársak közvetlenül segítségét. Tájékoztassa alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető.|
| A felhasználói azonosító megadása után jelenik meg a "a nem engedélyezett a jelszó alaphelyzetbe állítása" hiba | A fiók nincs engedélyezve a jelszó alaphelyzetbe állítása. <br> <br> Sajnáljuk, de az informatikai munkatársak nem állította be a fiók a szolgáltatással való használatra. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Ezt az üzenetet is lát, mert az informatikai munkatársak nincs engedélyezve a jelszó alaphelyzetbe állítása a szervezete számára a **nem fér hozzá a fiókjához** hivatkozásra, vagy még nem rendelkezik licenccel a funkció használatát. <br> <br> A jelszó alaphelyzetbe állításához, válassza ki a **kérje a rendszergazda** hivatkozásra. E-mailt kapnak a vállalati informatikai személyzetet tart fenn. Az e-mailt lehetővé teszi, hogy ismeri a alaphelyzetbe állítja a jelszót, ez a funkció az Ön is engedélyezhető. |
| A felhasználói azonosító megadása után jelenik meg a "nem tudtuk ellenőrizni fiókját" hiba | Nem tudtuk ellenőrizni a fiókját. <br> <br> Ha azt szeretné, a jelszó alaphelyzetbe állításához a szervezet rendszergazda is megkereshetjük. | Most azt láthatja ezt az üzenetet, mert a jelszó alaphelyzetbe állítása folyamatban engedélyezve van, de még nem regisztrált a szolgáltatás használatához. Regisztrálhatnak a jelszóváltoztatásra, keresse fel a [jelszó-változtatási regisztrációs lapjához](http://aka.ms/ssprsetup) után fiókjába rendelkeznie helyreállt a hozzáférést. <br> <br> A jelszó alaphelyzetbe állításához, válassza ki a **kérje a rendszergazda** hivatkozás egy e-mailt küldhet a vállalat informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>Következő lépések

* [A jelszó megváltoztatásához használja az önkiszolgáló jelszóváltoztatás](active-directory-passwords-update-your-own-password.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Ha nem tud bejelentkezni Microsoft-fiókja](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Jelszó-átállítási regisztráció oldaláról, valamint a regisztrált módszerek és a Befejezés gombra"

