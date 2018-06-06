---
title: Bejelentkezés során lép az Azure AD Identity Protection |} Microsoft Docs
description: A felhasználói élmény áttekintést nyújt, ha Identity Protection problémák elhárításáról vagy javítja a felhasználó, vagy ha a többtényezős hitelesítési házirend szükséges.
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 424ea1eca637653c5e7cfed9dc4b970d5afd91ad
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713408"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Az Azure AD Identity Protection bejelentkezési élmény
Az Azure Active Directory azonosító adatok védelmét a következőket teheti:

* a felhasználóknak a multi-factor authentication regisztrálása
* kockázatos bejelentkezéseket és a sérült biztonságú felhasználók kezelése

A válasz a rendszer ezeket a problémákat a felhasználói bejelentkezés során tapasztal élmény hatással van, mert közvetlenül aláírás-a felhasználónév megadásával, és a jelszó nem lehet többé. További lépések szükségesek a felhasználók biztonságosan beolvasandó business programba.

Ez a cikk áttekintést nyújt a felhasználói bejelentkezési felhasználói felület minden olyan esetben, amik akkor léphetnek fel.

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

A rendszergazdák, amely megköveteli a felhasználóktól további biztonsági ellenőrzés a fiókok beállítása házirendet állíthat be. Ez a házirend lehetővé teszi a felhasználóknak a multi-factor authentication regisztráció kihagyása 14 napig. A 14 napos türelmi időszak nem lehet konfigurálni.

**A multi-factor Authentication hitelesítés regisztrációs rendelkezik három lépést:**

1. Az első lépésben a felhasználó élvezheti a követelmény a multi-factor Authentication fiók értesítést. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/140.png "szervizelés")
2. Többtényezős hitelesítés beállításához, hogy a rendszer tudja, hogyan szeretné kapcsolódni kell.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/141.png "szervizelés")
3. A rendszer elküldi a könnyű, és meg kell válaszolni.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/142.png "szervizelés")

## <a name="risky-sign-in-recovery"></a>Kockázatos bejelentkezési helyreállítási
Egy rendszergazda úgy konfigurálta a kockázatok bejelentkezési házirend, az érintett felhasználó értesítést kap bejelentkezés megkísérlésekor. 

**A kockázatos bejelentkezési folyamata van két lépésből áll:** 

1. A felhasználó tájékoztatják, hogy valami szokatlan rendszer észlelte a bejelentkezéssel kapcsolatban, például egy új helyről, eszközről vagy alkalmazásból jelentkezik be. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/120.png "szervizelés")
2. A felhasználó kell igazolnia az identitását egy biztonsági kérdéssel megoldása által. Ha a felhasználó a multi-factor authentication regisztrálva van szükségük round trip egy biztonsági kódot a telefonszámát. Mivel ez csak egy kockázatos bejelentkezési és a nem biztonságos fiókok, a felhasználó nem kell módosítani a jelszót a folyamatot. 
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/121.png "szervizelés")

## <a name="risky-sign-in-blocked"></a>Kockázatos bejelentkezés letiltva
A rendszergazdák is beállíthatja a kockázat bejelentkezési házirend beállítása a felhasználók blokkolása után bejelentkezhet attól függően, hogy a kockázati szintjét. Ahhoz, hogy feloldja, a végfelhasználók kapcsolatba kell lépnie egy rendszergazda vagy a help ügyfélszolgálati, vagy próbálnak jelentkezik be egy ismert helyre vagy egy eszközt. A multi-factor authentication megoldása által önálló helyreállítása lehetőség nem érhető el ebben az esetben.

![Szervizelés](./media/active-directory-identityprotection-flows/200.png "szervizelés")

## <a name="compromised-account-recovery"></a>Sérült biztonságú fiók helyreállítási
Ha egy felhasználó kockázat biztonsági házirendet konfiguráltak, felhasználók, akik megfelelnek a felhasználó kockáztatja a házirendben megadott szint (és ezért feltételezik sérült) haladjon végig a felhasználó biztonsági sérülés helyreállítási folyamata, mielőtt bejelentkeznének. 

**A felhasználó biztonsági sérülés helyreállítási folyamat három lépést rendelkezik:**

1. A felhasználót, hogy a fiók biztonsági kockázatnak miatt gyanús tevékenységet vagy hitelesítő adatok szivárgását tájékoztatást kapjon.
   
    ![Szervizelés](./media/active-directory-identityprotection-flows/101.png "szervizelés")
2. A felhasználó kell igazolnia az identitását egy biztonsági kérdéssel megoldása által. Ha a felhasználó a multi-factor authentication regisztrálva azok önállóan helyreállítani feltörésének. Akkor kell round trip egy biztonsági kódot a telefonszámát. 
   
   ![Szervizelés](./media/active-directory-identityprotection-flows/110.png "szervizelés")
3. Végezetül a felhasználónak meg kell a jelszó módosítására, mivel valaki más is hozzáférhetett a fiókjához. 
   A felhasználói felület képernyőfelvételek alatt van.
   
   ![Szervizelés](./media/active-directory-identityprotection-flows/111.png "szervizelés")

## <a name="compromised-account-blocked"></a>Sérült biztonságú fiók blokkolva van
Ahhoz, hogy a felhasználó által feloldva felhasználói kockázat biztonsági házirend letiltott, a felhasználónak kell forduljon a rendszergazdához vagy a segélyszolgálathoz. A multi-factor authentication megoldása által önálló helyreállítása lehetőség nem érhető el ebben az esetben.

![Szervizelés](./media/active-directory-identityprotection-flows/104.png "szervizelés")

## <a name="reset-password"></a>Új jelszó létrehozása
Sérült biztonságú felhasználók nincs hozzáférése a bejelentkezés, ha a rendszergazda egy ideiglenes jelszót hozhat létre a számukra. A felhasználók módosíthatják a jelszavukat a következő bejelentkezés során lesz.

![Szervizelés](./media/active-directory-identityprotection-flows/160.png "szervizelés")

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

