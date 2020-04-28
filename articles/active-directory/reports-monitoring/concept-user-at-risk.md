---
title: Kockázatosként megjelölt felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon | Microsoft Docs
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014449"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>A Azure Portal kockázati jelentésére megjelölt felhasználók

Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús műveleteket. Minden észlelt művelethez létrejön egy [kockázati észlelés](concept-risk-events.md) nevű rekord.

A [Azure Portal](https://portal.azure.com) biztonsági jelentéseinek eléréséhez válassza a **Azure Active Directory** panelt, majd navigáljon a **Biztonság** szakaszra. 

Az észlelt kockázati észlelések a következők kiszámítására használhatók:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A kockázatok észlelését kiváltó szabályzatok konfigurálásával kapcsolatos további információkért lásd: [a felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md). 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Milyen Azure AD-licencre van szükség a veszélyeztetett felhasználóknak szóló jelentéshez való hozzáféréshez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatosként megjelölt felhasználók jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- A **ingyenes Azure Active Directory és az alapszintű kiadásokban**megjelenik a kockázatként megjelölt felhasználók listája. 

- Emellett a **prémium szintű Azure Active Directory 1** kiadás lehetővé teszi, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázati észlelést. 

- A **prémium szintű Azure Active Directory 2** kiadás az összes mögöttes kockázati észleléssel kapcsolatos legrészletesebb információkat tartalmazza, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Az Azure AD ingyenes és alapszintű kiadásaihoz tartozó veszélyeztetett felhasználókról szóló jelentés

Az Azure AD ingyenes és alapszintű kiadásaiban a kockázati jelentésre megjelölt felhasználók listáját az esetlegesen sérült felhasználói fiókok listája tartalmazza. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/03.png)

A felhasználó kiválasztása bejelentkezési adatokat biztosít. A veszélyeztetett felhasználók esetében áttekintheti a felhasználó bejelentkezési előzményeit, és szükség esetén alaphelyzetbe állíthatja a jelszót.

Ez a párbeszédablak a következő lehetőségeket kínálja:

- A jelentés letöltése
- Felhasználók keresése

    ![Kockázatos bejelentkezések](./media/concept-user-at-risk/16.png)

Részletesebb tájékoztatásért prémium szintű licencre van szükség.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Az Azure AD Premium kiadásait érintő kockázati jelentéssel rendelkező felhasználók

Az Azure AD prémium kiadásaiban a kockázati jelentésre megjelölt felhasználók a következőket biztosítják:

- Egy lista azokról a felhasználói fiókokról, amelyeknek elképzelhető, hogy sérült a biztonsága 

- Az észlelt [kockázati észlelési típusok](concept-risk-events.md) összesített adatai

- Lehetőség a jelentés letöltésére

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

![Kockázatos bejelentkezések](./media/concept-user-at-risk/71.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A jelentett kockázati észlelések vizsgálata a felhasználó számára. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/324.png)

A kockázatok észlelésének kivizsgálásához válasszon ki egyet a listából, és nyissa meg a kockázatkezelés **részletei paneljét** . A **részletek** panelen lehetősége van manuálisan lezárni a kockázatkezelést, vagy újraaktiválni a manuálisan lezárt kockázatok észlelését. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>További lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A kockázatkezelési házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

