---
title: A bejelentkezési kockázati szabályzat beállítása az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg az Azure AD Identity Protection bejelentkezési kockázati szabályzat konfigurálása.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 7350cbd3e8aed6098f24d0edaa5807d241890287
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581474"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Útmutató: A bejelentkezési kockázati szabályzat beállítása

Az Azure Active Directory észleli [kockázati események típusairól](../reports-monitoring/concept-risk-events.md#risk-event-types) a valós idejű és offline állapotban van. Minden kockázati esemény észlelt egy bejelentkezhet a felhasználó hozzájárul a kockázatos bejelentkezési neve egy logikai fogalom. Kockázatos bejelentkezés egy mutató a egy bejelentkezési kísérlet, amely előfordulhat, hogy nem hajtott végre egy felhasználói fiók jogos tulajdonosa.


## <a name="sign-in-risk-level"></a>Bejelentkezés kockázati szintje

A bejelentkezési kockázat szintje feltüntetése (magas, közepes vagy alacsony) annak a valószínűségét, hogy egy bejelentkezési kísérlet után nem hajtottak végre egy felhasználói fiók jogos tulajdonosa.

## <a name="mitigating-sign-in-risk-events"></a>Bejelentkezési kockázati események csökkentése

Egy megoldás egy olyan műveletet, korlátozhatja, hogy a támadó egy sérült biztonságú identitás vagy az eszköz biztonsági visszaállítja a identitás vagy az eszköz biztonságos állapotban. A megoldás nem oldja meg a identitás vagy az eszköz társított korábbi bejelentkezési kockázati események.

Csökkentése érdekében automatikusan a kockázatos bejelentkezések, konfigurálhatja a bejelentkezési kockázat biztonsági szabályzatokat. Használja ezeket a házirendeket, érdemes megfontolni a felhasználó vagy a bejelentkezés kockázatos bejelentkezések letiltása vagy a felhasználónak a multi-factor authentication végrehajtása a kockázati szintjét. Ezek a műveletek megakadályozhatja, hogy a támadó egy ellopott identitás okozhatnának kiaknázása, és előfordulhat, hogy adjon egy ideig a identitásának védelmét.

## <a name="sign-in-risk-security-policy"></a>Biztonsági házirend bejelentkezési kockázat
A bejelentkezési kockázati házirend egy feltételes hozzáférési szabályzatot, amely egy adott bejelentkezési kockázat és előre meghatározott feltételeknek és szabályoknak az alapján kockázatcsökkentő vonatkozik.

![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1014.png "bejelentkezési kockázati házirend")

Az Azure AD Identity Protection, hogy lehetővé teszi a csökkenti a kockázatos bejelentkezések kezeléséhez nyújt segítséget:

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1015.png "bejelentkezési kockázati házirend")
* A bejelentkezési kockázati szint küszöbértéke (alacsony, közepes vagy magas), amely elindítja a szabályzat beállítása:

    ![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1016.png "bejelentkezési kockázati házirend")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket:  

    ![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1017.png "bejelentkezési kockázati házirend")
* Váltás a házirend állapota:

    ![MFA-regisztráció](./media/howto-sign-in-risk-policy/403.png "MFA-regisztráció")
* Tekintse át, és azt aktiválása előtt változás hatásának vizsgálatában:

    ![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1018.png "bejelentkezési kockázati házirend")

## <a name="what-you-need-to-know"></a>Mit kell tudni
Konfigurálhatja a bejelentkezési kockázat biztonsági házirend többtényezős hitelesítés megkövetelése:

![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1017.png "bejelentkezési kockázati házirend")

Azonban biztonsági okokból a beállítás csak akkor működik, amely a multi-factor authentication regisztrált felhasználók számára. Ha a többtényezős hitelesítés megkövetelése a feltétel teljesül egy felhasználóhoz, aki még nem regisztrált multi-factor Authentication a felhasználó le van tiltva.

Ajánlott eljárásként Ha azt szeretné, a többtényezős hitelesítés megkövetelése a kockázatos bejelentkezések el a következőket:

1. Engedélyezze a [többtényezős hitelesítési regisztrációs házirend](#multi-factor-authentication-registration-policy) az érintett felhasználók számára.
2. MFA-regisztráció végrehajtásához kockázatos munkamenetben az érintett felhasználók a bejelentkezéshez szükséges

Ezen lépések elvégzése biztosítja, hogy a többtényezős hitelesítés szükséges a kockázatos bejelentkezés.

## <a name="best-practices"></a>Ajánlott eljárások
Kiválasztása egy **magas** küszöbérték csökkenti a szabályzat akkor aktiválódik, és minimalizálja a felhasználók számát.  

Azonban nem tartalmazza **alacsony** és **Közepes** a szabályzatot, amely esetleg blokkolja egy támadó a sérült biztonságú identitás kihasználhassa a kockázatosként megjelölt bejelentkezések.

A házirend beállításakor

* Felhasználók, akik nem / nem lehet többtényezős hitelesítés kihagyása
* Zárja ki a területi beállításokat, amennyiben a szabályzat engedélyezése nem célszerű a felhasználók (például segélyszolgálat nincs hozzáférés)
* Zárja ki a felhasználók, akik várhatóan sok vakriasztások (a fejlesztők, adatbiztonsági elemzők) létrehozása
* Használata egy **magas** küszöbérték során kezdeti házirend, vagy ha kell minimálisra csökkenthető a végfelhasználók által látott kihívásokat.
* Használja a **alacsony** küszöbértéket, ha a szervezet megköveteli a nagyobb biztonság. Válassza a **alacsony** küszöbérték vezet be a további felhasználói bejelentkezési kihívásokat, de a nagyobb biztonság.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbérték egyensúlyozni használhatósági és a biztonság között.

A bejelentkezési kockázati házirend van:

* Böngésző forgalom és a modern hitelesítést használó bejelentkezések alkalmazza.
* Tiltsa le a WS-Trust végpont a következő az összevont Identitásszolgáltató, például az AD FS korábbi biztonsági protokollokat használó alkalmazások nem alkalmazza.

A **kockázati események** az Identity Protection konzolon lap felsorolja az összes eseményt:

* Ez a házirend lett alkalmazva
* Áttekintheti a tevékenység, és megtudhatja, hogy a művelet megfelelő volt-e vagy sem

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Kockázatos bejelentkezési helyreállítási](flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés blokkolva](flows.md#risky-sign-in-blocked)  
* [Az Azure AD Identity Protection a bejelentkezési élmény](flows.md)  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **bejelentkezési kockázati házirend**.

    ![Felhasználói kockázati házirend](./media/howto-sign-in-risk-policy/1014.png "felhasználói kockázati házirend")




## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
