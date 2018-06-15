---
title: Az Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - konfiguráció kezelése
description: FedRAMP webes alkalmazások Automation - konfiguráció kezelése
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206989"
---
# <a name="configuration-management-cm"></a>Konfigurációkezelés (CM)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-cm-1"></a>A NIST 800-53 vezérlő CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Konfigurációs felügyeleti házirend és eljárások

**CM-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy konfiguráció-kezelési házirenddel, amely célú, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás, a koordinációs szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a konfigurációs felügyeleti szabályzatot, és a társított konfigurációs felügyeleti vezérlők; végrehajtásának megkönnyítése ellenőrzi, és frissíti a jelenlegi konfiguráció-kezelési házirend [hozzárendelés: szervezet által meghatározott gyakoriság]; és felügyeleti eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű konfiguráció-kezelési házirend és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-2"></a>A NIST 800-53 vezérlő CM-2

#### <a name="baseline-configuration"></a>Alapvető konfigurációhoz

**CM-2** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumok és konfigurációs-szabályozás a információs rendszer aktuális alapkonfiguráció tart fenn.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Resource Manager-sablonok és a tervezetének alkotó kísérő erőforrások képviselő "kódú" alapkonfiguráció telepített architektúrájának. A megoldás azonban használhat konfiguráció-ellenőrzés a github webhelyen valósul meg. A megoldás a központilag telepített virtuális gépekhez szükséges konfiguráló (DSC) alapkonfigurációt tartalmaz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-1a"></a>A NIST 800-53 vezérlő CM-2 (1) sémaszintjén

#### <a name="baseline-configuration--reviews-and-updates"></a>Alapkonfigurációjának |} Ellenőrzések és frissítések

**CM-2 (1) sémaszintjén** a szervezet ellenőrzi, és frissíti az adatokat a rendszer az alapvető konfigurációhoz [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős felülvizsgálata és frissítése az alapvető konfigurációhoz, a telepített ügyfél-erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-1b"></a>A NIST 800-53 vezérlő CM-2 (1) .b

#### <a name="baseline-configuration--reviews-and-updates"></a>Alapkonfigurációjának |} Ellenőrzések és frissítések

**CM-2 (1) .b** a szervezet ellenőrzi, és frissíti az alapvető konfigurációhoz, az információs rendszer miatt [hozzárendelés szervezet által meghatározott körülmények között] szükség esetén.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős felülvizsgálata és frissítése az alapvető konfigurációhoz, az ügyfél telepített erőforrások szükség esetén. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-1c"></a>A NIST 800-53 vezérlő CM-2 (1) .c

#### <a name="baseline-configuration--reviews-and-updates"></a>Alapkonfigurációjának |} Ellenőrzések és frissítések

**CM-2 (1) .c** a szervezet ellenőrzi, és frissíti az adatokat a rendszer az alapvető konfigurációhoz információk rendszer összetevő telepítések és frissítések szerves része.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős felülvizsgálata és frissítése az alapvető konfigurációhoz, az ügyfél telepített erőforrások szükség esetén. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-2"></a>A NIST 800-53 vezérlő CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Alapkonfigurációjának |} Automatizálás támogatása pontosak / pénznem

**CM-2 (2)** a szervezet egy naprakész, befejeződött, a pontos és azonnal elérhetők legyenek alapkonfigurációjának a információs rendszer fenntartásához automatizált mechanizmusok funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Resource Manager-sablonok és a tervezetének alkotó kísérő erőforrások képviselő "kódú" alapkonfiguráció telepített architektúrájának. A megoldás azonban használhat konfiguráció-ellenőrzés a github webhelyen valósul meg. Az Azure portálon az automatizálási parancsfájl az összes telepített erőforrás érhető el, és az erőforrások mindig naprakész ábrázolását biztosítja.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-3"></a>A NIST 800-53 vezérlő CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Alapkonfigurációjának |} A fenti konfiguráció megőrzést

**CM-2 (3)** őrzi meg a szervezet [hozzárendelés: eredeti konfigurációk a információs rendszer korábbi verzióiban szervezet által meghatározott] visszaállítási támogatásához.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős megőrzése az ügyfél telepített erőforrások alapterv konfigurációi korábbi verzióiban. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-2-7a"></a>A NIST 800-53 CM-2 (7) sémaszintjén szabályozása

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Alapkonfigurációjának |} A magas kockázatú területek rendszerek, összetevők és eszközök konfigurálása

