---
title: Kockázati szabályzatainak konfigurálása az Azure Active Directory identity protection (frissítve) |} A Microsoft Docs
description: Hogyan kockázati szabályzatainak konfigurálása az Azure Active Directory identity protection (frissítve).
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 1ff63dddfa21ba92bb3da44c6711b64e8ad3f1b6
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913936"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>kézikönyv: Kockázati szabályzatainak konfigurálása az Azure Active Directory identity protection (frissítve)


Az Azure AD észleli a kockázati események, amelyek vélhetően feltört identitásokat jelzőit. Kockázati szabályzatainak konfigurálásával meghatározhatja az automatikus válaszok az észlelés eredménye:

- A bejelentkezési kockázati házirend konfigurálhatja a felhasználói bejelentkezés során észlelt valós idejű kockázati események választ. 
- A felhasználói kockázati házirend minden aktív felhasználói kockázat, hogy egy felhasználó idővel észlelt válasz konfigurálhatja.  


## <a name="what-is-the-sign-in-risk-policy"></a>Mi az a bejelentkezési kockázati házirend?

Azure ad-ben elemzi az egyes bejelentkezési felhasználói. Az elemzés célja, észleli a gyanús tevékenységeket és a bejelentkezési származnak. Ha például a bejelentkezési kész névtelen IP-cím használatával, vagy egy ismeretlen helyről kezdeményezett bejelentkezési? Az Azure ad-ben a rendszer képes észlelni a gyanús tevékenységeket is ismertek kockázati események. A kockázati események, amely az észlelt a bejelentkezés során, az Azure AD kiszámítja egy érték alapján. Az érték a valószínűség (alacsony, közepes, magas), hogy a bejelentkezés nem történik meg a jogos felhasználója jelöli. A neve annak a valószínűsége **bejelentkezési kockázati szint**.

A bejelentkezési kockázati házirend egy automatikus válasz egy konkrét bejelentkezési kockázati szint konfigurálható. A válaszban az erőforrásokhoz való hozzáférés letiltása, vagy átadja a többtényezős hitelesítés (MFA) kihívást eléréséhez szükséges.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hogyan érhetem el a bejelentkezési kockázati házirend?
   
A bejelentkezési kockázati házirend van a **konfigurálása** szakaszában a [Azure AD Identity Protection lapról](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Bejelentkezési kockázati házirend](./media/howto-configure-risk-policies/1014.png "bejelentkezési kockázati házirend")


## <a name="sign-in-risk-policy-settings"></a>Bejelentkezési kockázati házirend-beállítások

A bejelentkezési kockázati házirend konfigurálásakor kell beállítani:

- A felhasználók és csoportok, a szabályzat vonatkozik:

    ![Felhasználók és csoportok](./media/howto-configure-risk-policies/11.png)

- A bejelentkezési kockázati szintet, amely elindítja a szabályzat:

    ![Bejelentkezés kockázati szintje](./media/howto-configure-risk-policies/12.png)

- Milyen típusú hozzáférést szeretne kényszeríti ki a bejelentkezési kockázati szint teljesülésekor:  

    ![Hozzáférés](./media/howto-configure-risk-policies/13.png)

- A szabályzat állapotát:

    ![Szabályzat kényszerítése](./media/howto-configure-risk-policies/14.png)


A szabályzat konfigurációs párbeszédpanel újrakonfigurálás hatásának megbecsüléséhez lehetőséget biztosít.

