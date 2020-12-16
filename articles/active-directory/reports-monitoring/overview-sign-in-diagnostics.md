---
title: Mi a bejelentkezési diagnosztika az Azure AD-ben? | Microsoft Docs
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
ms.openlocfilehash: d6aedf41fbf1ed0d70467a2efe97431fdecaa4fa
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585912"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Mi a bejelentkezési diagnosztika az Azure AD-ben?

Az Azure AD rugalmas biztonsági modellt biztosít annak szabályozására, hogy a felhasználók milyen műveleteket végezhetnek a felügyelt erőforrásokkal. Az ehhez az erőforrásokhoz való hozzáférést nem csak **a maga vezérli, hanem azt is** , **hogy hogyan** férhet hozzájuk. A rugalmasság általában egy bizonyos fokú összetettséggel jár, mivel a konfigurációs beállítások száma megtörtént. A bonyolultság a hibák kockázatának növelésére is képes.

Rendszergazdaként olyan megoldásra van szüksége, amely megfelelő szintű betekintést nyújt a rendszer tevékenységeibe, így könnyen diagnosztizálhatja és megoldhatja a problémákat. Az Azure AD-hez készült bejelentkezési diagnosztika példa erre a megoldásra. A diagnosztika segítségével elemezheti, hogy mi történt a bejelentkezés során, és milyen műveleteket hajthat végre a problémák megoldásához anélkül, hogy a Microsoft támogatási szolgálatához kellene tartoznia.

Ez a cikk áttekintést nyújt a megoldás működéséről, valamint arról, hogyan használhatja azt.


## <a name="requirements"></a>Követelmények

A bejelentkezési diagnosztika az Azure AD minden kiadásában elérhető.<br> Az Azure AD-ben globális rendszergazdának kell lennie ahhoz, hogy használhassa.

## <a name="how-it-works"></a>Működés

Az Azure AD-ben a bejelentkezési kísérletre adott válasz ahhoz kötődik, hogy **ki** és **Hogyan** fér hozzá a bérlőhöz. Rendszergazdaként például a bérlő minden aspektusát beállíthatja, amikor bejelentkezik a vállalati hálózatról. Előfordulhat azonban, hogy akkor is le van tiltva, ha nem megbízható hálózatról jelentkezik be ugyanazzal a fiókkal.
 
Mivel a rendszer nagyobb rugalmasságot biztosít a bejelentkezési kísérletek megválaszolására, előfordulhat, hogy az olyan helyzetekben fejeződik be, ahol a bejelentkezések hibakeresése szükséges. A bejelentkezési diagnosztika szolgáltatás a következőket eredményezi:

- A bejelentkezések adatainak elemzése. 

- Megjeleníti, hogy mi történt, és javaslatokat tesz a problémák megoldására. 

Az Azure AD bejelentkezési diagnosztika célja, hogy lehetővé tegye a bejelentkezési hibák öndiagnosztizálását. A diagnosztikai folyamat elvégzéséhez a következőket kell tennie:

![Bejelentkezési diagnosztika folyamata](./media/overview-sign-in-diagnostics/process.png)
 
1. A bejelentkezési események hatókörének **meghatározása**

2. **Válassza ki** az áttekinteni kívánt bejelentkezést

3. A diagnosztikai eredmény **áttekintése**

4. Műveletek **elvégzése**

 
### <a name="define-scope"></a>Hatókör definiálása

Ennek a lépésnek a célja, hogy meghatározza a vizsgálni kívánt bejelentkezések hatókörét. A hatókör vagy egy felhasználó vagy egy azonosító (correlationId, kérelemazonosító) és egy időtartomány alapján történik. A hatókör további csökkentése érdekében megadhatja az alkalmazás nevét is. Az Azure AD a hatókör-információkat használja a megfelelő események megkereséséhez.  

### <a name="select-sign-in"></a>Bejelentkezés kiválasztása  

A keresési feltételek alapján az Azure AD lekéri az összes egyező bejelentkezést, és egy hitelesítési összefoglaló lista nézetben jeleníti meg azokat. 

