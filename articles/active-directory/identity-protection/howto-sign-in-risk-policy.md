---
title: A bejelentkezési kockázati szabályzat konfigurálása a Azure Active Directory Identity Protectionban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a Azure AD Identity Protection bejelentkezési kockázati házirendet.
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
ms.openlocfilehash: 0645e01c8ad9c620b77abd9af6cf7fe7c26ab4ea
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335403"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>kézikönyv: A bejelentkezési kockázati szabályzat konfigurálása

A Azure Active Directory valós időben és offline állapotban észleli a [kockázati események típusait](../reports-monitoring/concept-risk-events.md#risk-event-types) . A felhasználók bejelentkezéséhez észlelt összes kockázati esemény a kockázatos bejelentkezés nevű logikai fogalomhoz járul hozzá. A kockázatos bejelentkezés egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosa nem hajtott végre.

## <a name="what-is-the-sign-in-risk-policy"></a>Mi a bejelentkezési kockázati házirend?

Az Azure AD elemzi a felhasználók minden egyes bejelentkezését. Az elemzés célja, hogy észlelje a bejelentkezéshez kapcsolódó gyanús műveleteket. Például a bejelentkezés névtelen IP-cím használatával történik, vagy ismeretlen helyről kezdeményezett bejelentkezés? Az Azure AD-ben a rendszer által észlelt gyanús műveletek is ismert kockázati események. A bejelentkezés során észlelt kockázati események alapján az Azure AD kiszámítja az értéket. Az érték azt a valószínűséget (alacsony, közepes, magas) jelöli, amelyet a bejelentkezés nem a legitim felhasználó hajt végre. A valószínűség neve **bejelentkezési kockázati szint**.

A bejelentkezési kockázati szabályzat egy adott bejelentkezési kockázati szinthez beállítható automatizált válasz. Válaszában letilthatja az erőforrásokhoz való hozzáférést, vagy megkövetelheti a többtényezős hitelesítés (MFA) kihívásának elérését.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hogyan hozzáférni a bejelentkezési kockázati szabályzathoz?
   
A bejelentkezési kockázati házirend a [Azure ad Identity Protection lap](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) **Konfigurálás** szakaszában található.
   
![Bejelentkezési kockázati házirend](./media/howto-sign-in-risk-policy/1014.png "Bejelentkezési kockázati házirend")

## <a name="policy-settings"></a>Szabályzatbeállítások

A bejelentkezési kockázati házirend konfigurálásakor a következőket kell megadnia:

- Azok a felhasználók és csoportok, amelyekre a szabályzat vonatkozik:

    ![Felhasználók és csoportok](./media/howto-sign-in-risk-policy/11.png)

- A házirendet kiváltó bejelentkezési kockázati szint:

    ![Bejelentkezés kockázati szintje](./media/howto-sign-in-risk-policy/12.png)

- A bejelentkezési kockázati szint teljesülése esetén érvényesíteni kívánt hozzáférés típusa:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- A szabályzat állapota:

    ![Szabályzat érvénybe léptetése](./media/howto-sign-in-risk-policy/14.png)

A házirend konfigurálása párbeszédpanelen lehetősége van az újrakonfigurálás hatásának becslésére.

![Becsült hatás](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Alapismeretek

A bejelentkezési kockázatokra vonatkozó biztonsági szabályzatot az MFA megköveteléséhez is konfigurálhatja:

![MFA megkövetelése](./media/howto-sign-in-risk-policy/16.png)

Biztonsági okokból azonban ez a beállítás csak olyan felhasználók esetében működik, akik már regisztráltak az MFA-hoz. Az Identity Protection az MFA-követelményekkel rendelkező felhasználókat blokkolja, ha még nincsenek regisztrálva az MFA-ban.

Ha a kockázatos bejelentkezésekhez az MFA-t szeretné megkövetelni, a következőket kell tennie:

1. Engedélyezze a [multi-Factor Authentication regisztrációs szabályzatot](howto-mfa-policy.md) az érintett felhasználók számára.
2. Az MFA-regisztráció elvégzéséhez az érintett felhasználóknak nem kockázatos munkamenetbe kell bejelentkezniük.

A lépések elvégzésével biztosíthatja, hogy a többtényezős hitelesítésre van szükség a kockázatos bejelentkezéshez.

A bejelentkezési kockázati szabályzat a következőket eredményezi:

- A rendszer az összes böngésző-forgalomra és bejelentkezésre alkalmazza a modern hitelesítést használva.
- Nem lett alkalmazva a régebbi biztonsági protokollokat használó alkalmazásokra, ha letiltja a WS-Trust végpontot az összevont IDENTITÁSSZOLGÁLTATÓ, például az ADFS-t.

A kapcsolódó felhasználói élmény áttekintését lásd:

* [Kockázatos bejelentkezések helyreállítása](flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés letiltva](flows.md#risky-sign-in-blocked)  
* [Bejelentkezési élmény Azure AD Identity Protection](flows.md)  

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

A Azure AD Identity Protection áttekintéséhez tekintse meg a [Azure ad Identity Protection áttekintését](overview.md).
