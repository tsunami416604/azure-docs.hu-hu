---
title: Kockázatcsökkentés - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: A Microsoft Fenyegetésmodellezési eszköz kockázatcsökkentési lapja, amely a leginkább elérhetőnek kitett fenyegetések lehetséges megoldásait emeli ki.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728036"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>A Microsoft fenyegetésmodellezési eszközének kockázatlevantálása

A fenyegetésmodellezési eszköz a Microsoft biztonsági fejlesztési életciklusának (SDL) központi eleme. Lehetővé teszi a szoftvertervezők számára a potenciális biztonsági problémák korai azonosítását és enyhítését, amikor azok viszonylag egyszerűek és költséghatékonyak. Ennek eredményeképpen jelentősen csökkenti a teljes fejlesztési költséget. Az eszközt nem biztonsági szakértőkszem előtt tartva terveztük, így a fenyegetésmodellezés tetszővé válik minden fejlesztő számára azáltal, hogy egyértelmű útmutatást nyújt a fenyegetésmodellek létrehozásához és elemzéséhez.

Látogasson el a **[Threat Modeling Tool](threat-modeling-tool.md)** az induláshoz még ma!

## <a name="mitigation-categories"></a>Kockázatcsökkentési kategóriák

A fenyegetésmodellezési eszköz kockázatcsökkentő megoldásai a webalkalmazás biztonsági kerete szerint vannak kategorizálva, amely a következőkből áll:

| Kategória | Leírás |
| -------- | ----------- |
| **[Naplózás és naplózás](threat-modeling-tool-auditing-and-logging.md)** | Ki mit és mikor csinált? Naplózás és naplózás azt szeretné, hogy az alkalmazás hogyan rögzíti a biztonsággal kapcsolatos eseményeket |
| **[Hitelesítés](threat-modeling-tool-authentication.md)** | hogy vagy? A hitelesítés az a folyamat, amelynek során egy entitás egy másik entitás identitását bizonyítja, általában hitelesítő adatokkal, például felhasználónévvel és jelszóval. |
| **[Engedélyezési](threat-modeling-tool-authorization.md)** | Mit lehet tenni? Az engedélyezés az, ahogyan az alkalmazás hozzáférést biztosít az erőforrásokhoz és műveletekhez |
| **[Kommunikáció biztonsága](threat-modeling-tool-communication-security.md)** | Kihez beszélsz? Kommunikációs biztonság biztosítja, hogy minden kommunikáció a lehető legbiztonságosabb legyen |
| **[Konfigurációkezelés](threat-modeling-tool-configuration-management.md)** | Kiként fut az alkalmazás? Mely adatbázisokhoz kapcsolódik? Hogyan történik az ön alkalmazása? Hogyan biztosítják ezeket a beállításokat? A konfigurációkezelés azt a módját olvassa el, hogy az alkalmazás hogyan kezeli ezeket a működési problémákat |
| **[Titkosítás](threat-modeling-tool-cryptography.md)** | Hogy tudsz titkot tartani? Hogyan hamisítod az adatokat vagy a könyvtárakat (sértetlenség)? Hogyan biztosít magokat véletlenszerű értékekhez, amelyeknek kriptográfiailag erősnek kell lenniük? A kriptográfia arra utal, hogy az alkalmazás hogyan érvényesíti a titkosságot és az integritást |
| **[Kivételkezelés](threat-modeling-tool-exception-management.md)** | Ha egy metódus hívása az alkalmazásban sikertelen, mit csinál az alkalmazás? Mennyit árulsz el? Rövid hibaadatokat ad vissza a végfelhasználóknak? Értékes kivételadatokat ad át a hívónak? Az alkalmazás nem kecsesen? |
| **[Bemeneti érvényesítés](threat-modeling-tool-input-validation.md)** | Honnan tudja, hogy az alkalmazás által kapott bemenet érvényes és biztonságos? A bemeneti ellenőrzés azt vizsgálja, hogy az alkalmazás hogyan szűri, cserálja vagy utasítja el a bemenetet a további feldolgozás előtt. Fontolja meg a bemeneti bevitelkorlátozást a belépési pontokon keresztül, és a kimeneti kimenetet a kilépési pontokon keresztül. Megbízik az olyan forrásokból származó adatokban, mint például adatbázisok és fájlmegosztások? |
| **[Érzékeny adatok](threat-modeling-tool-sensitive-data.md)** | Hogyan kezeli az alkalmazás a bizalmas adatokat? A bizalmas adatok azt eredményezik, hogy az alkalmazás hogyan kezeli azokat az adatokat, amelyeket a memóriában, a hálózaton keresztül vagy az állandó tárolókban kell védeni. |
| **[Munkamenet-kezelés](threat-modeling-tool-session-management.md)** | Hogyan kezeli és védi az alkalmazás a felhasználói munkameneteket? A munkamenet a felhasználó és a webalkalmazás közötti kapcsolódó interakciók sorozatára utal |

Ez segít azonosítani:

* Hol vannak a leggyakoribb hibák?
* Hol vannak a leginkább végrehajtható fejlesztések?

Ennek eredményeképpen ezeket a kategóriákat használja a biztonsági munka összpontosítására és rangsorolására, így ha ismeri a legelterjedtebb biztonsági problémákat a bemeneti érvényesítési, hitelesítési és engedélyezési kategóriákban, akkor ott kezdheti meg. További információkért látogasson el ** [a szabadalmi linkre](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>További lépések

Látogasson el **[a fenyegetésmodellezési eszköz fenyegetések,](threat-modeling-tool-threats.md)** hogy többet tudjon meg a fenyegetés kategóriák az eszköz segítségével a lehetséges tervezési fenyegetések.