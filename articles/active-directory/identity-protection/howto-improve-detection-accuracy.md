---
title: Az észlelési pontosság javítása Azure Active Directory Identity Protectionban (frissítve) | Microsoft Docs
description: Az észlelési pontosság javítása Azure Active Directory Identity Protectionban (frissítve).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333942"
---
# <a name="how-to-improve-the-detection-accuracy"></a>kézikönyv: Az észlelés pontosságának javítása 

Az Identity Protection olyan mechanizmusokat biztosít, amelyekkel visszajelzést küldhet az Azure AD-nek a környezetben felderített kockázatok észlelése érdekében. A visszajelzések megadásához ellenőrizheti az észlelt kockázatos felhasználó vagy bejelentkezési esemény állapotát. A Microsoft-felhasználók ezt a visszajelzést az aktuális kockázati észlelések elvégzéséhez és a jövőbeli észlelések pontosságának javításához. 

## <a name="what-is-detection"></a>Mi az észlelés?

Az észlelés a gyanús tevékenységek azonosításának folyamata a felhasználói fiókokkal együtt. Az Azure AD által észlelt gyanús tevékenységek neve [kockázati esemény](../reports-monitoring/concept-risk-events.md). Az észlelési folyamat az adaptív gépi tanulási algoritmusokon és a heurisztikus műveleteken alapul, hogy észlelje a felhasználók kockázati eseményeit.

Az észlelési eredmények segítségével megállapítható, hogy a felhasználók és a bejelentkezések veszélyeztetettek-e. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Hogyan javíthatom az észlelési pontosságot?

Mivel az észlelés egy automatizált folyamat, lehetséges, hogy az Azure AD jelentések hamis pozitívak. Az észlelési pontosság javításához visszajelzéseket biztosít az Azure AD számára az észlelési eredményekkel kapcsolatban.

Az észlelési pontosságot háromféleképpen lehet javítani: a feltört bejelentkezések megerősítése, a biztonságos bejelentkezés megerősítése és a felhasználói kockázat elvetése. Ezt a következő jelentésekben végezheti el:

- **Kockázatos bejelentkezések jelentése –** A kockázatos bejelentkezések jelentésben ellenőrizheti, hogy a bejelentkezések biztonságosak vagy feltörtek-e
- **Kockázatos felhasználók jelentés –** A kockázatos felhasználók jelentésben elkerülheti a felhasználói kockázatot 

Visszajelzését az Azure AD dolgozza fel az észlelési eredmények pontosságának javítása érdekében. Általában visszajelzést kell küldenie a felhasználói kockázat vagy a bejelentkezési kockázat vizsgálatának részeként. További információ: [a kockázatos felhasználók és bejelentkezések vizsgálata](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Feltörés megerősítése

A bejelentkezési eseménynek az Azure AD-be feltört jeleként való megerősítése, hogy az identitás tulajdonosa nem adta meg a bejelentkezést. Ha a "feltörtek megerősítése" lehetőséget választja, az Azure AD-t

- Növelje az érintett felhasználó felhasználói kockázatát magasra.
- A kockázati eseményeket észlelő gépi tanulás optimalizálásának elősegítése
- További intézkedések végrehajtása a szervezet további biztonsága érdekében

Sérült bejelentkezés megerősítése:

- **A kockázatos bejelentkezési jelentés** – ez a beállítás lehetővé teszi, hogy egy vagy több bejelentkezési eseménynél erősítse meg a sérült bejelentkezést.

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/07.png)

- A **kockázatos bejelentkezések jelentésének részletes nézete** – ez a beállítás lehetővé teszi, hogy a kockázatos bejelentkezések jelentésében megerősítse a kiválasztott bejelentkezési esemény feltört fiókját. 

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Megerősítés biztonságosként

A bejelentkezési esemény biztonságos jelekként való megerősítése az Azure AD-ben, hogy a bejelentkezést a megfelelő identitás tulajdonosa **hagyta** jóvá. Ha a "biztonság megerősítése" lehetőséget választja, az Azure AD a következőket teszi:

- A kiválasztott bejelentkezések felhasználói kockázati hozzájárulásának visszaállítása
- A mögöttes kockázati események lezárása
- A kockázati eseményeket észlelő gépi tanulás optimalizálásának elősegítése
- További intézkedések végrehajtása a szervezet további biztonsága érdekében
 
Biztonságos bejelentkezés megerősítése a következőben:

- **A kockázatos bejelentkezési jelentés** – ez a beállítás lehetővé teszi a biztonságos bejelentkezés megerősítését egy vagy több bejelentkezési esemény esetében.

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/08.png)

- A **kockázatos bejelentkezések jelentésének részletes nézete** – ez a beállítás lehetővé teszi a biztonságos bejelentkezés megerősítését a kockázatos bejelentkezések jelentés kiválasztott bejelentkezési eseményéhez. 

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Felhasználói kockázat elvetése

Ha már elvégezte a kockázatos felhasználó szervizelési műveleteit, vagy úgy gondolja, hogy hamisan vannak megjelölve, akkor elkerülheti a felhasználó kockázatát. A felhasználó kockázatának hiányában a felhasználót nem kockázatos állapotba állítja vissza. A kiválasztott felhasználóhoz tartozó összes múltbeli kockázatos bejelentkezés és kockázati esemény el lesz vetve.

A jelentett felhasználói kockázatot a következőket követheti el:

- **A kockázatos felhasználók jelentés** – ez a beállítás lehetővé teszi, hogy egy vagy több kiválasztott felhasználónál utasítsa el a felhasználói kockázatot.

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/02.png)

- **A részletek nézet** – ez a beállítás lehetővé teszi, hogy utasítsa el a felhasználói kockázatot a kiválasztott felhasználó számára a felhasználói kockázat jelentésében. 

   ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/01.png)

**Tudnivalók:**

- Ezt a műveletet nem lehet visszaállítani.
- A művelet befejezéséhez néhány percet is igénybe vehet, ezért ne küldje el újra a kérelmet.
- Ezt a műveletet csak akkor hajthatja végre, ha az AD kezeli a felhasználó hitelesítő adatait. 

## <a name="best-practices"></a>Ajánlott eljárások

A felhasználó kockázatának elutasítása az egyik lehetőség, ha a felhasználói kockázati házirend letiltotta őket, és nem tudja önmagát szervizelni, mert nincs engedélyezve a jelszó-visszaállítás és/vagy az MFA engedélyezése. Ebben az esetben érdemes meggyőződni arról, hogy a felhasználó ezt követően regisztrálja a jelszó-visszaállítást és az MFA-t, hogy képesek legyenek a jövőbeli kockázati események önkiszolgálására.

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview-v2.md).
