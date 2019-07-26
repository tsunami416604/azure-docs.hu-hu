---
title: Kockázatkezelési szabályzatok konfigurálása Azure Active Directory Identity Protectionben (frissítve) | Microsoft Docs
description: Kockázatkezelési szabályzatok konfigurálása Azure Active Directory Identity Protectionben (frissítve).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334029"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>kézikönyv: Kockázatkezelési szabályzatok konfigurálása Azure Active Directory Identity Protectionben (frissítve)

Az Azure AD olyan kockázati eseményeket észlel, amelyek az esetlegesen feltört identitásokra mutató mutatók. A kockázati szabályzatok konfigurálásával automatizált válaszokat adhat meg az észlelési eredményekre:

- A bejelentkezési kockázati házirenddel a felhasználók bejelentkezését követően észlelt valós idejű kockázati eseményekre adott válasz állítható be. 
- A felhasználói kockázati házirend segítségével választ kaphat a felhasználó által az idő múlásával észlelt összes aktív felhasználói kockázatra.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Mi a bejelentkezési kockázati házirend?

Az Azure AD elemzi a felhasználók minden egyes bejelentkezését. Az elemzés célja, hogy észlelje a bejelentkezéshez kapcsolódó gyanús műveleteket. Például a bejelentkezés névtelen IP-cím használatával történik, vagy ismeretlen helyről kezdeményezett bejelentkezés? Az Azure AD-ben a rendszer által észlelt gyanús műveletek is ismert kockázati események. A bejelentkezés során észlelt kockázati események alapján az Azure AD kiszámítja az értéket. Az érték azt a valószínűséget (alacsony, közepes, magas) jelöli, amelyet a bejelentkezés nem a legitim felhasználó hajt végre. A valószínűség neve **bejelentkezési kockázati szint**.

A bejelentkezési kockázati szabályzat egy adott bejelentkezési kockázati szinthez beállítható automatizált válasz. Válaszában letilthatja az erőforrásokhoz való hozzáférést, vagy megkövetelheti a többtényezős hitelesítés (MFA) kihívásának elérését.

Amikor egy felhasználó sikeresen befejezte a bejelentkezési kockázati házirend által aktivált MFA-kérést, visszajelzést küld az Identity Protectionnek, hogy a bejelentkezés a legitim felhasználótól származik. Így az MFA-kérést kiváltó bejelentkezési kockázati esemény automatikusan be lesz zárva, és az Identity Protection megakadályozza, hogy ez az esemény hozzájáruljon a felhasználói kockázat megszerzéséhez. A bejelentkezési kockázati házirend engedélyezése csökkentheti a kockázatos bejelentkezések noisiness, mivel lehetővé teszi a felhasználók számára, hogy az MFA-ra való felszólításkor és a kapcsolódó kockázatos bejelentkezést követően automatikusan lezárja a felhasználókat.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hogyan hozzáférni a bejelentkezési kockázati szabályzathoz?
   
A bejelentkezési kockázati házirend a [Azure ad Identity Protection lap](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) **Konfigurálás** szakaszában található.
   
![Bejelentkezési kockázati házirend](./media/howto-configure-risk-policies/1014.png "Bejelentkezési kockázati házirend")

## <a name="sign-in-risk-policy-settings"></a>Bejelentkezési kockázati házirend beállításai

A bejelentkezési kockázati házirend konfigurálásakor a következőket kell megadnia:

- Azok a felhasználók és csoportok, amelyekre a szabályzat vonatkozik:

   ![Felhasználók és csoportok](./media/howto-configure-risk-policies/11.png)

- A házirendet kiváltó bejelentkezési kockázati szint:

   ![Bejelentkezés kockázati szintje](./media/howto-configure-risk-policies/12.png)

- A bejelentkezési kockázati szint teljesülése esetén érvényesíteni kívánt hozzáférés típusa:  

   ![Access](./media/howto-configure-risk-policies/13.png)

- A szabályzat állapota:

   ![Szabályzat érvénybe léptetése](./media/howto-configure-risk-policies/14.png)

A házirend konfigurálása párbeszédpanelen lehetősége van az újrakonfigurálás hatásának becslésére.

