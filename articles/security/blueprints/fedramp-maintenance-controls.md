---
title: Az Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - karbantartás
description: FedRAMP webes alkalmazások Automation - karbantartás
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
ms.locfileid: "29150919"
---
# <a name="maintenance-ma"></a>Karbantartási (MA)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-ma-1"></a>A NIST 800-53 vezérlő MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Rendszer karbantartására vonatkozó házirend és eljárások

**MA-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy rendszer karbantartására vonatkozó házirend célja, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás, koordinációs orvosló szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a rendszer karbantartására vonatkozó házirend és a kapcsolódó rendszer karbantartási vezérlők; végrehajtásának megkönnyítése ellenőrzi, és frissíti az aktuális rendszer karbantartására vonatkozó házirend [hozzárendelés: szervezet által meghatározott gyakoriság]; és rendszer-karbantartási eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer karbantartására vonatkozó házirend és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-2a"></a>A NIST 800-53 vezérlő MA-2.a

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.a** a szervezet ütemezi, hajt végre, dokumentumokat, és ellenőrzi, hogy rekordok karbantartási és a javítások információk rendszer összetevőinek vagy gyártójához specifikációk és/vagy a szervezeti követelményeknek megfelelően.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ellenőrzött karbantartási feladata. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-2b"></a>A NIST 800-53 vezérlő MA-2.b

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.b** a szervezet jóvá, és figyeli az összes karbantartás tevékenység, akár a helyen, vagy távolról, és hogy a készülék szervizelt helyen, vagy eltávolítani egy másik helyre.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ellenőrzött karbantartási feladata. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-2c"></a>A NIST 800-53 vezérlő MA-2.c

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.c** a szervezet megköveteli, hogy [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] explicit módon a rendszer vagy a szervezeti berendezések a telephelytől távoli karbantartás vagy javítások rendszerösszetevők eltávolításának jóváhagyását.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure megköveteli, hogy a tulajdonság eszközök (például a hálózati eszköz vagy a kiszolgáló) átviteli külső helyszínen igénylő rendelkezik explicit eszköz tulajdonos jóváhagyása. |


 ## <a name="nist-800-53-control-ma-2d"></a>A NIST 800-53 vezérlő MA-2.d

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.d** a szervezet sanitizes berendezések eltávolítani a hozzá tartozó adathordozó szervezeti berendezések a telephelytől távoli karbantartás vagy javítások eltávolítása előtt az összes információit.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure eszköz védelmi szabványos határozza meg az eszköz kezelése az eszközök külső helyszínen történő átvitelhez szükséges óvintézkedéseket. Az eszköz védelmi szabványos szükség van-e adatokat tároló eszközök NIST SP 800-88 Media tisztítási vonatkozó irányelvek konzisztens módon bejelölve/kiürítése előtt az Adatközpont hagyja. |


 ## <a name="nist-800-53-control-ma-2e"></a>A NIST 800-53 vezérlő MA-2.e

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.e** A szervezet összes esetlegesen érintett biztonsági vezérlők győződjön meg arról, hogy a vezérlők továbbra is megfelelően működnek megfelelően a következő karbantartási és javítási műveletek ellenőrzi.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ellenőrzött karbantartási feladata. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-2f"></a>A NIST 800-53 vezérlő MA-2.f

#### <a name="controlled-maintenance"></a>A vezérelt karbantartás

**MA-2.f** a szervezet tartalmaz [hozzárendelés: karbantartási kapcsolódó információk szervezet által meghatározott] szervezeti karbantartási rekordokban.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ellenőrzött karbantartási feladata. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-2-2a"></a>A NIST 800-53 vezérlő MA-2 (2) sémaszintjén

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Karbantartási szabályozott |} Automatikus karbantartás tevékenységek

**MA-2 (2) sémaszintjén** a szervezet automatizált mechanizmusokat, ütemezés, a viselkedési, és a dokumentum karbantartási és a javítások funkcióit használja.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős karbantartási tevékenységek automatizálása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-2-2b"></a>A NIST 800-53 vezérlő MA-2 (2) .b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Karbantartási szabályozott |} Automatikus karbantartás tevékenységek

**MA-2 (2) .b** a szervezet fel dátumig, pontos, hozza létre, és minden karbantartási és a javítási művelet teljes rekordok kért, ütemezett folyamat során, és befejeződött.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős karbantartási tevékenységek automatizálása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-3"></a>A NIST 800-53 vezérlő MA-3

