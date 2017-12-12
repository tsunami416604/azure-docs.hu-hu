---
title: "Az Azure Active Directory kockázati események |} Microsoft Docs"
description: "Ez a témakör Mik azok a kockázati események részletes áttekintést nyújt."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, biztonság, kockázat, kockázati szint, biztonsági rést, biztonsági házirend"
author: MarkusVi
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 385e2703c5b21fb78d058dc71f66a6c98c1e227f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-risk-events"></a>Az Azure Active Directory kockázati események

A biztonsági problémák többsége kerül sor, amikor a támadók hozzáférést egy olyan környezetben a felhasználók személyazonossága ellopása. Sérült biztonságú identitások felderítésére nem egyszerű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztikus használja, amely kapcsolódik a felhasználói fiókok gyanús tevékenységek észlelése. Minden észlelt gyanús művelet tárolódik nevű rekord *kockázat esemény*.

Jelenleg Azure Active Directory észlelt kockázati események típusai hat:

- [Kiszivárgott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) 
- [Névtelen IP-címekről bejelentkezések](#sign-ins-from-anonymous-ip-addresses) 
- [Lehetetlen odautazás bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) 
- [Fertőzött eszközökről bejelentkezések](#sign-ins-from-infected-devices) 
- [Bejelentkezések gyanús tevékenységeket IP-címekről](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) 


![Kockázati esemény](./media/active-directory-reporting-risk-events/91.png)

A a észlelt kockázati események történő betekintést az Azure AD-előfizetés van kötve. Ha az Azure AD Premium P1 edition (EMS E3 ajánlat szerepel), amelyek nem tartoznak a licenc jelentett **bejelentkezés az észlelt további kockázat**. Ez azt jelenti megjelenik egy bejelentkezési kockázat esemény észlelhető a következőnél: Azure AD Identity Protection előfizetőknek kizárólagos észlelések további kockázattal.


Ez a témakör minden, a rendszer milyen kockázati események részletes áttekintést nyújt, és hogyan használhatja az Azure AD védő.


## <a name="risk-event-types"></a>Kockázati események típusai

A kockázat esemény típusa tulajdonság a gyanús művelet egy kockázat eseményrekord azonosító lett létrehozva.  
A Microsoft folyamatos beruházások azokat az észlelési folyamat során előfordulhat, hogy:

- A meglévő kockázati események észlelésének pontosságának fejlesztései 
- Új kockázat eseménytípusok, a jövőben hozzáadni

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Internetes bűnözők érvényes jelszavak jogos felhasználók hibát okoz, ha a bűnözők gyakran ossza meg ezeket a hitelesítő adatokat. Általában ehhez könyvelés nyilvánosan az sötét web vagy Beillesztés helyeken vagy kereskedelmi vagy a hitelesítő adatok bejelentkezésre kínál. A Microsoft hitelesítő adatok szivárgását szolgáltatás szerez be a felhasználónév / jelszó párokat nyilvános és a sötét webhelyek figyelése és a használata:

- Kutatók
- Értve a rendészeti szerveket
- A Microsoft biztonsági csoportok
- Más megbízható források 

Ha a szolgáltatás szerez be felhasználónév / jelszó párok, amelyek veti össze AAD-felhasználókat aktuális érvényes hitelesítő adatokat. Amikor a program egyezést talál, az azt jelenti, hogy a jelszó feltörték, és egy *elszivárgott a hitelesítő adatok kockázat esemény* jön létre.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címekről bejelentkezések

A kockázat esemény a névtelen proxy IP-címként azonosított IP-cím sikeresen bejelentkezett felhasználók azonosítja. Ezek a proxyk szeretné az eszköz IP-címet, és a rosszindulatú behatolással szemben használható személyek által használt.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

A kockázat esemény két bejelentkezéseket földrajzilag távoli helyről, ahol a helyek közül legalább egy is lehet a felhasználó alapján ezúttal szokatlan, korábbi viselkedés azonosítja. Többek között számos a gépi tanulási algoritmus figyelembe veszi a két bejelentkezést és az első helyen utazik, és a második, amely azt jelzi, hogy egy másik felhasználó azonos használ a felhasználó rendelkezik szükséges idő közötti idő hitelesítő adatok.

Az algoritmus figyelmen kívül hagyja a nyilvánvaló "vakriasztásokat" hozzájárul a lehetetlen odautazás feltételek, például a virtuális magánhálózatok és rendszeresen a szervezeten belüli más felhasználók által használt helyek. A rendszer kezdeti tanulási időszaka 14 napos, amely során egy új felhasználó bejelentkezési viselkedésének Tanulja meg. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

A kockázat eseménytípus úgy ítéli meg, bejelentkezési helyek túli (szélesség, IP / hosszúság és a ASN) új / ismeretlen helyek meghatározásához. A rendszer fent említett helyeken, felhasználó által használt kapcsolatos információkat tárolja, és úgy ítéli meg ezeket a "megszokott" helyeket. A kockázat esemény akkor váltódik ki, amikor a bejelentkezés következik be, amely még nem ismeri helyek listáját egy helyről. A rendszer kezdeti tanulási időszaka a 30 nap során, ami azt nem ez a jelző bármely új helyek ismeretlen helyként. A rendszer is bejelentkezések ismerős eszközöket, és egy ismert helyre földrajzilag megközelítik helyek figyelmen kívül hagyja. 

### <a name="sign-ins-from-infected-devices"></a>Fertőzött eszközökről indított bejelentkezések

A kockázat esemény aktívan kommunikálni egy botnetes kiszolgálóhoz ismert, kártevővel fertőzött eszközökről származó bejelentkezések azonosítja. Ez határozza meg IP-címét a felhasználó-eszköz IP-címek, amelyek szerepelnek a botnetes kiszolgálóhoz volt elleni használatával történik. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
A kockázat eseménytípus azonosítja az IP-címek, ahol nagyszámú sikertelen bejelentkezési kísérlet volt, körében tapasztalható több felhasználói fiókot, egy rövid időtartamra vonatkozóan. Ez megegyezik a támadók által használt IP-címek adatforgalmi mintái jellemzően, és erős jelzi, hogy fiókok már vagy megsértik a művelettel. Ez az a gépi tanulási algoritmus figyelmen kívül hagyja a nyilvánvaló "*hamis-pozitív*", például az IP-címek, amelyek rendszeresen szolgálnak a szervezeten belüli más felhasználók által.  A rendszer időszaka kezdeti tanulási 14 napos ahol Tanulja meg a bejelentkezési viselkedését egy új felhasználó és egy új bérlőt.


## <a name="detection-type"></a>Észlelési típusa

Az észlelési típusú tulajdonság azt jelzi (valós idejű vagy kapcsolat nélküli) az a kockázat esemény észlelése időkeretétől.  
Jelenleg a legtöbb kockázati események észlelésének offline utó-feldolgozási művelet után a kockázati eseményekről.

A következő táblázat sorolja fel, hogy mennyi ideig tart a kapcsolódó jelentés megjelennek észlelési típusok esetében:

| Észlelési típusa | Jelentéskészítési késés |
| --- | --- |
| Valós idejű | 5-10 perc |
| Offline | 2 – 4 óra |


A kockázat esemény esetében Azure Active Directory észlel az észlelési típusok a következők:

| Kockázati esemény típusa | Észlelési típusa |
| :-- | --- | 
| [Kiszivárgott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) | Offline |
| [Névtelen IP-címekről bejelentkezések](#sign-ins-from-anonymous-ip-addresses) | Valós idejű |
| [Lehetetlen odautazás bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) | Offline |
| [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) | Valós idejű |
| [Fertőzött eszközökről bejelentkezések](#sign-ins-from-infected-devices) | Offline |
| [Bejelentkezések gyanús tevékenységeket IP-címekről](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Kockázati szint

A kockázati szint a kockázati események tulajdonsága egy kijelző súlyossága és az abban, hogy a kockázati esemény (magas, közepes vagy alacsony). Ez a tulajdonság segítségével priorizálhatja azokat a műveleteket, végre kell hajtania. 

A kockázat esemény súlyossága a jel erősségével egy előrejelzőjének az identitás illetéktelen behatolásoknak meghatalmazottként képviseli.  
Az vetett bizalmat egy téves lehetőségét. 

Például: 

* **Magas**: magas megbízhatósági és magas súlyosságú kockázat esemény. Ezeket az eseményeket, amelyek a felhasználói identitás feltörték, és azonnal érintett fiókokat kell szervizelni erős mutatók.

* **Közepes**: magas súlyosságú, de alacsonyabb abban, hogy kockázat esemény, vagy fordítva. Ezek az események potenciálisan veszélyes, és egyetlen felhasználói fiókot sem érintett szervizelni kell.

* **Alacsony**: alacsony abban, hogy és alacsony súlyosságú kockázat esemény. Ez az esemény egy azonnali beavatkozásra nincs szükség lehet, de más kockázati események kombinálva előfordulhat jelzi, hogy az identitás sérül.

![Kockázati szint](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Kockázati események besorolt hitelesítő adatok szivárgását egy **magas**, mert ezek biztosítanak egy tiszta arra vonatkozóan, hogy a felhasználónév és jelszó a támadó rendelkezésére.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címekről bejelentkezések

A kockázati szintje a kockázat eseménytípus **Közepes** mert névtelen IP-cím nincs egy fiók biztonsági sérülése a jelzi.  
Azt javasoljuk, hogy azonnal forduljon a felhasználót, hogy ellenőrizze, hogy ha melyikét használta éppen névtelen IP-címeket.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Lehetetlen odautazás az általában jól jelzi, hogy egy támadó képes bejelentkezés sikeres volt. Hamis-pozitív fordulhat azonban amikor egy felhasználó utazik, egy új eszköz vagy a szervezeten belüli más felhasználók által általában nem használ VPN használatával. Hamis – figyelmeztetéséket egy másik forrása alkalmazásokat helytelenül kiszolgáló IP-címek ügyfél IP-címek, amelyek a Megjelenés zajlik az adatközpontból, ahol az alkalmazás csomagazonosítóját háttér-üzemelteti a bejelentkezések (gyakran ezek azok a Microsoft számára fenntartott adatközpontokban, amelyek bejelentkezések véve megjelenésének helyezze el a Microsoft tulajdonában lévő IP-címek). Eredményeképpen ezek hamis figyelmeztetéséket kockázat eseményhez kockázati szintje nem **Közepes**.

> [!TIP]
> A kockázat eseménytípus jelentett hamis positves mennyisége segítségével csökkenthető [helyek nevű](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Ismeretlen helyek biztosíthat egy erős annak jelzése, hogy egy támadó képes ellopott identitás használatára. Hamis – pozitív akkor fordulhat elő, amikor egy felhasználó utazik, próbálja ki az új eszköz vagy egy új VPN-t használja. A vakriasztások miatt a kockázati szintjét esemény van **Közepes**.

### <a name="sign-ins-from-infected-devices"></a>Fertőzött eszközökről indított bejelentkezések

A kockázat esemény IP-címek, nem a felhasználói eszközök azonosítja. Ha több eszköz egyetlen IP-címnek mögött találhatók, és a csak néhány van botot hálózat, a más eszközökről származó bejelentkezések a eseményindító ezt az eseményt feleslegesen, amelynek az oka a kockázat esemény osztályozásához, **alacsony**.  

Azt javasoljuk, hogy lépjen kapcsolatba a felhasználóval, és a felhasználói eszközök. Lehetőség arra is, hogy a felhasználó személyes eszköz fertőzött, vagy a korábban említett, hogy valaki más használta az azonos IP-címről egy fertőzött eszköz felhasználóként. Víruskereső szoftver még nem azonosított, és azt is jelezheti, felhasználói rossz a szokásokat, amelyek oka az eszköz megfertőző kártevők által fertőzött eszközök gyakran fertőzött.

Cím kártevőszoftver-fertőzések kapcsolatos további információkért lásd: a [Kártevőkezelési központ](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel

Azt javasoljuk, hogy lépjen kapcsolatba a felhasználó ellenőrizheti, ha azokat ténylegesen bejelentkezett lett megjelölve, a gyanús IP-címről. Az esemény kockázati szintje "**Közepes**" lehet, hogy több eszköz mögött ugyanazt a címet, mert csak néhány lehet felelős a gyanús tevékenység során. 


 
## <a name="next-steps"></a>Következő lépések

Kockázati események védelméhez az Azure AD-identitások foundation rendszer. Az Azure AD jelenleg képes észlelni hat kockázati események: 


| Kockázati esemény típusa | Kockázati szint | Észlelési típusa |
| :-- | --- | --- |
| [Kiszivárgott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) | Magas | Offline |
| [Névtelen IP-címekről bejelentkezések](#sign-ins-from-anonymous-ip-addresses) | Közepes | Valós idejű |
| [Lehetetlen odautazás bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) | Közepes | Offline |
| [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) | Közepes | Valós idejű |
| [Fertőzött eszközökről bejelentkezések](#sign-ins-from-infected-devices) | Alacsony | Offline |
| [Bejelentkezések gyanús tevékenységeket IP-címekről](#sign-ins-from-ip-addresses-with-suspicious-activity) | Közepes | Offline|

Hol találnak a környezetében észlelt kockázati események?
Nincsenek két helyen, ahol megtekintheti a jelentett kockázat események:

 - **Az Azure AD jelentéskészítési** -kockázati események részei az Azure AD biztonsági jelentések. További részletekért lásd: a [kockázat biztonsági jelentés felhasználók](active-directory-reporting-security-user-at-risk.md) és a [kockázatos bejelentkezések biztonsági jelentés](active-directory-reporting-security-risky-sign-ins.md).

 - **Az Azure AD Identity Protection** -kockázati események olyan is részei [Azure Active Directory Identity Protection által](active-directory-identityprotection.md) jelentéskészítési képességek.
    

A kockázati eseményekről már észlelését jelöli, a identitások védelmének fontos eleme, amíg lehetősége is van a manuálisan kezelje őket, vagy még akkor is megvalósíthatja automatikus válaszok feltételes hozzáférési szabályzatok konfigurálása. További részletekért lásd: a [Azure Active Directory Identity Protection által](active-directory-identityprotection.md).
 
