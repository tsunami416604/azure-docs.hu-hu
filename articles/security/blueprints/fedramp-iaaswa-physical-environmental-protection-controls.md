---
title: "Az Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - fizikai és környezeti védelmi"
description: "FedRAMP webes alkalmazások Automation - fizikai és környezeti védelmi"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Fizikai és környezeti védelmi (PE)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-pe-1"></a>A NIST 800-53 vezérlő PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Fizikai és környezeti védelmi házirend- és eljárások

**PE-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy fizikai és környezeti védelmi házirendje, amely a célja, a hatókör, a szerepkörök, a feladatkörei, a felügyeleti megoldást előfizetési, koordinálásának szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a fizikai és környezeti védelmi házirendet és a kapcsolódó fizikai és környezeti védelmi vezérlők; végrehajtásának megkönnyítése ellenőrzi, és frissíti az aktuális fizikai és környezeti védelmi házirend [hozzárendelés: szervezet által meghatározott gyakorisága]; és a fizikai és környezeti védelmi eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű fizikai és környezeti védelmi házirendet és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pe-2a"></a>A NIST 800-53 vezérlő PE-2.a

#### <a name="physical-access-authorizations"></a>Fizikai hozzáférési engedélyek

**PE-2.a** a szervezet házon belül fejlesztett alkalmazásokra, jóvá és a létesítmény, ahol az adatokat a található engedélyezett hozzáféréssel rendelkező személyek listáját.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Egy Microsoft adatközpontba való fizikai hozzáférés jóvá kell hagynia az Adatközpont-felügyeleti (DCM) csapata a datacenter hozzáférés eszközzel. Hozzáférés-hozzárendelések szükséges befejező dátumát, ezt követően a hozzáférés automatikusan törlődik, és jóvá kell hagyni. Továbbá, ha hozzáférést már nincs szükség, datacenter biztonsági tisztviselő vagy felügyeleti manuálisan kéri a hozzáférés megszűnését. |


 ## <a name="nist-800-53-control-pe-2b"></a>A NIST 800-53 vezérlő PE-2.b

#### <a name="physical-access-authorizations"></a>Fizikai hozzáférési engedélyek

**PE-2.b** a szervezet állít ki engedélyezési létesítmény hozzáférési hitelesítő adatait.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A datacenter-elérés eszközt egy a mérvadó hitelesített hozzáférést egy adott adatközpont személyzetnek listázása. Az eszköz kapcsolódik az eszközök az Adatközpont fizikai biztonsági hozzáférést, és engedélyezi a hozzáférést a DCM-csapat által engedélyezett hozzáférési szintek alapján. A hozzáférési szintek vagy egy felhasználó Microsoft jelvényt vagy egy ideiglenes hozzáférést jelvény, amely hozzá van rendelve az adatközpontjában, a vezérlő hely felügyelő (CRS) által kiállított az eszköz van hozzárendelve. A hozzáférési szintek a DCM-csapat jóváhagyása. Mellett a hitelesítő adatokat fizikai jelvények rendelt az Adatközpont bizonyos területeken regisztrálásához, a felhasználó biometrikai adatokon alapuló (az aktuális geometriai vagy az ujjlenyomat) szükséges. |


 ## <a name="nist-800-53-control-pe-2c"></a>A NIST 800-53 vezérlő PE-2.c

#### <a name="physical-access-authorizations"></a>Fizikai hozzáférési engedélyek

