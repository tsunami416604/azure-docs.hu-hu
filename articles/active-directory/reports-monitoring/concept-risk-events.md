---
title: Azure Active Directory kockázati észlelések | Microsoft Docs
description: Ez a artice részletes áttekintést nyújt a kockázatok észleléséről.
services: active-directory
keywords: Azure Active Directory Identity Protection, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80383956"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory kockázati észlelések

A biztonsági rések túlnyomó többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. A feltört identitások feltárása nem egyszerű feladat. Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. A rendszer minden észlelt gyanús műveletet egy **kockázati észlelésnek**nevezett rekordban tárol.

A jelentett kockázati észleléseket két helyen tekintheti át:

 - Az **Azure ad Reporting** – a kockázati észlelések az Azure ad biztonsági jelentései részét képezik. További információkért tekintse meg a [veszélyeztetett felhasználók biztonsági jelentését](concept-user-at-risk.md) és a [kockázatos bejelentkezések biztonsági jelentését](concept-risky-sign-ins.md).

 - **Azure ad Identity Protection** – a kockázati észlelések a [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)jelentéskészítési képességeinek is részét képezik.

Emellett az [Identity Protection kockázati észlelések API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) -val programozási hozzáférést nyerhet a biztonsági észlelésekhez Microsoft Graph használatával. További információ: Ismerkedés [a Azure Active Directory Identity Protection és Microsoft Graphával](../identity-protection/graph-get-started.md). 

Jelenleg a Azure Active Directory hat típusú kockázati észlelést észlel:

