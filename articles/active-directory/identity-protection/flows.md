---
title: Bejelentkezési élmény a Azure AD Identity Protectionkal | Microsoft Docs
description: Áttekintést nyújt azokról a felhasználói élményekről, amikor az Identity Protection enyhített vagy szervizelt egy felhasználót, vagy ha egy házirendhez többtényezős hitelesítés szükséges.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335496"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Bejelentkezési élmény Azure AD Identity Protection

A Azure Active Directory Identity Protection a következőket teheti:

* a többtényezős hitelesítés regisztrálásának megkövetelése a felhasználóktól
* kockázatos bejelentkezések és feltört felhasználók kezelése

Ha a rendszer ezekre a problémákra reagál, hatással van a felhasználó bejelentkezési felületére, mivel a felhasználó nevének megadásával közvetlenül bejelentkezik, és a jelszó nem lesz többé lehetséges. További lépések szükségesek ahhoz, hogy a felhasználó biztonságosan visszakerüljön a vállalatba.

Ez a cikk áttekintést nyújt a felhasználó bejelentkezési élményéről minden olyan esetben, amely előfordulhat.

**Többtényezős hitelesítés**

* Multi-Factor Authentication-regisztráció

**Kockázatos bejelentkezés**

* Kockázatos bejelentkezések helyreállítása
* Kockázatos bejelentkezés letiltva
* Multi-Factor Authentication-regisztráció a kockázatos bejelentkezés során

**Veszélyeztetett felhasználó**

* Sérült fiók helyreállítása
* Sérült fiók blokkolva

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication-regisztráció
A feltört fiók-helyreállítási folyamat és a kockázatos bejelentkezési folyamat legjobb felhasználói élménye az, amikor a felhasználó saját maga állíthatja helyre. Ha a felhasználók regisztrálva vannak a többtényezős hitelesítéshez, akkor már rendelkezik egy, a fiókjához társított telefonszámmal, amely biztonsági kihívásokat adhat át. Nem szükséges ügyfélszolgálati vagy rendszergazdai beavatkozás a fiókok biztonságának helyreállításához. Ezért javasoljuk, hogy a felhasználók a többtényezős hitelesítéshez legyenek regisztrálva. 

A rendszergazdák beállíthat egy szabályzatot, amely megköveteli, hogy a felhasználók a fiókokat a további biztonsági ellenőrzéshez állítsa be. Ez a szabályzat lehetővé teszi a felhasználók számára, hogy legfeljebb 14 napig kihagyják a többtényezős hitelesítés regisztrációját. A 14 napos türelmi időszak nem konfigurálható.

**A multi-Factor Authentication-regisztráció három lépésből áll:**

1. Az első lépésben a felhasználó értesítést kap arról, hogy milyen követelmény a fiók beállítása a többtényezős hitelesítéshez. 
   
    ![Szervizelés](./media/flows/140.png "Szervizelés")
2. A többtényezős hitelesítés beállításához meg kell adnia, hogy a felhasználó hogyan kíván kapcsolatba lépni.
   
    ![Szervizelés](./media/flows/141.png "Szervizelés")
3. A rendszer elküld egy kihívást Önnek, és Önnek kell válaszolnia.
   
    ![Szervizelés](./media/flows/142.png "Szervizelés")

## <a name="risky-sign-in-recovery"></a>Kockázatos bejelentkezések helyreállítása
Ha egy rendszergazda szabályzatot konfigurált a bejelentkezési kockázatokhoz, az érintett felhasználók értesítést kapnak, amikor megpróbálnak bejelentkezni. 

**A kockázatos bejelentkezési folyamat két lépésből áll:** 

1. A felhasználó értesítést kap arról, hogy valami szokatlant észlelt a bejelentkezéssel kapcsolatban, például egy új helyről, eszközről vagy alkalmazásból való bejelentkezést. 
   
    ![Szervizelés](./media/flows/120.png "Szervizelés")
2. A felhasználónak meg kell bizonyítania személyazonosságát egy biztonsági kérdés megoldásával. Ha a felhasználó regisztrálva van a többtényezős hitelesítéshez, meg kell adnia egy biztonsági kódot a telefonszámára. Mivel ez csak egy kockázatos bejelentkezés, és nem sérült fiók, a felhasználónak nem kell módosítania a jelszót ebben a folyamatban. 
   
    ![Szervizelés](./media/flows/121.png "Szervizelés")

## <a name="risky-sign-in-blocked"></a>Kockázatos bejelentkezés letiltva
A rendszergazdák emellett dönthetnek úgy is, hogy bejelentkezési kockázati házirendet állítanak be, amely letiltja a felhasználókat a kockázati szinttől függően. A zárolás feloldásához a végfelhasználóknak kapcsolatba kell lépniük egy rendszergazdával vagy ügyfélszolgálattal, vagy megpróbálnak bejelentkezni ismerős helyről vagy eszközről. Ebben az esetben a többtényezős hitelesítés megoldásával történő önálló helyreállítás nem lehetséges.

![Szervizelés](./media/flows/200.png "Szervizelés")

## <a name="compromised-account-recovery"></a>Sérült fiók helyreállítása
Ha a felhasználó biztonsági házirendje konfigurálva van, a házirendben megadott felhasználói kockázati szintnek megfelelő felhasználók a bejelentkezés előtt el kell mennek a felhasználói kompromisszumos helyreállítási folyamaton. 

**A felhasználó által feltört helyreállítási folyamat három lépésből áll:**

1. A rendszer értesíti a felhasználót, hogy a fiók biztonsága gyanús tevékenység vagy kiszivárgott hitelesítő adatok miatt veszélyben van.
   
    ![Szervizelés](./media/flows/101.png "Szervizelés")
2. A felhasználónak meg kell bizonyítania személyazonosságát egy biztonsági kérdés megoldásával. Ha a felhasználó a többtényezős hitelesítéshez van regisztrálva, akkor a biztonsága illetéktelen lehet. Egy biztonsági kódot kell átadni a telefonszámra. 
   
   ![Szervizelés](./media/flows/110.png "Szervizelés")
3. Végül a felhasználónak meg kell változtatnia a jelszavát, mert valaki más is hozzáférhet a fiókjához. 
   A jelen élmény képernyőképei alább találhatók.
   
   ![Szervizelés](./media/flows/111.png "Szervizelés")

## <a name="compromised-account-blocked"></a>Sérült fiók blokkolva
Ha olyan felhasználót szeretne beolvasni, amelyet a felhasználói kockázat biztonsági szabályzata blokkolt, akkor a felhasználónak kapcsolatba kell lépnie a rendszergazdával vagy az ügyfélszolgálattal. Ebben az esetben a többtényezős hitelesítés megoldásával történő önálló helyreállítás nem lehetséges.

![Szervizelés](./media/flows/104.png "Szervizelés")

## <a name="reset-password"></a>Másik jelszó kérése
Ha a feltört felhasználók nem tudnak bejelentkezni, a rendszergazda ideiglenes jelszót hozhat hozzájuk. A felhasználóknak a következő bejelentkezéskor módosítaniuk kell a jelszavukat.

![Szervizelés](./media/flows/160.png "Szervizelés")

## <a name="see-also"></a>Lásd még

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
