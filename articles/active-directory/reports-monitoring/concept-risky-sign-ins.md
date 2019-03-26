---
title: Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Tudnivalók a kockázatos bejelentkezésekre vonatkozó jelentésekről az Azure Active Directory portálon
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e125f8e1e7909c5866a03c0571f49ec42d690a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437934"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt művelet, egy rekord nevű egy **kockázati esemény** jön létre. További részletekért lásd: [Azure ad-ben kockázati események](concept-risk-events.md). 

Érheti el a biztonsági jelentések, a [az Azure portal](https://portal.azure.com) kiválasztásával a **Azure Active Directory** panelre, és ugorjon a **biztonsági** szakaszban. 

Két különböző biztonsági jelentés áll, amelyek kiszámítása a kockázati események alapján:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/10.png)

Ismerje meg, hogyan konfigurálhatja a házirendeket a kockázati eseményeket kiváltó, lásd: [konfigurálása a felhasználói kockázati házirend](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>A kockázatos bejelentkezések jelentés férhet hozzá?

A kockázatos bejelentkezések jelentéseit a felhasználók számára a következő szerepkörök érhetők el:

- Biztonsági rendszergazda
- Globális rendszergazda
- Biztonsági olvasó

Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználó kapcsolatban lásd: [nézet és rendelhet hozzá rendszergazdai szerepköröket az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD minden kiadása biztosítja a kockázatos bejelentkezések jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az a **Azure Active Directory ingyenes és alapszintű kiadásaiban**, kap egy kockázatos bejelentkezések listája. 

- Emellett a **Azure Active Directory Premium 1** edition lehetővé teszi, hogy megvizsgáljon néhány, amely az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat minden mögöttes kockázatos eseményről, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Kockázatos bejelentkezések jelentés az Azure Active Directory ingyenes és alapszintű kiadása

Az Azure AD ingyenes és alapszintű kiadása biztosít egy listát a kockázatos bejelentkezések, amely az észlelt a felhasználók számára. Minden rekord tartalmazza a következő attribútumokat:

- **Felhasználói** – a felhasználó a bejelentkezési művelet során használt nevét.
- **IP** – az Azure Active Directoryban való kapcsolódáshoz használt eszköz IP-cím.
- **Hely** – Azure Active Directoryban való kapcsolódáshoz használt helyet. Ez az a legjobb erőfeszítés előállításához, nyomkövetéseket, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján.
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/01.png)

Kapcsolatos, a kockázatos bejelentkezés vizsgálata alapján visszajelzést küldhet az Azure AD a következő műveletekkel:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/21.png)

Ez a jelentés is nyújt lehetőséget:

- Erőforrások keresése
- A jelentésadatok letöltése

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Az Azure AD prémium kiadásaiban a kockázatos bejelentkezések jelentés

Az Azure AD prémium kiadásaiban a kockázatos bejelentkezések jelentés a következőket tartalmazza:

- Összesített adatok a [kockázati események típusairól](concept-risk-events.md) észlelt. Az a **Azure AD Premium P1 edition**, a kockázati esemény jelenik meg, amelyek nem tartoznak a licenc **bejelentkezési további észlelt kockázattal rendelkező**. Az a **Azure AD Premium P2 kiadás**, a legrészletesebb információkat minden mögöttes észlelés kap.

- Lehetőség a jelentés letöltésére

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/456.png)

Egy kockázati esemény kiválasztásakor megkapja az esemény részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

- A kockázati esemény észlelési idővonalának áttekintését  

- Azon felhasználók listájának áttekintését, amelyeknél a rendszer kockázati esemény észlelt

- Kockázati események manuális bezárását. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Egyes esetekben előfordulhat, anélkül, hogy a megfelelő bejelentkezési bejegyzés egy kockázati esemény a [bejelentkezésekre](concept-sign-ins.md). Ennek az az oka az Identity Protection kockázati kiértékeli mindkét **interaktív** és **nem interaktív** bejelentkezések, mivel a bejelentkezések jelentés azt mutatja, csak az interaktív bejelentkezések.

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A felhasználóhoz kapcsolódó, jelentett kockázati események vizsgálatát. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/324.png)

Egy kockázati esemény vizsgálatához válassza ki azt a listából.  
Ekkor megnyílik az eseményre vonatkozó **Részletek** panel. A **Részletek** panelen manuálisan bezárhatja a kockázati eseményeket vagy újra aktiválhatja a manuálisan bezárt kockázati eseményeket. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>További lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A felhasználóikockázat-csökkentési házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Kockázati események típusai](concept-risk-events.md)