**CM-2 (7) sémaszintjén** a szervezet problémák [hozzárendelés: szervezet által meghatározott információs rendszerekkel, rendszerösszetevők vagy eszközök] rendelkező [hozzárendelés: szervezet által meghatározott konfigurációk] helyekre utazás egyének részére, amely a szervezet megfelelően konfiguráltnak ítéli a jelentős kockázat lennie.

**Feladatok:** `Not Applicable`

|||
|---|---|
| **Ügyfél** | Nincsenek ügyfél által felügyelt fizikai eszközök telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure felhasználói tartalma soha nem található a Microsoft Azure, amely fizikailag található, az Amerikai Egyesült Államokban szárazföldi kívül. A Microsoft Azure csoporthoz nem utaznak a Microsoft Azure-készletben lévő eszközökkel. A vezérlő így nem tulajdonság Microsoft Azure vonatkozik. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>A NIST 800-53 CM-2 (7) .b szabályozása

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Alapkonfigurációjának |} A magas kockázatú területek rendszerek, összetevők és eszközök konfigurálása

**CM-2 (7) .b** a szervezetek [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] az eszközökön, ha az egyéni felhasználók számára adja vissza.

**Feladatok:** `Not Applicable`

|||
|---|---|
| **Ügyfél** | Nincsenek ügyfél által felügyelt fizikai eszközök telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure-on kívüli soha nem tárolja a Microsoft Azure felhasználói tartalmat és Microsoft Azure csoporthoz nem utaznak a Microsoft Azure-készletben lévő eszközökkel, így a vezérlő nem alkalmazható. |


 ## <a name="nist-800-53-control-cm-3a"></a>A NIST 800-53 vezérlő CM-3.a

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.a** a szervezet a rendszerben bekövetkezett változások információkat, amelyek a konfigurációs által szabályozott típusú határozza meg.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél milyen változtatásokat ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között) konfigurációs által szabályozott meghatározása felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3b"></a>A NIST 800-53 vezérlő CM-3.b

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.b** a szervezet ellenőrzi, hogy az információk rendszer javasolt konfiguráció által szabályozott módosításait, és hagyja jóvá vagy fennmaradhat biztonsági hatás elemzések explicit szempont a változások.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős javasolt konfiguráció által szabályozott módosítások ügyfél telepített erőforrásokhoz való megtekintésével. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3c"></a>A NIST 800-53 vezérlő CM-3

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3** a szervezet dokumentumok konfigurációs változásokat az információk rendszerre döntéseket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ügyfél telepített erőforrások (lásd a CM-03.b) társított konfigurációs által szabályozott módosítások dokumentálásáért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3d"></a>A NIST 800-53 vezérlő CM-3.d

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.d** a szervezete bevezet az információs rendszer konfigurációs által szabályozott jóváhagyott változtatásokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős jóvá a CM-03.b konfigurációs által szabályozott módosítások végrehajtásához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3e"></a>A NIST 800-53 vezérlő CM-3.e

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.e** A szervezet megőrzi az információs rendszer konfigurációs által szabályozott módosításait rögzíti [hozzárendelés: szervezet által meghatározott időszak].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél megőrzése az ügyfél telepített erőforrások módosításai konfigurációs által szabályozott rekord felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3f"></a>A NIST 800-53 vezérlő CM-3.f

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.f** a szervezet naplózás, és ellenőrzi, hogy az információkat a system configuration által szabályozott módosításai társított tevékenységet.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a naplózás, és tekintse át a konfigurációs módosítások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-3g"></a>A NIST 800-53 vezérlő CM-3.g

#### <a name="configuration-change-control"></a>Konfigurációs Változáskezelésre

