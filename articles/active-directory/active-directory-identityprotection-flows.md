---
title: "Bejelentkezés során lép az Azure AD Identity Protection |} Microsoft Docs"
description: "A felhasználói élmény áttekintést nyújt, ha Identity Protection problémák elhárításáról vagy javítja a felhasználó, vagy ha a többtényezős hitelesítési házirend szükséges."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f7abbab27a6794c934344c78ba1776c8dbb97088
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Az Azure AD Identity Protection bejelentkezési élmény
Az Azure Active Directory azonosító adatok védelmét a következőket teheti:

* a felhasználóknak a multi-factor authentication regisztrálása
* kockázatos bejelentkezéseket és a sérült biztonságú felhasználók kezelése

A válasz a rendszer ezeket a problémákat a felhasználói bejelentkezés során tapasztal élmény hatással van, mert csak közvetlenül aláírás-a felhasználónév megadásával, és a jelszó nem lehet többé. További lépések szükségesek a felhasználók biztonságosan beolvasandó business programba.

Ez a témakör áttekintést nyújt a felhasználói bejelentkezési felhasználói felület minden olyan esetben, amik akkor léphetnek fel.

**Többtényezős hitelesítés**

* A multi-factor authentication regisztráció

**Bejelentkezés veszélyben**

* Kockázatos bejelentkezési helyreállítási
* Kockázatos bejelentkezés letiltva
* A multi-factor authentication regisztráció egy kockázatos bejelentkezés során

**Felhasználói veszélyben**

* Sérült biztonságú fiók helyreállítási
* Sérült biztonságú fiók blokkolva van

## <a name="multi-factor-authentication-registration"></a>A multi-factor authentication regisztráció
A legjobb felhasználói élmény is, a sérült biztonságú fiók helyreállítási folyamata és a kockázatos bejelentkezési folyamata, akkor, ha a felhasználó önállóan helyre tudja állítani. Ha a felhasználók a multi-factor authentication van regisztrálva, már rendelkeznek használható jelentette biztonsági kihívásokkal átadni a fiókjához társított telefonszám. A fiók biztonsága sérülésétől helyreállítása nincs súgó ügyfélszolgálat vagy a rendszergazdai beavatkozás szükséges. Így az rendelkezik kifejezetten ajánljuk, hogy a felhasználók a többtényezős hitelesítés regisztrált. 

A rendszergazdák a következőket teheti:

* Állítsa be a további biztonsági ellenőrzési felhasználóknak házirend beállítása. 
* engedélyezi a multi-factor authentication regisztráció kihagyása akár 30 napig, abban az esetben, ha szeretnék lehetőséget nyújt a felhasználóknak a türelmi időszak, mielőtt regisztrálná.

**A multi-factor Authentication hitelesítés regisztrációs rendelkezik három lépést:**

1. Az első lépésben a felhasználó élvezheti a követelmény a multi-factor Authentication fiók értesítést. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/140.png "szervizelés")
2. Többtényezős hitelesítés beállításához, hogy a rendszer tudja, hogyan szeretné kapcsolódni kell.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/141.png "szervizelés")
3. A rendszer elküldi a könnyű, és meg kell válaszolni.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/142.png "szervizelés")

## <a name="risky-sign-in-recovery"></a>Kockázatos bejelentkezési helyreállítási
Amikor egy rendszergazda úgy konfigurálta a kockázatok bejelentkezési házirend, az érintett felhasználó értesítést kap próbálnak bejelentkezni. 

**A kockázatos bejelentkezési folyamata van két lépésből áll:** 

1. A felhasználó tájékoztatják, hogy valami szokatlan rendszer észlelte a bejelentkezéssel kapcsolatban, például egy új helyről, eszközről vagy alkalmazásból jelentkezik be. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/120.png "szervizelés")
2. A felhasználó kell igazolnia az identitását egy biztonsági kérdéssel megoldása által. Ha a felhasználó a multi-factor authentication regisztrálva van szükségük round-trip telefonszámukat biztonsági kódot. Mivel ez csak egy kockázatos bejelentkezési és nem sérült biztonságú fiók, a felhasználónak kell-e a nem a folyamatot a jelszó módosítására. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/121.png "szervizelés")

## <a name="risky-sign-in-blocked"></a>Kockázatos bejelentkezés letiltva
A rendszergazdák is beállíthatja a kockázat bejelentkezési házirend beállítása a felhasználók blokkolása után bejelentkezhet attól függően, hogy a kockázati szintjét. Ahhoz, hogy feloldja, a végfelhasználók kapcsolatba kell lépnie egy rendszergazda vagy a help ügyfélszolgálati, vagy próbálnak jelentkezik be egy ismert helyre vagy egy eszközt. A multi-factor authentication megoldása által önálló helyreállítása lehetőség nem érhető el ebben az esetben.

![Szervizelés](./media/active-directory-identityprotection-flows/200.png "szervizelés")

## <a name="compromised-account-recovery"></a>Sérült biztonságú fiók helyreállítási
Ha egy felhasználó kockázat biztonsági házirendet konfiguráltak, felhasználók, akik megfelelnek a felhasználó kockáztatja a házirendben megadott szint (és ezért feltételezik sérült) haladjon végig a felhasználó biztonsági sérülés helyreállítási folyamata, mielőtt azok is bejelentkezhet. 

**A felhasználó biztonsági sérülés helyreállítási folyamat három lépést rendelkezik:**

1. A felhasználót, hogy a fiók biztonsági kockázatnak miatt gyanús tevékenységet vagy hitelesítő adatok szivárgását tájékoztatást kapjon.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/101.png "szervizelés")
2. A felhasználó kell igazolnia az identitását egy biztonsági kérdéssel megoldása által. Ha a felhasználó a multi-factor authentication regisztrálva azok önállóan helyreállítani feltörésének. Akkor kell round-trip telefonszámukat biztonsági kódot. 
   
   ![Szervizelés](./media/active-directory-identityprotection-flows/110.png "szervizelés")
3. Végezetül a felhasználónak meg kell a jelszó módosítására, mivel valaki más is hozzáférhetett a fiókjához. 
   A felhasználói felület képernyőfelvételek alatt van.
   
   ![Szervizelés](./media/active-directory-identityprotection-flows/111.png "szervizelés")

## <a name="compromised-account-blocked"></a>Sérült biztonságú fiók blokkolva van
Ahhoz, hogy a felhasználó által feloldva felhasználói kockázat biztonsági házirend letiltott, a felhasználónak kell forduljon a rendszergazdához vagy a segélyszolgálathoz. A multi-factor authentication megoldása által önálló helyreállítása lehetőség nem érhető el ebben az esetben.

![Szervizelés](./media/active-directory-identityprotection-flows/104.png "szervizelés")

## <a name="reset-password"></a>Jelszó alaphelyzetbe állítása
Sérült biztonságú felhasználók nincs hozzáférése a bejelentkezés, ha a rendszergazda egy ideiglenes jelszót hozhat létre a számukra. A felhasználók módosíthatják a jelszavukat a következő bejelentkezés során lesz.

![Szervizelés](./media/active-directory-identityprotection-flows/160.png "szervizelés")

## <a name="see-also"></a>Lásd még:
* [Az Azure Active Directory azonosító adatok védelmét](active-directory-identityprotection.md) 

