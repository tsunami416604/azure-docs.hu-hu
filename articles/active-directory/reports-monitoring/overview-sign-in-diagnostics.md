---
title: Mik a bejelentkezési diagnosztika az Azure AD-ben? | Microsoft Docs
description: Általános áttekintést nyújt a bejelentkezési diagnosztika szolgáltatásról az Azure AD-ben.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: e113753bee5c0a94fbec47a2ea14b98c1779a394
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578137"
---
# <a name="what-are-sign-in-diagnostics-in-azure-ad"></a>Mik a bejelentkezési diagnosztika az Azure AD-ben?

Az Azure AD rugalmas biztonsági modellt biztosít annak szabályozására, hogy a felhasználók milyen műveleteket végezhetnek a felügyelt erőforrásokkal. Az ehhez az erőforrásokhoz való hozzáférést nem csak a maga vezérli, hanem azt is, hogy hogyan férhet hozzájuk. A rugalmasság általában egy bizonyos fokú összetettséggel jár, mivel a konfigurációs beállítások száma megtörtént. A bonyolultság a hibák kockázatának növelésére is képes.

Rendszergazdaként olyan megoldásra van szüksége, amely megfelelő szintű betekintést nyújt a rendszer tevékenységeibe, így egyszerűen diagnosztizálhatja és megoldhatja a problémákat. Az Azure AD-hez készült bejelentkezési diagnosztika példa erre az eszközre. A diagnosztika segítségével elemezheti, hogy mi történt a bejelentkezés során, és milyen műveleteket hajthat végre a problémák megoldásához anélkül, hogy a Microsoft támogatási szolgálatához kellene tartoznia.

Ez a cikk áttekintést nyújt arról, hogy az eszköz milyen módon működik, és hogyan használhatja azt.


## <a name="requirements"></a>Követelmények

A bejelentkezési diagnosztika az Azure AD minden kiadásában elérhető.<br> Az eszköz használatához globális rendszergazdai tagnak kell lennie az Azure AD-ben.

## <a name="how-it-works"></a>Működés

Az Azure AD-ben a bejelentkezési kísérletekre adott válasz nem csak az Ön számára van kötve, hanem a bérlőhöz való hozzáféréshez is. Rendszergazdaként például megadhatja a bérlő összes aspektusát, miközben Ön bejelentkezett a vállalati hálózatról. Előfordulhat azonban, hogy akkor is blokkolva van, ha egy nem megbízható hálózatról ugyanazzal a fiókkal jelentkezik be. Annak érdekében, hogy a rendszer nagyobb rugalmassággal reagáljon a bejelentkezési kísérletre, előfordulhat, hogy olyan forgatókönyvekben végezheti el a megvalósítást, amelyeken a bejelentkezési kísérletek hibakeresésére van szükség. A bejelentkezési diagnosztika egy olyan szolgáltatás, amely elemzi az adatok bekapcsolását, és adott üzeneteket jelenít meg arról, hogy mi történt, és javaslatokat tesz a probléma megoldására. Az Azure AD bejelentkezési diagnosztikája úgy lett kialakítva, hogy lehetővé tegye a bejelentkezési hibák öndiagnosztizálását. A diagnosztikai folyamat négy fő építőelemet tartalmaz:

![Bejelentkezési diagnosztika folyamata](./media/overview-sign-in-diagnostics/process.png)
 
1. **Adja meg** az Ön számára fontos bejelentkezési események hatókörét

2. **Válassza ki** az áttekinteni kívánt eseményt

3. Diagnosztika **áttekintése**

4. Műveletek **elvégzése**

 
### <a name="define-sign-ins"></a>Bejelentkezések definiálása

Ennek a lépésnek a célja, hogy meghatározza a kivizsgálni kívánt bejelentkezési események hatókörét. A hatókör vagy egy felhasználó vagy egy azonosító (correlationId, kérelemazonosító) és egy időtartomány alapján történik. Emellett megadhatja az alkalmazás nevét is. Az Azure AD a hatókör-információkat használja a megfelelő események megkereséséhez.  

### <a name="select-sign-in-event"></a>Bejelentkezési esemény kiválasztása 

A keresési feltételek alapján az Azure AD lekéri az összes egyező bejelentkezési eseményt, és egy hitelesítési összesítő listanézet-nézetben jeleníti meg azokat. 