![Becsült hatás](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Tudnivalók a bejelentkezési kockázati házirendekről

A bejelentkezési kockázatokra vonatkozó biztonsági szabályzatot az MFA megköveteléséhez is konfigurálhatja:

![MFA megkövetelése](./media/howto-configure-risk-policies/16.png)

Biztonsági okokból azonban ez a beállítás csak olyan felhasználók esetében működik, akik már regisztráltak az MFA-hoz. Az Identity Protection az MFA-követelményekkel rendelkező felhasználókat blokkolja, ha még nincsenek regisztrálva az MFA-ban.

Ha a kockázatos bejelentkezésekhez az MFA-t szeretné megkövetelni, a következőket kell tennie:

1. Engedélyezze a multi-Factor Authentication regisztrációs szabályzatot az érintett felhasználók számára.
2. Annak megkövetelése, hogy az érintett felhasználók nem kockázatos munkamenetben jelentkezzenek be MFA-regisztráció elvégzéséhez.

A lépések elvégzésével biztosíthatja, hogy a többtényezős hitelesítésre van szükség a kockázatos bejelentkezéshez.

A bejelentkezési kockázati szabályzat a következőket eredményezi:

- A rendszer az összes böngésző-forgalomra és bejelentkezésre alkalmazza a modern hitelesítést használva.
- Nem lett alkalmazva a régebbi biztonsági protokollokat használó alkalmazásokra, ha letiltja a WS-Trust végpontot az összevont IDENTITÁSSZOLGÁLTATÓ, például az ADFS-t.

A kapcsolódó felhasználói élmény áttekintését lásd:

* [Kockázatos bejelentkezések helyreállítása](flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés letiltva](flows.md#risky-sign-in-blocked)  
* [Bejelentkezési élmény Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Mi az a felhasználói kockázati házirend?

Az Azure AD elemzi a felhasználók minden egyes bejelentkezését. Az elemzés célja, hogy észlelje a bejelentkezéshez kapcsolódó gyanús műveleteket. Az Azure AD-ben a rendszer által észlelt gyanús műveletek is ismert kockázati események. Néhány kockázati eseményt valós időben lehet észlelni, több időt igénylő kockázati események is vannak. Ha például szokatlan helyekre való lehetetlen utazást szeretne felderíteni, a rendszernek egy 14 napos kezdeti tanulási időszakot kell megadnia a felhasználó szokásos működésének megismeréséhez. Több lehetőség is van az észlelt kockázati események feloldására. Például az egyes kockázati események manuálisan is feloldhatók, vagy a bejelentkezési kockázat vagy a felhasználói kockázat feltételes hozzáférési szabályzata alapján megoldhatók.

A felhasználó számára észlelt és nem feloldott összes kockázati eseményt aktív kockázati eseménynek nevezzük. A felhasználóhoz társított aktív kockázati eseményeket felhasználói kockázatnak nevezzük. A felhasználói kockázat alapján az Azure AD kiszámítja azt a valószínűséget (alacsony, közepes, magas), amelyet a felhasználó feltört. A valószínűség neve felhasználói kockázati szint.

![Felhasználói kockázatok](./media/howto-configure-risk-policies/11031.png)

A felhasználói kockázati házirend egy adott felhasználói kockázati szinthez beállítható automatizált válasz. A felhasználói kockázati házirend segítségével letilthatja a hozzáférést az erőforrásokhoz, vagy jelszó-módosítást igényelhet a felhasználói fiók tiszta állapotba való visszaállításához.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hogyan hozzáférni a felhasználói kockázati házirendhez?
   
A felhasználói kockázati házirend a [Azure ad Identity Protection lap](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) **configure (Konfigurálás** ) szakaszában található.
   
![Felhasználói kockázati házirend](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Felhasználói kockázati házirend beállításai

A felhasználói kockázati házirend konfigurálásakor a következőket kell megadnia:

- Azok a felhasználók és csoportok, amelyekre a szabályzat vonatkozik:

   ![Felhasználók és csoportok](./media/howto-configure-risk-policies/111.png)

- A házirendet kiváltó bejelentkezési kockázati szint:

   ![Felhasználói kockázati szint](./media/howto-configure-risk-policies/112.png)

- A bejelentkezési kockázati szint teljesülése esetén érvényesíteni kívánt hozzáférés típusa:  

   ![Access](./media/howto-configure-risk-policies/113.png)

- A szabályzat állapota:

   ![Szabályzat érvénybe léptetése](./media/howto-configure-risk-policies/114.png)

A házirend konfigurálása párbeszédpanelen lehetősége van a konfiguráció hatásának becslésére.

![Becsült hatás](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Tudnivalók a felhasználói kockázati házirendekről

Megadhat egy felhasználói kockázati biztonsági házirendet, amely letiltja a felhasználók bejelentkezését a kockázati szinttől függően.

![Blokkolás](./media/howto-configure-risk-policies/116.png)

Bejelentkezés letiltása:

* Megakadályozza az új felhasználói kockázati események létrehozását az érintett felhasználó számára
* Lehetővé teszi a rendszergazdáknak a felhasználó identitását befolyásoló kockázati események manuális szervizelését és biztonságos állapotba való visszaállítását.

## <a name="best-practices"></a>Ajánlott eljárások

A **magas** küszöbérték kiválasztása csökkenti a szabályzatok indításának számát, és minimálisra csökkenti a felhasználókra gyakorolt hatást.  

Azonban nem zárja ki a kockázatnak kitett alacsony és **közepes** **szintű** bejelentkezéseket a szabályzatból, ami esetleg nem blokkolja a támadók számára a sérült identitások kihasználását.

A szabályzat beállításakor

- A többtényezős hitelesítéssel nem rendelkező felhasználók kizárása
- A területi beállításokban lévő felhasználók kizárása, ahol a szabályzat engedélyezése nem praktikus (például nem fér hozzá az ügyfélszolgálathoz)
- Olyan felhasználók kizárása, akik valószínűleg sok hamis pozitívat (fejlesztőket, biztonsági elemzőket) hoznak
- Használjon **magas** küszöbértéket a kezdeti házirend kiépítése során, vagy ha minimálisra kell csökkentenie a végfelhasználók által tapasztalt kihívásokat.
- Ha a szervezet nagyobb biztonságot igényel, **alacsony** küszöbértéket használjon. Az **alacsony** küszöbérték kiválasztásával további felhasználói bejelentkezési kihívásokat is megadhat, de nagyobb biztonságot is jelent.

A legtöbb szervezet számára ajánlott alapértelmezett beállítás egy **közepes** küszöbértékre vonatkozó szabály konfigurálása, amely egyensúlyt teremt a használhatóság és a biztonság között.

## <a name="next-steps"></a>További lépések

 [Channel 9: Azure AD-és identitás-megjelenítés: Identity Protection – előzetes verzió](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
