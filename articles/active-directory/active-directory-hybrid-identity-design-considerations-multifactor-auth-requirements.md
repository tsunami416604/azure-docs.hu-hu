---
title: "Hibrid identitás Tervező - Azure multi-factor authentication követelményeinek |} Microsoft Docs"
description: "Feltételes hozzáférés-vezérlést az Azure Active Directory ellenőrzi a megadott feltételek, ha a felhasználó hitelesítése és az alkalmazáshoz való hozzáférés előtt válasszon. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférni az alkalmazáshoz engedélyezett."
documentationcenter: 
services: active-directory
author: femila
manager: billmath
editor: 
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: bbdf60ed22a720f4f735108da79fe3b2ace1660a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás a többtényezős hitelesítési követelmények meghatározása
A világ mobilitási, a felhasználói adatokat és alkalmazásokat a felhőben, és egy eszközről ezek az információk védelme vált kiemelkedő.  Minden nap van egy új főcím kapcsolatos biztonsági problémák.  Bár, nem garantálja az ilyen problémák elleni, a többtényezős hitelesítést, ezek a problémák megelőzése érdekében biztonsági további réteget biztosít.
Indítsa el a multi-factor authentication a szervezetek szükséges követelmények értékelésekor. Ez azt jelenti, hogy mi a szervezet próbál biztonságos.  Ez a kiértékelés fontos, hogy a és a szervezetek felhasználók a multi-factor authentication lehetővé teszi a műszaki követelményeinek meghatározása.

> [!NOTE]
> Ha nem ismeri a többtényezős hitelesítés és a hatása, erősen ajánlott, hogy olvassa el a cikk [Mi az Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) előzetes folytatja a fejezet elolvasása.
> 
> 

Győződjön meg arról, hogy válaszoljon a következő:

* A vállalat próbál Microsoft-alkalmazások védelmét? 
* Hogyan közzétett ezeket az alkalmazásokat?
* A vállalat biztosítja a távoli hozzáférés úgy, hogy az alkalmazottak a helyszíni alkalmazások elérésére?

Ha igen, milyen típusú távelérési? Is fel kell mérnie, amelyben a felhasználók ezeket az alkalmazásokat elérő található. Ez a kiértékelés egy másik fontos eleme a megfelelő a multi-factor authentication-stratégia meghatározása. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Ha a felhasználók fog található?
* Ezek lehetnek bárhol?
* Nem a vállalat kíván létesíteni korlátozások a felhasználó földrajzi helye alapján?

Ezek a követelmények elsajátítása után fontos is a többtényezős hitelesítést a felhasználói követelmények kiértékeléséhez. Ez a kiértékelés fontos, mert azt határozza meg a multi-factor Authentication hitelesítés terítésével követelményei. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* A felhasználók a multi-factor authentication tisztában van?
* Néhány felhasználását lesz szükség a további hitelesítés?  
  * Ha igen, minden esetben, ha külső hálózatokat vagy férnek hozzá bizonyos alkalmazásokat, vagy más feltételek mellett érkező?
* A felhasználóinak kell képzési beállítása és valósítja meg a multi-factor authentication?
* Mik a legfontosabb forgatókönyvek, amely a vállalat lehetővé szeretné tenni a felhasználók a többtényezős hitelesítést?

Miután a fenti kérdések megválaszolása, fogja érti, ha nincsenek a helyszíni már megvalósította a multi-factor authentication. Ez a kiértékelés fontos, hogy a és a szervezetek felhasználók a multi-factor authentication lehetővé teszi a műszaki követelményeinek meghatározása. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Vállalatának meg kell a multi-factor Authentication szolgáltatás a kiemelt jogosultságú fiókok védelméhez?
* Vállalatának meg kell ahhoz, hogy az egyes alkalmazás megfelelőségi okokból MFA?
* Vállalatának meg kell engedélyezéséről az ezen alkalmazás-vagy csak a rendszergazdák az összes jogosult felhasználók számára?
* Szükség van mindig engedélyezve van az MFA- vagy csak amikor a felhasználók bejelentkeznek a vállalati hálózaton kívül?

## <a name="next-steps"></a>További lépések
[A hibrid identitás bevezetési stratégia meghatározása](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)

