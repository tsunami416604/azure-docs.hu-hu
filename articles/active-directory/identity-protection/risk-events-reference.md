---
title: Az Azure Active Directory Identity Protection kockázati események leírása |} A Microsoft Docs
description: Az Azure Active Directory Identity Protection kockázati események hivatkozhat.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: ff53cf69d53aba7965b2a7d79a4aa04494c61ab1
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913907"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Az Azure Active Directory Identity Protection kockázati események leírása

Biztonsági rések túlnyomó többsége kerül sor, amikor a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Feltört identitásokat felderítése nem könnyű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. Minden észlelt gyanús művelet egy rekord nevű kockázati esemény van tárolva.


## <a name="anonymous-ip-address"></a>Névtelen IP cím

**Észlelés típusa:** Valós idejű  
**Régi név:** Sign-ins from anonymous IP address


A kockázati esemény típusa azt jelzi, hogy a névtelen IP-címet (például a Tor böngésző, anonymizer VPN-eket) történő bejelentkezések.
Ezen IP-címek actors, aki szeretné, hogy azok vélhetően ártó szándékkal bejelentkezési telemetriai (IP-cím, tartózkodási hely, eszköz, stb.) által általában használt.


## <a name="atypical-travel"></a>Szokatlan útvonal

**Észlelés típusa:** Offline  
**Régi név:** Bejelentkezés szokatlan helyekről


A kockázati esemény típusa két bejelentkezések földrajzilag távoli helyekről, ahol a helyek közül legalább is lehet a felhasználó szokatlan, adott múltbeli viselkedés azonosítja. Számos más tényező befolyásolja, többek között a gépi tanulási algoritmus figyelembe veszi a két bejelentkezést és a vett volna igénybe a felhasználó számára az első helyen a második, amely azt jelzi, hogy egy másik felhasználó azonos használja az utazási idő között eltelt idő hitelesítő adatok.

Az algoritmus figyelmen kívül hagyja a nyilvánvaló "vakriasztásokat" a lehetetlen utazás feltételek, például a VPN-EK és a szervezet más felhasználói által rendszeresen használt helyek hozzájárul. A rendszer a legkorábbi, 14 nap vagy 10 bejelentkezéseket, amely során egy új felhasználói bejelentkezési viselkedés képes megtanulni az betanulási időszakra van.


## <a name="leaked-credentials"></a>Kiszivárogtatott hitelesítő adatokkal

**Észlelés típusa:** Offline  
**Régi név:** Felhasználók, akiknek kiszivárogtak a hitelesítő adatai


A kockázati esemény típusa, az azt jelzi, hogy a felhasználó érvényes hitelesítő adatokkal rendelkezik lett kiszivárgott.
Amikor az internetes bűnözők veszélyeztetheti a jogosult felhasználók érvényes jelszavak, a bűnözők gyakran ossza meg ezeket a hitelesítő adatokat. Ez általában közzétételével őket nyilvánosan a sötét webes vagy illessze be a webhelyek vagy kereskedelmi vagy a hitelesítő adatokat, a fekete piacon értékesítése történik. A Microsoft kiszivárgott hitelesítő adatok szolgáltatás beszerzi a felhasználónév / jelszó párosítja, nyilvános és a sötét webhelyek figyelése és a használata:

- Kutatók

- Rendészeti

- A Microsoft biztonsági csoportokkal

- Más megbízható forrásokból

Ha a szolgáltatás felhasználói hitelesítő adatokat, a webről, illessze be a webhelyek vagy a fenti adatforrások beszerzi, azok veti össze az Azure AD-felhasználók aktuális érvényes hitelesítő adatokat érvényes egyezés található.


## <a name="malware-linked-ip-address"></a>Kártevő szoftverrel összekapcsolt IP-cím

**Észlelés típusa:** Offline  
**Régi név:** Bejelentkezések fertőzött eszközökről


A kockázati esemény típusa által aktívan kiszolgálóval való kommunikációhoz egy robot ismert kártevő szoftverrel fertőzött IP-címekről indított bejelentkezések jelzi. Ez határozza meg, hogy a felhasználó-eszköz IP-címekkel, a bot kiszolgáló aktív állapotában bot kiszolgáló összevetésén alapulnak, amelyek IP-címek használatával történik.


## <a name="unfamiliar-sign-in-properties"></a>Szokatlan bejelentkezési tulajdonságok

**Észlelés típusa:** Valós idejű  
**Régi név:** Bejelentkezések ismeretlen helyekről

A kockázati esemény típusa ismeretlen tulajdonságokkal bejelentkezések meghatározásához korábbi bejelentkezési tulajdonságok (például eszköz, tartózkodási hely, hálózati) figyelembe veszi. A rendszer korábbi, felhasználó által használt helyek tulajdonságok tárolja, és ilyen fájlokra úgy tekint "ismerős". A kockázati esemény akkor aktiválódik, ha még nem ismert tulajdonságok listája a tulajdonságokkal történik a bejelentkezés. A rendszer egy 30 napos, amely során azt nem jelöli meg rendellenesként új észleléseket betanulási időszakra van.
Azt is futtathatja az észlelés az egyszerű hitelesítés (vagy az örökölt protokollok). Modern tulajdonságai, például az ügyfél-azonosító nem rendelkezik ezeket a protokollokat, mert nincs vakriasztások csökkentésének érdekében korlátozott telemetriai adatokat. Azt javasoljuk, hogy ügyfeleink számára, hogy helyezze át a modern hitelesítést.

