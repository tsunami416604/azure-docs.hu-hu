---
title: A felhasználói kockázati szabályzat beállítása az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Identity Protection felhasználói kockázati házirend.
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
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 3ba8c93d158a6ff80c3db788924503e9d4e87242
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378119"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Útmutató: A felhasználói kockázati szabályzat beállítása

A felhasználói kockázat, az Azure AD észleli a valószínűsége annak, hogy egy felhasználói fiókot feltörték-e. Rendszergazdaként konfigurálhatja a felhasználói kockázati feltételes hozzáférési szabályzatot, az automatikusan, egy adott felhasználói kockázat szintjének megfelelően válaszolnak.
 
Ez a cikk egy felhasználói kockázati házirend konfigurálásához szükséges információkat biztosít.


## <a name="what-is-a-user-risk-policy"></a>Mi az felhasználói kockázati házirendet?

Azure ad-ben elemzi az egyes bejelentkezési felhasználói. Az elemzés célja, észleli a gyanús tevékenységeket és a bejelentkezési származnak. Az Azure ad-ben a rendszer képes észlelni a gyanús tevékenységeket is ismertek kockázati események. Miközben néhány kockázati események valós idejű észlelése, még nincsenek több időt igénylő kockázati események. Például egy bejelentkezés szokatlan helyekről való lehetetlen utazás észlelése, a rendszer egy normál felhasználói viselkedés megismeréséhez 14 napos betanulási időszakra igényel. Többféle módon oldja meg az észlelt kockázati eseményeket. Például egyes kockázati események manuális oldható meg, vagy megtekintheti a bejelentkezési kockázat vagy feltételes hozzáférési szabályzat felhasználói kockázat használatával oldja fel őket.

Az összes kockázati események, amelyek az észlelt felhasználó számára, és nem megfogalmazott aktív kockázati események nevezzük. Az aktív kockázati események, amelyek a felhasználóval társított felhasználói kockázat nevezzük. A felhasználói kockázat alapján, az Azure ad-ben a valószínűség (alacsony, közepes, magas), hogy feltörték-e egy felhasználó számítja ki. A valószínűsége annak a felhasználói kockázati szint neve.

![Felhasználói kockázat](./media/howto-user-risk-policy/1031.png)

A felhasználói kockázati házirend egy automatikus válasz konfigurálhatja egy adott felhasználói kockázati szint. Felhasználói kockázati házirendet az erőforrásokhoz való hozzáférés letiltása, vagy egy felhasználói fiók visszaszerzéséhez tiszta állapotot jelszómódosítás megkövetelése.


## <a name="how-do-i-access-the-user-risk-policy"></a>Hogyan férhetek hozzá a felhasználói kockázati házirend?
   
A bejelentkezési kockázati házirend van a **konfigurálása** szakaszában a [Azure AD Identity Protection lapról](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Szabályzat beállításai

A bejelentkezési kockázati házirend konfigurálásakor kell beállítani:

- A felhasználók és csoportok, a szabályzat vonatkozik:

    ![Felhasználók és csoportok](./media/howto-user-risk-policy/11.png)

- A bejelentkezési kockázati szintet, amely elindítja a szabályzat:

    ![Felhasználói kockázati szint](./media/howto-user-risk-policy/12.png)

- Milyen típusú hozzáférést szeretne kényszeríti ki a bejelentkezési kockázati szint teljesülésekor:  

    ![Hozzáférés](./media/howto-user-risk-policy/13.png)

- A szabályzat állapotát:

    ![Szabályzat kényszerítése](./media/howto-user-risk-policy/14.png)

A szabályzat konfigurációs párbeszédpanel a konfiguráció hatásának megbecsüléséhez lehetőséget biztosít.

![Becsült hatás](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Alapismeretek

Biztonsági szabályzat felhasználói kockázat és bejelentkezési függően a kockázati szint esetén a felhasználók megadható.

![Blokkolás](./media/howto-user-risk-policy/16.png)


A bejelentkezés blokkolása:

* Megakadályozza a létrehozását az új felhasználói kockázati események az érintett felhasználók
* Lehetővé teszi a rendszergazdáknak manuálisan kockázatcsökkentési műveleteket az hatással lenne a felhasználó identitását, és állítsa vissza azt egy biztonságos állapotba

## <a name="best-practices"></a>Ajánlott eljárások

Kiválasztása egy **magas** küszöbérték csökkenti a szabályzat akkor aktiválódik, és minimalizálja a felhasználók számát.
Azonban nem tartalmazza **alacsony** és **Közepes** a szabályzatot, amely előfordulhat, hogy nem biztonságos identitások vagy eszközöket, amelyek a kockázatosként megjelölt felhasználók korábban gyanús vagy ismert legyen feltörni.

A házirend beállításakor

* Zárja ki a felhasználók, akik várhatóan sok vakriasztások (a fejlesztők, adatbiztonsági elemzők) létrehozása
* Zárja ki a területi beállításokat, amennyiben a szabályzat engedélyezése nem célszerű a felhasználók (például segélyszolgálat nincs hozzáférés)
* Használata egy **magas** küszöbérték során kezdeti házirend, vagy ha kell minimálisra csökkenthető a végfelhasználók által látott kihívásokat.
* Használja a **alacsony** küszöbértéket, ha a szervezet megköveteli a nagyobb biztonság. Válassza a **alacsony** küszöbérték vezet be a további felhasználói bejelentkezési kihívásokat, de a nagyobb biztonság.

A legtöbb szervezet számára ajánlott alapértelmezés szerint a rendszer a szabály konfigurálása egy **Közepes** küszöbérték egyensúlyozni használhatósági és a biztonság között.

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [Feltört fiók helyreállítási folyamat](flows.md#compromised-account-recovery).  
* [Feltört fiók blokkolása folyamat](flows.md#compromised-account-blocked).  

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **felhasználói kockázati házirend**.

    ![Felhasználói kockázati házirend](./media/howto-user-risk-policy/1009.png "felhasználói kockázati házirend")




## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
