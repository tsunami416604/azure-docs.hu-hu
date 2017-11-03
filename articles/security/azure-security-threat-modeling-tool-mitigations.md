---
title: "-Microsoft fenyegetés modellezési eszköz - megoldást Azure |} Microsoft Docs"
description: "A Microsoft fenyegetés modellezési eszköz Kiemelés a leginkább kitett lehetséges megoldás megoldást lap fenyegetések jönnek létre."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft fenyegetések modellezése eszköz megoldást

A fenyegetések modellezése eszköz a Microsoft biztonsági fejlesztési életciklus (SDL) alapeleme. Lehetővé teszi a szoftverek fejlesztők azonosítása és potenciális biztonsági problémákat korai, amelyek viszonylag egyszerű és költséghatékony megoldásához. Emiatt nagy mértékben csökkenti a fejlesztési költségek. Is azt használható az eszköz szakértőivel nem biztonsági szem előtt, könnyítve modellezni az összes létrehozására, illetve elemzése fenyegetés modellek világos útmutatást megadásával.

Látogasson el a  **[fenyegetések modellezése eszköz](./azure-security-threat-modeling-tool.md)**  való kezdjen neki még ma!

## <a name="mitigation-categories"></a>Megoldás kategóriák

A fenyegetések modellezése eszköz azok mérséklési szerint vannak kategóriába megfelelően a webes alkalmazás biztonsági keretben, melyik a következőkből áll:

| Kategória | Leírás |
| -------- | ----------- |
| **[A vizsgálati és naplózási](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Ki nem adott meg, mit és mikor? Vizsgálati és naplózási tekintse meg az alkalmazást hogyan rögzíti, biztonsággal kapcsolatos események |
| **[Hitelesítés](./azure-security-threat-modeling-tool-authentication.md)** | hogy vagy? A hitelesítés az a folyamat, ahol egy entitás egy másik entitás, általában a hitelesítő adatokat, például egy felhasználónevet és jelszót keresztül identitását igazolja |
| **[Engedélyezési](./azure-security-threat-modeling-tool-authorization.md)** | Mire szolgál? Engedélyezési, hogy az alkalmazás biztosítja az erőforrások és a műveletek hozzáférés-vezérlést |
| **[A kommunikáció biztonsága](./azure-security-threat-modeling-tool-communication-security.md)** | Aki akkor beszélünk? Kommunikációs biztonsági biztosítja végzett összes kommunikáció megfelelő biztonsága |
| **[Konfigurációkezelés](./azure-security-threat-modeling-tool-configuration-management.md)** | Aki futtató az alkalmazást? Mely adatbázisokat nem azt csatlakozni? Hogyan történik az az alkalmazás? Hogyan biztonságosak ezeket a beállításokat? Az alkalmazás a működési problémák kezelésének hivatkozik konfigurációkezelés |
| **[Titkosítás](./azure-security-threat-modeling-tool-cryptography.md)** | Hogyan meg megakadályozzák titkos kulcsokat (bizalmas)? Hogyan esetleg illetéktelen ellenőrző az adatok és a szalagtárak (integritása)? Hogyan akkor ad magok meg lehet erős titkosítással véletlenszerű értéket? Titkosítás hivatkozik hogyan érvénybe lépteti az alkalmazás a titkosítás és integritás |
| **[Kivételek kezelése](./azure-security-threat-modeling-tool-exception-management.md)** | Ha az alkalmazás metódus hívása nem sikerült, mire az alkalmazást? Milyen mértékű tegye meg felfedése? A végfelhasználók számára hajtsa végre ismét rövid hibainformációk? Vissza a hívók számára tegye át értékes kivételek adatai? Szabályosan elakad az alkalmazást? |
| **[Bemenet-ellenőrzést](./azure-security-threat-modeling-tool-input-validation.md)** | Honnan tudhatja, hogy a bemeneti alkalmazás kap-e érvényes és biztonságos? Bemenet-ellenőrzéshez hogyan az alkalmazás-szűrők, scrubs vagy elutasítja a további feldolgozás előtt bevitel hivatkozik. Vegye figyelembe a korlátozási belépési ponton keresztül bemeneti és kimeneti kilépési ponton keresztül kódolás. Megbízik, például az adatbázisok és a fájlmegosztások forrásból származó adatokat? |
| **[Bizalmas adatok](./azure-security-threat-modeling-tool-sensitive-data.md)** | Hogyan az alkalmazás bizalmas adatokat kezelni? Bizalmas adatok hivatkozik, amely védeni kell a memória, a hálózaton keresztül, vagy az állandó tároló adatokat az alkalmazás kezelésének |
| **[Munkamenet-kezelés](./azure-security-threat-modeling-tool-session-management.md)** | Hogyan nem az alkalmazás kezelése és védelme a felhasználói munkamenetek? A munkamenet a felhasználó és a webes alkalmazás közötti kapcsolati kapcsolódó tevékenységek sorozata hivatkozik. |

Ez segít azonosítani:

* Ha végzett a leggyakoribb hibák
* Hol találhatók a legtöbb végrehajthatóként fejlesztései

Ennek eredményeképpen a fókusz ezen kategóriák használ, és munkánk biztonsági, így ha tudja, hogy a legjellemzőbb biztonsági problémákat tapasztal a bemeneti érvényesítési, hitelesítési és engedélyezési kategóriák, megkezdheti van. További információt  **[a szabadalmi hivatkozás](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Következő lépések

Látogasson el  **[fenyegetések modellezése eszköz fenyegetések](./azure-security-threat-modeling-tool-threats.md)**  további információt az eszköz alapján hoz létre a lehetséges tervezési fenyegetések fenyegetés kategóriák.