**CM-3.g** a szervezet koordinálja és felügyeletet biztosít a konfigurációs módosítás vezérlő tevékenységek [hozzárendelés: szervezet által meghatározott konfigurációs módosítás vezérlő elem (pl. Bizottság, board)], amely convenes [kijelölés () egy vagy több): [hozzárendelés: szervezet által meghatározott gyakoriság]; [Hozzárendelés: szervezet által meghatározott konfigurációs módosítás feltételek]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél koordinációs és és felügyeletet biztosít a konfigurációs módosítás vezérlő tevékenységek felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1a"></a>A NIST 800-53 vezérlő CM-3 (1) sémaszintjén

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) sémaszintjén** a szervezet az információs rendszer javasolt módosításait dokumentálása automatizált mechanizmusok funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél automatikus mechanizmusok dokumentálása (lásd a CM-03.b) változtatási alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1b"></a>A NIST 800-53 vezérlő CM-3 (1) .b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) .b** a szervezet védett értesíteni automatizált mechanizmusok [hozzárendelés: rendezve által definiált jóváhagyási hatóságok] az adatokat javasolt módosításait, rendszer és a kérelem módosítása jóváhagyást.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy olyan ügyfél telepített erőforrások javasolt módosításainak útvonal és a kérelem jóváhagyása automatikus mechanizmust alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1c"></a>A NIST 800-53 vezérlő CM-3 (1) .c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) .c** a szervezet funkcióit használja az automatizált mechanizmusokat, jelölje ki az információkat a rendszer, amely nem engedélyezett vagy letiltott által javasolt módosításait [hozzárendelés: szervezet által meghatározott időszak].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél át módosítás javaslatokat jelöljön ki egy olyan automatikus mechanizmust alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1d"></a>A NIST 800-53 vezérlő CM-3 (1) .d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) .d** a szervezet a adatokat a rendszer letiltja az csak a kijelölt jóváhagyások fogadásának automatizált mechanizmusok funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy olyan automatikus mechanizmust letiltja az ügyfél telepített erőforrások jóvá nem hagyott módosítások végrehajtása alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1e"></a>A NIST 800-53 vezérlő CM-3 (1) .e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) .e** A szervezet az adatokat a rendszer minden módosításának dokumentálása automatizált mechanizmusok funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy olyan automatikus mechanizmust ügyfél telepített erőforrások megvalósított módosításai dokumentum alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-1f"></a>A NIST 800-53 vezérlő CM-3 (1) .f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Konfigurációs Változáskezelésre |} A dokumentum automatikus vagy értesítés / változások tiltása

**CM-3 (1) .f** a szervezet védett értesíteni automatizált mechanizmusok [hozzárendelés: szervezet által meghatározott csoporthoz] Ha az információ rendszerhez jóváhagyott változtatások elvégzése.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy olyan automatikus mechanizmust adja meg értesítések, amikor az ügyfél telepített erőforrások jóváhagyott változtatások elvégzése alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-2"></a>A NIST 800-53 vezérlő CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Konfigurációs Változáskezelésre |} Teszt / ellenőrzése / módosítások dokumentálása

**CM-3 (2)** a szervezet teszteli, ellenőrzi, és a műveleti rendszerfiókok a módosítások végrehajtása előtt a információk rendszer dokumentumokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős tesztelése, érvényesítése és dokumentálása módosítások ügyfél telepített erőforrások végrehajtása előtt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-4"></a>A NIST 800-53 vezérlő CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Konfigurációs Változáskezelésre |} Biztonsági képviselője

**CM-3 (4)** a szervezet megköveteli az információk biztonsági képviselőjére tagja a [hozzárendelés: szervezet által meghatározott konfigurációs módosítás vezérlő elem].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hozzárendelése a CM-03.g meghatározott módosítás vezérlő elem tagja egy biztonsági információk képviselőjére felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-3-6"></a>A NIST 800-53 vezérlő CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Konfigurációs Változáskezelésre |} Titkosítás kezelése

**CM-3 (6)** a szervezet biztosítja, hogy titkosítási mechanizmus segítségével adja meg [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] kezelés alatt állnak.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata, hogy a titkosítási mechanizmus konfigurációs felügyelet alatt álljanak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-4"></a>A NIST 800-53 vezérlő CM-4

#### <a name="security-impact-analysis"></a>Biztonsági hatáselemzés

**CM-4** a szervezet elemzi a információk rendszer módosítás végrehajtása előtt lehetséges biztonsági hatások meghatározásához.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ügyfél telepített erőforrások javasolt módosításainak elemzése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-4-1"></a>A NIST 800-53 vezérlő CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Biztonsági hatáselemzés |} Külön tesztkörülmények között

**CM-4 (1)** a szervezet elemzi a működési környezetben, biztonsági hatások hibái, gyenge, alkalmazással való inkompatibilitás miatt, vagy szándékos keres a megvalósítás előtt külön tesztkörnyezetben információs rendszer módosításai malice.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ügyfél telepített erőforrások egy tesztkörnyezetben működési környezetben a megvalósítás előtt javasolt módosításainak elemzése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-5"></a>A NIST 800-53 vezérlő CM-5

#### <a name="access-restrictions-for-change"></a>Módosítsa a hozzáférés korlátozása

