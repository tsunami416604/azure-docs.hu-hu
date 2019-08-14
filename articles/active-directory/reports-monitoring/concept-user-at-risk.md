---
title: Kockázatosként megjelölt felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon | Microsoft Docs
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 950d0dc2f4a1a88e0c317b3398c7492cc6c52e2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989837"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>A Azure Portal kockázati jelentésére megjelölt felhasználók

Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús műveleteket. Minden észlelt művelethez létrejön egy [kockázati esemény](concept-risk-events.md) nevű rekord.

A [Azure Portal](https://portal.azure.com) biztonsági jelentéseinek eléréséhez válassza a **Azure Active Directory** panelt, majd navigáljon a **Biztonság** szakaszra. 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A kockázati eseményeket kiváltó szabályzatok konfigurálásával kapcsolatos további információkért lásd: [a felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md). 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Milyen Azure AD-licencre van szükség a veszélyeztetett felhasználóknak szóló jelentéshez való hozzáféréshez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatosként megjelölt felhasználók jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- A **ingyenes Azure Active Directory és**az alapszintű kiadásokban megjelenik a kockázatként megjelölt felhasználók listája. 

- Emellett a **prémium szintű Azure Active Directory 1** kiadással megvizsgálhatja az egyes jelentésekhez észlelt mögöttes kockázati eseményeket. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat minden mögöttes kockázatos eseményről, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.


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

- Összesített adatok az észlelt [kockázati események típusairól](concept-risk-events.md)

- Lehetőség a jelentés letöltésére

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

![Kockázatos bejelentkezések](./media/concept-user-at-risk/71.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A felhasználóhoz kapcsolódó, jelentett kockázati események vizsgálatát. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/324.png)

Egy kockázati esemény vizsgálatához válassza ki azt a listából a kockázati eseményhez tartozó **Részletek** panel megnyitásához. A **Részletek** panelen manuálisan bezárhatja a kockázati eseményeket vagy újra aktiválhatja a manuálisan bezárt kockázati eseményeket. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>További lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A kockázatkezelési házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