![Becsült hatás](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Mit kell tudni a bejelentkezési kockázati házirend

Konfigurálhatja a bejelentkezési kockázat biztonsági házirend többtényezős hitelesítés megkövetelése:

![MFA megkövetelése](./media/howto-configure-risk-policies/16.png)

Azonban biztonsági okokból a beállítás csak akkor működik, amely a multi-factor Authentication regisztrált felhasználók számára. Identity protection a többtényezős hitelesítés követelménye rendelkező felhasználók letiltja a nem regisztrált multi-factor Authentication még.

Ha azt szeretné, a többtényezős hitelesítés megkövetelése a kockázatos bejelentkezések, a következőket kell elvégeznie:

1. Engedélyezze a [többtényezős hitelesítési regisztrációs házirend](#multi-factor-authentication-registration-policy) az érintett felhasználók számára.

2. Az érintett felhasználókat, hogy jelentkezzen be egy kockázatos munkamenetben az MFA-regisztráció végrehajtásához szükséges.

Ezen lépések elvégzése biztosítja, hogy a többtényezős hitelesítés szükséges a kockázatos bejelentkezés.

A bejelentkezési kockázati házirend van:

- Böngésző forgalom és a modern hitelesítést használó bejelentkezések alkalmazza.

- Tiltsa le a WS-Trust végpont a következő az összevont Identitásszolgáltató, például az AD FS korábbi biztonsági protokollokat használó alkalmazások nem alkalmazza.


A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Kockázatos bejelentkezési helyreállítási](flows.md#risky-sign-in-recovery)
* [Kockázatos bejelentkezés blokkolva](flows.md#risky-sign-in-blocked)  
* [Az Azure AD Identity Protection a bejelentkezési élmény](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>Mi az felhasználói kockázati házirendet?

Azure ad-ben elemzi az egyes bejelentkezési felhasználói. Az elemzés célja, észleli a gyanús tevékenységeket és a bejelentkezési származnak. Az Azure ad-ben a rendszer képes észlelni a gyanús tevékenységeket is ismertek kockázati események. Miközben néhány kockázati események valós idejű észlelése, még nincsenek több időt igénylő kockázati események. Például egy bejelentkezés szokatlan helyekről való lehetetlen utazás észlelése, a rendszer egy normál felhasználói viselkedés megismeréséhez 14 napos betanulási időszakra igényel. Többféle módon oldja meg az észlelt kockázati eseményeket. Például egyes kockázati események manuális oldható meg, vagy megtekintheti a bejelentkezési kockázat vagy feltételes hozzáférési szabályzat felhasználói kockázat használatával oldja fel őket.

Az összes kockázati események, amelyek az észlelt felhasználó számára, és nem megfogalmazott aktív kockázati események nevezzük. Az aktív kockázati események, amelyek a felhasználóval társított felhasználói kockázat nevezzük. A felhasználói kockázat alapján, az Azure ad-ben a valószínűség (alacsony, közepes, magas), hogy feltörték-e egy felhasználó számítja ki. A valószínűsége annak a felhasználói kockázati szint neve.

![Felhasználói kockázat](./media/howto-configure-risk-policies/11031.png)

A felhasználói kockázati házirend egy automatikus válasz konfigurálhatja egy adott felhasználói kockázati szint. Felhasználói kockázati házirendet az erőforrásokhoz való hozzáférés letiltása, vagy egy felhasználói fiók visszaszerzéséhez tiszta állapotot jelszómódosítás megkövetelése.


## <a name="how-do-i-access-the-user-risk-policy"></a>Hogyan férhetek hozzá a felhasználói kockázati házirend?
   
A felhasználói kockázati házirend szerepel a **konfigurálása** szakaszában a [Azure AD Identity Protection lapról](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Felhasználói kockázati házirend](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Felhasználói kockázati házirend-beállítások

A bejelentkezési kockázati házirend konfigurálásakor kell beállítani:

- A felhasználók és csoportok, a szabályzat vonatkozik:

    ![Felhasználók és csoportok](./media/howto-configure-risk-policies/111.png)

- A bejelentkezési kockázati szintet, amely elindítja a szabályzat:

    ![Felhasználói kockázati szint](./media/howto-configure-risk-policies/112.png)

- Milyen típusú hozzáférést szeretne kényszeríti ki a bejelentkezési kockázati szint teljesülésekor:  

    ![Hozzáférés](./media/howto-configure-risk-policies/113.png)

- A szabályzat állapotát:

    ![Szabályzat kényszerítése](./media/howto-configure-risk-policies/114.png)

A szabályzat konfigurációs párbeszédpanel a konfiguráció hatásának megbecsüléséhez lehetőséget biztosít.

![Becsült hatás](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Mit kell tudni a felhasználói kockázat szabályzatok

Biztonsági szabályzat felhasználói kockázat és bejelentkezési függően a kockázati szint esetén a felhasználók megadható.

![Blokkolás](./media/howto-configure-risk-policies/116.png)


A bejelentkezés blokkolása:

* Megakadályozza a létrehozását az új felhasználói kockázati események az érintett felhasználók
* Lehetővé teszi a rendszergazdáknak manuálisan kockázatcsökkentési műveleteket az hatással lenne a felhasználó identitását, és állítsa vissza azt egy biztonságos állapotba


























## <a name="best-practices"></a>Ajánlott eljárások

Kiválasztása egy **magas** küszöbérték csökkenti a szabályzat akkor aktiválódik, és minimalizálja a felhasználók számát.  

Azonban nem tartalmazza **alacsony** és **Közepes** a szabályzatot, amely esetleg blokkolja egy támadó a sérült biztonságú identitás kihasználhassa a kockázatosként megjelölt bejelentkezések.

A házirend beállításakor

- Felhasználók, akik nem / nem lehet többtényezős hitelesítés kihagyása

- Zárja ki a területi beállításokat, amennyiben a szabályzat engedélyezése nem célszerű a felhasználók (például segélyszolgálat nincs hozzáférés)

- Zárja ki a felhasználók, akik várhatóan sok vakriasztások (a fejlesztők, adatbiztonsági elemzők) létrehozása

- Használja a **magas** küszöbérték kezdeti szabályzat bevezetés során, vagy ha kell minimálisra csökkenthető a végfelhasználók által látott kihívásokat.

- Használja a **alacsony** küszöbértéket, ha a szervezet megköveteli a nagyobb biztonság. Válassza a **alacsony** küszöbérték vezet be a további felhasználói bejelentkezési kihívásokat, de a nagyobb biztonság.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbérték egyensúlyozni használhatósági és a biztonság között.





## <a name="next-steps"></a>További lépések

 [Channel 9-on: Az Azure AD és az identitás megjelenítése: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

