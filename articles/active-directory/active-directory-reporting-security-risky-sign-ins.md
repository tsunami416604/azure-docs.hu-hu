---
title: "Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs"
description: "Tudnivalók a kockázatos bejelentkezésekre vonatkozó jelentésekről az Azure Active Directory portálon"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/01/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: f66c4c0b7c391b3fc5ee042cc7a6643e46566d3f
ms.contentlocale: hu-hu
ms.lasthandoff: 08/04/2017

---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) biztonsági jelentéseivel megtudhatja, hogy a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok biztonsága. 

Az Azure AD észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt tevékenység esetében létrehoz egy *kockázati esemény* nevű rekordot. További részletek: [Az Azure Active Directory kockázati eseményei](active-directory-identity-protection-risk-events.md). 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: [Kockázatos bejelentkezések](active-directory-identityprotection.md#risky-sign-ins). 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: [Kockázatosként megjelölt felhasználók](active-directory-identityprotection.md#users-flagged-for-risk).  

Az [Azure Portalon](https://portal.azure.com) a biztonsági jelentések az **Azure Active Directory** panel **Biztonság** szakaszában találhatók. 

![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatos bejelentkezések jelentéseit.  
A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Már az **Azure Active Directory ingyenes és alapszintű kiadásaiban** is szerepel a kockázatos bejelentkezések listája. 

- Az **Azure Active Directory 1. prémium** kiadása kibővíti ezt a modellt, mert lehetővé teszi azt is, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat a mögöttes kockázatos eseményekről, és lehetővé teszi olyan biztonsági házirendek konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory – ingyenes és alapszintű kiadások

Az Azure Active Directory ingyenes és alapszintű kiadása biztosít egy listát, amely az észlelt kockázatos bejelentkezéseket tartalmazza a felhasználókra vonatkozóan. A kockázati eseményekről szóló jelentés a következőket biztosítja:

- **Felhasználó** – A bejelentkezési művelet során használt felhasználónév
- **IP** – Az Azure Active Directoryhoz való csatlakozáshoz használt eszköz IP-címe
- **Hely** – Az Azure Active Directoryhoz való csatlakozáshoz használt hely
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota

A jelentés adatai le is tölthetők.

![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/01.png)

A kockázatos bejelentkezéssel kapcsolatos vizsgálata alapján visszajelzést küldhet az Azure Active Directorynak az alábbi műveletek formájában:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/21.png)

További részletek: [Kockázati események manuális lezárása](active-directory-identityprotection.md#closing-risk-events-manually).

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory – prémium szintű kiadások

Az Azure Active Directory prémium szintű kiadásaiban a kockázatos bejelentkezési jelentések a következőket tartalmazzák:

- Összesített adatok az észlelt [kockázati események típusairól](active-directory-identity-protection-risk-events.md)

- Lehetőség a jelentés letöltésére


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Egy kockázati esemény kiválasztásakor megkapja az esemény részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Egy [felhasználóikockázat-csökkentési szabályzat](active-directory-identityprotection.md#user-risk-security-policy) konfigurálását  

- A kockázati esemény észlelési idővonalának áttekintését  

- Azon felhasználók listájának áttekintését, amelyeknél a rendszer kockázati esemény észlelt

- [A kockázati események manuális bezárását](active-directory-identityprotection.md#closing-risk-events-manually) és a manuálisan bezárt kockázati események újbóli aktiválását. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A felhasználóhoz kapcsolódó, jelentett kockázati események vizsgálatát. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Egy kockázati esemény vizsgálatához válassza ki azt a listából.  
Ekkor megnyílik az eseményre vonatkozó **Részletek** panel. A **Részletek** panelen [manuálisan bezárhatja a kockázati eseményeket](active-directory-identityprotection.md#closing-risk-events-manually) vagy újra aktiválhatja a manuálisan bezárt kockázati eseményeket. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Következő lépések

- Az Azure Active Directory Identity Protectionnel kapcsolatos további részletekért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


