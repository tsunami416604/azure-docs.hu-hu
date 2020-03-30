---
title: Azure Active Directory kockázatészlelések | Microsoft dokumentumok
description: Ez az artice részletes áttekintést nyújt a rról, hogy mik a kockázatészlelések.
services: active-directory
keywords: Azure active directory identitásvédelem, biztonság, kockázat, kockázati szint, sebezhetőség, biztonsági házirend
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383956"
---
# <a name="azure-active-directory-risk-detections"></a>Az Azure Active Directory kockázatészlelései

A biztonsági rések túlnyomó többsége akkor történik, amikor a támadók a felhasználó személyazonosságának ellopásával férnek hozzá egy környezethez. A feltört identitások felfedezése nem könnyű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikát használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet et egy **kockázatészlelésnek**nevezett rekord tárol.

Két helyen tekintheti át a jelentett kockázatészleléseket:

 - **Azure AD-jelentéskészítés** – A kockázatészlelések az Azure AD biztonsági jelentéseinek részét képezik. További információt a [veszélyeztetett felhasználók biztonsági jelentésében](concept-user-at-risk.md) és a [kockázatos bejelentkezések biztonsági jelentésében](concept-risky-sign-ins.md)talál.

 - **Azure AD Identity Protection** – A kockázatészlelések az [Azure Active Directory-identitásvédelem](../active-directory-identityprotection.md)jelentéskészítési képességeinek is részét képezik.

Emellett az [Identity Protection kockázatészlelési API-val](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) programozott hozzáférést kaphat a biztonsági észlelésekhez a Microsoft Graph használatával. További információ: [Az Azure Active Directory identity protection és a Microsoft Graph – első lépések című témakörben talál.](../identity-protection/graph-get-started.md) 

Az Azure Active Directory jelenleg hatféle kockázatészlelést észlel:

