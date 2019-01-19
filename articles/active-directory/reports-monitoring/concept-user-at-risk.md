---
title: Kockázatosként megjelölt felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon | Microsoft Docs
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/17/2019
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7b27dbbf34f1c19efa0fc21af71339ce6cacd9d8
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410818"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Megjelölt felhasználók szóló jelentés az Azure Portalon

Az Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt művelet, egy rekord nevű egy [kockázati esemény](concept-risk-events.md) jön létre.

Érheti el a biztonsági jelentések, a [az Azure portal](https://portal.azure.com) kiválasztásával a **Azure Active Directory** panelre, és ugorjon a **biztonsági** szakaszban. 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

Ismerje meg, hogyan konfigurálhatja a házirendeket a kockázati eseményeket kiváltó, lásd: [konfigurálása a felhasználói kockázati házirend](../identity-protection/howto-user-risk-policy.md). 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Milyen Azure AD-licencre van szüksége a felhasználókról szóló jelentés eléréséhez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatosként megjelölt felhasználók jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az a **Azure Active Directory ingyenes és alapszintű kiadásaiban**, kockázatosként megjelölt felhasználók listájának beolvasása. 

- Emellett a **Azure Active Directory Premium 1** edition lehetővé teszi, hogy megvizsgáljon néhány, amely az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat minden mögöttes kockázatos eseményről, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Felhasználókról szóló jelentés az Azure AD ingyenes és alapszintű kiadása

A kockázatosként megjelölt felhasználókról szóló jelentés az Azure ad ingyenes és alapszintű kiadásai biztosít felhasználói fiókokat, előfordulhat, hogy sérült a biztonsága listáját. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/03.png)

Az egyes felhasználók kijelölésével biztosít a bejelentkezési adatokat. A veszélyeztetett felhasználók esetében áttekintheti a felhasználó bejelentkezési előzményeit, és szükség esetén alaphelyzetbe állíthatja a jelszót.

Ez a párbeszédablak a következő lehetőségeket kínálja:

- A jelentés letöltése
- Felhasználók keresése

    ![Kockázatos bejelentkezések](./media/concept-user-at-risk/16.png)

Részletesebb információkért premium licenc szükséges.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Az Azure AD prémium szintű kiadásokban szóló jelentés a felhasználók

Szóló jelentés az Azure AD prémium kiadásaiban a kockázatosként megjelölt felhasználókról tartalmazza:

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
- [A felhasználóikockázat-csökkentési házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