![Hitelesítés összegzése](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
A nézetben megjelenített oszlopokat testre szabhatja.

### <a name="review-diagnosis"></a>Diagnosztika áttekintése

A kiválasztott bejelentkezési esemény esetén az Azure AD diagnosztikai eredményt biztosít. 

![Diagnosztikai eredmények](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Az eredmény értékeléssel kezdődik. Az értékelés néhány mondatban elmagyarázza, mi történt. A magyarázat segít megérteni a rendszerviselkedést. 

A következő lépés a kiválasztott bejelentkezésre alkalmazott feltételes hozzáférési szabályzatok összefoglalása. A probléma megoldásához ezt a részt a javasolt szervizelési lépések elvégzésével végezheti el. Mivel nem minden esetben lehetséges problémák megoldására további segítség nélkül, egy javasolt lépés lehet egy támogatási jegy megnyitása. 

### <a name="take-action"></a>Művelet elvégzése 
Ezen a ponton meg kell adnia a probléma megoldásához szükséges információkat.


## <a name="scenarios"></a>Forgatókönyvek

Ez a szakasz áttekintést nyújt az érintett diagnosztikai forgatókönyvekről. A következő forgatókönyvek implementálva vannak: 
 
- Feltételes hozzáférés által blokkolva

- Sikertelen feltételes hozzáférés

- MFA a feltételes hozzáférésből

- MFA más követelmények alapján

- MFA-ellenőrzés szükséges

- Az MFA ellenőrzése kötelező, de a felhasználó bejelentkezési kísérlete nem biztonságos helyről

- Sikeres bejelentkezés


### <a name="blocked-by-conditional-access"></a>Feltételes hozzáférés által blokkolva

Ezt a forgatókönyvet egy feltételes hozzáférési szabályzat által blokkolt bejelentkezés okozta.

![Hozzáférés letiltása](./media/overview-sign-in-diagnostics/block-access.png)

A diagnosztikai szakasz a felhasználói bejelentkezés és az alkalmazott szabályzatok részleteit jeleníti meg.


### <a name="failed-conditional-access"></a>Sikertelen feltételes hozzáférés

Ez a forgatókönyv általában egy olyan bejelentkezés eredménye, amely nem sikerült, mert a feltételes hozzáférési szabályzat követelményei nem teljesültek. Néhány gyakori példa:

![Vezérlők megkövetelése](./media/overview-sign-in-diagnostics/require-controls.png)

- Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

- Jóváhagyott ügyfélalkalmazás megkövetelése

- Alkalmazásvédelmi szabályzat megkövetelése   


A diagnosztika részletesen ismerteti a felhasználói bejelentkezést, valamint az alkalmazott házirendet vagy házirendeket.


### <a name="mfa-from-conditional-access"></a>MFA a feltételes hozzáférésből

Ezt a forgatókönyvet egy feltételes hozzáférési szabályzat okozta, amely a többtényezős hitelesítési készlettel való bejelentkezés követelménye.

![Többtényezős hitelesítés megkövetelése](./media/overview-sign-in-diagnostics/require-mfa.png)

A diagnosztikai szakasz a felhasználói bejelentkezés és az alkalmazott szabályzatok részleteit jeleníti meg.



### <a name="mfa-from-other-requirements"></a>MFA más követelmények alapján

Ez a forgatókönyv akkor fordul elő, ha a többtényezős hitelesítést nem kényszeríti ki feltételes hozzáférési szabályzat. Például a többtényezős hitelesítés konfigurálása felhasználónként történik a bérlőben.


![Multi-Factor Authentication felhasználónként való megkövetelése](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Ennek a diagnosztikai forgatókönyvnek a célja, hogy további részletekkel lássa el a következőket:

- A multi-Factor Authentication megszakítás forrása. 
- Az ügyfél interakciójának eredménye.

Emellett ez a szakasz a felhasználó bejelentkezési kísérletével kapcsolatos összes részletet is tartalmazza. 


### <a name="mfa-proof-up-required"></a>MFA-ellenőrzés szükséges

Ez a forgatókönyv a többtényezős hitelesítés beállítására irányuló kérések által megszakított bejelentkezések eredményét eredményezi. Ez a telepítési folyamat más néven "Proof up".

A többtényezős hitelesítés olyankor fordul elő, amikor egy felhasználónak a többtényezős hitelesítést kell használnia, de még soha nem állította be, vagy ha a rendszergazda úgy állította be a felhasználót, hogy beállítsa azt.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a többtényezős hitelesítés megszakítása volt a beállítás, és hogy a felhasználó elvégezte az ellenőrzés elvégzését.

### <a name="mfa-proof-up-required-but-user-sign-in-attempt-is-not-from-secure-location"></a>Az MFA ellenőrzése kötelező, de a felhasználó bejelentkezési kísérlete nem biztonságos helyről

Ez a forgatókönyv a többtényezős hitelesítés beállítására irányuló kérelem által megszakított bejelentkezések eredményét eredményezi, de a bejelentkezés kockázatos volt. 

A többtényezős hitelesítés olyankor fordul elő, amikor egy felhasználónak a többtényezős hitelesítést kell használnia, de még soha nem állította be, vagy ha a rendszergazda úgy állította be a felhasználót, hogy beállítsa azt.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a többtényezős hitelesítés megszakítása volt a beállítás, hogy a felhasználó elvégezze az ellenőrzés elvégzését, azonban olyan hálózati helyről, amely nem kockázatos. Ha például egy vállalati hálózat megnevezett helyként van definiálva, akkor a rendszer megkísérli a vállalati hálózatról érkező ellenőrzés elvégzését.


### <a name="successful-sign-in"></a>Sikeres bejelentkezés

Ez a forgatókönyv az Azure AD-bejelentkezést a feltételes hozzáférés vagy a többtényezős hitelesítés megszakítása nélkül fedi le.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a felhasználó milyen módon adta meg a bejelentkezést, ha feltételes hozzáférési szabályzatot vagy házirendeket alkalmaztak, vagy egy konfigurált többtényezős hitelesítést, amely a felhasználói bejelentkezés megszakadását várta.



## <a name="next-steps"></a>További lépések

* [Mik azok az Azure Active Directory-jelentések?](overview-reports.md)
* [Mi az Azure Active Directory-figyelés?](overview-monitoring.md)
