---
title: Kockázati események Azure Active Directory | Microsoft Docs
description: Ez a artice részletes áttekintést nyújt a kockázati eseményekről.
services: active-directory
keywords: Azure Active Directory Identity Protection, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend
author: cawrites
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5711d900653ae7786899ce1c53f22cf181f5b8bf
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988274"
---
# <a name="azure-active-directory-risk-events"></a>Kockázati események Azure Active Directory

A biztonsági rések túlnyomó többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. A feltört identitások feltárása nem egyszerű feladat. Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet egy **kockázati eseménynek**nevezett rekordban van tárolva.

Két helyen tekintheti át a jelentett kockázati eseményeket:

 - Az **Azure ad jelentéskészítési** kockázati eseményei az Azure ad biztonsági jelentéseinek részét képezik. További információkért tekintse meg a [veszélyeztetett felhasználók biztonsági jelentését](concept-user-at-risk.md) és a [kockázatos bejelentkezések biztonsági jelentését](concept-risky-sign-ins.md).

 - **Azure ad Identity Protection** -kockázati események a [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)jelentéskészítési képességeinek is részét képezik.

Emellett az [Identity Protection kockázati eseményei API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) -val programozási hozzáférést nyerhet a biztonsági észlelésekhez Microsoft Graph használatával. További információ: Ismerkedés [a Azure Active Directory Identity Protection és Microsoft Graphával](../identity-protection/graph-get-started.md). 

A Azure Active Directory jelenleg hat típusú kockázati eseményt észlel:

