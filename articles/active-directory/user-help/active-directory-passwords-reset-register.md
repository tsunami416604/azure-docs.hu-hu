---
title: Regisztrálja a önkiszolgáló jelszó-átállítási – Azure Active Directory |} A Microsoft Docs
description: Az Azure AD önkiszolgáló jelszó-hitelesítési adatok regisztrálása alaphelyzetbe állítása
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.openlocfilehash: f8b4fb8861760d2b5b10f61b3ab6a8c718f849b6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346392"
---
# <a name="register-for-self-service-password-reset"></a>Regisztráció önkiszolgáló jelszó-visszaállításra

> [!IMPORTANT]
> Van itt, mert nem tud bejelentkezni? Ha igen, tekintse meg a [a munkahelyi vagy iskolai jelszó visszaállítása](active-directory-passwords-update-your-own-password.md).

Végfelhasználóval teszteljen, mint a jelszó alaphelyzetbe állítása, vagy feloldja fiókját saját magának, ha az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) használja. Ez a funkció használata előtt kell a hitelesítési módszerek regisztrálása vagy megerősítése az előre definiált hitelesítési módszereket, amelyek a rendszergazda rendelkezik-e töltve.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR hitelesítés adatok regisztrálása vagy megerősítése

1. Nyissa meg eszközén a webböngészőt és lépjen a [jelszó-visszaállítási regisztrációs oldalra](https://aka.ms/ssprsetup).
2. Adja meg a felhasználónevét és jelszavát a rendszergazda által biztosított.
3. Attól függően, hogy az informatikai részleg által konfigurált egy vagy több, az alábbi lehetőségek érhetők el konfigurálása és ellenőrzése. A rendszergazda a jogosult az információk, ha azok töltheti fel az Ön számára az adatok egy részét.
    * **Irodai telefon**: csak a rendszergazda ezzel a beállítással adhatja meg.
    * **Hitelesítéshez használt telefon**: ehhez a beállításhoz, amely hozzáféréssel rendelkezik egy másik telefonszámot. Például egy mobiltelefonszámot, amely képes fogadni, vagy a hívás akkor.
    * **Hitelesítési E-mail**: ehhez a beállításhoz, amely alaphelyzetbe állítja a jelszó használata nélkül elérheti másodlagos e-mail cím.
    * **Biztonsági kérdések**: A rendszergazda által jóváhagyott megadott, megválaszolandó kérdések listája. Nem használja ugyanezt a kérdést és választ egynél többször.
4. Adja meg, és ellenőrizze, hogy a rendszergazda megköveteli az adatokat. Ha egynél több lehetőség áll rendelkezésre, javasoljuk, hogy regisztrálja a különböző módszerek. Ez nagyobb rugalmasságot biztosít a módszerek nem érhető el. Például, ha utazás közben, és Ön nem fér hozzá az irodai telefonjára.

    ![Regisztrálja a hitelesítési módszereket, és válassza ki a Befejezés gombra][Register]

5. Válassza ki **Befejezés**. Mostantól használhatja az SSPR, amikor szüksége van, a jövőben.

Ha az adatok bevitele **hitelesítéshez használt telefon** vagy **hitelesítési E-mail**, már nem jelenik meg a globális címjegyzékben. Ezeket az adatokat kizárólag Ön és a rendszergazdák láthatják. Kizárólag Ön láthatja a biztonsági kérdésekre adott válaszokat.

A rendszergazdák szükség lehet, hogy erősítse meg a hitelesítési módszereket, hogy továbbra is rendelkezik a megfelelő módszerek regisztrált egy idő után.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

 Az alábbiakban néhány gyakori hibák és megoldásaik:

| Hibaesetét| Milyen hibaüzenetet lát?| Megoldás |
| --- | --- | --- |
| A felhasználói azonosító megadása után jelenik meg a "forduljon a rendszergazdához" lap | Forduljon a rendszergazdához. <br> <br> Azt észleltük, hogy a Microsoft által nem felügyelt felhasználói fiókjának jelszavát. Ennek eredményeképpen nem tudjuk automatikusan alaphelyzetbe állítja a jelszót. <br> <br> További segítségért lépjen kapcsolatba az informatikai részleg. | Ezt az üzenetet lát, mert az informatikai részleg kezeli a jelszót a helyszíni környezetben, és nem teszi lehetővé a jelszó, a **nem tudja elérni a fiókját** hivatkozásra. <br> <br> A jelszó alaphelyzetbe állításához, kérje az informatikai részleg közvetlen segítségét. Tudassa vele, hogy azt szeretné, így az Ön számára ez a funkció is engedélyezhető a jelszó alaphelyzetbe állításához.|
| Egy "a fiók a nem engedélyezett a jelszó-visszaállításhoz" hiba jelenik meg a felhasználói azonosító megadása után | A fiók nincs engedélyezve a jelszó-visszaállításhoz. <br> <br> Sajnáljuk, de az informatikai részleg nem állította be a fiók a szolgáltatással való használatra. <br> <br> Ha szeretné, elküldhetjük a rendszergazda a szervezet a jelszó alaphelyzetbe állításához. | Ezt az üzenetet lát, mert az informatikai részleg nem engedélyezte a jelszó-visszaállítást a szervezet számára a **nem tudja elérni a fiókját** hivatkozásra, vagy még nem rendelkezik licenccel, hogy a funkció használatához. <br> <br> A jelszó alaphelyzetbe állításához válassza ki a **forduljon a rendszergazdához,** hivatkozásra. E-mailt küld a vállalat informatikai személyzetet tart fenn. Az e-mailben lehetővé teszi, hogy alaphelyzetbe állítja a jelszavát, ezért ez a funkció az Ön számára engedélyezheti őket. |
| A felhasználói azonosító megadása után jelenik meg a "nem tudtuk ellenőrizni a fiók" hiba | Nem tudtuk ellenőrizni fiókját. <br> <br> Ha szeretné, elküldhetjük a rendszergazda a szervezet a jelszó alaphelyzetbe állításához. | Ezt az üzenetet lát, mert a jelszó-visszaállítás engedélyezve vannak, de még nem regisztrált a szolgáltatás használatához. Regisztrálhat a jelszó-visszaállítás, nyissa meg a [jelszó-visszaállítási regisztrációs oldalra](https://aka.ms/ssprsetup) után, rendelkezik helyreállt hozzáférést a fiókjához. <br> <br> A jelszó alaphelyzetbe állításához válassza ki a **forduljon a rendszergazdához,** hivatkozásra kattintva egy e-mailt küldhet a cég informatikai személyzetet tart fenn. |

## <a name="next-steps"></a>További lépések

* [A jelszó módosítása az önkiszolgáló jelszó-visszaállítás használatával](active-directory-passwords-update-your-own-password.md)
* [Jelszó-visszaállítási regisztrációs oldal](https://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Ha nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Jelszó-átállítási regisztráció-oldalról a regisztrált módszerekkel és a Befejezés gombra"

