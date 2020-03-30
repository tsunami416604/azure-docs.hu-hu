---
title: Kockázatos bejelentkezési jelentés a portálon | Microsoft dokumentumok
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
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273843"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Kockázatos bejelentkezésekre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) észleli a felhasználói fiókokhoz kapcsolódó gyanús műveleteket. Minden észlelt művelethez létrejön egy **kockázatészlelés** nevű rekord. További részletek: [Azure AD-kockázatészlelések.](concept-risk-events.md) 

A biztonsági jelentéseket az [Azure Portalon](https://portal.azure.com) az **Azure Active Directory** panel kiválasztásával érheti el, majd navigálhat a **Biztonság** szakaszra. 

A rendszer két különböző biztonsági jelentést számít ki a kockázatészlelések alapján:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/10.png)

A kockázatészlelést kiváltó házirendek konfigurálásáról a [Felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)című témakörből megtudhatja.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Ki férhet hozzá a kockázatos bejelentkezések jelentéshez?

A kockázatos bejelentkezési jelentések a következő szerepkörökben lévő felhasználók számára érhetők el:

- Biztonsági rendszergazda
- Globális rendszergazda
- Biztonsági olvasó

Ha tudni szeretné, hogyan rendelhet felügyeleti szerepköröket egy felhasználóhoz az Azure Active Directoryban, olvassa el [a Rendszergazdai szerepkörök megtekintése és hozzárendelése az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD minden kiadása kockázatos bejelentkezési jelentéseket biztosít. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az **Azure Active Directory ingyenes kiadásában**a kockázatos bejelentkezések listáját kapja meg. 

- Emellett az **Azure Active Directory Premium 1-es** kiadás lehetővé teszi, hogy vizsgálja meg az egyes jelentésekben észlelt alapvető kockázatészlelések némelyikét. 

- Az **Azure Active Directory Premium 2-kiadás** a legrészletesebb információkat nyújt az összes mögöttes kockázatészlelésről, és lehetővé teszi a biztonsági házirendek konfigurálását is, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Kockázatos bejelentkezési jelentés az Azure AD ingyenes kiadására

Az Azure AD ingyenes kiadás a felhasználók számára észlelt kockázatos bejelentkezések listáját tartalmazza. Minden rekord a következő attribútumokat tartalmazza:

- **Felhasználó** – a bejelentkezési művelet során használt felhasználó neve.
- **IP–** Az Azure Active Directoryhoz való csatlakozáshoz használt eszköz IP-címe.
- **Hely** – Az Azure Active Directoryhoz való csatlakozáshoz használt hely. Ez egy legjobb erőfeszítés közelítés alapján nyomon követések, rendszerleíró adatok, fordított felkeresési és egyéb információkat.
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/01.png)

A kockázatos bejelentkezés vizsgálata alapján visszajelzést adhat az Azure AD-nek a következő műveletek et követve:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/21.png)

Ez a jelentés a következőket is lehetővé teszi:

- Erőforrások keresése
- A jelentésadatok letöltése

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Kockázatos bejelentkezési jelentés az Azure AD prémium verzióihoz

A kockázatos bejelentkezések jelentés az Azure AD prémium verziók biztosít Önnek:

- Összesített információk az észlelt [kockázatészlelési típusokról.](concept-risk-events.md) Az **Azure AD Premium P1 kiadással**a licenc által nem szabályozott észlelések a kockázatészlelési bejelentkezésként jelennek **meg, további kockázattal.** Az **Azure AD Premium P2 kiadással**a legrészletesebb információkat kaphatja meg az összes alapul szolgáló észlelésről.

- Lehetőség a jelentés letöltésére

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/456.png)

Amikor kiválaszt egy kockázatészlelést, részletes jelentésnézetet kap ehhez a kockázatészleléshez, amely lehetővé teszi a következőket:

- Egy [felhasználóikockázat-csökkentési szabályzat](../identity-protection/howto-user-risk-policy.md) konfigurálását  

- A kockázatészlelés észlelési ütemtervének áttekintése  

- Tekintse át azoknak a felhasználóknak a listáját, akik esetében ez a kockázatészlelés észlelhető

- Manuálisan zárja le a kockázatészleléseket. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Előfordulhat, hogy a [bejelentkezési jelentésben](concept-sign-ins.md)megfelelő bejelentkezési bejegyzés nélkül talál kockázatészlelést. Ennek az az oka, hogy az Identity Protection kiértékeli a kockázatot mind **az interaktív,** mind **a nem interaktív** bejelentkezések esetében, míg a bejelentkezések jelentés csak az interaktív bejelentkezéseket jeleníti meg.

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- Vizsgálja meg a felhasználó jelentett kockázatészleléseit. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/324.png)

A kockázatészlelés vizsgálatához válasszon egyet a listából.  
Ez megnyitja a **Részletek** panelt a kockázatészleléshez. A **Részletek** panelen manuálisan bezárhatja a kockázatészlelést, vagy újraaktiválhat egy manuálisan lezárt kockázatészlelést. 

![Kockázatos bejelentkezések](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>További lépések

- [A felhasználói kockázati házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [A kockázatjavítási házirend konfigurálása](../identity-protection/howto-user-risk-policy.md)
- [Kockázatészlelési típusok](concept-risk-events.md)