- [Felhasználók, akiknek kiszivárogtak a hitelesítő adatai](#leaked-credentials) 
- [Bejelentkezések névtelen IP-címről](#sign-ins-from-anonymous-ip-addresses) 
- [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) 
- [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) 
- [Bejelentkezések gyanús tevékenységeket mutató IP-címekkel](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) 

![Kockázatészlelés](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Előfordulhat, hogy a [bejelentkezési jelentésben](concept-sign-ins.md)megfelelő bejelentkezési bejegyzés nélkül talál kockázatészlelést. Ennek az az oka, hogy az Identity Protection kiértékeli a kockázatot mind **az interaktív,** mind **a nem interaktív** bejelentkezések esetében, míg a bejelentkezések jelentés csak az interaktív bejelentkezéseket jeleníti meg.

Az észlelt kockázatészleléshez betekintés az Azure AD-előfizetéshez van kötve. 

* Az **Azure AD Premium P2 kiadással**a legrészletesebb információkat kaphatja meg az összes alapul szolgáló észlelésről. 
* Az **Azure AD Premium P1 kiadás,** speciális észlelések (például ismeretlen bejelentkezési tulajdonságok) nem vonatkozik a licenc, és megjelenik a neve **Bejelentkezés további kockázatot észlelt.** Ezenkívül a kockázati szint és a kockázatrészletmezők rejtettek.

Míg a kockázatészlelések észlelése már fontos szempont az identitások védelmében, lehetősége van arra is, hogy manuálisan foglalkozzon velük, vagy automatikus válaszokat valósítson meg feltételes hozzáférési házirendek konfigurálásával. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Kockázatészlelési típusok

A **kockázatészlelési típus** tulajdonság a gyanús művelet azonosítója, amelyhez kockázatészlelési rekord jött létre.

A Microsoft folyamatos befektetései az észlelési folyamatba a következőkhöz vezetnek:

- A meglévő kockázatészlelések észlelési pontosságának javítása 
- Új kockázatészlelési típusok, amelyek a jövőben kerülnek hozzáadásra

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Ha a kiberbűnözők veszélyeztetik a jogos felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ez általában úgy történik, kiküldetés őket nyilvánosan a sötét interneten vagy beillesztés oldalak vagy a kereskedelmi vagy eladási hitelesítő adatokat a fekete piacon. A Microsoft kiszivárgott hitelesítő adatok szolgáltatás szerez felhasználónév / jelszó pár figyelésével nyilvános és sötét weboldalak és a munka:

- Kutatók
- Bűnüldöző
- Biztonsági csapatok a Microsoftnál
- Egyéb megbízható források 

Amikor a szolgáltatás felhasználónév/jelszó párokat szerez be, a rendszer összeveti őket az AAD-felhasználók aktuális érvényes hitelesítő adataival. Ha egyezést talál, az azt jelenti, hogy a felhasználó jelszava biztonsága sérül, és létrejön egy **kiszivárgott hitelesítő adatok kockázatészlelése.**

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

Ez a kockázatészlelési típus azonosítja azokat a felhasználókat, akik sikeresen bejelentkeztek egy névtelen proxy IP-címként azonosított IP-címről. Ezeket a proxykat olyan személyek használják, akik el akarják rejteni az eszköz IP-címét, és rosszindulatú szándékra használhatók.

### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Ez a kockázatészlelési típus két földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a korábbi viselkedése miatt legalább az egyik hely atipikus lehet a felhasználó számára. Számos egyéb tényező mellett ez a gépi tanulási algoritmus figyelembe veszi a két bejelentkezés közötti időt és azt az időt, amely alatt a felhasználó az első helyről a másodikhelyre utazott volna, jelezve, hogy egy másik felhasználó ugyanazt a felhasználót használja. Megbízólevél.

Az algoritmus figyelmen kívül hagyja a nyilvánvaló "hamis pozitív" hozzájárul a lehetetlen utazási feltételek, mint például a VPN-ek és a helyek rendszeresen használják a szervezet más felhasználók számára. A rendszer kezdeti tanulási időszaka 14 nap, amely alatt megtanulja egy új felhasználó bejelentkezési viselkedését. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Ez a kockázatészlelési típus figyelembe veszi a korábbi bejelentkezési helyeket (IP, Latitude / Longitude és ASN) az új / ismeretlen helyek meghatározásához. A rendszer a felhasználó által használt korábbi helyekkel kapcsolatos információkat tárolja, és ezeket az "ismerős" helyeket tekinti figyelembe. A kockázatészlelés akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel a megszokott helyek listájában. A rendszer kezdeti tanulási időszaka 30 nap, amely alatt nem jelöl meg ismeretlen helyként új helyeket. A rendszer figyelmen kívül hagyja az ismerős eszközökről és földrajzilag egy ismerős helyhez földrajzilag közel lévő helyekről érkező bejelentkezéseket is. 

Az Identity Protection észleli az ismeretlen helyekről történő bejelentkezéseket az alapszintű hitelesítéshez / örökölt protokollokhoz is. Mivel ezek a protokollok nem rendelkeznek modern ismerős funkciókkal, például ügyfélazonosítóval, nincs elég telemetriai adatok a hamis pozitív értékek csökkentéséhez. Az észlelt kockázatészlelések számának csökkentése érdekében át kell helyeznie a modern hitelesítésre.   

> [!NOTE]
> Ha a bejelentkezési felhasználónév és a jelszó nem egyezik, a bejelentkezés sikertelen lesz, és a kockázatészlelés nem történik meg. Az ismeretlen helyről történő bejelentkezés csak a sikeres bejelentkezések esetén aktiválódik.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázatészlelési típus azonosítja a rosszindulatú programokkal fertőzött eszközök bejelentkezéseit, amelyekről ismert, hogy aktívan kommunikálnak egy robotkiszolgálóval. Ezt a felhasználó eszközének IP-címeinek a robotkiszolgálóval kapcsolatban lévő IP-címekkel való összevetése határozza meg. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
Ez a kockázatészlelési típus azonosítja azokat az IP-címeket, amelyekről rövid idő alatt számos sikertelen bejelentkezési kísérlet történt több felhasználói fiókban. Ez megfelel a támadók által használt IP-címek forgalmi mintáinak, és erősen jelzi, hogy a fiókok már vagy hamarosan veszélybe kerülnek. Ez egy gépi tanulási algoritmus, amely figyelmen kívül hagyja a nyilvánvaló hamis pozitív, például az IP-címeket, amelyeket rendszeresen használnak a szervezet más felhasználói.  A rendszer kezdeti tanulási időszaka 14 nap, ahol megtanulja az új felhasználó és az új bérlő bejelentkezési viselkedését.

## <a name="detection-type"></a>Észlelés típusa

Az észlelési típus tulajdonság a kockázatészlelés észlelési időkeretének jelzője (**valós idejű** vagy **offline).** Jelenleg a legtöbb kockázatészlelések offline észlelése egy utófeldolgozási művelet után a kockázatészlelések történt.

Az alábbi táblázat az észlelési típusok kapcsolódó jelentésekben való megjelenítéséhez szükséges időt sorolja fel:

| Észlelés típusa | Késés jelentése |
| --- | --- |
| Valós idejű | 5-10 perc |
| Offline | 2-4 óra |


Az Azure Active Directory által észlelt kockázatészlelési típusok esetében az észlelési típusok a következők:

| Kockázatészlelés típusa | Észlelés típusa |
| :-- | --- | 
| [Felhasználók, akiknek kiszivárogtak a hitelesítő adatai](#leaked-credentials) | Offline |
| [Bejelentkezések névtelen IP-címről](#sign-ins-from-anonymous-ip-addresses) | Valós idejű |
| [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) | Offline |
| [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) | Valós idejű |
| [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) | Offline |
| [Bejelentkezések gyanús tevékenységeket mutató IP-címekkel](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Kockázati szint

A kockázatészlelés kockázati szintű tulajdonsága a kockázatészlelés súlyosságára és megbízhatóságára vonatkozó mutató (**Magas,** **Közepes**vagy **Alacsony**). Ez a tulajdonság segít a végrehajtandó műveletek fontossági sorrendbe állítása. 

A kockázatészlelés súlyossága a jel erősségét jelenti, mint az identitásbiztonság szempontjából. A bizalom a hamis pozitív umok lehetőségét jelzi. 

Például: 

* **Magas**: Magas megbízhatóság és magas súlyosságú kockázat észlelése. Ezek az események erős jelzik, hogy a felhasználó identitása veszélybe került, és minden érintett felhasználói fiókok azonnal orvosolható.

* **Közepes**: Magas súlyosságú, de alacsonyabb megbízhatósági kockázat észlelése, vagy fordítva. Ezek az események potenciálisan kockázatosak, és minden érintett felhasználói fiókot ki kell újulni.

* **Alacsony**: Alacsony megbízhatóság és alacsony súlyosságú kockázatészlelése. Ez az esemény nem feltétlenül igényel azonnali műveletet, de más kockázatészleléssel kombinálva erős jelzést adhat arról, hogy az identitás sérült.

![Kockázati szint](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

A kiszivárgott hitelesítő adatok kockázatészlelése **magasnak**minősül, mivel egyértelműen jelzik, hogy a felhasználónév és a jelszó elérhető a támadó számára.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

A kockázatészlelési típus kockázati szintje **közepes,** mert a névtelen IP-cím nem jelzi a fiók sérülését. Javasoljuk, hogy azonnal lépjen kapcsolatba a felhasználóval, és ellenőrizze, hogy névtelen IP-címeket használtak-e.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Lehetetlen utazás általában egy jó mutatója, hogy a hacker képes volt sikeresen bejelentkezni. Azonban hamis pozitív előfordulhat, ha a felhasználó utazik egy új eszköz, vagy egy VPN, amely általában nem használják a szervezet más felhasználói. A hamis pozitív alkalmazások egy másik forrása olyan alkalmazások, amelyek helytelenül adják át a kiszolgálóIP-eket ügyfélIP-ként, ami az alkalmazás háttérrendszerének otthont adó adatközpontból érkező bejelentkezések megjelenését eredményezheti (gyakran ezek a Microsoft adatközpontjai, amelyek a Microsoft tulajdonában lévő IP-címekről történő bejelentkezések látszatát keltheti). A hamis pozitív értékek eredményeként a kockázatészlelés kockázati szintje **közepes.**

> [!TIP]
> A kockázatészlelési típushoz jelentett hamis pozitívok mennyiségét [az elnevezett helyek](../active-directory-named-locations.md)konfigurálásával csökkentheti. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Az ismeretlen helyek erősen jelezhetik, hogy a támadó képes lopott személyazonosságot használni. Hamis pozitív lehet, ha a felhasználó utazik, új eszközt próbál ki, vagy új VPN-t használ. A hamis pozitív értékek eredményeképpen az eseménytípus kockázati szintje **Közepes**.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázatészlelés az IP-címeket azonosítja, nem a felhasználói eszközöket. Ha több eszköz mögött egy IP-címet, és csak néhány által ellenőrzött bot hálózat, bejelentkezések más eszközökről én eseményindító ez az esemény feleslegesen, ezért ez a kockázatészlelése minősül **Alacsony**.  

Javasoljuk, hogy lépjen kapcsolatba a felhasználóval, és szkalegezzen meg a felhasználó összes eszközét. Az is lehetséges, hogy a felhasználó személyes eszköze fertőzött, vagy hogy valaki más a felhasználóval azonos IP-címről származó fertőzött eszközt használt. A fertőzött eszközöket gyakran olyan rosszindulatú programokkal fertőzik meg, amelyeket még nem azonosított a víruskereső szoftver, és jelezhetik azokat a rossz felhasználói szokásokat is, amelyek az eszköz megfertőződését okozhatták.

A kártevők elleni fertőzések kezeléséről további információt a [Kártevők elleni védelem központban talál.](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel

Javasoljuk, hogy vegye fel a kapcsolatot a felhasználóval, és ellenőrizze, hogy valóban gyanúsként megjelölt IP-címről jelentkezett-e be. Az eseménytípus kockázati szintje "**Közepes**", mert több eszköz is lehet ugyanazon az IP-cím mögött, míg csak néhány felelős lehet a gyanús tevékenységért. 


## <a name="next-steps"></a>Következő lépések

* [Veszélyeztetett felhasználók biztonsági jelentése](concept-user-at-risk.md)
* [Kockázatos bejelentkezési biztonsági jelentés](concept-risky-sign-ins.md)
* [Az Azure AD identitásvédelem](../active-directory-identityprotection.md).