**PE-2.c** a szervezet ellenőrzi, hogy a hozzáférési lista, és részletesen leírja az egyéni felhasználók által engedélyezett létesítmény hozzáférési [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A leírt hozzáférésének visszavonása mellett a Microsoft Azure részében értékelést engedélyezett hozzáférés listák az Azure adatközpontjaiban 90 naponta ahhoz, hogy távolítsa el vagy frissítéséhez szükség szerint egyéni hozzáférési. |


 ## <a name="nist-800-53-control-pe-2d"></a>A NIST 800-53 vezérlő PE-2.d

#### <a name="physical-access-authorizations"></a>Fizikai hozzáférési engedélyek

**PE-2.d** a szervezet távolít el egyéni felhasználók számára a létesítmény hozzáférési amikor hozzáférési már nincs szükség.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure automatikusan letiltja a hozzáférést a hozzáférés hozzárendelése záró dátum elérésekor. Hozzáférés már nincs szükség, amikor datacenter biztonsági tisztviselő vagy felügyeleti manuálisan kérni fogja a datacenter-elérés eszközt a hozzáférés megszűnését. Emellett a Microsoft Azure eltávolítja az felesleges hozzáférési engedélyek miatt a lista áttekintése c. részében leírt felderített. |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 Control PE-3.a

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.a** a szervezet érvénybe lépteti a fizikai hozzáférési engedélyek [hozzárendelés: szervezet által meghatározott kilépést mutat a létesítmény az információs rendszert tartalmazó] egyedi hozzáférési engedélyek megadása előtt ellenőrzésével a hozzáférést a létesítmény; belépés és kilépés a létesítmény történő vezérlő [kijelölés (egy vagy több): [hozzárendelés: fizikai hozzáférés szervezet által meghatározott rendszerek/eszközök]; megóvja].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure megköveteli a fizikai hozzáférési engedélyek az összes fizikai hozzáférés pont 24 x 7 személyzeti használata Azure adatközpontjaiban, riasztások, figyelőrendszer, többtényezős hitelesítést és man-trap portálon az eszközökön. |


 ## <a name="nist-800-53-control-pe-3b"></a>A NIST 800-53 vezérlő PE-3.b

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.b** a szervezet fizikai hozzáférés a naplókban talál tart fenn [hozzárendelés: szervezet által meghatározott kilépést pontok].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Azure-adatközpontban létesítményekben való összes hozzáférések naplózza, és naplóz. |


 ## <a name="nist-800-53-control-pe-3c"></a>A NIST 800-53 vezérlő PE-3

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3** biztosít a szervezet [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] belül a létesítmény hivatalosan kijelölt nyilvánosan elérhető való hozzáférés szabályozása.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Azure adatközpontjaiban területek, amely egy nyilvánosan elérhető, nem tartalmaznak. |


 ## <a name="nist-800-53-control-pe-3d"></a>A NIST 800-53 vezérlő PE-3.d

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.d** a szervezet kísérő látogatók személyek és figyeli a látogatói tevékenység [hozzárendelés: szervezet által meghatározott körülmények között igénylő látogató kísérő személyek és figyelési].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Jóváhagyott a datacenter (lásd a PE-2) a hozzáférést a látogatói jelöli a rendszer "Kíséreti csak" a jelvényt vagy más visual köteg (pl. színes jelvény) keresztül, és azok kísérő személyek mindig továbbra is szükségesek. Kísérettel ellátott látogatók nem rendelkeznek a megadott hozzáférési szintet, és csak a saját kísérő személyek hozzáférését a is változatlan marad. Kísérő személyek a látogató az adatközpontban lévő összes tevékenységek figyelését. |


 ## <a name="nist-800-53-control-pe-3e"></a>A NIST 800-53 vezérlő PE-3.e

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.e** A szervezet biztosítja a kulcsokat, kombinációk és más fizikai hozzáférést biztosító eszközök.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Fizikai billentyűk és jelvények ideiglenes hozzáférést biztosított a biztonsági műveletek központ (SOC) belül. Biztonsági tisztviselő személyzettel 24 x 7. Kulcsok vannak ki van véve az adott személyek a személy hozzáférés jelvény fizikai kulcsának megfelelő. Kulcs készletek során minden egyes shift végzik, és a kulcsok használata nem engedélyezett a érdekében elvihetők a helyszínről. |


 ## <a name="nist-800-53-control-pe-3f"></a>A NIST 800-53 vezérlő PE-3.f

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.f** a szervezet készletek [hozzárendelés: fizikai hozzáférés szervezet által meghatározott eszközök] minden [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Azure adatközpontjaiban fizikai hozzáférést eszközéről a leltárba bevont legalább évente. Kulcsok és ideiglenes hozzáférést jelvények leltárba bevont naponta több alkalommal minden shift során. Hozzáférés jelvény olvasók és hasonló hozzáférést biztosító eszközök a fizikai biztonsági rendszer, ha folyamatosan képviselt állapot van csatolva. |


 ## <a name="nist-800-53-control-pe-3g"></a>A NIST 800-53 vezérlő PE-3.g

#### <a name="physical-access-control"></a>Fizikai hozzáférés-vezérlés

**PE-3.g** a szervezet kombinációk és kulcsok módosítása [hozzárendelés: szervezet által meghatározott gyakoriság] és/vagy a kulcsok elvesznek, ha kombinációk integritása sérül, vagy egyéni átvitt vagy leállt.  

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban van azokban az esetekben, amikor egy hozzáférési jelvény szükséges eljárásoknak kulcs elveszett vagy egy személy megszakadt, vagy át. A megszüntetés vagy átviteli a hozzáférést azonnal eltávolítják a rendszerből, és a hozzáférés jelvény eltávolítja. |


 ### <a name="nist-800-53-control-pe-3-1"></a>A NIST 800-53 vezérlő PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Fizikai hozzáférés-vezérlés |} Adatok rendszer elérése

**PE-3 (1)** a szervezet érvénybe lépteti a információk rendszer mellett a fizikai hozzáférés-vezérléssel: a létesítmény fizikai hozzáférési engedélyek [hozzárendelés: szervezet által meghatározott fizikai szóközt tartalmazó egy vagy több összetevője az információk rendszer].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | További Microsoft Azure korlátozza a Microsoft adatközpontjaiban elektronikus hozzáférés-vezérlés, a biometrikus eszközök, például különböző biztonsági mechanizmusokon keresztül kritikus rendszerek esetében (pl. colocations, kritikus környezetek, MDF helyiségekben stb.) tartalmazó területeket és kártevőirtó-passback szabályozza. Az Azure colocations való hozzáférés, a különálló, magasabb szintű DCAT hozzáférést, mint más hozzáférési területek az Adatközpont. Emellett minden Azure FTE és szállítók, akik hozzáférhetnek a Azure Government colocations kell hivatalosan kell alávetni, a Microsoft Cloud szűrési és USA lehetőségekkel ellenőrzési, mielőtt a környezet elérésére jogosult. A felhő-szűrés az az Azure Government colocations kapcsolatos további részletekért lásd a PS-03 részét. |


 ## <a name="nist-800-53-control-pe-4"></a>A NIST 800-53 vezérlő PE-4

#### <a name="access-control-for-transmission-medium"></a>Átviteli eszközön hozzáférés-vezérlés

**PE-4** a szervezet vezérel való fizikai hozzáférés [hozzárendelés: szervezet által megadott információk rendszer terjesztési és átviteli sorok] belül szervezeti létesítményekben használatával [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] .

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure van megvalósítva a hozzáférés-vezérlés átviteli eszközön keresztül a tervezési és a fő terjesztési keret (MDF) kiszolgálótermek és colocations épület információk rendszer terjesztési és átviteli sorok véletlen sérülés elleni védelméhez megszakítás és fizikai illetéktelen módosítását. Hozzáférés MDF kiszolgálótermek és colocations szükség két többtényezős hitelesítés (hozzáférés jelvényt vagy biometrikus adatokat). Ez biztosítja, hogy hozzáférést korlátozni csak arra jogosult személyek (lásd a PE-2, PE-3). Az MDF belül átviteli vagy védi a véletlen kárt, szüneteltetése és fizikai illetéktelen nélküli távolítják, a zárolt rackszekrények, a célra vagy a kábel tálca révén. |


 ## <a name="nist-800-53-control-pe-5"></a>A NIST 800-53 vezérlő PE-5

#### <a name="access-control-for-output-devices"></a>Kimeneti eszközök hozzáférés-vezérlés

**PE-5** a szervezet adatokat kimeneti a Rendszereszközök segítségével akadályozni, hogy illetéktelen személyek a kimeneti fizikai elérését szabályozza.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban nem rendelkezik kimeneti eszköz (figyelők, nyomtatók, hangeszközök, stb.), Azure eszközök véglegesen csatlakozik, vagy Azure megosztott eszközök. Biztonsági tisztviselő mellett nem rendelkezik kimeneti eszközök, hajtsa végre a fizikai forgatókönyvek a létesítmény többször / shift például zárolva ajtók és titkosítását állványok ellenőrzése. Datacenter hozzáférés személyek hozzáférési engedélyek jóváhagyni korlátozódik. Colocations szükséges kéttényezős hitelesítéssel (hozzáférés jelvényt vagy biometrikus adatokat) ahhoz, hogy hozzáférjenek a kettőt. |


 ## <a name="nist-800-53-control-pe-6a"></a>A NIST 800-53 vezérlő PE-6.a

#### <a name="monitoring-physical-access"></a>Fizikai hozzáférés figyelése

**PE-6.a** a szervezet a létesítmény, ahol az adatokat a található észlelése és a fizikai biztonsági incidensekre való fizikai hozzáférés figyelése.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Fizikai hozzáférés figyelt végrehajtási biztonsági eszközök és az adatközpontok folyamatait. Például 24 x 7 elektronikus figyelését hozzáférés-vezérlés, riasztás és videó rendszerek, valamint a hely biztonsági őrjáratok a létesítmény és okokból 24 x 7. A vezérlő hely felügyelő a SOC található mindenkor az Adatközpont fizikai hozzáférés figyelésére. |


 ## <a name="nist-800-53-control-pe-6b"></a>A NIST 800-53 vezérlő PE-6.b

#### <a name="monitoring-physical-access"></a>Fizikai hozzáférés figyelése

**PE-6.b** belépési naplók ellenőrzi, hogy a szervezet [hozzárendelés: szervezet által meghatározott gyakoriság] és a előfordulása [hozzárendelés: szervezet által meghatározott események vagy potenciális jelzések események].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Fizikai hozzáférés naplók folyamatosan felül, és későbbi kivizsgálási felülvizsgálat változatlan marad. |


 ## <a name="nist-800-53-control-pe-6c"></a>A NIST 800-53 vezérlő PE-6.c lépést

#### <a name="monitoring-physical-access"></a>Fizikai hozzáférés figyelése

**PE-6.c lépést** a szervezet koordinálja az ellenőrzések és a szervezeti incidensválasz-képességgel rendelkező vizsgálat eredményeit. 

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Az adatközponton belül előforduló biztonsági események által a biztonsági csoport leírása. A biztonsági csoport jelentést készít, az esemény bekövetkezte után biztonsági esemény részletes adatait rögzíti. <br /> Kormányzati bejelentési incidensek a Microsoft Azure biztonsági csapatának összehangolják a súlyos alkalmazás-szolgáltatóval (például Office 365) értesíteni a kormányzati ügynökség ügyfél Velünk CERT és FedRAMP belül USA-tanúsítvány-irányelvek (lásd az IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>A NIST 800-53 vezérlő PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Fizikai hozzáférés figyelése |} Behatolás riasztások / felügyeleti berendezések

**(1) PE-6** a szervezet fizikai behatolás riasztásokat és felügyeleti berendezések figyeli.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A 24 x 7 helyszínen biztonsági mellett Microsoft Azure adatközpontjaiban (bérelt és a teljes körűen felügyelt) is igénybe vehetik a riasztó rendszerek és zárt láncú TV figyelése. Riasztások folyamatosan figyelemmel kísért és válaszol a vezérlő hely felügyelő állomásozó a szociális 24 x 7 Egy válasz helyzet során a vezérlő hely felügyelő használja az incidens ahhoz, hogy megkapja a válaszadó vizsgált területén kamerák valós idejű információkat. |


 ### <a name="nist-800-53-control-pe-6-4"></a>A NIST 800-53 vezérlő PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Fizikai hozzáférés figyelése |} Információs rendszerekkel való fizikai hozzáférés figyelése

**PE-6 (4)** a szervezet figyeli a fizikai hozzáférés információk mellett a létesítmény, mint a fizikai hozzáférés figyelése [hozzárendelés: az információ egy vagy több összetevőt tartalmazó szervezet által meghatározott fizikai szóközöket a rendszer].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure a létesítmények, valamint az adatközpontokban belül az információs rendszerekkel való fizikai hozzáférés figyelése. Az összes Microsoft online services' berendezések adatközpontok helyeinek, ahol a rendszer figyeli a fizikai hozzáférés kerül. Az összes olyan közös elhelyezés MDF helyiségekben védettek, hozzáférés-vezérlés, a riasztások és a videó. |


 ## <a name="nist-800-53-control-pe-8a"></a>A NIST 800-53 vezérlő PE-8.a

#### <a name="visitor-access-records"></a>Visitor Access Records

**PE-8.a** a szervezet kezeli a létesítmény, ahol a információ található a látogatói access rekordok [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A datacenter-elérés eszközt jóváhagyott kérelmek formájában Datacenter access rekordok karbantartása. A PE-3 leírtak látogatók szükségesek mindig kapcsolódniuk kell kísérni. A kíséreti hozzáférés az adatközponton belül a rendszer naplózza, és ha szükséges a látogató adott területre a jövőbeli felülvizsgálatra is összefüggő. |


 ## <a name="nist-800-53-control-pe-8b"></a>A NIST 800-53 vezérlő PE-8.b

#### <a name="visitor-access-records"></a>Visitor Access Records

**PE-8.b** látogató access rekordok ellenőrzi, hogy a szervezet [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Engedélyezett hozzáférési kérelem a látogatók fog rendelkezni az azonosító kiadott kormányzati űrlap ellenőrizve a azonosításuk időpontjában áttekintette rekordot, vagy a Microsoft kiadott jelvény. PE-3 leírtak látogatók mindig az adatközpontban, amelyek kísért. |


 ### <a name="nist-800-53-control-pe-8-1"></a>A NIST 800-53 vezérlő PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Látogató Access rekordok |} Automatikus rekordok karbantartási / áttekintése

**PE-8 (1)** a szervezet funkcióit használja az automatikus mechanizmusok lehetővé teszi a karbantartási és a látogató access rekordok át kell tekinteni.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure datacenter hozzáférés rekordok jóváhagyott DCAT kérelmek formájában DCAT tart fenn. DCAT kérelmek csak a DCM-csapat jóvá. Hozzáférési szintek az adatközponton belül társítva, vagy felügyelt DCAT belül. Datacenter hozzáférés felül negyedévente vizsgálni. Azure adatközpontjaiban hozzáférésének DCAT rögzített, és későbbi esetleges vizsgálatok érhető el. Kell kísért mindig a látogatók szükségesek. Az adatközponton belül a kíséreti hozzáférés a felügyeleti rendszer, és ha szükséges a látogató adott területre a jövőbeli felülvizsgálatra is összefüggő riasztás belül kerül. Látogató hozzáférés áttekintik folyamatosan a hozzárendelt kíséreti és a vezérlő hely felügyelő zárt láncú TV és a riasztás megfigyelési rendszere keresztül. Látogatók nem kapnak hozzáférést, és kell adni a bájtméretet a kísérő személyek mindig. |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 Control PE-9

#### <a name="power-equipment-and-cabling"></a>Energiagazdálkodási berendezések és kábelezése

**PE-9** a szervezet power berendezések és az információs rendszer kárt és megsemmisítése kábelek power védi.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure biztosít védelmi szóközöket és a megfelelő címkézés kábelek. A Microsoft Azure infrastruktúra berendezések, például a kábelek, a elektromos sorok és a biztonsági mentési generátorokat környezetben, amely rendelkezik az adatokkal való visszaéléseket, tűz, robbanóanyagok, füst, vízjel, port, környezeti kockázatok elleni védelemre van fejthetők vissza kell elhelyezni. vibráció, földrengés, káros chemicals, elektromos zavaró tényező, áramkimaradások, elektromos zavarok (igényeiben jelentkező). Minden hordozható online services-eszközök (például rackszekrények, kiszolgálók, hálózati eszközök) zárolva vagy annak ellopása vagy adatátviteli sérülés elleni védelem érdekében, hogy a hely rögzíteni. Minden Microsoft Azure-környezeten belül Power és információk rendszer kábelek feliratú megfelelően, és természeti katasztrófa vagy hozzáférés elleni védelmét. Energiagazdálkodási és információk rendszer kábelek zavaró tényező elkerülése érdekében egy környezeten belül minden ponton elkülönülnek egymástól. |


 ## <a name="nist-800-53-control-pe-10a"></a>A NIST 800-53 vezérlő PE-10.a

#### <a name="emergency-shutoff"></a>Vészhelyzeti kikapcsolása

**PE-10.a** a szervezet lehetővé teszi az összetevők vészhelyzetben a rendszer vagy az egyes rendszer, az energiagazdálkodási kikapcsolása.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure sürgős Power ki (EPO) gombok telepített helyeken helyi tűz kód által megkövetelt az adatközponton belül. Az egyes felügyelt Microsoft Azure adatközpontjaiban a datacenter tervezési EPO gombok már nem szükséges. |


 ## <a name="nist-800-53-control-pe-10b"></a>A NIST 800-53 vezérlő PE-10.b

#### <a name="emergency-shutoff"></a>Vészhelyzeti kikapcsolása

**PE-10.b** a szervezet helyezi vészhelyzeti gyors kapcsolók, illetve eszközcsoportjához [hozzárendelés: szervezet által meghatározott helyre információk rendszer vagy rendszerösszetevő] személyzetet biztonságosan és könnyen megkönnyítése érdekében.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. EPO gombok stratégiailag kerülnek vészhelyzetben aktiválás engedélyezéséhez. EPO gombok elhelyezhetők a colocations, felügyelt létesítményekben művelet adatközpontok (FOCs), vagy jogi kötelezettség helyi tűz kódot. |


 ## <a name="nist-800-53-control-pe-10c"></a>A NIST 800-53 vezérlő PE-10.c

#### <a name="emergency-shutoff"></a>Vészhelyzeti kikapcsolása

**PE-10.c** a szervezet vészhelyzeti energiagazdálkodási gyors képesség megakadályozza a jogosulatlan aktiválás.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Véletlen aktiválási megelőzése érdekében EPO gombok valószínűleg egy védelmi ház, kettős aktiválni kell, vagy hallható figyelmeztető használata előtt aktiválás figyelmeztetés. Emellett EPO gombok videó felügyelet alatt áll. |


 ## <a name="nist-800-53-control-pe-11"></a>A NIST 800-53 vezérlő PE-11

#### <a name="emergency-power"></a>Vészhelyzeti Power

**PE-11** a szervezet nyújt a rövid távú szünetmentes áramforrás megkönnyítése érdekében [kijelölés (egy vagy több): a információs rendszer szabályosan leállítja; átmenet hosszú távú alternatív energiagazdálkodási adatokat rendszerrel], egy forrás az elsődleges áramellátás megszakadása.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Microsoft Azure biztonsági javítást alkalmazott, datacenter berendezések védelmével, és kapcsolatok, amely egy szünetmentes rendelkezik biztosító power biztosításához, amíg a generátorokat nem képes online állapotba a rövid távú tápegység (UPS) rendszer adja meg. |


 ### <a name="nist-800-53-control-pe-11-1"></a>A NIST 800-53 vezérlő PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Vészhelyzeti Power |} Hosszú távú alternatív tápegység - minimális üzemeltetési kapacitása

**PE-11 (1)** a információs rendszer, amely képes a karbantartásának minimálisan szükséges az elsődleges áramellátás forrás egy kiterjesztett adatvesztés üzemeltetési kapacitása kínál a szervezet egy hosszú távú alternatív tápegység.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure javítást alkalmazott, amely képes a karbantartása minimális működési funkció, ha az elsődleges áramellátás forrás egy kiterjesztett adatvesztés történik az információk rendszerhez egy hosszú távú alternatív tápegység. Energiagazdálkodási meghibásodik, vagy elutasítja azokat az elfogadhatatlan feszültség szintre, szünetmentes tápegység (UPS) rendszerek azonnal indítsa, és vegye át az energiagazdálkodási betöltése. A kiszolgálók működését, amíg a generátorokat át tudja venni elég power biztosít. Vészhelyzeti generátorokat meg biztonsági másolatát a power, a hosszabb kimaradások és a tervezett karbantartáshoz, és az Adatközpont helyszíni üzemanyag tartalék természeti katasztrófa működhet. Azure dízel generátor: adatközpontjainkba számos tart fenn. Biztonsági mentési generátorokat használt rács stabilitásának fenntartásához szükséges vagy a rendkívüli javítási és karbantartási helyzetekben szükséges, hogy ki a power rács adatközpontjainkba igénybe. |


 ## <a name="nist-800-53-control-pe-12"></a>A NIST 800-53 vezérlő PE-12

#### <a name="emergency-lighting"></a>Vészhelyzeti megvilágítási

**PE-12** a szervezet alkalmazza, és automatikus vészhelyzeti megvilágítási számára az információs rendszer, amely aktiválja áramkimaradás vagy megszakítása és, amely lefedi vészhelyzeti kilép, és a létesítmény belül kiürítési útvonalak tart fenn.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban (bérelt és a teljes körűen felügyelt) vészhelyzeti megvilágítási terhelés vészhelyzeti megvilágítási dedikált Kapcsolatcsoportok a biztonsági mentés UPS és készítő rendszerek (lásd a PE-11) formájában valósítja meg. Automatikus vészhelyzeti megvilágítási útvonalon minden fürttag áttelepítéséhez, vészhelyzeti kilép, és a nemzeti tűz és védelmi társítás (NFPA) élettartama biztonsági kódot megfelelően colocations belül van megvalósítva. Abban az esetben, ha segédprogram áramkimaradás történik, a sürgős megvilágítási automatikusan a UPS és készítő rendszerek által biztosított power vált. A Microsoft Azure adatközpontjaiban belül vészhelyzeti megvilágítási rendszerek számára, hogy azok maradtak megfelelő sorrendben ütemezett karbantartás változni. |


 ## <a name="nist-800-53-control-pe-13"></a>A NIST 800-53 vezérlő PE-13

#### <a name="fire-protection"></a>Tűz védelme

**PE-13** a szervezet alkalmazza, és tart fenn a tűz tiltási és észlelési eszközök/információk rendszer által támogatott rendszerek független energia forrás.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure tűz és tűz tiltási rendszerek, a Microsoft Azure adatközpontjaiban telepítése tűz védelmi van megvalósítva. <br /> A Microsoft Azure adatközpontjaiban robusztus tűz észlelési mechanizmusok megvalósítása. A Microsoft Azure tűz védelmi módszert fotoelektromos füstérzékelők telepítve, a emelet alatt pedig a felső határ az tűz védelmi vízpermettel rendszerrel integrálva vannak, amelyek magukban foglalják. Emellett nincsenek Xtralis VESDA (nagyon korai ellenőrző észlelési készülék) rendszereket minden közös elhelyezést a figyelheti a vezeték nélkül regisztrálja. VESDA egységek azok nagyon érzékeny vezeték nélkül mintavételi rendszerek szóközöket nagy értékű teljes telepítése. Mielőtt egy tényleges tűz-észlelési riasztás kivizsgálási választ VESDA egységek teszik lehetővé. <br /> "Pull állomás" tűz riasztás mezőkbe az adatközpontok manuális tűz riasztás-értesítési teljes vannak telepítve. Tűz készülékek teljes az adatközpontokban elhelyezett megfelelően megvizsgálni, szervizelt és címkézett évente. A biztonsági személyzet őrjáratok alkalmazásával az összes szolgáló helyiségekbe többször minden shift. Datacenter személyzet hajtsa végre a napi hely segédlet biztosítása összes tűz figyelési követelmények teljesülnek. <br /> Érzékeny elektromos berendezések tartalmazó területek (colocations, MDFs, stb.) dupla reteszelő előtti művelet (száraz cső) sprinklerberendezések védi. Száraz cső sprinklerek egy két szakaszból álló előtti művelet rendszer, mind a vízpermettel központi aktiválási (miatt tűz), valamint FÜSTÉRZÉKELŐ vízjel kibocsátási igénylő. A vízpermettel központi aktiválási a csövek a vezeték nélkül nyomás, amely lehetővé teszi, hogy a pipe-ok használatával kitölthet vízjel kiadását. Vízjel aktiválásakor füst- vagy érzékelő is megjelenik. <br /> A datacenter UPS és rendszerek biztosítva a redundáns áramforrásról vezetékes tűz észlelési/tiltási és vészhelyzeti megvilágítási rendszerek. |


 ### <a name="nist-800-53-control-pe-13-1"></a>A NIST 800-53 vezérlő PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Védelmi érvényesítést |} Észlelési eszközök / rendszerek

**PE-13 (1)** eszközök/rendszerek az adatokat a rendszer automatikusan aktiválja, és értesíti a szervezet védett érvényesítést [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] és [hozzárendelés: szervezet által meghatározott sürgős válaszadók] tűz.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure alkalmazza érvényesítést észlelési eszközök/rendszerek az adatokat a rendszer automatikusan aktiválja, és értesíti a datacenter személyzet tűz vészhelyzeti válaszadók együtt. Abban az esetben, ha a tűz észlelési módszerek egyikével aktiválva van a közös elhelyezést, a helyi tűz részleg automatikus értesítést kap a tűz riasztó rendszerek. Emellett a tűz védelmét és tűz rendszerek vannak társítva, amely értesíti a helyi létesítményt a személyes és a biztonsági személyzet biztonsági rendszerbe. |


 ### <a name="nist-800-53-control-pe-13-2"></a>A NIST 800-53 vezérlő PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Védelmi érvényesítést |} Tiltási eszközök / rendszerek

**PE-13 (2)** tiltási eszközök/rendszerek bármely az aktiválás automatikus értesítést küldenek az információk rendszerhez a szervezet védett érvényesítést [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] és [hozzárendelése: szervezet által meghatározott vészhelyzeti válaszadók].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Abban az esetben, ha a tűz tiltási rendszert az adatközpontjában, aktív, a helyi tűz részleg automatikus értesítést kap a tűz riasztó rendszerek. Emellett a tűz védelmét és tűz rendszerek vannak társítva, amely értesíti a helyi létesítményt a személyes és a biztonsági személyzet biztonsági rendszerbe. |


 ### <a name="nist-800-53-control-pe-13-3"></a>A NIST 800-53 vezérlő PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Védelmi érvényesítést |} Automatikus tűz tiltási

**(3) PE-13** a szervezet funkcióit használja az automatikus tűz tiltási magasabb a információs rendszer, ha a létesítmény nem személyzettel folyamatosan.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban személyzettel 24 x 7. Tűz tiltási rendszerek automatikusan megszólítása beavatkozás nélkül, egy riasztás tűz helyzet észlelése esetén. |


 ## <a name="nist-800-53-control-pe-14a"></a>A NIST 800-53 vezérlő PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Hőmérséklet és a páratartalom vezérlők

**PE-14.a** a szervezeten belül a létesítmény, ahol a információ található a hőmérséklet és a páratartalom szintek tart fenn [hozzárendelés: elfogadható szintű szervezet által meghatározott].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. Microsoft Azure a hőmérséklet és a páratartalom szintek American társadalom a melegítés, Refrigerating és légkondicionáló mérnökök (ASHRAE) irányelvek megfelelően kezeli. A hőmérséklet és a páratartalom szintek folyamatosan figyeli a datacenter épület felügyeleti rendszer (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>A NIST 800-53 vezérlő PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Hőmérséklet és a páratartalom vezérlők

**PE-14.b** a szervezet figyeli a hőmérséklet és a páratartalom szintek [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban hőmérséklet és a páratartalom szintek vannak folyamatosan figyelemmel kísérni az épület felügyeleti rendszer (BMS). Datacenter csoporthoz a BMS a a létesítmények műveletek Center (FOC), figyeléséhez, úgy, hogy kezelni tudja a hőmérséklet és a páratartalom az adatközponton belül előtt minden riasztás pontok számát. |


 ### <a name="nist-800-53-control-pe-14-2"></a>A NIST 800-53 vezérlő PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Hőmérséklet és a páratartalom vezérlők |} Figyelés a riasztások vagy értesítések

**(2) PE-14** a szervezet alkalmazta a hőmérséklet és a páratartalom figyelést, egy riasztás vagy potenciálisan káros változásairól szóló értesítések nyújt tanácsadási csoporthoz vagy a készülék.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure adatközpontjaiban hőmérséklet és a páratartalom szintek vannak folyamatosan figyelemmel kísérni az épület felügyeleti rendszer (BMS). Datacenter csoporthoz a BMS a a létesítmények műveletek Center (FOC), figyeléséhez, úgy, hogy kezelni tudja a hőmérséklet és a páratartalom az adatközponton belül előtt minden riasztás pontok számát. |


 ## <a name="nist-800-53-control-pe-15"></a>A NIST 800-53 vezérlő PE – 15

#### <a name="water-damage-protection"></a>Vízjel kárt védelme

**PE-15** a szervezet a információs rendszer megvédi a fő kikapcsolása vagy elkülönítési szelepek, amelyek a elérhető, akkor működik megfelelően, és ismert kulcsfontosságú személyzet megadásával vízjel kiszivárgásának károk.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure biztosít a vízjel/észlelését, fennáll a kockázata, vízjel kiszivárgásának (például a vezeték nélkül kezelők egységek) területen. Tűz tiltási rendszerek is memóriavesztés észlelési riasztásokat, amelyek figyelése. A vízjel/memóriavesztés rendszer integrálva van a létesítmény riasztás és értesítés a rendszer. Az adatközpontokban a sprinklerberendezések úgy kell zónákba. Datacenter személyzet jártas vészhelyzeti eljárások a vízjel gyors szelepek és a helyek használni. A vízpermettel risers állítsa le, egyénileg vagy egy csoporthoz gömbházas rendelkezésükre áll. A kritikus területen található összes sprinklerek dupla reteszelő előtti művelettípus sprinklerek, amelyek kétféle aktiválási folyamat kezdeményezett szükség. A terhelés a vízpermettel rendszer ellenőrzött és vízjel adatszivárgás elleni alarmed. |


 ### <a name="nist-800-53-control-pe-15-1"></a>A NIST 800-53 vezérlő PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Kárt védelmi víz |} Automatizálás támogatása

**(1) PE-15** a szervezet alkalmazza a rendszer és a riasztások közelében vízjel észleléséhez automatizált mechanizmusok [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure automatizált mechanizmusokat, vízjel jelenlétét észleli az adatközpontokban a védett, és riasztást küld a datacenter csoporthoz. Azure biztosít vízjel/észlelését, fennáll a kockázata, vízjel kiszivárgásának (például a vezeték nélkül kezelők egységek) területen. Tűz tiltási rendszerek is memóriavesztés észlelési riasztásokat, amelyek figyelése. A vízjel/memóriavesztés rendszer integrálva van a létesítmény riasztás és értesítés a rendszer. A terhelés a vízpermettel rendszer ellenőrzött és vízjel adatszivárgás elleni alarmed. |


 ## <a name="nist-800-53-control-pe-16"></a>A NIST 800-53 vezérlő PE-16

#### <a name="delivery-and-removal"></a>Kézbesítési és eltávolítása

**PE-16** a szervezet engedélyezi, figyeli, és szabályozza [hozzárendelés: szervezet által meghatározott típusú információk rendszerösszetevők] belépés és kilépés a létesítmény és tart fenn a rekordok azok az elemek.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure megvalósítja ezt a követelményt ügyfeleik nevében. A Microsoft Azure és az Adatközpont kilépési megengedett érték szigorú végrehajtásának valósítja meg. Az összes rendszer összetevő/eszköz az eszköz felügyeleti eszköz adatbázis követi. |


 ## <a name="nist-800-53-control-pe-17a"></a>A NIST 800-53 vezérlő PE-17.a

#### <a name="alternate-work-site"></a>Alternatív munkaterület

**PE-17.a** alkalmaz a szervezet [hozzárendelés: szervezet által meghatározott biztonsági vezérlők] munkahelyi másodlagos helyeken.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél másik munkahelyi vállalati szintű hely rendelkezések elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pe-17b"></a>A NIST 800-53 vezérlő PE-17.b

#### <a name="alternate-work-site"></a>Alternatív munkaterület

**PE-17.b** a szervezet értékelésére ez megvalósítható, biztonsági hatékonyságát másik munkahelyi helyek szabályozza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél másik munkahelyi vállalati szintű hely rendelkezések elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pe-17c"></a>A NIST 800-53 vezérlő PE-17.c

#### <a name="alternate-work-site"></a>Alternatív munkaterület

**PE-17.c** a szervezet lehetővé teszi az alkalmazottak információk biztonsági csoporthoz biztonsági incidensek és problémák esetén folytatott kommunikációhoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Lehet, hogy az ügyfél másik munkahelyi vállalati szintű hely rendelkezések elegendő ahhoz, hogy a vezérlő kezelje. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-pe-18"></a>A NIST 800-53 vezérlő PE 18.

#### <a name="location-of-information-system-components"></a>Információk rendszerösszetevők helye

**PE-18** a szervezet adatokat rendszerösszetevők belül a létesítmény minimalizálása érdekében a fellépő potenciális károknak pozíciók [hozzárendelés: szervezet által meghatározott fizikai és környezeti veszélyek] és minimalizálása érdekében a lehetőséget, a nem engedélyezett a hozzáférés.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | Azure stratégiai datacenter tervezett módszert alkalmaz, a rendszer összetevők vezérlő helyét kiszolgálásához valósítja meg. Az összes Microsoft online services' berendezések helyezkedik el, a helyeken, amely rendelkezik az adatokkal való visszaéléseket, tűz, robbanóanyagok, füst, vízjel, port, vibráció, földrengés, káros chemicals, elektromos zavarok környezeti kockázatok elleni védelemre van fejthetők vissza áramkimaradások elektromos zavarok (igényeiben jelentkező) és sugárzási. A létesítmény és infrastruktúra megvalósítását szeizmikus merevítés beépítése környezeti veszélyek elleni védelem. Az összes olyan közös elhelyezés MDF helyiségekben védettek, hozzáférés-vezérlés, a riasztások és a videó. A konstrukció is kamerafigyelő biztonsági tisztviselő 24 x 7 által. Összes hordozható Azure eszköz zárolva van, vagy annak ellopása vagy adatátviteli sérülés elleni védelem érdekében, hogy a hely rögzíteni. |


