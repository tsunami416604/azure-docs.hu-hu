---
title: Hitelesítő adatok regisztrálása a saját jelszavának alaphelyzetbe állításához – Azure AD
description: Regisztrálja az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatásának ellenőrzési módszerének adatait, így rendszergazdai segítség nélkül állíthatja vissza saját jelszavát.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6dddd13f31366e5a99d6c68ab82b048de4064b2
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681361"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>A saját jelszavának alaphelyzetbe állításához regisztrálja az ellenőrzési módszer adatait

> [!IMPORTANT]
> Azért van itt, mert nem tud bejelentkezni? Ha igen, tekintse [meg a munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md)című témakört.

Végfelhasználóként visszaállíthatja a jelszavát, vagy saját maga oldhatja fel a fiókját, ha Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítást (SSPR) használ. A funkció használatához regisztrálnia kell az ellenőrzési módszereket, vagy meg kell erősítenie a rendszergazda által feltöltött előre meghatározott ellenőrzési módszereket.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR hitelesítés adatok regisztrálása vagy megerősítése

1. Nyissa meg az eszközön a webböngészőt, és lépjen a [jelszó-visszaállítási regisztrációs oldalra](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).
2. Adja meg a felhasználónevét és a rendszergazda által megadott jelszót.
3. Attól függően, hogy az informatikai részleg hogyan konfigurálta a dolgokat, az alábbi beállítások közül egy vagy több konfigurálható és ellenőrizhető. Ha a rendszergazda jogosult az Ön adatainak használatára, feltöltheti néhány információt.
    * **Irodai telefon**: csak a rendszergazda állíthatja be ezt a beállítást.
    * **Hitelesítő telefon**: ezt a beállítást egy másik telefonszámra állíthatja be, amelyhez hozzáféréssel rendelkezik. Ilyen lehet például egy mobiltelefon, amely szöveges vagy hívást tud fogadni.
    * **Hitelesítési e-mail**: ezt a beállítást egy másodlagos e-mail-címre kell beállítani, amelyet az alaphelyzetbe állítani kívánt jelszó használata nélkül érhet el.
    * **Biztonsági kérdések**: a rendszergazda jóváhagyta a kérdésekre adott válaszokat. Nem használhatja ugyanazt a kérdést vagy választ többször is.
4. Adja meg és ellenőrizze a rendszergazda által igényelt adatokat. Ha több lehetőség is rendelkezésre áll, javasoljuk, hogy több metódust regisztráljon. Ez rugalmasságot biztosít, ha az egyik metódus nem érhető el. Ilyen például az, amikor utazik, és nem fér hozzá az irodai telefonhoz.

    ![Ellenőrzési módszerek regisztrálása és a Befejezés kiválasztása][Register]

5. Válassza a **Befejezés**lehetőséget. Mostantól a jövőben is használhatja a SSPR-t.

Ha **hitelesítő** vagy **hitelesítési e-mail-címet**ad meg, az nem jelenik meg a globális könyvtárban. Ezeket az adatokat kizárólag Ön és a rendszergazdák láthatják. Csak a biztonsági kérdésekre adott válaszok láthatók.

Előfordulhat, hogy a rendszergazdáknak egy adott idő elteltével meg kell erősíteniük az ellenőrzési módszereket annak biztosításához, hogy továbbra is a megfelelő módszerek legyenek regisztrálva.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásuk

 Íme néhány gyakori hiba eset és megoldásuk:

| Hiba esete| Milyen hibát lát?| Megoldás |
| --- | --- | --- |
| "Forduljon a rendszergazdához" oldal a felhasználói azonosító megadása után | Forduljon a rendszergazdához. <br> <br> A rendszer azt észlelte, hogy a felhasználói fiók jelszavát nem a Microsoft felügyeli. Ennek eredményeképpen nem lehet automatikusan alaphelyzetbe állítani a jelszavát. <br> <br> További segítségért vegye fel a kapcsolatot az informatikai részleggel. | Ezt az üzenetet látja, mert az informatikai részleg a helyi környezetben kezeli a jelszavát, és nem teszi lehetővé, hogy alaphelyzetbe állítsa a **fiókja** hivatkozását. <br> <br> A jelszó alaphelyzetbe állításához vegye fel a kapcsolatot az informatikai részleggel közvetlenül a segítségért. Tudassa velünk, hogy szeretné alaphelyzetbe állítani a jelszavát, hogy engedélyezze ezt a funkciót.|
| "A fiók nincs engedélyezve a jelszó-visszaállításhoz" hibaüzenet jelenik meg a felhasználói azonosító megadása után | A fiókja nincs engedélyezve a jelszó-visszaállításhoz. <br> <br> Sajnos az informatikai részleg nem állította be a fiókját a szolgáltatással való használatra. <br> <br> Ha szeretné, felvehetjük a kapcsolatot a szervezet rendszergazdájával, hogy alaphelyzetbe állítsa a jelszavát. | Ezt az üzenetet látja, mert az informatikai munkatárs nem engedélyezte a jelszó-visszaállítást a szervezet számára a **nem tud hozzáférni a fiók** hivatkozásához, vagy nem rendelkezik licenccel a funkció használatához. <br> <br> A jelszó alaphelyzetbe állításához válassza a **kapcsolat rendszergazdaként** hivatkozást. A rendszer e-mailt küld a vállalat informatikai munkatársának. Az e-mailben megtudhatja, hogy szeretné-e alaphelyzetbe állítani a jelszavát, így engedélyezheti ezt a funkciót. |
| "Nem sikerült ellenőrizni a fiókját" hibaüzenet jelenik meg a felhasználói azonosító megadása után | Nem lehetett ellenőrizni a fiókját. <br> <br> Ha szeretné, felvehetjük a kapcsolatot a szervezet rendszergazdájával, hogy alaphelyzetbe állítsa a jelszavát. | Ezt az üzenetet látja, mert engedélyezve van a jelszó alaphelyzetbe állítása, de nem regisztrált a szolgáltatás használatára. Ha regisztrálni szeretne a jelszó-visszaállításra, lépjen a [jelszó-visszaállítási regisztrációs oldalra](https://aka.ms/ssprsetup) , miután visszanyerte a fiókhoz való hozzáférést. <br> <br> A jelszó alaphelyzetbe állításához jelölje be a **rendszergazda** hivatkozása, amely e-mailt küld a vállalat informatikai munkatársának. |

## <a name="next-steps"></a>Következő lépések

* [Jelszó módosítása az önkiszolgáló jelszó-visszaállítás használatával](active-directory-passwords-update-your-own-password.md)
* [Jelszó-visszaállítási regisztrációs oldal](https://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Ha nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Jelszó-visszaállítási regisztrációs oldal a regisztrált metódusok és a Befejezés gomb megjelenítéséhez"