#### <a name="maintenance-tools"></a>Karbantartási eszközök

**MA-3** a szervezet jóvá, vezérlők, és figyeli a rendszer-felügyeleti eszközök információkat.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure befejezni számos eszközt használja. Eszközök jóváhagyása, felügyelt és a Microsoft Azure karbantartása szoftverkarbantartást módosítása, és felszabadíthatja a folyamat. <br /> A hely csapatnak fenntartja az adatközponton belüli használatra jóváhagyott karbantartási eszközök leltárt (lásd a MA-3). A megadott karbantartási eszközök karbantartási csoporthoz van irányítva. Datacenter jóváhagyásra van szükség ahhoz, hogy nem a datacenter által biztosított eszközök segítségével. Az aktuális fizikai eszközök (csavarhúzók, csavarkulcsot stb.) nem érvényes a vezérlőről. <br /> Minden létesítmény tartalmazza a korlátozott fizikai zárolási lista vagy a hozzáférés ellenőrzött helyiségben tárolására szolgáló specializált karbantartási eszközök, például fluke éter hatókörök, fluke fiber channel tesztelők webhelyről, Ethernet festékpatronokat, stb. A helyszolgáltatás csapata ellenőrzi rutin készlet összes eszközök állapotának ellenőrzése. Hozzáférés zárolását, mezőben vagy a karbantartási tárolási hely nyomon a hozzáférési jelvény olvasó naplófájlokban, vizsgálat esetén elérhető van. |


 ### <a name="nist-800-53-control-ma-3-1"></a>A NIST 800-53 vezérlő MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Karbantartási eszközök |} Vizsgálja meg az eszközök

**MA-3 (1)** a szervezet megvizsgálja a karbantartási személyzet nem megfelelő vagy illetéktelen módosítások azokat a létesítmény végzett karbantartási eszközökkel.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure Site Services team fenntartja az adatközponton belüli használatra jóváhagyott karbantartási eszközök leltárt (további részleteket lásd: MA-3). A megadott karbantartási eszközök karbantartási csoporthoz van irányítva. DCM-jóváhagyás szükséges ahhoz, hogy nem a datacenter által biztosított eszközök segítségével. |


 ### <a name="nist-800-53-control-ma-3-2"></a>A NIST 800-53 vezérlő MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Karbantartási eszközök |} Vizsgálja meg az adathordozó

**MA-3 (2)** a szervezet adathordozót tartalmazó diagnosztikai és a vizsgálati programok rosszindulatú kódot előtt ellenőrzi az adathordozót a információs rendszerben használt.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a hordozható számítógépekhez vagy a Microsoft Azure adatközpontok datacenter felügyeleti jóváhagyása nélkül éles környezetben tárolási adathordozókon. Személyes tulajdonú media tilos a Microsoft Azure adatközpontok éles környezetben használatát. <br /> A Microsoft Azure vizsgálata, mielőtt az éles környezetben a Microsoft Azure adatközpontjaiban használt laptopok folyamat van megvalósítva. Biztonsági tisztviselő képzett arra az éles környezetben laptopok segítségével ellenőrizze, hogy a hordozható számítógépek olyan mentek át, és ellenőrzés átadott csoporthoz. |


 ### <a name="nist-800-53-control-ma-3-3"></a>A NIST 800-53 vezérlő MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Karbantartási eszközök |} Megakadályozza a jogosulatlan eltávolítása

**MA-3 (3)** a szervezet megakadályozza, hogy a szervezeti adatokat tartalmazó ellenőrzi, hogy nincs szervezeti adatait a készülék; tisztítására vagy megsemmisítése karbantartási berendezések jogosulatlan eltávolítása a berendezések; a készülék belül a létesítmény; megőrzése vagy az beszerzése alóli [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] explicit módon engedélyezése a készülék a létesítmény az eltávolítása.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure datacenter adott karbantartási eszközök, amelyek a létesítmény belül megmaradnak, és a rendszer nem távolítja el funkcióit használja. Minden egyes létesítmény tartalmaz egy korlátozott fizikai zárolási mező vagy a tárolási hely, amely tárolja a karbantartási eszközök, például a fluke éter hatókörök, fluke fiber channel tesztelők webhelyről, Ethernet festékpatronokat, stb. Hozzáférés a zárolási mező vagy a tárolási hely a DCAT a karbantartási eszközök jogosulatlan hozzáférés tiltása a ellenőrzés alatt. <br /> Szervezeti adatok MA-4 vezérlő védi-karbantartás során. Szervezeti adatok eléréséhez a felhasználónak kell privilegizált fiókok és hitelesítők. |


 ## <a name="nist-800-53-control-ma-4a"></a>A NIST 800-53 vezérlő MA-4.a

