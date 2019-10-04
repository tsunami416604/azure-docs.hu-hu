---
title: A felhasználói kockázati házirend konfigurálása a Azure Active Directory Identity Protectionban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Azure AD Identity Protection felhasználói kockázati házirendet.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126285"
---
# <a name="how-to-configure-the-user-risk-policy"></a>kézikönyv: A felhasználói kockázati szabályzat konfigurálása

A felhasználói kockázattal az Azure AD észleli a felhasználói fiók biztonságának valószínűségét. Rendszergazdaként beállíthat egy felhasználói kockázatra vonatkozó feltételes hozzáférési szabályzatot, amely automatikusan reagál egy adott felhasználói kockázati szintre.
 
Ez a cikk a felhasználói kockázati házirend konfigurálásához szükséges információkat tartalmazza.

## <a name="what-is-a-user-risk-policy"></a>Mi az a felhasználói kockázati házirend?

Az Azure AD elemzi a felhasználók minden egyes bejelentkezését. Az elemzés célja, hogy észlelje a bejelentkezéshez kapcsolódó gyanús műveleteket. Az Azure AD-ben a rendszer által észlelt gyanús műveletek a kockázati észlelések is ismertek. Míg egyes kockázati észlelések valós időben észlelhetők, több időt igénylő kockázati észlelések is megoldhatók. Ha például szokatlan helyekre való lehetetlen utazást szeretne felderíteni, a rendszernek egy 14 napos kezdeti tanulási időszakot kell megadnia a felhasználó szokásos működésének megismeréséhez. Több lehetőség is van az észlelt kockázati észlelések feloldására. Például az egyes kockázati észleléseket manuálisan is megoldhatja, vagy a bejelentkezési kockázat vagy a felhasználói kockázat feltételes hozzáférési szabályzata segítségével megoldható.

A felhasználók számára észlelt és nem feloldott összes kockázati észlelést aktív kockázati észlelésnek nevezzük. A felhasználóhoz társított aktív kockázati észleléseket felhasználói kockázatnak nevezzük. A felhasználói kockázat alapján az Azure AD kiszámítja azt a valószínűséget (alacsony, közepes, magas), amelyet a felhasználó feltört. A valószínűség neve felhasználói kockázati szint.

![Felhasználói kockázatok](./media/howto-user-risk-policy/1031.png)

A felhasználói kockázati házirend egy adott felhasználói kockázati szinthez beállítható automatizált válasz. A felhasználói kockázati házirend segítségével letilthatja a hozzáférést az erőforrásokhoz, vagy jelszó-módosítást igényelhet a felhasználói fiók tiszta állapotba való visszaállításához.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hogyan hozzáférni a felhasználói kockázati házirendhez?
   
A bejelentkezési kockázati házirend a [Azure ad Identity Protection lap](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) **Konfigurálás** szakaszában található.
   
![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Szabályzatbeállítások

A bejelentkezési kockázati házirend konfigurálásakor a következőket kell megadnia:

- Azok a felhasználók és csoportok, amelyekre a szabályzat vonatkozik:

    ![Felhasználók és csoportok](./media/howto-user-risk-policy/11.png)

- A házirendet kiváltó bejelentkezési kockázati szint:

    ![Felhasználói kockázati szint](./media/howto-user-risk-policy/12.png)

- A bejelentkezési kockázati szint teljesülése esetén érvényesíteni kívánt hozzáférés típusa:  

    ![Access](./media/howto-user-risk-policy/13.png)

- A szabályzat állapota:

    ![Szabályzat érvénybe léptetése](./media/howto-user-risk-policy/14.png)

A házirend konfigurálása párbeszédpanelen lehetősége van a konfiguráció hatásának becslésére.

![Becsült hatás](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Alapismeretek

Megadhat egy felhasználói kockázati biztonsági házirendet, amely letiltja a felhasználók bejelentkezését a kockázati szinttől függően.

![Blokkolás](./media/howto-user-risk-policy/16.png)

Bejelentkezés letiltása:

* Megakadályozza az új felhasználói kockázati észlelések létrehozását az érintett felhasználó számára
* Lehetővé teszi a rendszergazdáknak, hogy manuálisan javítsanak a felhasználó identitását befolyásoló kockázati észleléseket, és visszaállítsa azt egy biztonságos állapotba

## <a name="best-practices"></a>Ajánlott eljárások

A **magas** küszöbérték kiválasztása csökkenti a szabályzatok indításának számát, és minimálisra csökkenti a felhasználókra gyakorolt hatást.
Azonban kizárja az alacsony és **közepes** **szintű** felhasználókat, akik a szabályzatban felmerülnek, és nem biztos, hogy biztonságban vannak a korábban feltételezett vagy sérült identitások vagy eszközök.

A szabályzat beállításakor

* Kizárhatja azokat a felhasználókat, akik valószínűleg sok hamis pozitívat (fejlesztőket, biztonsági elemzőket) hoznak
* A területi beállításokban lévő felhasználók kizárása, ahol a szabályzat engedélyezése nem praktikus (például nem fér hozzá az ügyfélszolgálathoz)
* A kezdeti házirendekben **magas** küszöbértéket használjon, vagy ha a végfelhasználók által tapasztalt kihívásokat kell minimálisra csökkentenie.
* Ha a szervezet nagyobb biztonságot igényel, **alacsony** küszöbértéket használjon. Az **alacsony** küszöbérték kiválasztásával további felhasználói bejelentkezési kihívásokat is megadhat, de nagyobb biztonságot is jelent.

A legtöbb szervezet számára ajánlott alapértelmezett beállítás egy **közepes** küszöbértékre vonatkozó szabály konfigurálása, amely egyensúlyt teremt a használhatóság és a biztonság között.

A kapcsolódó felhasználói élmény áttekintését lásd:

* [Sérült fiók helyreállítási folyamata](flows.md#compromised-account-recovery).  
* A [fiók sérült](flows.md#compromised-account-blocked), letiltott folyamata.  

**A kapcsolódó konfiguráció párbeszédpanel megnyitása**:

- A **Azure ad Identity Protection** panel **Konfigurálás** szakaszában kattintson a **felhasználói kockázati házirend**elemre.

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1009.png "Felhasználói kockázati házirend")

## <a name="next-steps"></a>További lépések

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview.md).
