---
title: A feltételes hozzáférés mi van ha eszköz – Azure Active Directory
description: Ismerje meg, hogyan ismerheti meg a feltételes hozzáférési szabályzatok környezetre gyakorolt hatását.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620687"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>A Feltételes hozzáférés eszköz Mi lenne, ha eszközével kapcsolatos hibaelhárítás

[A feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) az Azure Active Directory (Azure AD) egyik képessége, amely lehetővé teszi annak szabályozását, hogy a jogosult felhasználók hogyan férnek hozzá a felhőalapú alkalmazásokhoz. Honnan tudja, hogy mire számíthat a feltételes hozzáférési házirendek től a környezetben? A kérdés megválaszolásához használhatja a **Feltételes hozzáférés mi után eszköz ét.**

Ebből a cikkből megtudhatja, hogyan használhatja ezt az eszközt a feltételes hozzáférési házirendek teszteléséhez.

## <a name="what-it-is"></a>Mi ez?

A **feltételes hozzáférés, ha a házirend eszköz** lehetővé teszi a feltételes hozzáférési házirendek környezetre gyakorolt hatásának megismerése. Ahelyett, hogy tesztelené a szabályzatok manuális végrehajtásával több bejelentkezést, ez az eszköz lehetővé teszi egy felhasználó szimulált bejelentkezésének kiértékelését. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést. A jelentés nem csak az alkalmazott feltételes hozzáférési házirendeket sorolja fel, hanem [a klasszikus házirendeket](policy-migration.md#classic-policies) is, ha léteznek.    

A **Mi után** eszköz segítségével gyorsan meghatározhatja az adott felhasználóra vonatkozó házirendeket. Az információkat használhatja, például ha el kell hárítania egy problémát.    

## <a name="how-it-works"></a>Működés

A **Feltételes hozzáférés , ha eszközelőször**meg kell konfigurálnia a szimulálni kívánt bejelentkezési forgatókönyv beállításait. Ezek a beállítások többek között:

- A tesztelni kívánt felhasználó 
- A felhőalkalmazások, amelyekhez a felhasználó megpróbálna hozzáférni
- A konfigurált felhőalkalmazásokhoz való hozzáférés elvégzésének feltételei
     
Következő lépésként kezdeményezhet egy szimulációs futtatást, amely kiértékeli a beállításokat. Csak az engedélyezett házirendek képezik a kiértékelési futtatás részét.

Az értékelés befejezése után az eszköz jelentést készít az érintett házirendekről.

## <a name="running-the-tool"></a>Az eszköz futtatása

Az Azure Portalon megtalálja a **Mi a ha** eszközt a Feltételes hozzáférés – **[szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** lapon.

Az eszköz elindításához a házirendek listájának tetején lévő eszköztáron kattintson a **Mi lenne, ha**gombra.

![What If](./media/what-if-tool/01.png)

A kiértékelés futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a rész a szimulációfuttatásának beállításaival kapcsolatos információkat tartalmazza.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Felhasználó

Csak egy felhasználót jelölhet ki. Ez az egyetlen kötelező mező.

### <a name="cloud-apps"></a>Felhőalapú alkalmazások

A beállítás alapértelmezett beállítása Az **Összes felhőalapú alkalmazás.** Az alapértelmezett beállítás a környezetben elérhető összes szabályzat kiértékelését végzi. Leszűkítheti a hatókört az adott felhőalapú alkalmazásokat érintő szabályzatokra.

### <a name="ip-address"></a>IP-cím

Az IP-cím egyetlen IPv4-cím, amely a [helyfeltételt](location-condition.md)utánozza. A cím a felhasználó által a bejelentkezéshez használt eszköz internetes címét jelöli. Az eszköz IP-címét például a [Mi az ÉN IP-címem](https://whatismyipaddress.com)elemre navigálva ellenőrizheti.    

### <a name="device-platforms"></a>Eszközplatformok

Ez a beállítás az [eszköz platformok állapotának](concept-conditional-access-conditions.md#device-platforms) utánzatát utánozza, és az összes platform megfelelőjét képviseli (beleértve a **nem támogatottakat is).** 

### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás az [ügyfélalkalmazások állapotát](concept-conditional-access-conditions.md#client-apps-preview)utánozza.
Alapértelmezés szerint ez a beállítás az összes olyan házirend kiértékelését eredményezi, amelynek **böngésző-** vagy **mobilalkalmazásai és asztali ügyfelei** egyenként vagy mindkettő be van jelölve. Észleli az Exchange **ActiveSync (EAS)** rendszerére vonatkozó házirendeket is. Ezt a beállítást a következők kiválasztásával szűkítheti:

- **Böngésző,** hogy értékelje az összes politika, amelynek legalább **böngésző** kiválasztva. 
- **Mobilalkalmazások és asztali ügyfelek,** hogy értékelje az összes olyan szabályzatot, amelylegalább **mobilalkalmazások at és asztali ügyfeleket** jelölt ki. 

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás a [bejelentkezési kockázati feltételt](concept-conditional-access-conditions.md#sign-in-risk)utánozza.   

## <a name="evaluation"></a>Értékelés 

A kiértékelést a **Mi**lenne, ha gombra kattintva kezdi meg. Az értékelés eredménye egy jelentést ad, amely a következőkből áll: 

![What If](./media/what-if-tool/03.png)

- Annak jelzése, hogy léteznek-e klasszikus házirendek a környezetben
- A felhasználóra vonatkozó irányelvek
- A felhasználóra nem érvényes irányelvek

Ha [klasszikus szabályzatok](policy-migration.md#classic-policies) léteznek a kiválasztott felhőalapú alkalmazásokhoz, egy jelző jelenik meg. A kijelzőre kattintva a klasszikus házirendek oldalra lesz átirányítva. A klasszikus szabályzatok lapon áttelepítheti a klasszikus házirendet, vagy egyszerűen letilthatja azt. Az oldal bezárásával visszatérhet a kiértékelési eredményhez.

A kiválasztott felhasználóra vonatkozó házirendek listájában megtalálhatja a [támogatási vezérlők](concept-conditional-access-grant.md) és a [munkamenet-vezérlők](concept-conditional-access-session.md) listáját is, amelyeket a felhasználónak teljesítenie kell.

A felhasználóra nem vonatkozó szabályzatok listájában megtalálhatja és megtalálhatja azokat az okokat is, amelyek miatt ezek a szabályzatok nem érvényesek. Minden felsorolt házirend esetében az ok az első olyan feltételt jelöli, amely nem teljesült. A szabályzat nem alkalmazott egyik lehetséges oka a letiltott házirend, mert nem értékeli kitévtovább.   

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan konfigurálhat feltételes hozzáférési szabályzatot, olvassa [el az MFA megkövetelése bizonyos alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel című témakört.](app-based-mfa.md)
- Ha készen áll a feltételes hozzáférési szabályzatok konfigurálására a környezetéhez, olvassa el az Azure Active Directory feltételes hozzáféréssel kapcsolatos [gyakorlati tanácsait.](best-practices.md) 
- ha klasszikus szabályzatokat szeretne áttelepíteni, olvassa el [a Klasszikus szabályzatok áttelepítése az Azure Portalon című témakört.](policy-migration.md)  