![Hitelesítés összegzése](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
A nézetben megjelenített oszlopokat testre szabhatja.

### <a name="review-diagnostic"></a>Diagnosztika áttekintése 

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

Ez a forgatókönyv egy feltételes hozzáférési szabályzat által blokkolt bejelentkezésen alapul.

![Hozzáférés letiltása](./media/overview-sign-in-diagnostics/block-access.png)

A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezés és az alkalmazott szabályzatok részleteit jeleníti meg.


### <a name="failed-conditional-access"></a>Sikertelen feltételes hozzáférés

Ez a forgatókönyv általában egy olyan bejelentkezés eredménye, amely nem sikerült, mert a feltételes hozzáférési szabályzat követelményei nem teljesültek. Néhány gyakori példa:

![Vezérlők megkövetelése](./media/overview-sign-in-diagnostics/require-controls.png)

- Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

- Jóváhagyott ügyfélalkalmazás megkövetelése

- Alkalmazásvédelmi szabályzat megkövetelése   


A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezés és az alkalmazott szabályzatok részleteit jeleníti meg.


### <a name="mfa-from-conditional-access"></a>MFA a feltételes hozzáférésből

Ez a forgatókönyv egy feltételes hozzáférési szabályzaton alapul, amely a multi-Factor Authentication-készlettel való bejelentkezés követelménye.

![Többtényezős hitelesítés megkövetelése](./media/overview-sign-in-diagnostics/require-mfa.png)

A forgatókönyv diagnosztikai szakasza a felhasználói bejelentkezés és az alkalmazott szabályzatok részleteit jeleníti meg.



### <a name="mfa-from-other-requirements"></a>MFA más követelmények alapján

Ez a forgatókönyv olyan többtényezős hitelesítési követelményen alapul, amelyet a feltételes hozzáférési szabályzat nem kényszerített ki. Például a többtényezős hitelesítés felhasználónként történik.


![Multi-Factor Authentication felhasználónként való megkövetelése](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Ennek a diagnosztikai forgatókönyvnek a célja, hogy további részletekkel lássa el a következőket:

- A multi-Factor Authentication megszakítás forrása. 
- Az ügyfél interakciójának eredménye.

Emellett ez a szakasz a felhasználó bejelentkezési kísérletével kapcsolatos összes részletet is tartalmazza. 


### <a name="mfa-proof-up-required"></a>MFA-ellenőrzés szükséges

Ez a forgatókönyv a többtényezős hitelesítés beállítására irányuló kérések által megszakított bejelentkezéseken alapul. Ezt a telepítőt "ellenőrzésnek" is nevezzük.

A többtényezős hitelesítés olyankor fordul elő, amikor egy felhasználónak a többtényezős hitelesítést kell használnia, de még nincs konfigurálva, vagy ha a rendszergazda konfigurálta a felhasználót a konfigurálásához.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a többtényezős hitelesítés megszakítása volt a beállítás, és hogy a felhasználó elvégezte az ellenőrzés elvégzését.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>A kockázatos bejelentkezéshez szükséges MFA-ellenőrzés

Ez a forgatókönyv olyan bejelentkezések eredményét eredményezi, amelyeket a többtényezős hitelesítés kockázatos bejelentkezéssel való beállítására vonatkozó kérelem megszakított. 

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a többtényezős hitelesítés megszakítása volt a beállítás, hogy a felhasználó elvégezze az ellenőrzés elvégzését, azonban olyan hálózati helyről, amely nem kockázatos. Ha például egy vállalati hálózat megnevezett helyként van definiálva, akkor a rendszer megkísérli a vállalati hálózatról érkező ellenőrzés elvégzését.


### <a name="successful-sign-in"></a>Sikeres bejelentkezés

Ez a forgatókönyv a feltételes hozzáférés vagy a többtényezős hitelesítés által nem megszakított bejelentkezéseken alapul.

Ennek a diagnosztikai forgatókönyvnek a célja, hogy betekintést nyújtson arról, hogy a felhasználó milyen módon adta meg a bejelentkezést, ha feltételes hozzáférési szabályzatot vagy házirendeket alkalmaztak, vagy egy konfigurált többtényezős hitelesítést, amely a felhasználói bejelentkezés megszakadását várta.



## <a name="next-steps"></a>Következő lépések

* [Mik azok az Azure Active Directory-jelentések?](overview-reports.md)
* [Mi az Azure Active Directory-figyelés?](overview-monitoring.md)
