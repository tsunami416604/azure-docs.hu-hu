---
title: Az Azure Active Directory kockázati eseményeivel |} A Microsoft Docs
description: Ez artice Mik azok a kockázati események részletes áttekintést nyújt.
services: active-directory
keywords: az Azure active directory identity protection, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend
author: priyamohanram
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 4bca7ed2f10ce6b0231a260a9cf5b7468476343f
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652149"
---
# <a name="azure-active-directory-risk-events"></a>Az Azure Active Directory kockázati eseményei

Biztonsági rések túlnyomó többsége kerül sor, amikor a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Feltört identitásokat felderítése nem könnyű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. Minden észlelt gyanús művelet nevű rekordot tárolja egy **kockázati esemény**.

Nincsenek két helyen, ahol áttekintheti a jelentett kockázati események:

 - **Az Azure AD-jelentéskészítés** -kockázati események tartoznak az Azure AD biztonsági jelentések. További információkért lásd: a [felhasználókról szóló biztonsági jelentés](concept-user-at-risk.md) és a [kockázatos bejelentkezés biztonsági jelentés](concept-risky-sign-ins.md).

 - **Az Azure AD Identity Protection** -kockázati események is részei a jelentési képességeivel, [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Emellett használhatja a [Identity Protection kockázati események API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) programozott hozzáférést a Microsoft Graph használatával biztonsági észlelések eléréséhez. További információkért lásd: [Ismerkedés az Azure Active Directory Identity Protection és Microsoft Graph](../identity-protection/graph-get-started.md). 

Jelenleg az Azure Active Directory észleli hat kockázati események típusai:

- [Kiszivárogtatott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) 
- [Névtelen IP-címekről indított bejelentkezések](#sign-ins-from-anonymous-ip-addresses) 
- [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) 
- [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) 
- [Gyanús tevékenységeket mutató IP-címekről indított bejelentkezések](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Ismeretlen helyről történő bejelentkezések](#sign-in-from-unfamiliar-locations) 

![Kockázati esemény](./media/concept-risk-events/91.png)

Az insight észlelt kockázati eseményekhez kap az Azure AD-előfizetéshez vannak kötve. 

* Az a **Azure AD Premium P2 kiadás**, a legrészletesebb információkat minden mögöttes észlelés kap. 
* Az a **Azure AD Premium P1 edition**, a kockázati esemény jelenik meg, amelyek nem tartoznak a licenc **bejelentkezési további észlelt kockázattal rendelkező**.

Kockázati események már észlelését jelöli, fontos szempont a védelmet nyújtanak a, miközben lehetősége is van manuálisan ismertetésére vagy megvalósítása az automatikus reakciók feltételes hozzáférési szabályzatok konfigurálásával. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Kockázati események típusai

A **kockázati esemény típusa** tulajdonság egy kockázati esemény rekordot létre lett hozva a gyanús művelet azonosítója.

A Microsoft folyamatos beruházások kiterjeszthetők az észlelési folyamat vezet:

- A létező kockázati események észlelési pontosságának fejlesztései 
- A jövőben hozzáadni kívánt új kockázati események típusai

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Amikor az internetes bűnözők veszélyeztetheti a jogosult felhasználók érvényes jelszavak, gyakran oszthatnak meg ezeket a hitelesítő adatokat. Ez általában történik közzétételével őket nyilvánosan a sötét webes vagy illessze be a webhelyek vagy kereskedelmi vagy a hitelesítő adatokat, a fekete piaci értékesítési. A Microsoft kiszivárgott hitelesítő adatok szolgáltatás beszerzi a felhasználónév / jelszó párosítja, nyilvános és a sötét webhelyek figyelése és a használata:

- Kutatók
- Rendészeti
- A Microsoft biztonsági csoportokkal
- Más megbízható forrásokból 

Ha a szolgáltatás beszerzi a felhasználónév / jelszó párok, amelyek veti össze AAD-felhasználók aktuális érvényes hitelesítő adatokat. Ha van egyezés, az azt jelenti, hogy egy felhasználó jelszavát biztonsága sérült, és a egy **kiszivárgott hitelesítő adatok kockázati esemény** jön létre.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

A kockázati esemény típusa azonosítja a felhasználókat, akik sikeresen bejelentkezett egy névtelen proxy IP-címként azonosított IP-címről. Ezek proxyk, akik az eszköz IP-címének elrejtésére és rosszindulatú tevékenységek végrehajtására használhatók.

### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

A kockázati esemény típusa két bejelentkezések földrajzilag távoli helyekről, ahol a helyek közül legalább is lehet a felhasználó szokatlan, adott múltbeli viselkedés azonosítja. Számos más tényező befolyásolja, többek között a gépi tanulási algoritmus figyelembe veszi a két bejelentkezést és a vett volna igénybe a felhasználó számára az első helyen a második, amely azt jelzi, hogy egy másik felhasználó azonos használja az utazási idő között eltelt idő hitelesítő adatok.

Az algoritmus figyelmen kívül hagyja a nyilvánvaló "vakriasztásokat" a lehetetlen utazás feltételek, például a VPN-EK és a szervezet más felhasználói által rendszeresen használt helyek hozzájárul. A rendszer egy mely során egy új felhasználói bejelentkezési viselkedés megtanulja 14 napos betanulási időszakra van. 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

A kockázati esemény típusa figyelembe veszi a korábbi bejelentkezési helyek (IP, szélesség / hosszúság és az ASN) új / ismeretlen helyek meghatározásához. A rendszer korábbi, felhasználó által használt helyek adatait tárolja, és figyelembe veszi az "ismerős" helyek. A kockázati esemény akkor aktiválódik, ha a bejelentkezés történik, amely még nem ismert helyeihez helyről. A rendszer egy 30 napos, amely során azt nem jelöli meg rendellenesként ismeretlen helyről, új helyekről betanulási időszakra van. A rendszer is figyelmen kívül hagyja a jól ismert eszközöket és helyeket, amelyek földrajzilag közel egy ismert helyre történő bejelentkezések. 

Identity Protection észleli az egyszerű hitelesítést is ismeretlen helyről történő bejelentkezések / örökölt protokollok. Modern ismerős szolgáltatások, például az ügyfél-azonosító nem rendelkezik ezeket a protokollokat, mert nem szerepel elegendő telemetriai vakriasztások csökkentésének érdekében. Észlelt kockázati események mennyiségének csökkentése érdekében helyezze át a modern hitelesítést.   

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

A kockázati esemény típusa azonosítja az ismert, hogy aktív a robot-kiszolgálóval való kommunikációhoz, kártevő szoftverrel fertőzött eszközökről származó bejelentkezéseket. Ez határozza meg a robot kiszolgáló összevetésén alapulnak, amelyek IP-címekkel a felhasználó-eszköz IP-címek használatával történik. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
A kockázati esemény típusa azonosítja az IP-címet, amelyről nagy számú sikertelen bejelentkezési kísérletek voltak különböző bérlőknél észlelt, több felhasználói fiókot, egy rövid időn át. Ez megegyezik a támadók által használt IP-címek adatforgalmi mintái, és egy erős jelzi, hogy a fiókok már, vagy olyan sérülhet. Ez a machine learning algoritmus, amely figyelmen kívül hagyja a nyilvánvaló vakriasztások, például a szervezet más felhasználói által rendszeresen használt IP-címek.  A rendszer egy 14 napos betanulási időszakra van, ahol, megtanulja a bejelentkezési viselkedését egy új felhasználót és egy új bérlőt.

## <a name="detection-type"></a>Észlelés típusa

Észlelési tulajdonság egy jelző (**valós idejű** vagy **Offline**) számára egy kockázati esemény észlelési időkeretét. Jelenleg a legtöbb kockázati események észlelésének offline utófeldolgozási művelet után a kockázati esemény történt.

A következő táblázat sorolja fel, hogy mennyi ideig tart észlelési írja be a következőt egy kapcsolódó jelentésre megjelennek:

| Észlelés típusa | Jelentéskészítés késése |
| --- | --- |
| Valós idejű | 5 – 10 perc |
| Offline | 2 – 4 óra |


Az Azure Active Directory észleli a kockázati események típusai a fenyegetésészlelés típusai a következők:

| Kockázati esemény típusa | Észlelés típusa |
| :-- | --- | 
| [Kiszivárogtatott hitelesítő adatokkal rendelkező felhasználók](#leaked-credentials) | Offline |
| [Névtelen IP-címekről indított bejelentkezések](#sign-ins-from-anonymous-ip-addresses) | Valós idejű |
| [Bejelentkezés szokatlan helyekről](#impossible-travel-to-atypical-locations) | Offline |
| [Ismeretlen helyről történő bejelentkezések](#sign-in-from-unfamiliar-locations) | Valós idejű |
| [Bejelentkezések fertőzött eszközökről](#sign-ins-from-infected-devices) | Offline |
| [Gyanús tevékenységeket mutató IP-címekről indított bejelentkezések](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Kockázati szint

A kockázati szint tulajdonság egy kockázati esemény egy jelző (**magas**, **Közepes**, vagy **alacsony**) a súlyosság és a egy kockázati esemény bizalmát. Ez a tulajdonság segítségével rangsorolhatja a műveleteket kell végeznie. 

A kockázati esemény súlyosságát az erőssége a jel, egy előjelző az identitás veszélyeztetése jelöli. A megbízhatóság: egy mutató a vakriasztások lehetőségét. 

Például: 

* **Magas**: Magas megbízhatóság és magas súlyossági szintű kockázati esemény. Ezek az események olyan erős mutatók, amelyek a felhasználói identitás biztonsága sérült, és minden érintett felhasználói fiókok azonnal szervizelni kell.

* **Közepes**: Súlyos, de alacsonyabb megbízhatósági kockázati esemény, vagy fordítva. Ezek az események potenciálisan veszélyes, és minden érintett felhasználói fiókok szervizelni kell.

* **Alacsony**: Alacsony megbízhatósági és az alacsony súlyossági szintű kockázati esemény. Ez az esemény azonnali műveletet nem lehet szükség, de más kockázati események kombinálva rendelkezhetnek jelzi, hogy az identitás biztonsága sérült.

![Kockázati szint](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

Kiszivárgott hitelesítő adatok kockázati események besorolt egy **magas**, mert ezek biztosítanak egy egyértelmű jelzés, hogy a felhasználónév és jelszó egy támadó rendelkezésére állnak.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

A kockázati szintje a kockázati esemény típus **Közepes** mert névtelen IP-cím nem egy erős arra utalhat, hogy egy fiók biztonsága sérült. Azt javasoljuk, hogy azonnal forduljon a felhasználó számára, győződjön meg arról, ha azokat használja névtelen IP-címeket.


### <a name="impossible-travel-to-atypical-locations"></a>Bejelentkezés szokatlan helyekről

Lehetetlen utazás az általában jól jelzi, hogy egy támadó sikeresen jelentkezhet be lett. Azonban a vakriasztások fordulhat elő, amikor egy felhasználó utazik, egy új eszköz vagy a szervezet más felhasználói által általában nem használt VPN használatával. Vakriasztások egy másik forrása az alkalmazásokat, amelyek helytelenül adja át a kiszolgáló IP-címeket, ügyfél IP-címek, amelyek megjelenését a bejelentkezések zajlik a az adatközpontban, ahol az alkalmazás a háttér-üzemel (gyakran ezek a Microsoft adatközpontjai, amely a látszatát bejelentkezéseket zajló a Microsoft tulajdonában lévő IP-címek). Ezek vakriasztások eredményeként a kockázati eseményhez tartozó kockázati szintje nem **Közepes**.

> [!TIP]
> A kockázati esemény típusa jelentett vakriasztások mennyisége csökkenthető konfigurálása [nevesített helyek](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Bejelentkezés ismeretlen helyekről

Ismeretlen helyről biztosíthatja, hogy egy támadó az ellopott identitás használatára képes jelzi. Vakriasztások akkor fordulhat elő, ha egy felhasználó utazik, próbálja ki az új eszközt, vagy egy új VPN használ. Ezek vakriasztások eredményeként kockázati esemény típus szintje **Közepes**.

### <a name="sign-ins-from-infected-devices"></a>Bejelentkezések fertőzött eszközökről

A kockázati esemény IP-címek, nem a felhasználói eszközök azonosítja. Ha több eszköz is egyetlen IP-cím mögött, és csak néhány azok által vezérelt bot hálózaton, más eszközökről bejelentkezések saját eseményindító ezt az eseményt feleslegesen, éppen ezért a rendszer kockázati esemény ajánlottként megjelölt frissítést **alacsony**.  

Azt javasoljuk, hogy kapcsolattartás a felhasználókkal, és a felhasználói eszközök vizsgálata. Lehetőség arra is, hogy a felhasználó személyes eszközén fertőzött, illetve, hogy valaki más használta az ugyanazon az IP-címről egy fertőzött eszköz felhasználóként. Fertőzött eszközökről gyakran fertőzöttek kártevőkkel, víruskereső szoftver még nem azonosított, és azt is jelezheti bármely felhasználó rossz szokások, amely az eszköz megfertőző okozott.

Cím kártevőszoftver-fertőzések kapcsolatos további információkért lásd: a [Kártevőkezelési központ](https://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Bejelentkezések gyanús tevékenységeket mutató IP-címekkel

Azt javasoljuk, hogy lépjen kapcsolatba a felhasználó számára, győződjön meg arról, ha azok ténylegesen bejelentkezett IP-címek, amelyek gyanúsként lett megjelölve. Kockázati esemény típus szintje "**Közepes**", mert több eszköz mögött az azonos IP-cím lehet, miközben csak néhány felelős a gyanús tevékenység lehet. 


## <a name="next-steps"></a>További lépések

* [Felhasználókról szóló biztonsági jelentés](concept-user-at-risk.md)
* [Kockázatos bejelentkezések szóló biztonsági jelentés](concept-risky-sign-ins.md)
* [Az Azure AD Identity Protection](../active-directory-identityprotection.md).