#### <a name="nonlocal-maintenance"></a>Nem helyi karbantartás

**MA-4.a** a szervezet jóvá és nem helyi karbantartási és diagnosztikai tevékenységek figyeli.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata nem helyi karbantartás elvégzése az ügyfél telepített operációs rendszereken. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-4b"></a>A NIST 800-53 vezérlő MA-4.b

#### <a name="nonlocal-maintenance"></a>Nem helyi karbantartás

**MA-4.b** a szervezet engedélyezi, hogy a nem helyi karbantartási és a diagnosztika csak, szervezeti házirend konzisztens eszközökhöz, és részletes ismertetését lásd: a biztonsági tervnek az információk rendszerhez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata nem helyi karbantartás elvégzése az ügyfél telepített operációs rendszereken. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-4c"></a>A NIST 800-53 vezérlő MA-4.c

#### <a name="nonlocal-maintenance"></a>Nem helyi karbantartás

**MA-4.c** a szervezet nem helyi karbantartási és diagnosztikai munkamenet létrehozásában erős hitelesítők funkcióit használja.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata nem helyi karbantartás elvégzése az ügyfél telepített operációs rendszereken. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-4d"></a>A NIST 800-53 vezérlő MA-4.d

#### <a name="nonlocal-maintenance"></a>Nem helyi karbantartás

**MA-4.d** a szervezet kezeli a nem helyi karbantartási és diagnosztikai tevékenységeket rögzíti.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata nem helyi karbantartás elvégzése az ügyfél telepített operációs rendszereken. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-4e"></a>A NIST 800-53 vezérlő MA-4.e

#### <a name="nonlocal-maintenance"></a>Nem helyi karbantartás

**MA-4.e** A szervezet munkamenet és hálózati kapcsolatok megszakítása, ha a nem helyi karbantartás befejeződött.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata nem helyi karbantartás elvégzése az ügyfél telepített operációs rendszereken. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-4-2"></a>A NIST 800-53 vezérlő MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nem helyi karbantartási |} A dokumentum nem helyi karbantartás

**MA-4 (2)** a szervezet dokumentumok, a biztonsági csomagban az adatokat a rendszer, a házirendek és eljárások létrehozásáról és a nem helyi karbantartási és diagnosztikai kapcsolatok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél nem helyi karbantartási dokumentálásáért ügyfél telepített operációs rendszerek a biztonsági csomagban felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-4-3"></a>A NIST 800-53 vezérlő MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nem helyi karbantartási |} Hasonló biztonsági / tisztítási

**MA-4 (3)** a szervezet megköveteli, hogy a nem helyi karbantartási és diagnosztikai szolgáltatásokban hajtható végre, amely megvalósítja a funkció a rendszeren jelenleg kiszolgált; megvalósított összehasonlítható egy biztonsági funkció, vagy eltávolítja az adatokat rendszerből az összetevő nem helyi karbantartás vagy-szolgáltatások diagnosztikai információk rendszerből kiszolgálásra sanitizes (tekintetében a szervezeti adatok) az összetevő a szervezeti létesítményekben, és a szolgáltatás eltávolítása előtt végre, ellenőrzi és sanitizes az összetevő (tekintetében a potenciálisan kártevő szoftverek) az összetevő információs rendszer újracsatlakozás előtt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített operációs rendszerek minden nem helyi karbantartásának végrehajtásához, amelyben a hasonló biztonsági információk rendszerből. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-4-6"></a>A NIST 800-53 vezérlő MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nem helyi karbantartási |} Titkosítási védelem

**MA-4 (6)** az információs rendszer megvalósítja az integritásra és a titkosítás nem helyi karbantartási és diagnosztikai kommunikáció védelme érdekében titkosítási mechanizmus.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős titkosítási mechanizmusok megvalósítása nem helyi karbantartási és diagnosztikai ügyfél telepített operációs rendszerek végrehajtása során. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-5a"></a>A NIST 800-53 vezérlő MA-5.a

#### <a name="maintenance-personnel"></a>Karbantartási csoporthoz

