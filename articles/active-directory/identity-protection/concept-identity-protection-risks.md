---
title: Mi a kockázat? Azure AD Identity Protection
description: A kockázat magyarázata az Azure AD identity Protection-ben
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253688"
---
# <a name="what-is-risk"></a>Mi a kockázat?

Az Azure AD Identity Protection kockázatészlelései közé tartozik a címtárban lévő felhasználói fiókokhoz kapcsolódó azonosított gyanús műveletek.

Az Identity Protection hatékony erőforrásokhoz biztosít hozzáférést a szervezetek számára, hogy gyorsan láthassák ezeket a gyanús műveleteket, és gyorsan reagáljanak ezekre a gyanús műveletekre. 

![A biztonság áttekintése a kockázatos felhasználók és bejelentkezések bemutatásával](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Kockázati típusok és észlelés

Kétféle kockázat **Felhasználó** és **bejelentkezés,** és kétféle észlelési vagy számítási **valós idejű** és **offline**.

### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat azt a valószínűséget jelenti, hogy egy adott identitás vagy fiók biztonsága sérül. 

Ezeket a kockázatokat a Microsoft belső és külső fenyegetésfelderítési forrásai, köztük a biztonsági kutatók, a bűnüldöző szakemberek, a Microsoft biztonsági csapatai és más megbízható források segítségével számítják ki.

| Kockázatészlelés | Leírás |
| --- | --- |
| Kiszivárgott hitelesítő adatok | Ez a kockázatészlelési típus azt jelzi, hogy a felhasználó érvényes hitelesítő adatai kiszivárogtak. Ha a kiberbűnözők veszélyeztetik a jogos felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ez a megosztás általában történik kiküldetés nyilvánosan a sötét interneten, illessze oldalak, vagy a kereskedelmi és eladási hitelesítő adatok a fekete piacon. Amikor a Microsoft kiszivárgott hitelesítő adatok szolgáltatás beszerzi a felhasználói hitelesítő adatokat a sötét webről, beillesztési helyekről vagy más forrásokból, a rendszer ellenőrzi őket az Azure AD-felhasználók aktuális érvényes hitelesítő adataival, hogy érvényes egyezéseket találjanak. |
| Azure AD fenyegetésfelderítés | Ez a kockázatészlelési típus olyan felhasználói tevékenységet jelez, amely szokatlan az adott felhasználó számára, vagy konzisztens a Microsoft belső és külső fenyegetésfelderítési forrásain alapuló ismert támadási mintákkal. |

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt a valószínűséget jelenti, hogy egy adott hitelesítési kérelem nem rendelkezik az identitás tulajdonosa által. 

Ezek a kockázatok valós időben vagy offline módban is kiszámíthatók a Microsoft belső és külső fenyegetésfelderítési forrásaival, beleértve a biztonsági kutatókat, a bűnüldöző szakembereket, a Microsoft biztonsági csapatait és más megbízható forrásokat.

| Kockázatészlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| Névtelen IP-cím | Valós idejű | Ez a kockázatészlelési típus névtelen IP-címről (például Tor böngészőből vagy névtelen VPN-ből) történő bejelentkezéseket jelez. Ezeket az IP-címeket általában olyan szereplők használják, akik potenciálisan rosszindulatú szándék esetén el szeretnék rejteni a bejelentkezési telemetriai adataikat (IP-cím, hely, eszköz stb.). |
| Atipikus utazás | Offline | Ez a kockázatészlelési típus két földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a korábbi viselkedése miatt legalább az egyik hely atipikus lehet a felhasználó számára. Számos egyéb tényező mellett ez a gépi tanulási algoritmus figyelembe veszi a két bejelentkezés közötti időt és azt az időt, amely alatt a felhasználó az első helyről a másodikhelyre utazott volna, jelezve, hogy egy másik felhasználó ugyanazt a felhasználót használja. Megbízólevél. <br><br> Az algoritmus figyelmen kívül hagyja a nyilvánvaló "hamis pozitív" hozzájárul a lehetetlen utazási feltételek, mint például a VPN-ek és a helyek rendszeresen használják a szervezet más felhasználók számára. A rendszer kezdeti tanulási időszaka legkorábban 14 nap vagy 10 bejelentkezés, amely nek során megtanulja az új felhasználó bejelentkezési viselkedését. |
| Kártevőhez csatolt IP-cím | Offline | Ez a kockázatészlelési típus azt jelzi, hogy a robotkiszolgálóval aktívan kommunikáló rosszindulatú programokkal fertőzött IP-címekről érkező bejelentkezések. Ezt az észlelést a felhasználó eszközének IP-címeinek a robotkiszolgálóval a robotkiszolgáló aktív állapotban lévő IP-címekkel való összevetése határozza meg. |
| Ismeretlen bejelentkezési tulajdonságok | Valós idejű | Ez a kockázatészlelési típus a korábbi bejelentkezési előzményeket (IP, Latitude / Hosszúság és ASN) veszi figyelembe a rendellenes bejelentkezések megkereséséhez. A rendszer a felhasználó által használt korábbi helyekkel kapcsolatos információkat tárolja, és ezeket az "ismerős" helyeket tekinti figyelembe. A kockázatészlelés akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel a megszokott helyek listájában. Az újonnan létrehozott felhasználók "tanulási módban" lesznek egy ideig, amikor az ismeretlen bejelentkezési tulajdonságok kockázatészlelése ki lesz kapcsolva, miközben algoritmusaink megtanulják a felhasználó viselkedését. A tanulási mód időtartama dinamikus, és attól függ, hogy mennyi időt vesz igénybe az algoritmus elegendő információt gyűjteni a felhasználó bejelentkezési minták. A minimális időtartam öt nap. A felhasználó hosszú inaktivitás után visszatérhet tanulási módba. A rendszer figyelmen kívül hagyja az ismerős eszközökről és földrajzilag egy ismerős helyhez földrajzilag közel lévő helyekről érkező bejelentkezéseket is. <br><br> Ezt az észlelést alapfokú hitelesítéshez (vagy örökölt protokollokhoz) is futtatjuk. Mivel ezek a protokollok nem rendelkeznek modern tulajdonságokkal, például ügyfélazonosítóval, korlátozott telemetriai adatok vannak a hamis pozitívértékek csökkentésére. Javasoljuk ügyfeleinknek, hogy a modern hitelesítésre lépjenek át. |
| Rendszergazda megerősítette felhasználó feltörése | Offline | Ez az észlelés azt jelzi, hogy egy rendszergazda a Kockázatos felhasználók felhasználói felületén vagy a riskyUsers API használatával a "Felhasználó feltörésének megerősítése" lehetőséget választotta. Ha meg szeretné tudni, hogy melyik rendszergazda erősítette meg, hogy a felhasználó feltört, ellenőrizze a felhasználó kockázati előzményeit (felhasználói felületen vagy API-n keresztül). |
| Rosszindulatú IP-cím | Offline | Ez az észlelés rosszindulatú IP-címről történő bejelentkezést jelez. Az IP-cím az IP-címtől vagy más IP-hírnévforrás-forrásoktól kapott érvénytelen hitelesítő adatok miatt rosszindulatúnak minősül a magas hibaarányok alapján. |
| Gyanús beérkezett üzenetek manipulálására vonatkozó szabályok | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)fedezte fel. Ez az észlelési profil a környezetben, és riasztást ad ki, ha gyanús szabályok vannak beállítva, amelyek üzeneteket vagy mappákat törölnek vagy helyeznek át a felhasználó beérkezett üzenetein. Ez azt jelezheti, hogy a felhasználó fiókja biztonsága sérül, az üzenetek szándékosan rejtve vannak, és hogy a postaládát levélszemét vagy rosszindulatú program terjesztésére használják a szervezetben. |
| Lehetetlen utazás | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel)fedezte fel. Ez az észlelés két olyan felhasználói tevékenységet (egy vagy több munkamenetet) azonosít, amelyek földrajzilag távoli helyekről származnak, egy olyan időszakon belül, amely rövidebb, mint az az idő, amely alatt a felhasználó nak az első helyről a másodikhelyre kellett volna utaznia, jelezve, hogy hogy egy másik felhasználó ugyanazt a hitelesítő adatokat használja. |

### <a name="other-risk-detections"></a>Egyéb kockázatészlelések

| Kockázatészlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| További észlelt kockázat | Valós idejű vagy offline | Ez az észlelés azt jelzi, hogy a rendszer a fenti prémium észlelések egyikét észlelte. Mivel a prémium szintű észlelések csak az Azure AD Premium P2-ügyfelek számára láthatók, a "további észlelt kockázat" címet az Azure AD Premium P2-licencekkel nem rendelkező ügyfelek számára. |

## <a name="next-steps"></a>További lépések

- [A kockázatok csökkentésére rendelkezésre álló politikák](concept-identity-protection-policies.md)

- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)
