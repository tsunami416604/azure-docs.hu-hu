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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014449"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Kockázati jelentésként megjelölt felhasználók az Azure Portalon

Az Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús műveleteket. Minden észlelt művelethez létrejön egy [kockázatészlelés](concept-risk-events.md) nevű rekord.

A biztonsági jelentéseket az [Azure Portalon](https://portal.azure.com) az **Azure Active Directory** panel kiválasztásával érheti el, majd navigálhat a **Biztonság** szakaszra. 

Az észlelt kockázatészlelések a következők kiszámítására szolgálnak:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A kockázatészlelést kiváltó házirendek konfigurálásáról a [Felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)című témakörből megtudhatja. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Milyen Azure AD-licencre van szüksége a veszélyeztetett felhasználók jelentésének eléréséhez?  

Az Azure Active Directory minden kiadása biztosítja a kockázatosként megjelölt felhasználók jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az **Azure Active Directory ingyenes és alapszintű kiadásaiban**a kockázat szempontjából megjelölt felhasználók listáját kapja meg. 

- Emellett az **Azure Active Directory Premium 1-es** kiadás lehetővé teszi, hogy vizsgálja meg az egyes jelentésekben észlelt alapvető kockázatészlelések némelyikét. 

- Az **Azure Active Directory Premium 2-kiadás** a legrészletesebb információkat nyújt az összes mögöttes kockázatészlelésről, és lehetővé teszi a biztonsági házirendek konfigurálását is, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Veszélyeztetett felhasználók jelentés az Azure AD ingyenes és alapszintű kiadásokról

Az Azure AD ingyenes és alapszintű kiadásaiban kockázati jelentésként megjelölt felhasználók biztosítják a sérült felhasználói fiókok listáját. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/03.png)

A felhasználó kiválasztása bejelentkezési adatokat biztosít. A veszélyeztetett felhasználók esetében áttekintheti a felhasználó bejelentkezési előzményeit, és szükség esetén alaphelyzetbe állíthatja a jelszót.

Ez a párbeszédablak a következő lehetőségeket kínálja:

- A jelentés letöltése
- Felhasználók keresése

    ![Kockázatos bejelentkezések](./media/concept-user-at-risk/16.png)

További információkért prémium licencre van szükség.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Veszélyeztetett felhasználók jelentés az Azure AD prémium verzióihoz

Az Azure AD prémium kiadásokban kockázati jelentésként megjelölt felhasználók a következőket biztosítják:

- Egy lista azokról a felhasználói fiókokról, amelyeknek elképzelhető, hogy sérült a biztonsága 

- Összesített információk az észlelt [kockázatészlelési típusokról](concept-risk-events.md)

- Lehetőség a jelentés letöltésére

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

![Kockázatos bejelentkezések](./media/concept-user-at-risk/71.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- Vizsgálja meg a felhasználó jelentett kockázatészleléseit. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/324.png)

Kockázatészlelés vizsgálatához válasszon egyet a listából a **Részletek** panel megnyitásához a kockázatészleléshez. A **Részletek** panelen manuálisan bezárhatja a kockázatészlelést, vagy újraaktiválhat egy manuálisan lezárt kockázatészlelést. 

![Kockázatos bejelentkezések](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>További lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A kockázatjavítási házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md)