**MA-5.a** a szervezet egy folyamatot, karbantartási személyzet engedély létesít, és listát készít a jogosult karbantartási szervezetek vagy személyek.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű karbantartási személyzet engedélyezési és kíséreti rendszerfolyamatok elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-5b"></a>A NIST 800-53 vezérlő MA-5.b

#### <a name="maintenance-personnel"></a>Karbantartási csoporthoz

**MA-5.b** a szervezet biztosítja, hogy nem kísért személyzet karbantartás elvégzése az információk rendszeren szükséges hozzáférési engedélyeket.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű karbantartási személyzet engedélyezési és kíséreti rendszerfolyamatok elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-5c"></a>A NIST 800-53 vezérlő MA-5.c

#### <a name="maintenance-personnel"></a>Karbantartási csoporthoz

**MA-5.c** a szervezet meg, míg a hozzáférési engedélyek és műszaki hatáskörét felügyeli karbantartási személyzet, akik nem rendelkeznek a hozzáférési engedélyek szervezeti csoporthoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű karbantartási személyzet engedélyezési és kíséreti rendszerfolyamatok elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-5-1a"></a>A NIST 800-53 vezérlő MA-5 (1) sémaszintjén

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Karbantartási személyzet |} Egyéni felhasználók számára megfelelő hozzáférési nélkül

**MA-5 (1) sémaszintjén** a szervezete bevezet, nem rendelkezik megfelelő biztonsági engedély, vagy amelyek nem USA építését, amelyek tartalmazzák a következő követelmények karbantartási személyzet nem rendelkezik a szükséges karbantartás személyzet alkalmazását eljárásai hozzáférési engedélyek, távolság, vagy formális hozzáférés jóváhagyások kísért és felügyeli, karbantartási és diagnosztikai tevékenység az információs rendszer alatt a jóváhagyott szervezeti személyzet teljesen, rendelkeznek-e megfelelő hozzáférési engedélyek, és amelyeket technikailag minősített; kezdeményező karbantartás vagy a személyzet diagnosztikai tevékenységek számára tegye nem rendelkezik a szükséges hozzáférési engedélyek, távolság vagy formális hozzáférés jóváhagyások, minden "volatile" adatokat tárolási összetevőinek információk rendszerből vannak fertőtlenítsék és az összes nem felejtő tárolási adathordozókon eltávolítja vagy fizikailag bontotta a kapcsolatot a rendszer és védett.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű karbantartási személyzet engedélyezési és kíséreti rendszerfolyamatok elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ma-5-1b"></a>A NIST 800-53 vezérlő MA-5 (1) .b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Karbantartási személyzet |} Egyéni felhasználók számára megfelelő hozzáférési nélkül

**MA-5 (1) .b** a szervezet házon belül fejlesztett alkalmazásokra és valósít meg alternatív biztonsági óvintézkedéseket, abban az esetben egy információk rendszerösszetevő nem lehet fertőtlenítsék, törölve vagy bontotta a kapcsolatot a rendszer.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél vállalati szintű karbantartási személyzet engedélyezési és kíséreti rendszerfolyamatok elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ma-6"></a>A NIST 800-53 vezérlő MA-6

#### <a name="timely-maintenance"></a>Időben karbantartás

**MA-6** a szervezet beszerzi karbantartási a támogatási és/vagy tartalék részek [hozzárendelés: szervezet által megadott információk rendszerösszetevők] belül [hozzárendelés: szervezet által meghatározott időszak] hiba.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure adatközpontjaiban rezidens karbantartási csoporthoz, és támogatja az infrastruktúra-rendszereknek kritikus datacenter, valamint az Adatközpont működési karbantartása. A csoportok azonosította a kritikus fontosságú biztonsági és informatikai rendszerösszetevők amelyek megőriznek megkíméli a helyszínen. Kritikus rendszerek N + 1 konfigurációban, és a szolgáltatást tervezték, hogy rugalmasak lehetnek. Ez lehetővé teszi a datacenter vezetőség helyreállítási céljai szolgáltatáskiesést vagy feltételes terv helyzet. Fontos információk rendszerszolgáltatások törlődnek az egynél több adatközpont az egyik az adatközpontokban egy esemény miatt a szolgáltatás megszakadásának megelőzése érdekében. Alkalmazások vevői biztosít redundanciát és a rugalmasság több adatközpontokhoz telepítését végzik. |