- [Kiszivárgott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) 
- [Névtelen IP-címekről érkező bejelentkezések](#sign-ins-from-anonymous-ip-addresses) 
- [Nem lehet utazni atipikus helyszínekre](#impossible-travel-to-atypical-locations) 
- [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) 
- [Gyanús tevékenységet folytató IP-címekről érkező bejelentkezések](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Ismeretlen helyekről érkező bejelentkezések](#sign-in-from-unfamiliar-locations) 

![Kockázati esemény](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Időnként előfordulhat, hogy a [bejelentkezési jelentés](concept-sign-ins.md)megfelelő bejelentkezési bejegyzése nélkül is talál kockázati eseményt. Ennek az az oka, hogy az Identity Protection az **interaktív** és a **nem interaktív** bejelentkezések kockázatát is kiértékeli, míg a bejelentkezési jelentés csak az interaktív bejelentkezéseket jeleníti meg.

Az insight észlelt kockázati eseményekhez kap az Azure AD-előfizetéshez vannak kötve. 

* A **prémium szintű Azure ad P2 kiadással**a legrészletesebb információkat kapja meg az összes mögöttes észlelésről. 
* A **prémium szintű Azure ad P1 kiadással**a licencek nem fedik le a speciális észleléseket (például az ismeretlen bejelentkezési tulajdonságokat), és a név alatt jelennek meg, ha **további kockázat észlelhető**. Emellett a kockázati szint és a kockázat részleteit tartalmazó mezők rejtve maradnak.

Noha a kockázati események észlelése az identitások védelmének fontos aspektusa, a feltételes hozzáférési szabályzatok konfigurálásával manuálisan is megadhatja őket, vagy automatikusan is végrehajthatja a válaszokat. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Kockázati események típusai

A **kockázati esemény típusának** tulajdonsága a gyanús művelethez tartozó, kockázati esemény rekordjának azonosítója.

A Microsoft folyamatos beruházásai az észlelési folyamatba a következőket eredményezik:

- A meglévő kockázati események észlelési pontosságának fejlesztése 
- Új kockázati események típusai, amelyek a jövőben lesznek hozzáadva

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Ha a kiberbűnözők veszélyezteti a legitim felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ezt általában a sötét web-vagy a webhelyeken, illetve a fekete piacon a hitelesítő adatok kereskedelmi vagy értékesítési útján történő közzétételével hajtják végre. A Microsoft által kiszivárgott hitelesítő adatok a Felhasználónév/jelszó párokat szerzik be a nyilvános és a sötét webhelyek figyelésével, valamint a következővel:

- Kutatók
- Bűnüldözés
- Biztonsági csapatok a Microsoftnál
- Egyéb megbízható források 

Ha a szolgáltatás a Felhasználónév/jelszó párokat szerzi be, a rendszer a HRE-felhasználók aktuális érvényes hitelesítő adataival ellenőrzi azokat. Ha egyezést talál, az azt jelenti, hogy a felhasználó jelszava sérült, és a rendszer kiszivárgott **hitelesítő adatokat érintő kockázati eseményt** hoz létre.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címről történő bejelentkezések

Ez a kockázati eseménytípus azonosítja azokat a felhasználókat, akik sikeresen bejelentkezett egy névtelen proxy IP-címként azonosított IP-címről. Ezek proxyk, akik az eszköz IP-címének elrejtésére és rosszindulatú tevékenységek végrehajtására használhatók.

### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Ez a kockázati esemény típusa két, földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a felhasználók legalább egyike atipikus lehet a felhasználó számára, a múltbeli viselkedés miatt. Ez a gépi tanulási algoritmus számos más tényezőt is figyelembe vesz, és azt, hogy a felhasználó hogyan utazott el az első helyről a másodikra, és azt jelzi, hogy egy másik felhasználó ugyanazokat a lépéseket használja hitelesítő adatok.

Az algoritmus figyelmen kívül hagyja a "hamis pozitív" állapotot, amely hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer 14 napos kezdeti tanulási időszakot tartalmaz, amely során egy új felhasználó bejelentkezési viselkedését tanulja meg. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Ez a kockázati esemény típusa megtekinti a korábbi bejelentkezési helyeiket (IP-cím, szélesség/hosszúság és ASN) az új/ismeretlen helyszínek meghatározásához. A rendszer a felhasználó által használt korábbi helyszínekről tárol adatokat, és ezeket a "ismerős" helyet tekinti át. A kockázati esemény akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel az ismerős helyek listáján. A rendszer egy 30 napos kezdeti tanulási időszakot tartalmaz, amely során nem jelöli meg az új helyet ismeretlen tárolóhelyként. A rendszer emellett figyelmen kívül hagyja a jól ismert eszközökről érkező bejelentkezéseket és a jól ismert helyhez földrajzilag közel lévő helyeket. 

Az Identity Protection az alapszintű hitelesítéshez/örökölt protokollokhoz is észleli az ismeretlen helyekről érkező bejelentkezéseket. Mivel ezek a protokollok nem rendelkeznek olyan modern, ismerős funkciókkal, mint az ügyfél-azonosító, nincs elég telemetria a téves pozitív érték csökkentése érdekében. Az észlelt kockázati események számának csökkentése érdekében váltson át a modern hitelesítésre.   

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázati eseménytípus a kártevő szoftverrel fertőzött eszközökről azonosítja a bejelentkezéseket, amelyekről ismert, hogy aktívan kommunikálnak a robot-kiszolgálóval. Ezt a felhasználó eszközének IP-címeinek a bot-kiszolgálóval való érintkezésben lévő IP-címekkel való korrelációja határozza meg. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Gyanús tevékenységeket mutató IP-címekről indított bejelentkezések
Ez a kockázati eseménytípus azonosítja azokat az IP-címeket, amelyekben a sikertelen bejelentkezési kísérletek száma több felhasználói fiókon, rövid idő alatt. Ez megfelel a támadók által használt IP-címek forgalmi mintáinak, és egy erős mutató, amely a fiókok vagy a biztonsági rések sérülésének veszélye. Ez egy gépi tanulási algoritmus, amely figyelmen kívül hagyja a nyilvánvaló hamis pozitív értéket, például a szervezet más felhasználói által rendszeresen használt IP-címeket.  A rendszer 14 napos kezdeti tanulási időszakot tartalmaz, ahol megtanulja egy új felhasználó és új bérlő bejelentkezési viselkedését.

## <a name="detection-type"></a>Észlelés típusa

Az észlelés Type tulajdonsága egy kockázati esemény észlelési időkerete (**valós idejű** vagy **Offline**) jelzése. Jelenleg a legtöbb kockázati eseményt a rendszer offline állapotba helyezi a kockázati esemény után, a feldolgozás utáni művelet során.

A következő táblázat felsorolja, hogy mennyi ideig tart az észlelési típus egy kapcsolódó jelentésben való megjelenítéshez:

| Észlelés típusa | Jelentési késés |
| --- | --- |
| Valós idejű | 5 – 10 perc |
| Offline | 2 – 4 óra |


Az észlelt kockázati események Azure Active Directory típusainál az észlelési típusok a következők:

| Kockázati esemény típusa | Észlelés típusa |
| :-- | --- | 
| [Kiszivárgott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) | Offline |
| [Névtelen IP-címekről érkező bejelentkezések](#sign-ins-from-anonymous-ip-addresses) | Valós idejű |
| [Nem lehet utazni atipikus helyszínekre](#impossible-travel-to-atypical-locations) | Offline |
| [Ismeretlen helyekről érkező bejelentkezések](#sign-in-from-unfamiliar-locations) | Valós idejű |
| [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) | Offline |
| [Gyanús tevékenységet folytató IP-címekről érkező bejelentkezések](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Kockázati szint

Egy kockázati esemény kockázati szintje tulajdonsága egy adott kockázati esemény súlyosságát és megbízhatóságát jelző mutató (**magas**, **közepes**vagy **alacsony**). Ez a tulajdonság segít rangsorolni a végrehajtandó műveleteket. 

A kockázati esemény súlyossága a jel erősségét jelképezi, mint az identitások megtámadásának előrejelzője. A megbízhatóság a hamis pozitív állapotot jelző mutató. 

Például: 

* **Magas**: Nagy megbízhatóság és magas súlyosságú kockázati esemény. Ezek az események olyan erős mutatók, amelyekkel a felhasználó identitása sérült, és az érintett felhasználói fiókokat azonnal szervizelni kell.

* **Közepes**: Magas súlyosságú, de alacsonyabb megbízhatósági kockázati esemény, vagy fordítva. Ezek az események valószínűleg kockázatos jellegűek, és az érintett felhasználói fiókokat is javítani kell.

* **Alacsony**: Alacsony megbízhatóság és alacsony súlyosságú kockázati esemény. Előfordulhat, hogy ez az esemény nem igényel azonnali műveletet, de más kockázati eseményekkel kombinálva nagyban jelzi, hogy az identitás biztonsága sérült.

![Kockázati szint](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

A kiszivárgott hitelesítő adatok kockázati eseményei magasnakminősülnek, mivel egyértelmű jelzést adnak arról, hogy a Felhasználónév és a jelszó elérhető egy támadó számára.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címről történő bejelentkezések

A kockázati esemény típusának kockázati szintje **közepes** , mert a névtelen IP-címek nem jelentenek veszélyt a fiókok biztonsága szempontjából. Javasoljuk, hogy azonnal forduljon a felhasználóhoz annak ellenőrzéséhez, hogy névtelen IP-címeket használnak-e.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

A lehetetlen utazás általában jól jelzi, hogy a hacker sikeresen bejelentkezett. A hamis pozitív érték azonban akkor fordulhat elő, ha egy felhasználó új eszköz használatával utazik, vagy olyan VPN-t használ, amelyet a szervezet más felhasználói általában nem használnak. A hamis pozitívok egy másik forrása olyan alkalmazások, amelyek helytelenül adják át a kiszolgáló IP-címeit az ügyfél IP-címeiként, ami lehetővé teszi a bejelentkezések megjelenését azon adatközpontból, ahol az alkalmazás háttér-végpontja üzemel (ez gyakran a Microsoft-adatközpontok, amelyek a Microsoft tulajdonában lévő IP-címekről érkező bejelentkezések megjelenését is lehetővé teheti. A téves riasztások eredményeképpen a kockázati esemény **közepes méretű**.

> [!TIP]
> A kockázati esemény típusának jelentett hamis pozitív értékeit az [elnevezett helyszínek](../active-directory-named-locations.md)konfigurálásával csökkentheti. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Az ismeretlen helyszínek erős jelzést adnak arról, hogy egy támadó ellopott identitást tud használni. Hamis – pozitív érték jelenhet meg, ha egy felhasználó utazik, egy új eszközt próbál meg vagy új VPN-t használ. A téves riasztások eredményeképpen az adott eseménytípus kockázati szintje **közepes**.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázati esemény azonosítja az IP-címeket, nem pedig a felhasználói eszközöket. Ha több eszköz egyetlen IP-cím mögött van, és csak néhányat egy bot-hálózat vezérel, a más eszközökről indított bejelentkezések nem szükségszerűen jelennek meg, ezért ez a kockázati esemény alacsonynak minősül.  

Javasoljuk, hogy vegye fel a kapcsolatot a felhasználóval, és vizsgálja meg az összes felhasználó eszközét. Az is lehetséges, hogy a felhasználó személyes eszköze fertőzött, vagy ha valaki más egy fertőzött eszközt használ a felhasználótól megegyező IP-címről. A fertőzött eszközöket gyakran olyan kártevők fertőzik meg, amelyeket a víruskereső szoftver még nem azonosított, és olyan rossz felhasználói szokásokat is jelezhet, amelyek okozták az eszköz vírusfertőzését.

A kártevők elleni fertőzések kezelésével kapcsolatos további információkért tekintse meg a [kártevők elleni védelem](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)központját.

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Gyanús tevékenységeket mutató IP-címekről indított bejelentkezések

Javasoljuk, hogy vegye fel a kapcsolatot a felhasználóval annak ellenőrzéséhez, hogy ténylegesen bejelentkezett-e egy gyanúsként megjelölt IP-címről. Az Eseménytípus kockázati szintje "**közepes**", mert több eszköz is lehet ugyanazon az IP-címen, míg a gyanús tevékenységnek csak néhány feladata lehet. 


## <a name="next-steps"></a>További lépések

* [Veszélyeztetett felhasználók biztonsági jelentés](concept-user-at-risk.md)
* [Kockázatos bejelentkezések biztonsági jelentése](concept-risky-sign-ins.md)
* [Azure ad Identity Protection](../active-directory-identityprotection.md).
