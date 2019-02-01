---
title: Hogyan lehet az Azure Active Directory Identity Protection (frissítve) észlelési pontosságának |} A Microsoft Docs
description: Tudnivalók az Azure Active Directory Identity Protection (frissítve) észlelési pontosságának.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: d2b477c31898884761f260e9520913c67c7c6f83
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488917"
---
# <a name="how-to-improve-the-detection-accuracy"></a>kézikönyv: Az észlelési pontosságának növelése 

Identitásvédelmet biztosít mechanizmus visszajelzés küldése a kockázati észlelések a környezetében az Azure ad-hez. Visszajelzést, ellenőrizheti az észlelt kockázatos felhasználó vagy a bejelentkezési események állapotát. Microsoft-felhasználók a visszajelzés a művelet végrehajtása a jelenlegi kockázati észlelések és a jövőbeli észlelési pontosságának javítása. 


## <a name="what-is-detection"></a>Mi az észlelési?

Észlelés az a folyamat együtt a felhasználói fiókok gyanús tevékenységek azonosításához. Az Azure AD képes észlelni a gyanús tevékenységek az úgynevezett [kockázati esemény](../reports-monitoring/concept-risk-events.md). A folyamat során az adaptív gépi tanulási algoritmusok és heurisztika a felhasználók számára a kockázati események észlelése alapján történik.

Az észlelés eredménye segítségével meghatározhatja, hogy felhasználók és bejelentkezések veszélyben vannak. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Hogyan javíthatom észlelési pontosságának?

Mivel az észlelés a egy automatizált folyamattal, lehetséges, hogy az Azure AD-jelentések vakriasztások. Visszajelzés küldése az Azure AD a észlelésének eredménye vonatkozó észlelési pontosságának javíthatja.

Észlelési pontosságának növelése érdekében három módja van: sérült biztonságú bejelentkezési, comfirm biztonságos bejelentkezési erősítse meg, és a felhasználói kockázat bezárása. Ehhez az alábbi jelentések közül:

- **Kockázatos bejelentkezések jelentés -** a kockázatos bejelentkezések jelentés akkor is győződjön meg róla, hogy bejelentkezések csökkentett vagy sérült biztonságú

- **Kockázatos felhasználók jelentés -** a kockázatos felhasználók jelentésben, bezárhatja a felhasználói kockázat 

A visszajelzések dolgozza fel az észlelési eredmények pontosságának javítása az Azure ad-ben. Általában visszajelzést egy felhasználói kockázat és bejelentkezési kockázat vizsgálat részeként. További információkért lásd: [kockázatos felhasználók és bejelentkezések](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Megerősítés kockázatosként

A bejelentkezési esemény megerősítése biztonsága sérült, jelzi az Azure AD, a bejelentkezés nem volt engedélyezett az identitás tulajdonosa. Amikor kiválasztja a "feltört megerősítése", az Azure AD fog

- Növelheti a felhasználói kockázat az érintett felhasználó magas.

- A gépi tanulási, amely észleli a kockázati események optimalizálása érdekében
 
- Hajtsa végre a további intézkedések tovább a szervezet védelme érdekében



Sérült biztonságú bejelentkezési ellenőrzése:

- **A kockázatos bejelentkezések jelentés** – Ez a beállítás lehetővé teszi, hogy meg kell erősítenie egy sérült biztonságú bejelentkezési egy vagy több bejelentkezési eseményeket.

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/07.png)

- **A Részletek nézetben, a kockázatos bejelentkezések jelentés** – Ez a beállítás lehetővé teszi, hogy erősítse meg a kockázatos bejelentkezések jelentés a kijelölt bejelentkezési esemény egy sérült biztonságú fiókot. 

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Megerősítés biztonságosként


Biztonságos jelek, az Azure AD-t a bejelentkezési esemény megerősítése, hogy a bejelentkezési **volt** által az adott identitás tulajdonosa. Ha "megerősítése biztonságos" lehetőséget választja, az Azure AD fog:

- Visszaállítás a felhasználói kockázat hozzájárulását a kiválasztott bejelentkezések

- Zárja be a mögöttes kockázatos eseményekről

- A gépi tanulási, amely észleli a kockázati események optimalizálása érdekében

- Hajtsa végre a további intézkedések tovább a szervezet védelme érdekében
 

Egy biztonságos jelentkezzen be a ellenőrzése:

- **A kockázatos bejelentkezések jelentés** – Ez a beállítás lehetővé teszi, hogy meg kell erősítenie a biztonságos bejelentkezés egy vagy több bejelentkezési eseményeket.

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/08.png)

- **A Részletek nézetben, a kockázatos bejelentkezések jelentés** – Ez a beállítás lehetővé teszi egy biztonságos bejelentkezési a kockázatos bejelentkezések jelentés a kijelölt bejelentkezési esemény megerősítéséhez. 

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Felhasználói kockázat elvetése

Ha már elvégzett szervizelési műveletek kockázati felhasználó számára, vagy úgy érzi, hogy tévesen jelölt meg kockázatos, bezárhatja a felhasználói kockázat. A felhasználó kockázati elvetése visszaállítja a felhasználó nem kockázatos állapotba. Összes korábbi kockázatos bejelentkezések és a kockázati események a kiválasztott felhasználó fog eltűnik.


Bezárhatja a jelentett felhasználói kockázat:

- **A kockázatos felhasználók jelentés** – Ez a beállítás lehetővé teszi, hogy a felhasználói kockázat bezárása egy vagy több kijelölt felhasználók.

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/02.png)

- **A részleteket megjelenítő nézetet** – Ez a beállítás engedélyezi, hogy a felhasználó kockázati jelentés a kiválasztott felhasználó a felhasználói kockázat bezárása. 

    ![Felhasználói kockázat elvetése](./media/howto-improve-detection-accuracy/01.png)


**Tudnivalók:**

- Ez a művelet nem vonható vissza.

- Ez a művelet hajtható végre, ezért érdemes nem újból elküldeni a kérést néhány percet is igénybe vehet.

- Ez a művelet csak akkor végezzen, ha AD kezeli a felhasználó hitelesítő adatait. 



## <a name="best-practices"></a>Ajánlott eljárások

A felhasználó kockázati elvetése módja egy feloldásának őket, ha a felhasználói kockázati házirend által letiltott és nem lehet önmaga javítása miatt nem rendelkezik a jelszó-visszaállítás és/vagy a többtényezős hitelesítés engedélyezve van. Ebben a helyzetben érdemes biztosítása a felhasználónak ezután regisztrál a jelszó-visszaállítás és a többtényezős hitelesítés úgy tudja elérni a helyi kockázatcsökkentési műveleteket minden jövőbeli.


## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview-v2.md).


