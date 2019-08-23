---
title: Azure Active Directory Identity Protection kockázati események referenciája | Microsoft Docs
description: Azure Active Directory Identity Protection kockázati események referenciája.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905199"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Azure Active Directory Identity Protection kockázati események referenciája

A biztonsági rések túlnyomó többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. A feltört identitások feltárása nem egyszerű feladat. Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet egy kockázati esemény nevű rekordban tárolódik.

## <a name="anonymous-ip-address"></a>Névtelen IP cím

**Észlelés típusa:** Valós idejű  
**Régi név:** Névtelen IP-címről történő bejelentkezések

Ez a kockázati esemény típusa egy névtelen IP-címről (például Tor-böngésző, névtelen VPN) érkező bejelentkezéseket jelez.
Ezeket az IP-címeket jellemzően olyan szereplők használják, akik el szeretnék rejteni a bejelentkezési telemetria (IP-cím, hely, eszköz stb.) a potenciálisan rosszindulatú szándék érdekében.

## <a name="atypical-travel"></a>Szokatlan útvonal

**Észlelés típusa:** Offline  
**Régi név:** Bejelentkezés szokatlan helyekről

Ez a kockázati esemény típusa két, földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a felhasználók legalább egyike atipikus lehet a felhasználó számára, a múltbeli viselkedés miatt. Ez a gépi tanulási algoritmus számos más tényezőt is figyelembe vesz, és azt, hogy a felhasználó hogyan utazott el az első helyről a másodikra, és azt jelzi, hogy egy másik felhasználó ugyanazokat a lépéseket használja hitelesítő adatok.

Az algoritmus figyelmen kívül hagyja a "hamis pozitív" állapotot, amely hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer kezdeti tanulási időszaka a legkorábbi 14 nap vagy 10 bejelentkezés, amely során egy új felhasználó bejelentkezési viselkedését tanulja meg.

## <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

**Észlelés típusa:** Offline  
**Régi név:** Felhasználók, akiknek kiszivárogtak a hitelesítő adatai

Ez a kockázati esemény típusa azt jelzi, hogy a felhasználó érvényes hitelesítő adatait kiszivárgott.
Ha a kiberbűnözők veszélyezteti a legitim felhasználók érvényes jelszavait, a bűnözők gyakran megosztják ezeket a hitelesítő adatokat. Ezt általában a sötét web-vagy a webhelyeken történő közzétételsel, illetve a fekete piacon a hitelesítő adatok forgalmazásával vagy értékesítésével hajtják végre. A Microsoft által kiszivárgott hitelesítő adatok a Felhasználónév/jelszó párokat szerzik be a nyilvános és a sötét webhelyek figyelésével, valamint a következővel:

- Kutatók
- Bűnüldözés
- Biztonsági csapatok a Microsoftnál
- Egyéb megbízható források

Amikor a szolgáltatás felhasználói hitelesítő adatokat kér a sötét webhelyről, beilleszti a helyeket vagy a fenti forrásokat, a rendszer ellenőrzi, hogy érvényes egyezéseket keres-e az Azure AD-felhasználók aktuális érvényes hitelesítő adataival.

## <a name="malware-linked-ip-address"></a>Kártevő szoftverrel összekapcsolt IP-cím

**Észlelés típusa:** Offline  
**Régi név:** Bejelentkezések fertőzött eszközökről

Ez a kockázati esemény típusa olyan IP-címekről származó bejelentkezéseket jelez, amelyekről ismert, hogy aktívan kommunikálnak egy bot-kiszolgálóval. Ezt úgy határozzák meg, hogy a felhasználó eszközének IP-címeit a bot-kiszolgálóval érintkező IP-címekről, míg a bot Server aktív volt.

## <a name="unfamiliar-sign-in-properties"></a>Ismeretlen bejelentkezési tulajdonságok

**Észlelés típusa:** Valós idejű  
**Régi név:** Bejelentkezések ismeretlen helyekről

Ez a kockázati eseménytípus a rendellenes bejelentkezések kereséséhez a korábbi bejelentkezési előzményeket (IP, szélesség/hosszúság és ASN) veszi figyelembe. A rendszer a felhasználó által használt korábbi helyszínekről tárol adatokat, és ezeket a "ismerős" helyet tekinti át. A kockázati esemény akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel az ismerős helyek listáján. Az újonnan létrehozott felhasználók "tanulási módban" lesznek egy olyan időszakra, amelyben a nem ismerős bejelentkezési tulajdonságok kockázati eseményei ki lesznek kapcsolva, miközben az algoritmusok megismerhetik a felhasználó viselkedését. A tanulási mód időtartama dinamikus, és attól függ, hogy mennyi időt vesz igénybe az algoritmus, hogy elegendő információt gyűjtsön a felhasználó bejelentkezési mintáit illetően. A minimális időtartam öt nap. A felhasználók hosszú inaktivitás után visszatérhetnek a tanulási módba. A rendszer emellett figyelmen kívül hagyja a jól ismert eszközökről érkező bejelentkezéseket és a jól ismert helyhez földrajzilag közel lévő helyeket. 

Ezt az észlelést az alapszintű hitelesítéshez (vagy örökölt protokollokhoz) is futtatjuk. Mivel ezek a protokollok nem rendelkeznek modern tulajdonságokkal, például az ügyfél-AZONOSÍTÓval, a hamis pozitív telemetria csökkentése korlátozott. Javasoljuk ügyfeleinknek, hogy a modern hitelesítésre térjenek át.

## <a name="azure-ad-threat-intelligence"></a>Azure AD-fenyegetések felderítése

**Észlelés típusa:** Offline <br>
**Régi név:** Ez az észlelés megjelenik az örökölt Azure AD Identity Protection jelentésekben (a kockázatoknak megjelölt felhasználók, kockázati események) a "kiszivárgott hitelesítő adatokkal rendelkező felhasználók"

Ez a kockázati eseménytípus azt jelzi, hogy az adott felhasználó számára szokatlan felhasználói tevékenység van, vagy konzisztens a Microsoft belső és külső veszélyforrások elleni intelligencia-forrásaira épülő ismert támadási mintákkal.

## <a name="admin-confirmed-user-compromised"></a>Rendszergazda megerősítette a felhasználói fiók feltörését

**Észlelés típusa:** Offline <br>
Ez az észlelés azt jelzi, hogy egy rendszergazda a kockázatos felhasználók felhasználói felületén vagy a riskyUsers API-val jelölte meg a "felhasználói biztonság megerősítése" beállítást. Annak megtekintéséhez, hogy mely rendszergazda erősítette meg ezt a felhasználót, ellenőrizze a felhasználó kockázati előzményeit (UI vagy API használatával).
