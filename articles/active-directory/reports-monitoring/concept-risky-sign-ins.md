---
title: Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: Tudnivalók a kockázatos bejelentkezésekre vonatkozó jelentésekről az Azure Active Directory portálon
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fab0648a2ad0e0a4c193d37875913dd27bf9af4
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574276"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) biztonsági jelentéseivel megtudhatja, hogy a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok biztonsága. 

Az Azure AD észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt tevékenység esetében létrehoz egy *kockázati esemény* nevű rekordot. További részletek: [Az Azure Active Directory kockázati eseményei](concept-risk-events.md). 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletekért lásd: [a bejelentkezési kockázati szabályzat konfigurálásának](../identity-protection/howto-sign-in-risk-policy.md). 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletekért lásd: [konfigurálása a felhasználói kockázati házirend](../identity-protection/howto-user-risk-policy.md).  

Az [Azure Portalon](https://portal.azure.com) a biztonsági jelentések az **Azure Active Directory** panel **Biztonság** szakaszában találhatók. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatos bejelentkezések jelentéseit.  
A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Már az **Azure Active Directory ingyenes és alapszintű kiadásaiban** is szerepel a kockázatos bejelentkezések listája. 

- Az **Azure Active Directory 1. prémium** kiadása kibővíti ezt a modellt, mert lehetővé teszi azt is, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat minden mögöttes kockázatos eseményről, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory – ingyenes és alapszintű kiadások

Az Azure Active Directory ingyenes és alapszintű kiadása biztosít egy listát, amely az észlelt kockázatos bejelentkezéseket tartalmazza a felhasználókra vonatkozóan. Ez a jelentés a következőket listázza:

- **Felhasználó** – A bejelentkezési művelet során használt felhasználónév
- **IP** – Az Azure Active Directoryhoz való csatlakozáshoz használt eszköz IP-címe
- **Hely** – Az Azure Active Directoryhoz való csatlakozáshoz használt hely
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/01.png)

A kockázatos bejelentkezéssel kapcsolatos vizsgálata alapján visszajelzést küldhet az Azure Active Directorynak az alábbi műveletek formájában:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/21.png)



Ez a jelentés a következő lehetőségeket kínálja:

- Erőforrások keresése
- A jelentésadatok letöltése


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory – prémium szintű kiadások

Az Azure Active Directory prémium szintű kiadásaiban a kockázatos bejelentkezési jelentések a következőket tartalmazzák:

- Összesített adatok az észlelt [kockázati események típusairól](concept-risk-events.md)

- Lehetőség a jelentés letöltésére


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/456.png)


Egy kockázati esemény kiválasztásakor megkapja az esemény részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

- A kockázati esemény észlelési idővonalának áttekintését  

- Azon felhasználók listájának áttekintését, amelyeknél a rendszer kockázati esemény észlelt

- Kockázati események manuális bezárását. 


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/457.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A felhasználóhoz kapcsolódó, jelentett kockázati események vizsgálatát. 


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/324.png)


Egy kockázati esemény vizsgálatához válassza ki azt a listából.  
Ekkor megnyílik az eseményre vonatkozó **Részletek** panel. Az a **részletek** panelen, vagy manuálisan bezárhatja a kockázati eseményeket, vagy egy manuálisan bezárt kockázati esemény újraaktiválása lehetősége van. 


![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/325.png)





## <a name="next-steps"></a>További lépések

- Az Azure Active Directory Identity Protectionnel kapcsolatos további részletekért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

