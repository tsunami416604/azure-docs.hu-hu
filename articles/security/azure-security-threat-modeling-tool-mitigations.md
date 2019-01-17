---
title: Megoldások – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: Megoldások oldal a Microsoft Threat Modeling Tool kiemelése a leginkább kitett lehetséges megoldása a fenyegetések jönnek létre.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359136"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool megoldások

A Threat Modeling Tool a Microsoft biztonsági fejlesztési életciklus (SDL) alapeleme. Azonosíthatja és korai, csökkentheti a potenciális biztonsági problémákat, amelyek viszonylag egyszerű és költséghatékony oldja meg a szoftver tervezők lehetővé teszi. Ennek eredményeképpen jelentősen csökkenti a fejlődés a teljes költség. Ezenkívül alakítottuk ki az eszköz nem biztonsági szakértői szem előtt, könnyíti threat modellezési minden fejlesztő számára világos útmutatóval szolgál azáltal, hogy létrehozásával és enyhítési elemzése.

Látogasson el a **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** és vágjon bele most!

## <a name="mitigation-categories"></a>Kockázatcsökkentési kategóriák

A Threat Modeling Tool megoldások telefonokat megfelelően a webes alkalmazás biztonsági keret, amely a következőkből áll:

| Kategória | Leírás |
| -------- | ----------- |
| **[A vizsgálati és naplózási](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Ki nem adott meg, mit és mikor? Naplózás és a naplózás tekintse meg, hogy az alkalmazás rögzíti a biztonsági események |
| **[Hitelesítés](./azure-security-threat-modeling-tool-authentication.md)** | Ki Ön? Hitelesítés az a folyamat, ha egy entitás igazolja a hitelesítő adatokat, például egy felhasználónevet és jelszót keresztül általában egy másik entitás identitását |
| **[Engedélyezési](./azure-security-threat-modeling-tool-authorization.md)** | Mire használható? Engedély, hogy az alkalmazás hozzáférés-vezérléseket biztosít az erőforrások és műveletek |
| **[Kommunikációs biztonság](./azure-security-threat-modeling-tool-communication-security.md)** | Akik akkor beszélünk? Kommunikációs biztonság gondoskodik arról, hogy minden kommunikáció kész lehető legbiztonságosabb |
| **[Konfigurációkezelés](./azure-security-threat-modeling-tool-configuration-management.md)** | Akik futtató alkalmazását? Adatbázisok nem, hogy kapcsolódni? Hogyan történik az alkalmazását? Hogyan biztonságosak ezeket a beállításokat? Konfigurációkezelés hivatkozik hogyan az alkalmazás kezelje-e ezek működési problémák |
| **[Titkosítás](./azure-security-threat-modeling-tool-cryptography.md)** | Hogyan akadályozzák titkos kulcsokat (bizalmas)? Hogyan, hogy illetéktelen ellenőrző az adatok és a könyvtárakat (integritása)? Hogyan, ad magok számára, amelyet be kell kriptográfiai erős, véletlenszerű értékek? Titkosítás hivatkozik hogyan érvénybe lépteti az alkalmazás a bizalmas és -integritás |
| **[Kivételek kezelése](./azure-security-threat-modeling-tool-exception-management.md)** | Ha nem sikerül egy metódus hívása az alkalmazásban, mire az alkalmazás? IP-címek fenntartási tegye meg felfedése? Tegye felhasználóbarát hibaüzenetet visszatér a végfelhasználók? Tegye meg információkat adnak át értékes kivétel vissza a hívónak? Nem az alkalmazás nem sikerült szabályosan? |
| **[Bemenet-ellenőrzés](./azure-security-threat-modeling-tool-input-validation.md)** | Hogyan tudja, hogy az alkalmazás fogad a bemeneti-e érvényes és biztonságos? Bemenet-ellenőrzés módját az alkalmazás szűri, scrubs vagy elutasítja a további feldolgozás előtt bevitel hivatkozik. Fontolja meg a korlátozásra bemeneti keresztül belépési pontok és a kódolási kimeneti kilépési ponton keresztül. Megbízik, például adatbázisok és fájlmegosztások forrásokból származó adatokat? |
| **[Bizalmas adatok](./azure-security-threat-modeling-tool-sensitive-data.md)** | Hogyan kezeli az alkalmazás bizalmas adatokat? Bizalmas adatok hivatkozik, amely védeni kell a memória, a hálózaton keresztül, vagy állandó tárolók adatokat az alkalmazás kezelésének |
| **[Munkamenet-kezelés](./azure-security-threat-modeling-tool-session-management.md)** | Hogyan az alkalmazás kezelése és védelme a felhasználói munkamenetek? A munkamenet egy felhasználó és a webes alkalmazás közötti kapcsolódó interakciók sorozatát hivatkozik. |

Ennek segítségével azonosíthatja:

* Ha végzett a leggyakoribb hibák
* Hol találhatók a gyakorlatban leginkább hasznosítható fejlesztések

Ennek eredményeképpen alkalmazkodó e kategóriák használ, és rangsorolja a biztonsági munkahelyi azt tudja, hogy a bemeneti érvényesítést, hitelesítési és engedélyezési kategóriák a leggyakrabban előforduló biztonsági problémák fordulnak elő, ha meg van. További információt  **[szabadalmi ide](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>További lépések

Látogasson el **[Threat Modeling eszköz fenyegetések](./azure-security-threat-modeling-tool-threats.md)** további információ a lehetséges tervezési fenyegetések létrehozásához használja az eszköz fenyegetés kategóriák.