**CM-5** a szervezet határozza meg, dokumentumok, jóvá és érvénybe lépteti a információk rendszer társított fizikai és logikai hozzáférési korlátozásokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Active Directory-fiók jogosultságokat biztosító szigorú szerepkörök hozzárendelése a felhasználók a szerepköralapú hozzáférés-vezérlés használatával megvalósított szabályozhatja, hogy mely felhasználók keresztül tekintheti meg és vezérlés telepített erőforrásokhoz. Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni. Ezekből a biztonsági csoportokból szabályozza, a végrehajtandó műveleteket, a felhasználók is igénybe vehet, az operációs rendszer konfigurálása tekintetében. Ezek a szerepkör-alapú rendszerek bővíthető az ügyfél kritikus igényeinek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-5-1"></a>A NIST 800-53 vezérlő CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Hozzáférési korlátozásokat módosítása |} Hozzáférés kényszerítési automatikus / naplózási

**CM-5 (1)** az információs rendszer kikényszeríti a korlátozza a hozzáférést, és támogatja a kényszerítési műveletek naplózását.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Active Directory-fiók jogosultságokat biztosító szigorú szerepkörök hozzárendelése a felhasználók a szerepköralapú hozzáférés-vezérlés használatával megvalósított szabályozhatja, hogy mely felhasználók keresztül tekintheti meg és vezérlés telepített erőforrásokhoz. Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni. Ezekből a biztonsági csoportokból szabályozza, a végrehajtandó műveleteket, a felhasználók is igénybe vehet, az operációs rendszer konfigurálása tekintetében. Minden hozzáférések és hozzáférések naplóz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-5-2"></a>A NIST 800-53 vezérlő CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Hozzáférési korlátozásokat módosítása |} Tekintse át a rendszer módosítása

**CM-5 (2)** rendszer változtatások ellenőrzi, hogy a szervezet [hozzárendelés: szervezet által meghatározott gyakoriság] és [hozzárendelés: szervezet által meghatározott körülmények között] annak meghatározásához, hogy jogosulatlan változások történtek.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások annak meghatározásához, hogy jogosulatlan módosítások történtek módosítások megtekintésével. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-5-3"></a>A NIST 800-53 vezérlő CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Hozzáférési korlátozásokat módosítása |} Aláírt összetevőket

**CM-5 (3)** az adatokat a rendszer megakadályozza, hogy a telepítése [hozzárendelés: szervezet által meghatározott szoftver és a belső vezérlőprogram összetevők] ellenőrzése nélkül, hogy az összetevő rendelkezik digitálisan aláírt egy tanúsítvánnyal, a rendszer felismeri és a szervezet által jóváhagyott.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által központilag telepített virtuális gépek Windows, hogy mely felhasználók telepíteni, illetve bizonyos alkalmazások futtatásához adja meg az AppLocker valósítja meg. További, az összes Windows operációs rendszer frissítéseinek digitális singed vannak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-5-5a"></a>A NIST 800-53 CM-5 (5) sémaszintjén szabályozása

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Hozzáférési korlátozásokat módosítása |} Korlátozza a termelési / működési jogosultságokkal

**CM-5 (5) sémaszintjén** a szervezet korlátozza az információk rendszerösszetevők és rendszer-kapcsolatos információkat a termelési vagy a működési környezetben módosítása szükséges engedélyekkel.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős korlátozza az üzemi ügyfél telepített vagy a működési környezetben módosítások szükséges engedélyekkel. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-5-5b"></a>A NIST 800-53 CM-5 (5) .b szabályozása

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Hozzáférési korlátozásokat módosítása |} Korlátozza a termelési / működési jogosultságokkal