- [Felhasználók, akiknek kiszivárogtak a hitelesítő adatai](#leaked-credentials) 
- [Bejelentkezések névtelen IP-címről](#sign-ins-from-anonymous-ip-addresses) 
- [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) 
- [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) 
- [Bejelentkezések gyanús tevékenységeket mutató IP-címekkel](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) 

![Kockázati észlelés](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Időnként előfordulhat, hogy a rendszer a [bejelentkezési jelentés](concept-sign-ins.md)megfelelő bejelentkezési bejegyzése nélkül észleli a kockázatfelmérést. Ennek az az oka, hogy az Identity Protection az **interaktív** és a **nem interaktív** bejelentkezések kockázatát is kiértékeli, míg a bejelentkezési jelentés csak az interaktív bejelentkezéseket jeleníti meg.

Az észlelt kockázatok észlelésével kapcsolatos betekintés az Azure AD-előfizetéshez van kötve. 

* A **prémium szintű Azure ad P2 kiadással**a legrészletesebb információkat kapja meg az összes mögöttes észlelésről. 
* A **prémium szintű Azure ad P1 kiadással**a licencek nem fedik le a speciális észleléseket (például az ismeretlen bejelentkezési tulajdonságokat), és a név alatt jelennek meg, ha **további kockázat észlelhető**. Emellett a kockázati szint és a kockázat részleteit tartalmazó mezők rejtve maradnak.

Noha a kockázati észlelések észlelése az identitások védelmének egyik fontos aspektusa, a feltételes hozzáférési szabályzatok konfigurálásával manuálisan is megadhatja őket, vagy az automatizált válaszokat is végrehajthatja. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Kockázati észlelési típusok

A **kockázati észlelés Type** tulajdonsága a gyanús művelet azonosítóját adja meg a következőhöz: kockázati észlelési rekord.

A Microsoft folyamatos beruházásai az észlelési folyamatba a következőket eredményezik:

- A meglévő kockázati észlelések észlelési pontosságának fejlesztése 
- Új kockázati észlelési típusok, melyek a jövőben lesznek hozzáadva

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Ha a kiberbűnözők veszélyezteti a legitim felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ezt általában a sötét web-vagy a webhelyeken, illetve a fekete piacon a hitelesítő adatok kereskedelmi vagy értékesítési útján történő közzétételével hajtják végre. A Microsoft által kiszivárgott hitelesítő adatok a Felhasználónév/jelszó párokat szerzik be a nyilvános és a sötét webhelyek figyelésével, valamint a következővel:

- Kutatók
- Bűnüldözés
- Biztonsági csapatok a Microsoftnál
- Egyéb megbízható források 

Ha a szolgáltatás a Felhasználónév/jelszó párokat szerzi be, a rendszer a HRE-felhasználók aktuális érvényes hitelesítő adataival ellenőrzi azokat. Ha egyezést talál, az azt jelenti, hogy a felhasználó jelszava sérült, és a rendszer **kiszivárgott hitelesítő adatokat** hoz létre.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

Ez a kockázati észlelési típus azonosítja azokat a felhasználókat, akik sikeresen bejelentkezett olyan IP-címről, amelyet névtelen proxy IP-címként azonosítottak. Ezeket a proxykat olyan személyek használják, akik el szeretnék rejteni az eszköz IP-címét, és rosszindulatú szándékkal is használhatók.

### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Ez a kockázati észlelési típus két, földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a felhasználók legalább egyike atipikus lehet a felhasználó számára, a múltbeli viselkedés miatt. Ez a gépi tanulási algoritmus számos más tényezőt is figyelembe vesz, és azt, hogy a felhasználó az első helyről a másodikra utazzon, valamint azt, hogy egy másik felhasználó ugyanazokat a hitelesítő adatokat használja.

Az algoritmus figyelmen kívül hagyja a "hamis pozitív" állapotot, amely hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer 14 napos kezdeti tanulási időszakot tartalmaz, amely során egy új felhasználó bejelentkezési viselkedését tanulja meg. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Ez a kockázati észlelési típus a korábbi bejelentkezési helyszínek (IP-cím, szélesség/hosszúság és ASN) alapján határozza meg az új/ismeretlen helyet. A rendszer a felhasználó által használt korábbi helyszínekről tárol adatokat, és ezeket a "ismerős" helyet tekinti át. A kockázat észlelése akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel az ismerős helyek listáján. A rendszer egy 30 napos kezdeti tanulási időszakot tartalmaz, amely során nem jelöli meg az új helyet ismeretlen tárolóhelyként. A rendszer emellett figyelmen kívül hagyja a jól ismert eszközökről érkező bejelentkezéseket és a jól ismert helyhez földrajzilag közel lévő helyeket. 

Az Identity Protection az alapszintű hitelesítéshez/örökölt protokollokhoz is észleli az ismeretlen helyekről érkező bejelentkezéseket. Mivel ezek a protokollok nem rendelkeznek olyan modern, ismerős funkciókkal, mint az ügyfél-azonosító, nincs elég telemetria a téves pozitív érték csökkentése érdekében. Az észlelt kockázati észlelések számának csökkentése érdekében váltson át a modern hitelesítésre.   

> [!NOTE]
> Ha a bejelentkezési Felhasználónév és a jelszó nem egyezik, a bejelentkezés sikertelen lesz, és a kockázat észlelése nem történik meg. A nem ismerős helyről való bejelentkezés csak a sikeres bejelentkezések esetén aktiválódik.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázati észlelési típus a kártevő szoftverrel fertőzött eszközökről azonosítja a bejelentkezéseket, amelyekről ismert, hogy aktívan kommunikálnak a robot-kiszolgálóval. Ezt a felhasználó eszközének IP-címeinek a bot-kiszolgálóval való érintkezésben lévő IP-címekkel való korrelációja határozza meg. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
Ez a kockázati észlelési típus azonosítja azokat az IP-címeket, amelyekről rövid idő alatt több felhasználói fiók között több sikertelen bejelentkezési kísérletet észlelt a rendszer. Ez megfelel a támadók által használt IP-címek forgalmi mintáinak, és egy erős mutató, amely a fiókok vagy a biztonsági rések sérülésének veszélye. Ez egy gépi tanulási algoritmus, amely figyelmen kívül hagyja a nyilvánvaló hamis pozitív értéket, például a szervezet más felhasználói által rendszeresen használt IP-címeket.  A rendszer 14 napos kezdeti tanulási időszakot tartalmaz, ahol megtanulja egy új felhasználó és új bérlő bejelentkezési viselkedését.

## <a name="detection-type"></a>Észlelés típusa

Az észlelési típus tulajdonság a kockázatkezelés észlelési**időpontjára mutató (valós idejű** vagy **Offline**) jelzés. Jelenleg a kockázatok észlelése után a rendszer offline állapotba helyezi a legtöbb kockázati észlelést egy feldolgozás utáni művelet közben.

A következő táblázat felsorolja, hogy mennyi ideig tart az észlelési típus egy kapcsolódó jelentésben való megjelenítéshez:

| Észlelés típusa | Jelentési késés |
| --- | --- |
| Valós idejű | 5 – 10 perc |
| Offline | 2 – 4 óra |


Az észlelt kockázati észlelési típusok Azure Active Directory észleli a következő észlelési típusokat:

| Kockázat észlelésének típusa | Észlelés típusa |
| :-- | --- | 
| [Felhasználók, akiknek kiszivárogtak a hitelesítő adatai](#leaked-credentials) | Offline |
| [Bejelentkezések névtelen IP-címről](#sign-ins-from-anonymous-ip-addresses) | Valós idejű |
| [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) | Offline |
| [Bejelentkezések ismeretlen helyekről](#sign-in-from-unfamiliar-locations) | Valós idejű |
| [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) | Offline |
| [Bejelentkezések gyanús tevékenységeket mutató IP-címekkel](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Kockázati szint

A kockázatkezelés kockázati szintje tulajdonsága egy mutató (**magas**, **közepes**vagy **alacsony**), amely a kockázatok észlelésének súlyosságára és megbízhatóságára mutat. Ez a tulajdonság segít rangsorolni a végrehajtandó műveleteket. 

A kockázat észlelésének súlyossága a jel erősségét jelképezi a személyazonossági kompromisszum megjósolása alapján. A megbízhatóság a hamis pozitív állapotot jelző mutató. 

Például: 

* **Magas**: magas megbízhatóság és magas súlyosságú kockázatkezelés. Ezek az események olyan erős mutatók, amelyekkel a felhasználó identitása sérült, és az érintett felhasználói fiókokat azonnal szervizelni kell.

* **Közepes**: nagy súlyosságú, de alacsonyabb megbízhatósági kockázat észlelése, vagy fordítva. Ezek az események valószínűleg kockázatos jellegűek, és az érintett felhasználói fiókokat is javítani kell.

* **Alacsony**: alacsony megbízhatóság és alacsony súlyosságú kockázati észlelés. Előfordulhat, hogy ez az esemény nem igényel azonnali műveletet, de más kockázati észlelésekkel kombinálva erős jelzést ad arra, hogy az identitás biztonsága sérül.

![Kockázati szint](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

A kiszivárgott hitelesítő adatok kockázati észlelése **magasnak**minősül, mivel egyértelmű jelzést adnak arról, hogy a Felhasználónév és a jelszó elérhető egy támadó számára.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

A kockázat észlelési típusának kockázati szintje **közepes** , mert a névtelen IP-címek nem jelentenek veszélyt a fiókok biztonsága szempontjából. Javasoljuk, hogy azonnal forduljon a felhasználóhoz annak ellenőrzéséhez, hogy névtelen IP-címeket használnak-e.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

A lehetetlen utazás általában jól jelzi, hogy a hacker sikeresen bejelentkezett. A hamis pozitív érték azonban akkor fordulhat elő, ha egy felhasználó új eszköz használatával utazik, vagy olyan VPN-t használ, amelyet a szervezet más felhasználói általában nem használnak. A hamis pozitívok egy másik forrása olyan alkalmazások, amelyek helytelenül adják át a kiszolgálói IP-címeket az ügyfél IP-címeiként, ami lehetővé teszi a bejelentkezések megjelenését azon adatközpontból, ahol az alkalmazás háttér-végpontja üzemel (ez gyakran a Microsoft-adatközpontok, amelyek a Microsoft tulajdonában lévő IP-címekről érkező bejelentkezések megjelenését is lehetővé tehetik). A téves riasztások eredményeképpen a kockázatkezelés kockázati szintje **közepes**.

> [!TIP]
> Az [elnevezett helyszínek](../active-directory-named-locations.md)konfigurálásával csökkentheti a kockázat-észlelési típus jelentett hamis pozitív értékeit. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Az ismeretlen helyszínek erős jelzést adnak arról, hogy egy támadó ellopott identitást tud használni. Hamis – pozitív érték jelenhet meg, ha egy felhasználó utazik, egy új eszközt próbál meg vagy új VPN-t használ. A téves riasztások eredményeképpen az adott eseménytípus kockázati szintje **közepes**.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

Ez a kockázati észlelés azonosítja az IP-címeket, nem pedig a felhasználói eszközöket. Ha több eszköz egyetlen IP-cím mögött van, és csak néhányat egy bot-hálózat vezérel, a más eszközökről indított bejelentkezések nem feltétlenül okozták ezt az eseményt, ezért a kockázat észlelése **alacsonynak**minősül.  

Javasoljuk, hogy vegye fel a kapcsolatot a felhasználóval, és vizsgálja meg az összes felhasználó eszközét. Az is lehetséges, hogy a felhasználó személyes eszköze fertőzött, vagy ha valaki más egy fertőzött eszközt használ a felhasználótól megegyező IP-címről. A fertőzött eszközöket gyakran olyan kártevők fertőzik meg, amelyeket a víruskereső szoftver még nem azonosított, és olyan rossz felhasználói szokásokat is jelezhet, amelyek okozták az eszköz vírusfertőzését.

A kártevők elleni fertőzések kezelésével kapcsolatos további információkért tekintse meg a [kártevők elleni védelem központját](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel

Javasoljuk, hogy vegye fel a kapcsolatot a felhasználóval annak ellenőrzéséhez, hogy ténylegesen bejelentkezett-e egy gyanúsként megjelölt IP-címről. Az Eseménytípus kockázati szintje "**közepes**", mert több eszköz is lehet ugyanazon az IP-címen, míg a gyanús tevékenységnek csak néhány feladata lehet. 


## <a name="next-steps"></a>Következő lépések

* [Veszélyeztetett felhasználók biztonsági jelentés](concept-user-at-risk.md)
* [Kockázatos bejelentkezések biztonsági jelentése](concept-risky-sign-ins.md)
* [Azure ad Identity Protection](../active-directory-identityprotection.md).
