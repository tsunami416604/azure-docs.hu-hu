---
title: Kockázatos bejelentkezések jelentés a portálon | Microsoft Docs
description: Tudnivalók a kockázatos bejelentkezésekre vonatkozó jelentésekről az Azure Active Directory portálon
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bb0cd2c98d0133b0eaffce1730fb9f7cb74bf60
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043542"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon

Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús műveleteket. Minden észlelt művelethez létrejön egy **kockázati észlelés** nevű rekord. További részletek: [Azure ad kockázati észlelések](concept-risk-events.md). 

A [Azure Portal](https://portal.azure.com) biztonsági jelentéseinek eléréséhez válassza a **Azure Active Directory** panelt, majd navigáljon a **Biztonság** szakaszra. 

A kockázati észlelések alapján két különböző biztonsági jelentés van kiszámítva:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/10.png)

A kockázatok észlelését kiváltó szabályzatok konfigurálásával kapcsolatos további információkért lásd: [a felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Ki férhet hozzá a kockázatos bejelentkezések jelentéséhez?

A kockázatos bejelentkezési jelentések a következő szerepkörökben érhetők el a felhasználók számára:

- Biztonsági rendszergazda
- Globális rendszergazda
- Biztonsági olvasó

Ha meg szeretné tudni, hogyan rendelhet hozzá rendszergazdai szerepköröket egy felhasználóhoz Azure Active Directoryban, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD összes kiadása kockázatos bejelentkezési jelentéseket nyújt. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- A **ingyenes Azure Active Directory és az alapszintű kiadásokban**a kockázatos bejelentkezések listája jelenik meg. 

- Emellett a **prémium szintű Azure Active Directory 1** kiadás lehetővé teszi, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázati észlelést. 

- A **prémium szintű Azure Active Directory 2** kiadás az összes mögöttes kockázati észleléssel kapcsolatos legrészletesebb információkat tartalmazza, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Kockázatos bejelentkezések jelentés az Azure AD ingyenes és alapszintű kiadásához

Az Azure AD ingyenes és alapszintű kiadásai a felhasználók számára észlelt kockázatos bejelentkezések listáját tartalmazzák. Minden rekord a következő attribútumokat tartalmazza:

- **User (felhasználó** ) – a bejelentkezési művelet során használt felhasználó neve.
- **IP** – a Azure Active Directoryhoz való kapcsolódáshoz használt eszköz IP-címe.
- **Location (hely** ) – a Azure Active Directoryhoz való kapcsolódáshoz használt hely. Ez a legjobb közelítés a Nyomkövetések, a beállításjegyzék-adatok, a fordított megtekintő és egyéb információk alapján.
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/01.png)

A kockázatos bejelentkezés vizsgálata alapján az alábbi műveletek elvégzésével visszajelzést küldhet az Azure AD-nek:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/21.png)

A jelentés a következő lehetőségeket is biztosítja:

- Erőforrások keresése
- A jelentésadatok letöltése

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Kockázatos bejelentkezések jelentés az Azure AD prémium kiadásaihoz

Az Azure AD prémium kiadásaiban a kockázatos bejelentkezésekről szóló jelentés a következőket tartalmazza:

- Összesített információk a észlelt [kockázat-észlelési típusokról](concept-risk-events.md) . A **prémium szintű Azure ad P1 kiadással**a licenc által nem érintett észlelések nem jelennek meg, mivel a kockázatkezelési **szolgáltatás a további kockázatok**észlelésével jelentkezik. A **prémium szintű Azure ad P2 kiadással**a legrészletesebb információkat kapja meg az összes mögöttes észlelésről.

- Lehetőség a jelentés letöltésére

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/456.png)

Amikor kijelöl egy kockázati észlelést, részletes jelentés-nézetet kap ehhez a kockázat észleléséhez, amely a következőket teszi lehetővé:

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

- A kockázatkezelés észlelési idővonalának áttekintése  

- Tekintse át azoknak a felhasználóknak a listáját, akiknél ez a kockázat észlelése észlelhető

- A kockázati észlelések manuális lezárása. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Időnként előfordulhat, hogy a rendszer a [bejelentkezési jelentés](concept-sign-ins.md)megfelelő bejelentkezési bejegyzése nélkül észleli a kockázatfelmérést. Ennek az az oka, hogy az Identity Protection az **interaktív** és a **nem interaktív** bejelentkezések kockázatát is kiértékeli, míg a bejelentkezési jelentés csak az interaktív bejelentkezéseket jeleníti meg.

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A jelentett kockázati észlelések vizsgálata a felhasználó számára. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/324.png)

A kockázatok észlelésének kivizsgálásához válasszon egyet a listából.  
Ekkor megnyílik a kockázat észlelésének **részleteit tartalmazó panel** . A **részletek** panelen lehetősége van manuálisan lezárni a kockázatkezelést, vagy újraaktiválni a manuálisan lezárt kockázatok észlelését. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Következő lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A kockázatkezelési házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Kockázati észlelési típusok](concept-risk-events.md)
