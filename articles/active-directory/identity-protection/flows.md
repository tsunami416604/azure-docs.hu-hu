---
title: Bejelentkezési élmény az Azure AD Identity Protection |} A Microsoft Docs
description: A felhasználói élmény áttekintést nyújt, ha az Identity Protection problémák elhárításáról vagy szervizelni egy felhasználó, vagy ha a multi-factor authentication szolgáltatás egy házirend által igényelt.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3c1c681e2c7ccd6b5fd3eaa3639853d99cb1e0b7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005211"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Az Azure AD Identity Protection a bejelentkezési élmény
Az Azure Active Directory Identity Protection a következőket teheti:

* felhasználók regisztráljanak a többtényezős hitelesítés megkövetelése
* kockázatos bejelentkezések és felhasználók feltört

Ezeket a problémákat a rendszer a választ a felhasználó bejelentkezési élményére hatással van, mert közvetlen bejelentkezés felhasználói név megadásával, és a jelszó nem lehet többé. További lépések szükségesek, amikor a felhasználó biztonságosan lekérése business programba.

Ez a cikk áttekintést, a felhasználó bejelentkezési élményére minden olyan esetben, amely akkor fordulhat elő.

**Többtényezős hitelesítés**

* A multi-factor authentication regisztráció

**Bejelentkezési kockázati:**

* Kockázatos bejelentkezési helyreállítási
* Kockázatos bejelentkezés blokkolva
* A multi-factor authentication regisztrációs kockázatos bejelentkezés során

**Veszélyeztetett felhasználók**

* Sérült biztonságú fiók helyreállítása
* Sérült biztonságú fiók blokkolása

## <a name="multi-factor-authentication-registration"></a>A multi-factor authentication regisztráció
A legjobb felhasználói élmény is, a feltört fiók helyreállítási folyamat és a kockázatos bejelentkezési folyamata, akkor, ha a helyi helyreállíthatja a felhasználó. Ha a felhasználók a multi-factor authentication van regisztrálva, már van egy biztonsági kihívások átadására használható fiókjával társított telefonszám. Nincs súgó segélyszolgálathoz vagy a rendszergazda bevonása fiók sérülésétől helyre van szükség. Ezért erősen ajánlott utasítani a felhasználókat a multi-factor authentication regisztrált. 

A rendszergazdák olyan szabályzatot, amely megköveteli a felhasználóktól a fiókjaikat a további biztonsági ellenőrzés beállításához állíthatja be. Ez a szabályzat lehetővé teszi a felhasználóknak a többtényezős hitelesítési regisztráció kihagyását 14 napig. A 14 napos türelmi időszakban érték nem módosítható.

**A többtényezős hitelesítési regisztráció három lépésből áll:**

1. Az első lépésben a felhasználó élvezheti a fiók regisztrálásához a multi-factor authentication szolgáltatás beállítása a követelménnyel kapcsolatban értesítést. 
   
    ![Szervizelési](./media/flows/140.png "szervizelés")
2. Többtényezős hitelesítés beállításához, meg kell, hogy a rendszer tudja, hogyan szeretné elvégezni.
   
    ![Szervizelési](./media/flows/141.png "szervizelés")
3. A rendszer elküldi a vonatkozó kérdést állít be, és meg kell válaszolni.
   
    ![Szervizelési](./media/flows/142.png "szervizelés")

## <a name="risky-sign-in-recovery"></a>Kockázatos bejelentkezési helyreállítási
Amikor egy rendszergazda úgy konfigurálta egy házirendet a bejelentkezési kockázat, az érintett felhasználók megpróbálnak bejelentkezni, amikor értesítést kap. 

**A kockázatos bejelentkezési folyamat két lépésből áll:** 

1. A felhasználó értesítést kap, hogy szokatlan körülményt kapcsolatban a bejelentkezés, például egy új helyről, eszközről vagy alkalmazásból jelentkezik be észlelt. 
   
    ![Szervizelési](./media/flows/120.png "szervizelés")
2. A felhasználó személyazonossága igazolására egy biztonsági kérdéssel megoldására van szükség. Ha a felhasználó regisztrálva a többtényezős hitelesítéshez kell kerekíteni kivételre egy biztonsági kódot a telefonszámát. Mivel ez csak egy kockázatos bejelentkezéseket, és nem egy sérült biztonságú fiókot, a felhasználó nem kell módosítani a jelszót a folyamatban. 
   
    ![Szervizelési](./media/flows/121.png "szervizelés")

## <a name="risky-sign-in-blocked"></a>Kockázatos bejelentkezés blokkolva
A rendszergazdák a bejelentkezési kockázati szabályzat beállítása után jelentkezzen be a kockázati szint függően felhasználók számára is beállíthatja. Le nem tiltott lekéréséhez a végfelhasználóknak kapcsolatba kell lépnie az egy rendszergazda vagy a segélyszolgálathoz vagy megpróbálnak egy jól ismert helyről és eszközről jelentkezik be. Önálló megoldása a multi-factor authentication által helyreállítása lehetőség nem ebben az esetben.

![Szervizelési](./media/flows/200.png "szervizelés")

## <a name="compromised-account-recovery"></a>Sérült biztonságú fiók helyreállítása
Felhasználói kockázati biztonsági szabályzat van konfigurálva, amikor a felhasználók, akik megfelelnek a felhasználó kockázati a házirendben megadott szint (és így feltételezik biztonsága sérült) haladjon végig a felhasználói biztonsági sérülés helyreállítási folyamatot, mielőtt bejelentkeznének. 

**A felhasználó biztonsági sérülés helyreállítási folyamat három lépésből áll:**

1. A felhasználó értesítést kap, hogy a fiók biztonsági gyanús tevékenység miatt veszélyben van, vagy a kiszivárgott hitelesítő adatok.
   
    ![Szervizelési](./media/flows/101.png "szervizelés")
2. A felhasználó személyazonossága igazolására egy biztonsági kérdéssel megoldására van szükség. Ha a felhasználó regisztrálva van a multi-factor authentication önálló helyreállítását végezhetik illetéktelen kezekbe kerüljenek. Akkor kell round kivételre telefonszámán egy biztonsági kódot. 
   
   ![Szervizelési](./media/flows/110.png "szervizelés")
3. Végül a felhasználónak kötelező jelszó módosítására, mivel valaki más volna hozzáférést a fiókjához. 
   Ez a tapasztalat Pillanatképeiért alatt van.
   
   ![Szervizelési](./media/flows/111.png "szervizelés")

## <a name="compromised-account-blocked"></a>Sérült biztonságú fiók blokkolása
A felhasználó által feloldva felhasználói kockázati biztonsági házirendet letiltott lekéréséhez a felhasználónak kell forduljon a rendszergazdához, vagy a segélyszolgálathoz. Önálló megoldása a multi-factor authentication által helyreállítása lehetőség nem ebben az esetben.

![Szervizelési](./media/flows/104.png "szervizelés")

## <a name="reset-password"></a>Új jelszó létrehozása
Feltört felhasználók hozzáférése a bejelentkezés, ha a rendszergazda ideiglenes jelszót hozhat létre számukra. A felhasználóknak kell módosítania kell a jelszavát a következő bejelentkezés során.

![Szervizelési](./media/flows/160.png "szervizelés")

## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