**CM-5 (5) .b** a szervezet ellenőrzi és jogosultságokkal reevaluates [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős áttekintését és reevaluating CM-05 (05) sémaszintjén meghatározott jogosultságokat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-6a"></a>A NIST 800-53 vezérlő CM-6.a

#### <a name="configuration-settings"></a>Konfigurációs beállítások

**CM-6.a** a szervezet hoz létre, és beállításokat az alkalmazásban lévő adatokat rendszer a informatikai termékek dokumentumok [hozzárendelés: szervezet által meghatározott biztonsági konfiguráció ellenőrzőlisták] tükrözik a szigorúbb mód működési követelményekkel összhangban.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepített virtuális gépekhez szükséges konfiguráló (DSC) alapterv tartalmazza. A deklaratív PowerShell-parancsfájlok határozza meg, és konfigurálja az erőforrásokat, amelyhez a rendszer alkalmazza azokat. Az alapkonfiguráció érhető el ez a megoldás által telepített erőforrások a DSC bővíthető az ügyfél kritikus igényeinek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-6b"></a>A NIST 800-53 vezérlő CM-6.b

#### <a name="configuration-settings"></a>Konfigurációs beállítások

**CM-6.b** a szervezete bevezet a konfigurációs beállításokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepített virtuális gépekhez szükséges konfiguráló (DSC) alapterv tartalmazza. Az alapterveket a virtuális gépek automatikusan alkalmazza a használatával egyéni parancsfájl virtuálisgép-bővítmény telepítése során. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-6c"></a>CM-6.c lépést NIST 800-53 vezérlő

#### <a name="configuration-settings"></a>Konfigurációs beállítások

**CM-6.c lépést** a szervezet azonosítja, dokumentumokat, és hagyja jóvá a meghatározott konfigurációs beállításainak történő eltérések [hozzárendelés: szervezet által megadott információk rendszerösszetevők] alapján [hozzárendelés: szervezet által meghatározott működési követelményeinek].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói azonosító, értelmezhet és jóváhagyása történő ügyfél telepített erőforrások létrehozott konfigurációs beállításai eltérések felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-6d"></a>A NIST 800-53 vezérlő CM-6.d

#### <a name="configuration-settings"></a>Konfigurációs beállítások

**CM-6.d** szervezeti házirendek és eljárások a konfigurációs beállításokat a szervezet figyelők és vezérlők változik.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az Automation DSC. Automation DSC gépek konfigurációit, egy adott szervezet által meghatározott konfigurációjával igazítása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-6-1"></a>A NIST 800-53 vezérlő CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Konfigurációs beállítások |} Automatikus központi felügyeleti / alkalmazás / ellenőrzése

**CM-6 (1)** a szervezet funkcióit használja az automatizált mechanizmusok központilag kezelheti, alkalmazza, és konfigurációs beállításainak ellenőrzése [hozzárendelés: szervezet által megadott információk rendszerösszetevők].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének Azure Automation DSC telepíti. Automation DSC gépek konfigurációit, egy adott szervezet által meghatározott konfigurációjával igazítása, és folyamatosan figyeli a változásokat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-6-2"></a>A NIST 800-53 vezérlő CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Konfigurációs beállítások |} Jogosulatlan változásait

**CM-6 (2)** alkalmaz a szervezet [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] válaszolni a nem hitelesített módosításai [hozzárendelés: szervezet által megadott konfigurációs beállítások].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének Azure Automation DSC telepíti. Automation DSC egy riasztást, vagy konfigurációs hibák észlelésekor orvoslásához konfigurálható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-7a"></a>A NIST 800-53 vezérlő CM-7.a

#### <a name="least-functionality"></a>Legalább funkció

**CM-7.a** a szervezetnél konfigurálva van a információs rendszer csak az alapvető képességeit.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrások rendeltetésszerű legalább funkcionalitással vannak konfigurálva. Szükségeskonfiguráció-konfiguráló (DSC) alapterv megtalálható telepített virtuális gépekhez. A deklaratív PowerShell-parancsfájlok határozza meg, és konfigurálja az erőforrásokat, amelyhez a rendszer alkalmazza azokat. Az alapkonfiguráció érhető el ez a megoldás által telepített erőforrások a DSC bővíthető további korlátozására a kritikus igényeihez az ügyfél. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-7b"></a>A NIST 800-53 vezérlő CM-7.b

#### <a name="least-functionality"></a>Legalább funkció

**CM-7.b** a szervezet nem engedélyezi vagy korlátozza a következő funkciók, portok, protokollok, illetve szolgáltatások: [hozzárendelés: szervezet tiltott vagy korlátozott függvényeket, a portok, protokollok, illetve a szolgáltatások].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének Azure Application Gateway és a hálózati biztonsági csoportokat, ha csak a szükséges portok és protokollok használatának korlátozása telepíti. Alkalmazásátjáró, a hálózati biztonsági csoportok és a virtuális gépek DSC alaptervek további konfigurálhatja a felhasználói függvények, portok, protokollok, szolgáltatások és funkcionalitással csak a szánt használatát. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-1a"></a>A NIST 800-53 vezérlő CM-7 (1) sémaszintjén

#### <a name="least-functionality--periodic-review"></a>Legalább funkció |} Időszakosan ellenőrizhetjük

**CM-7 (1) sémaszintjén** az adatokat a rendszer ellenőrzi, hogy a szervezet [hozzárendelés: szervezet által meghatározott gyakoriság] szükségtelen és/vagy a nem biztonságos funkciók, portok, protokollok és szolgáltatások azonosításához.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között) megtekintésével szükségtelen és/vagy a nem biztonságos funkciók, portok, protokollok és szolgáltatások azonosításához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-1b"></a>A NIST 800-53 vezérlő CM-7 (1) .b

#### <a name="least-functionality--periodic-review"></a>Legalább funkció |} Időszakosan ellenőrizhetjük

**CM-7 (1) .b** a szervezet letiltja [hozzárendelés: szervezet által definiált függvények, portok, protokollok és a felesleges és/vagy a nem biztonságos kell tekinteni információk rendszerben szolgáltatások].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős letiltása funkciók, portok, protokollok és szolgáltatásokról, amelyek a szükségtelen vagy nem biztonságos kell tekinteni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-2"></a>A NIST 800-53 vezérlő CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Legalább funkció |} Az eszközprogram végrehajtása megakadályozása

**CM-7 (2)** az adatokat a rendszer megakadályozza, hogy a program végrehajtását összhangban [kijelölés (egy vagy több): [hozzárendelés: szervezet által meghatározott irányelvek mentén szoftver program használati és -korlátozások]; a feltételek engedélyező szabályok és feltételek program szoftverhasználatról].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős megakadályozza az ügyfél által definiált egyéni program használati házirendek szerint a program végrehajtását. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-5a"></a>A NIST 800-53 CM-7 (5) sémaszintjén szabályozása

#### <a name="least-functionality--authorized-software--whitelisting"></a>Legalább funkció |} Szoftver engedélyezett / engedélyezése

**CM-7 (5) sémaszintjén** azonosítja a szervezet [hozzárendelés: szervezet által meghatározott programok jogosult hajtható végre rajta az információs rendszer].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói azonosító az engedélyezett programok felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-5b"></a>A NIST 800-53 CM-7 (5) .b szabályozása

#### <a name="least-functionality--authorized-software--whitelisting"></a>Legalább funkció |} Szoftver engedélyezett / engedélyezése

**CM-7 (5) .b** a szervezet egy megtagadási – minden, a kivétel-engedélyezési házirendben engedélyezze az engedélyezett szoftverek végrehajtása a információs rendszerben alkalmazza.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy Megtagadás – minden, a kivétel-engedélyezési házirendet, az engedélyezett szoftverek végrehajtásának engedélyezése az ügyfél telepített erőforrás alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-7-5c"></a>A NIST 800-53 CM-7 (5) .c szabályozása

#### <a name="least-functionality--authorized-software--whitelisting"></a>Legalább funkció |} Szoftver engedélyezett / engedélyezése

**CM-7 (5) .c** a szervezet ellenőrzi, és az engedélyezett programok listájának frissítése [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős felülvizsgálata és frissítése engedélyezett szoftverek listáját. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-8a"></a>A NIST 800-53 vezérlő CM-8.a

#### <a name="information-system-component-inventory"></a>Információk rendszerleltár-összetevő

**CM-8.a** a szervezet házon belül fejlesztett alkalmazásokra és -dokumentumokat, amely pontosan tükrözi az aktuális információkat rendszer; az információs rendszer engedélyezési határain belül minden összetevőket tartalmazza; jelenleg adatokat rendszerösszetevők leltár a részletesség szintjét szükségesnek követésével és jelentésével; és tartalmaz [hozzárendelés: szervezet által megadott információk szükségesnek hatékony rendszer összetevő felelős eléréséhez].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az Azure Resource Manager biztosít a telepített erőforrások mindig naprakész listáját, és testre szabható címkét és csoport erőforrásokra vonatkozó készletkezelést. Ez a megoldás által telepített erőforrások kap egy meghatározott erőforrás címke lehet társítva a rendszer a határhoz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-8b"></a>A NIST 800-53 vezérlő CM-8.b

#### <a name="information-system-component-inventory"></a>Információk rendszerleltár-összetevő

**CM-8.b** a szervezet ellenőrzi, és frissíti az információkat az összetevő rendszerleltár [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az Azure Resource Manager egy telepített erőforrás áll a felülvizsgálatra az Azure portálon mindig naprakész listáját tartalmazza. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-1"></a>A NIST 800-53 vezérlő CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Információk a rendszerleltár összetevő |} Frissítések telepítése közben / eltávolítására

**CM-8 (1)** a szervezet azon információk rendszerösszetevők frissíti, az összetevő telepítését, eltávolítására és adatmódosításaira rendszer szerves részét.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az erőforrás panel az Azure portálon sorolja fel az összes telepített erőforrás, így mindig naprakész leltárt, az erőforrások telepítése és eltávolítása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-2"></a>A NIST 800-53 vezérlő CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Információk a rendszerleltár összetevő |} Automatikus karbantartás

**CM-8 (2)** a szervezet adatokat rendszerösszetevők naprakész, befejeződött, a pontos és azonnal elérhetők legyenek leltárt fenntartásához automatizált mechanizmusok funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az erőforrás panel az Azure portálon sorolja fel az összes telepített erőforrás, így mindig naprakész leltárt, az erőforrások telepítése és eltávolítása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-3a"></a>A NIST 800-53 CM-8 (3) sémaszintjén szabályozása

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Információk a rendszerleltár összetevő |} Automatikus jogosulatlan összetevő észlelése

**CM-8 (3) sémaszintjén** a szervezet funkcióit használja az automatikus mechanizmusok [hozzárendelés: szervezet által meghatározott gyakoriság] információk rendszerből jogosulatlan hardveres, szoftveres és belső vezérlőprogramjának összetevők észleléséhez.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél észleléséhez belül ügyfél telepített erőforrások jogosulatlan szoftverek automatikus mechanizmusok alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-3b"></a>A NIST 800-53 CM-8 (3) .b szabályozása

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Információk a rendszerleltár összetevő |} Automatikus jogosulatlan összetevő észlelése

