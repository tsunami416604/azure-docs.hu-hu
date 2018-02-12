---
title: "Az Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - tervezése"
description: "FedRAMP webes alkalmazások Automation - tervezése"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="planning-pl"></a>Tervezési (PL)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-pl-1"></a>A NIST 800-53 vezérlő PL-1

#### <a name="security-planning-policy-and-procedures"></a>Biztonsági tervezés házirend és eljárások

**PL-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy biztonsági házirend célja, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás, koordinációs orvosló tervezése szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a biztonsági házirend és biztonsági vezérlők; tervezési tervezési végrehajtásának megkönnyítése ellenőrzi, és frissíti az aktuális biztonsági házirend tervezése [hozzárendelés: szervezet által meghatározott gyakoriság]; biztonsági tervezési eljárásokat és [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű biztonsági házirend és eljárások tervezési elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 Control PL-2.a

#### <a name="system-security-plan"></a>Rendszer biztonsági csomag

**PL-2.a** a szervezet házon belül fejlesztett alkalmazásokra a adatokat a rendszer, amely megfelel a szervezet vállalati architektúra; explicit módon határozza meg a rendszer engedélyezési határa; ismerteti a működési környezetben, egy biztonsági tervezése az információk rendszer feladatok és az üzleti folyamatok; Itt az adatokat a rendszer, beleértve a profilkategóriák; támogató biztonsági kategorizálása ismerteti, a rendszer és kapcsolatokkal vagy más információs rendszerekkel használt; kapcsolatok működési környezetbe a rendszer a biztonsági követelmények áttekintése bármely megfelelő átfedések azonosítja, ha van ilyen; ismerteti a biztonsági intézkedések, vagy a tervezett teljesíti-e követelményeket, ideértve a tailoring döntéseket; egy indoklása és tekintse át, és jóváhagyott engedélyező hivatalos vagy kijelölt képviselő terv végrehajtása előtt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős rendszer biztonsági terv kidolgozása (SSP), amely a célként megadott engedélyezési (pl. FedRAMP) által meghatározott feltételeknek. Az ügyfelek hivatkozhatnak NIST különleges közlemény 800-18 R1, a biztonsági tervek fejlesztése a Szövetségi információs rendszerekkel használt útmutatója. Az ügyfél SSP kell a Microsoft Azure-ból örökölt vezérlők cím és a Microsoft Azure megosztottszolgáltatás-ellátó megvalósítása a részletekért tekintse meg. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 Control PL-2.b

#### <a name="system-security-plan"></a>Rendszer biztonsági csomag

**PL-2.b** a szervezet a biztonsági tervnek másolatait továbbítja, és a terv bekövetkező változások kommunikál [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a rendszer biztonsági csomag terjesztése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-2c"></a>A NIST 800-53 vezérlő PL-2.c

#### <a name="system-security-plan"></a>Rendszer biztonsági csomag

**PL-2.c** a szervezet ellenőrzi, hogy az adatokat a rendszer biztonsági tervezése [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a rendszer biztonsági tervének ellenőrzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-2d"></a>A NIST 800-53 vezérlő PL-2.d

#### <a name="system-security-plan"></a>Rendszer biztonsági csomag

**PL-2.d** a szervezet a terv frissíti az adatokat rendszer/környezet művelet vagy terv végrehajtása vagy a biztonsági vezérlő értékelések során azonosított problémák cím módosításai.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a rendszer biztonsági csomag frissítése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-2e"></a>A NIST 800-53 vezérlő PL-2.e

#### <a name="system-security-plan"></a>Rendszer biztonsági csomag

**PL-2.e** A szervezet védje a biztonsági tervnek a jogosulatlan és a módosítása.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a rendszer biztonsági terv védelme. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-pl-2-3"></a>A NIST 800-53 vezérlő PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Rendszer biztonsági terv |} Tervezze meg / egyeztessen az egyéb szervezeti entitások

**PL-2 (3)** a szervezeti terveket és koordinálja a rendszer adatokat érintő biztonsági tevékenységek [hozzárendelés: szervezet által meghatározott személyek vagy csoportok] ahhoz, hogy gyakorolt hatása csökkenthető az ilyen tevékenységek végrehajtása előtt egyéb szervezeti entitások.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős tervezési, valamint koordinációs tevékenység biztonsággal kapcsolatos egyéb szervezeti entitások gyakorolt hatás csökkentése érdekében. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-4a"></a>A NIST 800-53 vezérlő PL-4.a

#### <a name="rules-of-behavior"></a>Viselkedési szabályai

**PL-4.a** a szervezet hoz létre, és könnyen hozzáférhetővé teszi egyéni felhasználók számára az információs rendszer való hozzáférést igénylő a szabályokat, a felelősségeket és az információkat és adatokat rendszer várt működése használat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű szabályok működés, a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-4b"></a>A NIST 800-53 vezérlő PL-4.b

#### <a name="rules-of-behavior"></a>Viselkedési szabályai

**PL-4.b** a szervezet a aláírt visszaigazolás fogad ilyen egyéni felhasználók számára, jelezve, hogy azok olvasási megértése és elfogadják a viselkedés, a szabályok információkat és az információs rendszer való hozzáférés engedélyezése előtt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű szabályok működés, a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-4c"></a>A NIST 800-53 vezérlő PL-4.c

#### <a name="rules-of-behavior"></a>Viselkedési szabályai

**PL-4.c** a szervezet ellenőrzi, és frissíti a viselkedési szabályai [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű szabályok működés, a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-4d"></a>A NIST 800-53 vezérlő PL-4.d

#### <a name="rules-of-behavior"></a>Viselkedési szabályai

**PL-4.d** a szervezet megköveteli használhatják, akik regisztráltak a szabályokat, működés, hogy olvassa és írja alá újra a módosított/frissített a szabályok működés esetén egy korábbi verziója.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű szabályok működés, a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-pl-4-1"></a>A NIST 800-53 vezérlő PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Szabályok működés |} Közösségi média és a hálózatkezelés korlátozásai

**PL-4 (1)** a szervezet viselkedés, a közösségi média/hálózati helyek használatának explicit korlátozásai és a szervezeti adatok közzétételét a nyilvános webhelyeken szabályokat tartalmazza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű szabályok működés tartalmazhatnak közösségi korlátozásai / hálózati helyek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-8a"></a>A NIST 800-53 vezérlő PL-8.a

#### <a name="information-security-architecture"></a>Információk biztonsági architektúrája

**PL-8.a** a szervezet házon belül fejlesztett alkalmazásokra egy információk biztonsági architektúrája a információs rendszer, amely leírja a teljes alapvetően, követelmények és megközelítést a titkosítás, integritás, védelme végrehajtandó és szervezeti információk; leírja, hogyan információk biztonsági architektúrájának integrálva van, és támogatja a vállalati architektúra; és bármely információ biztonsági feltételezéseket, és a függőségek, külső szolgáltatások ismerteti.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy információk biztonsági architektúrája ügyfél telepített erőforrások fejlesztése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-8b"></a>A NIST 800-53 vezérlő PL-8.b

#### <a name="information-security-architecture"></a>Információk biztonsági architektúrája

**PL-8.b** a szervezet ellenőrzi, és frissíti az adatokat biztonsági architektúra [hozzárendelés: szervezet által meghatározott gyakoriság] frissítések a vállalati architektúra megfelelően.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felülvizsgálata és frissítése a információk biztonsági architektúrája felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pl-8c"></a>A NIST 800-53 vezérlő PL-8.c

#### <a name="information-security-architecture"></a>Információk biztonsági architektúrája

**PL-8.c** a szervezet biztosítja, hogy tervezett információk biztonsági architektúra módosítások megjelennek a biztonsági tervnek, a biztonsági műveletek koncepció (KONCEPCIÓ) és a szervezeti beszerzésekkel/kérése.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a tervezett módosítások nyilvántartási az információk biztonsági architektúra. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |
