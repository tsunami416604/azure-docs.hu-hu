---
title: Mi a kockázat? Azure AD Identity Protection
description: A Azure AD Identity Protection kockázatának ismertetése
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253688"
---
# <a name="what-is-risk"></a>Mi a kockázat?

A Azure AD Identity Protection kockázati észlelése magában foglalja a címtárban lévő felhasználói fiókokkal kapcsolatos azonosított gyanús műveleteket.

Az Identity Protection lehetővé teszi a szervezetek számára a hatékony erőforrásokhoz való hozzáférést, így gyorsan megtekintheti és reagálhat ezekre a gyanús műveletekre. 

![A kockázatos felhasználókat és bejelentkezéseket bemutató biztonsági áttekintés](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Kockázati típusok és észlelés

Kétféle kockázati **felhasználó** és **Bejelentkezés** , valamint két típusú észlelési vagy számítási módszer áll rendelkezésre a **valós időben** és a **kapcsolat nélküli üzemmódban**.

### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat azt jelzi, hogy egy adott identitás vagy fiók biztonsága sérül. 

Ezeket a kockázatokat a Microsoft belső és külső veszélyforrások elleni intelligenciával, például a biztonsági kutatókkal, bűnüldözési szakemberekkel, a Microsoft biztonsági csoportjaival és más megbízható forrásokkal együtt offline számítjuk ki.

| Kockázati észlelés | Leírás |
| --- | --- |
| Kiszivárgott hitelesítő adatok | Ez a kockázati észlelési típus azt jelzi, hogy a felhasználó érvényes hitelesítő adatait kiszivárgott. Ha a kiberbűnözők veszélyezteti a legitim felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ezt a megosztást általában nyilvánosan közzétesszük a Dark web, a Sites vagy a Trading szolgáltatásban, és a hitelesítő adatokat a fekete piacon forgalmazzák. Ha a Microsoft által kiszivárgott hitelesítő adatokkal rendelkező szolgáltatás felhasználói hitelesítő adatokat kér a sötét webhelyről, illetve más forrásokból, akkor azokat a rendszer az Azure AD-felhasználók aktuális érvényes hitelesítő adataival ellenőrzi, hogy érvényes egyezéseket keres. |
| Azure AD-fenyegetések felderítése | Ez a kockázati észlelési típus azt jelzi, hogy az adott felhasználó számára szokatlan felhasználói tevékenység van, vagy konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásaira épülő ismert támadási mintákkal. |

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára. 

Ezek a kockázatok valós időben vagy a Microsoft belső és külső veszélyforrások elleni intelligenciával, például biztonsági kutatókkal, bűnüldöző szakemberekkel, a Microsoft biztonsági csoportjaival és más megbízható forrásokkal is kiszámíthatók.

| Kockázati észlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| Névtelen IP-cím | Valós idejű | Ez a kockázati észlelési típus a névtelen IP-címről (például Tor-böngésző vagy névtelen VPN) érkező bejelentkezéseket jelzi. Ezeket az IP-címeket jellemzően olyan szereplők használják, akik el szeretnék rejteni a bejelentkezési telemetria (IP-cím, hely, eszköz stb.) a potenciálisan rosszindulatú szándék érdekében. |
| Atipikus utazás | Offline | Ez a kockázati észlelési típus két, földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a felhasználók legalább egyike atipikus lehet a felhasználó számára, a múltbeli viselkedés miatt. Ez a gépi tanulási algoritmus számos más tényezőt is figyelembe vesz, és azt, hogy a felhasználó hogyan utazott el az első helyről a másodikra, és azt jelzi, hogy egy másik felhasználó ugyanazokat a lépéseket használja hitelesítő adatok. <br><br> Az algoritmus figyelmen kívül hagyja a "hamis pozitív" állapotot, amely hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer kezdeti tanulási időszaka a legkorábbi 14 nap vagy 10 bejelentkezés, amely során egy új felhasználó bejelentkezési viselkedését tanulja meg. |
| Kártevők társított IP-címe | Offline | Ez a kockázat-észlelési típus olyan IP-címekről származó bejelentkezéseket jelez, amelyekről ismert, hogy aktívan kommunikálnak egy robot-kiszolgálóval. Ezt az észlelést a felhasználó eszközének IP-címeinek az IP-címekkel való korrelációja határozza meg, amelyek a bot-kiszolgálóval kapcsolatban voltak aktívak. |
| Ismeretlen bejelentkezési tulajdonságok | Valós idejű | Ez a kockázati észlelési típus a rendellenes bejelentkezések kereséséhez megtekinti a korábbi bejelentkezési előzményeket (IP-cím, szélesség/hosszúság és ASN). A rendszer a felhasználó által használt korábbi helyszínekről tárol adatokat, és ezeket a "ismerős" helyet tekinti át. A kockázat észlelése akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel az ismerős helyek listáján. Az újonnan létrehozott felhasználók "tanulási módban" lesznek egy olyan időszakra, amelyben a nem ismert bejelentkezési tulajdonságok kockázati észlelése ki lesz kapcsolva, miközben az algoritmusok megtudhatják a felhasználó viselkedését. A tanulási mód időtartama dinamikus, és attól függ, hogy mennyi időt vesz igénybe az algoritmus, hogy elegendő információt gyűjtsön a felhasználó bejelentkezési mintáit illetően. A minimális időtartam öt nap. A felhasználók hosszú inaktivitás után visszatérhetnek a tanulási módba. A rendszer emellett figyelmen kívül hagyja a jól ismert eszközökről érkező bejelentkezéseket és a jól ismert helyhez földrajzilag közel lévő helyeket. <br><br> Ezt az észlelést az alapszintű hitelesítéshez (vagy örökölt protokollokhoz) is futtatjuk. Mivel ezek a protokollok nem rendelkeznek modern tulajdonságokkal, például az ügyfél-AZONOSÍTÓval, a hamis pozitív telemetria csökkentése korlátozott. Javasoljuk ügyfeleinknek, hogy a modern hitelesítésre térjenek át. |
| A rendszergazda által megerősített felhasználó sérült | Offline | Ez az észlelés azt jelzi, hogy egy rendszergazda a kockázatos felhasználók felhasználói felületén vagy a riskyUsers API-val jelölte meg a "felhasználói biztonság megerősítése" beállítást. Annak megtekintéséhez, hogy mely rendszergazda erősítette meg ezt a felhasználót, ellenőrizze a felhasználó kockázati előzményeit (UI vagy API használatával). |
| Kártékony IP-cím | Offline | Ez az észlelés azt jelzi, hogy egy rosszindulatú IP-címről jelentkezik be a bejelentkezés. Az IP-címek a nagy meghibásodási arányok alapján kártékonynak minősülnek, mert az IP-címről vagy más IP-címekről származó, érvénytelen hitelesítő adatok érkeznek. |
| Gyanús beérkezett fájlok kezelésére vonatkozó szabályok | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)észlelte. Az észlelés profilokat a környezetben, és értesítést küld, amikor a felhasználó be vannak állítva a gyanús szabályok törlésére vagy áthelyezésére üzenetek vagy mappák. Ez azt jelezheti, hogy a felhasználó fiókja biztonságban van, és az üzenetek szándékosan rejtettek, és a postaláda a levélszemét vagy a kártevők terjesztésére szolgál a szervezetben. |
| Lehetetlen utazás | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel)észlelte. Az észlelés azonosítja a két felhasználói tevékenység (az egy vagy több munkamenet) származó földrajzilag távoli helyekről időnél rövidebb idő alatt, vett volna a felhasználót, hogy az első helyen a másodpercet jelző utazik hogy egy másik felhasználó használja ugyanazokat a hitelesítő adatokat. |

### <a name="other-risk-detections"></a>Egyéb kockázati észlelések

| Kockázati észlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| További kockázat észlelhető | Valós idejű vagy offline | Ez az észlelés azt jelzi, hogy a rendszer a fenti prémium észlelések egyikét észlelte. Mivel a prémium észlelések csak prémium szintű Azure AD P2-ügyfelek számára láthatók, a "további kockázat észlelhető", prémium szintű Azure AD P2 licencek nélküli ügyfelek számára. |

## <a name="next-steps"></a>Következő lépések

- [A kockázatok enyhítésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)

- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)