**CM-8 (3) .b** a szervezet a következő műveleteket hajtja végre jogosulatlan összetevők észlelésekor: [kijelölés (egy vagy több): letiltja a hálózati hozzáférés ilyen összetevők; elkülöníti az összetevők; értesíti [hozzárendelés: szervezet által meghatározott tanácsadási csoporthoz vagy a szerepkörök]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős tart a művelet nem engedélyezett szoftverek észlelése esetén. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-4"></a>A NIST 800-53 vezérlő CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Információk a rendszerleltár összetevő |} Elszámolási kötelezettségéről szóló információk

**CM-8 (4)** információk rendszer összetevő leltáradatait, által azonosítására szolgáló módszert tartalmazza a szervezet [kijelölés (egy vagy több): név; pozíció; szerepkör], személyek felelős/kiszolgálófelügyeletért felelős ezen összetevők felügyelete .

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az Azure erőforrás-címkék alapvető / érték párok, amelyek kategorizálhatja az erőforrásokat elszámolási kötelezettségéről szóló és/vagy felügyeleti célokra is alkalmazható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-8-5"></a>A NIST 800-53 vezérlő CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Információk a rendszerleltár összetevő |} Nem ismétlődő nyilvántartási összetevők

**CM-8 (5)** a szervezet ellenőrzi, hogy az információk rendszer engedélyezési határain belül minden összetevő nem lesznek duplikálva egyéb információkat a rendszer összetevő készletek.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének összes erőforrást az Azure Resource Manager erőforráscsoport központilag telepíti. Az Azure Resource Manager egy telepített erőforrások mindig naprakész listáját tartalmazza. Ez a megoldás által telepített erőforrások kap egy meghatározott erőforrás címke lehet társítva a rendszer a határhoz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-9a"></a>A NIST 800-53 vezérlő CM-9.a

#### <a name="configuration-management-plan"></a>Konfigurációs felügyeleti csomag

**CM-9.a** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat és valósítja meg a konfigurációs séma az információk rendszerhez, amely, kötelezettségeket, és konfiguráció kezelésével kapcsolatos folyamatokat és eljárásokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél fejleszt, értelmezhet és egy konfigurációs felügyeleti csomag ügyfél telepített erőforrások végrehajtási felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-9b"></a>A NIST 800-53 vezérlő CM-9.b

#### <a name="configuration-management-plan"></a>Konfigurációs felügyeleti csomag

**CM-9.b** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumok és valósítja meg a konfigurációs séma hoz létre a folyamat azonosító konfigurációelemeket a rendszer fejlesztési élettartama alatt ciklus, ezért az információk rendszerhez a konfiguráció a konfigurációelemek kezelése.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél fejleszt, értelmezhet és egy konfigurációs felügyeleti csomag ügyfél telepített erőforrások végrehajtási felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-9c"></a>A NIST 800-53 vezérlő CM-9.c

#### <a name="configuration-management-plan"></a>Konfigurációs felügyeleti csomag

**CM-9.c** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat és a konfigurációs felügyeleti csomag megvalósítja az adatokat a rendszer konfigurációját tartalmazó adatokat rendszer elemek és helyezi el a konfigurációs elemek konfigurációja felügyeleti.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél fejleszt, értelmezhet és egy konfigurációs felügyeleti csomag ügyfél telepített erőforrások végrehajtási felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-9d"></a>A NIST 800-53 vezérlő CM-9.d

#### <a name="configuration-management-plan"></a>Konfigurációs felügyeleti csomag

**CM-9.d** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és egy konfigurációs tervet a konfigurációs felügyeleti csomag megakadályozza a jogosulatlan és -módosítási információk rendszer valósítja meg.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél fejleszt, értelmezhet és egy konfigurációs felügyeleti csomag ügyfél telepített erőforrások végrehajtási felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-10a"></a>A NIST 800-53 vezérlő CM-10.a

#### <a name="software-usage-restrictions"></a>Szoftver használattal kapcsolatos korlátozások

**CM-10.a** a szervezet használja a szoftver és a kapcsolódó dokumentáció létrejött és szerzői jogi törvények megfelelően.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Windows és az SQL Server licence az e tervezetének által telepített erőforrások megtalálható. Ez az Azure beépített szolgáltatása. Meglévő szoftverlicenc-szerződés rendelkező szervezetek fontolja meg alternatív licenc modellek telepítéséről. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-10b"></a>A NIST 800-53 vezérlő CM-10.b

#### <a name="software-usage-restrictions"></a>Szoftver használattal kapcsolatos korlátozások

**CM-10.b** a szervezet nyomon követi a szoftver és a kapcsolódó dokumentáció mennyiségi licencek másolása és terjesztési által védett.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Windows és az SQL Server licence az e tervezetének által telepített erőforrások megtalálható. A felhasználónak nem kell külön-külön nyomon követheti a licenc használatát. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-10c"></a>A NIST 800-53 vezérlő CM-10.c

#### <a name="software-usage-restrictions"></a>Szoftver használattal kapcsolatos korlátozások

**CM-10.c** a szervezet vezérli, és a dokumentumokat a társ-társ fájlmegosztást annak biztosítására, hogy ez a funkció nem használatos a jogosulatlan terjesztési, a megjelenítési, a teljesítmény vagy a szerzői jogi sokszorosítása technológia használatát.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Nincs megosztás funkció a tervezetének által telepített társ-társ fájl. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-10-1"></a>A NIST 800-53 vezérlő CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Szoftver használattal kapcsolatos korlátozások |} Nyílt forráskódú szoftvereket

**CM-10-es (1)** a szervezet nyílt forráskódú szoftver használatára a következő korlátozások hoz létre: [hozzárendelés: szervezet által meghatározott korlátozások].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű konfigurációs felügyeleti házirend foglalkozhat nyílt forráskódú szoftver használatára vonatkozó korlátozások. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-11a"></a>A NIST 800-53 vezérlő CM-11.a

#### <a name="user-installed-software"></a>Felhasználó által telepített szoftverek

**CM-11.a** a szervezet létesít [hozzárendelés: szervezet által meghatározott házirendek] szabályozó felhasználók szoftver telepítését.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a szoftvertelepítést, a felhasználók által telepített ügyfél erőforrások vonatkozó házirendet felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-11b"></a>A NIST 800-53 vezérlő CM-11.b

#### <a name="user-installed-software"></a>Felhasználó által telepített szoftverek

**CM-11.b** a szervezet érvényesíti a szoftverfrissítési telepítési házirendeket keresztül [hozzárendelés: szervezet által definiált metódusok].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél szoftver telepítési házirendek érvényesítéséről felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-cm-11c"></a>A NIST 800-53 vezérlő CM-11.c

#### <a name="user-installed-software"></a>Felhasználó által telepített szoftverek

**CM-11.c** a szervezet figyeli a szabályzatoknak való megfelelés [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a CM-11.a meghatározott házirendek felhasználói telepített erőforrások ellenőrzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-cm-11-1"></a>A NIST 800-53 vezérlő CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Felhasználók által telepített szoftverek |} Jogosulatlan telepítések riasztásainak

**CM-11 (1)** információk teljesítményriasztások [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] Ha a jogosulatlan szoftver van telepítve.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél rendszer feladata biztosítani a riasztások, szoftver jogosulatlan telepítésének észlelése esetén. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |

