---
title: "FedRAMP Azure tervezetének Automation - hozzáférés-vezérlés"
description: "Webes alkalmazásokhoz, a FedRAMP - hozzáférés-vezérlés"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>Hozzáférés-vezérlés (AC)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-ac-1"></a>A NIST 800-53 vezérlő AC-1

#### <a name="access-control-policy-and-procedures"></a>Hozzáférés-vezérlési szabályzat és eljárások

**AC-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] célra, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás, koordinációs orvosló hozzáférés-vezérlési szabályzatok szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a hozzáférés-vezérlési szabályzat és a kapcsolódó hozzáférés-vezérlés; végrehajtásának megkönnyítése ellenőrzi, hogy és a hozzáférés-vezérlési házirend frissítések [hozzárendelés: szervezet által meghatározott gyakorisága]; és hozzáférési az ellenőrzési eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési szabályzat és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2a"></a>A NIST 800-53 vezérlő AC-2.a

#### <a name="account-management"></a>Fiókkezelés

**AC-2.a** a szervezet azonosítja, és kiválasztja a következő típusú információk rendszerfiókok szervezeti feladatok/üzleti funkciók támogatásához: [hozzárendelés: szervezet által megadott információk rendszer fióktípusok].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének alapul, és valósítja meg a következő rendszer fióktípus: Azure Active Directory-felhasználók (üzembe helyezéséhez és kezelése az Azure-erőforrások elérésére használt), Windows operációs rendszer felhasználók (Active Directory által kezelt), SQL Server szolgáltatásfiókja. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2b"></a>A NIST 800-53 vezérlő AC-2.b

#### <a name="account-management"></a>Fiókkezelés

**AC-2.b** a szervezet adatokat rendszerfiókok fiók feletteseit rendeli hozzá.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hozzárendelése a kezelők AC-02.a azonosított fiókok felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2c"></a>A NIST 800-53 vezérlő AC-2.c

#### <a name="account-management"></a>Fiókkezelés

**AC-2.c** a szervezet csoport és a szerepkör tagsági hoz létre.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói szerepkörök és csoportok tagsági feltételei (lásd az AC-02.a) ügyfél által felügyelt fiókok típusai létrehozó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2d"></a>A NIST 800-53 vezérlő AC-2.d

#### <a name="account-management"></a>Fiókkezelés

**AC-2.d** a szervezet határozza meg a hitelesített felhasználók a rendszer, a csoport és a szerepkör tagság és a hozzáférési engedélyek (például a jogosultságok) és egyéb attribútumai (szükség szerint) az egyes fiókok számára.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy meglévő vállalati szintű fiók hitelesítési folyamatot. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2e"></a>A NIST 800-53 vezérlő AC-2.e

#### <a name="account-management"></a>Fiókkezelés

**AC-2.e** A szervezet megköveteli a jóváhagyások által [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] kérelmeknél hozza létre a információt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy meglévő vállalati szintű fiók hitelesítési folyamatot. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2f"></a>A NIST 800-53 vezérlő AC-2.f

#### <a name="account-management"></a>Fiókkezelés

**AC-2.f** a szervezet hoz létre, lehetővé teszi, módosítja, letilthatja és információ rendszer fiókok támogatják; ezzel eltávolítja [hozzárendelés: szervezet által meghatározott eljárást, vagy feltételek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy meglévő vállalati szintű fiók felügyeleti folyamatot. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2g"></a>A NIST 800-53 vezérlő AC-2.g

#### <a name="account-management"></a>Fiókkezelés

**AC-2.g** a szervezet adatokat rendszerfiókok használatát figyeli.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megvalósítja az OMS biztonsági és hitelesítési megoldás identitások és hozzáférések irányítópult. Ez az irányítópult lehetővé teszi, hogy a fiók kezelők figyelése információ rendszer fiókok használatát. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2h"></a>A NIST 800-53 vezérlő AC-2.h

#### <a name="account-management"></a>Fiókkezelés

**AC-2.h** a szervezeti fiók kezelők értesítést küld, ha a fiókok már nem szükséges; felhasználók megszakadt és átvitt; és ha egyes információk rendszer használati vagy ismernie kell a változtatásokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások hozhat létre olyan folyamatot, amely a megfelelő fiókot kezelő értesítése, ha a fiók már nem szükséges. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2i"></a>A NIST 800-53 vezérlő AC-2.i

#### <a name="account-management"></a>Fiókkezelés

**AC-2.i** a szervezet engedélyezi a hozzáférést a információs rendszer egy érvényes hálózatelérési hitelesítést; tervezett rendszer használati; és a szervezet vagy a kapcsolódó feladatok/üzleti funkciók által megkövetelt egyéb attribútumai alapján.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások hozhat létre egy hozzáférés-engedélyezési folyamata. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2j"></a>A NIST 800-53 vezérlő AC-2.j

#### <a name="account-management"></a>Fiókkezelés

**AC-2.j** fiókot a következő fiók felügyeleti követelmények ellenőrzi, hogy a szervezet [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a szükséges gyakoriságra annak meghatározásához, hogy a fiókok megfelelőek a szervezet követelményeinek áttekintette ügyfél által felügyelt fiókok felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-2k"></a>A NIST 800-53 vezérlő AC-2.k

#### <a name="account-management"></a>Fiókkezelés

**AC-2.k** a szervezet hoz létre egy folyamatot, hosszkorlátját megosztott/csoport fiók hitelesítő adatait (ha telepítve vannak) egyéni felhasználók számára a csoport eltávolításakor.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások hozhat létre egy folyamatot, csoport fiók hitelesítő adatainak kezelése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-1"></a>A NIST 800-53 vezérlő AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Fiókkezelés |} Az automatikus rendszer-fiókkezelés

**(1) AC-2** a szervezet funkcióit használja az automatizált mechanizmusok információ rendszer fiókok felügyeletének támogatásához.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megvalósítja az OMS biztonsági és hitelesítési megoldás identitások és hozzáférések irányítópult. Ez az irányítópult engedélyezése fiók kezelők figyelése információ rendszer fiókok használatát. OMS beállítható úgy, hogy küld riasztást, ha a rendellenes tevékenységek gyanús vagy más előre meghatározott események következnek be. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-2"></a>A NIST 800-53 vezérlő AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Fiókkezelés |} Ideiglenes / vészhelyzeti fiókok eltávolítása

**AC-2 (2)** az adatokat a rendszer automatikusan [kijelölés: eltávolítja; letiltja] után ideiglenes és vészhelyzeti fiókok [hozzárendelés: szervezet által meghatározott időszakban az egyes fiók].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének nem ideiglenes vagy vészhelyzeti fiókokat kell telepítenie. Ha nem, manuálisan letiltva, a telepített tartományvezérlő automatikusan letiltja összes inaktív fiókok 35 nap után. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-3"></a>A NIST 800-53 vezérlő AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Fiókkezelés |} Inaktív fiókok letiltása

**(3) AC-2** az adatokat a rendszer automatikusan letiltja a után inaktív fiókok [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A tartományvezérlő által az Azure tervezetének telepített összes felhasználói fiók letiltása tétlen 35 nap után van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-4"></a>A NIST 800-53 vezérlő AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Fiókkezelés |} Automatizált naplózási műveletek

**(4) AC-2** az adatokat a rendszer automatikusan fiók létrehozását, módosítását, engedélyezése, letiltása, és eltávolítási műveletek naplózás, és értesíti, [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a következő rendszer fióktípus: Azure Active Directory-felhasználók, a Windows operációs rendszer felhasználók, az SQL Server szolgáltatásfiókja. Az Azure Active Directory-fiók felügyeleti műveletek generál egy eseményt az Azure tevékenységnapló; Az operációs rendszer szintű fiók felügyeleti műveletek esemény létrehozása a rendszernaplóba. Ezek a naplók Naplóelemzési által gyűjtött, és az OMS-tárház tárolja. OMS beállítható úgy, hogy küld riasztást, előre meghatározott események bekövetkezésekor.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-5"></a>A NIST 800-53 vezérlő AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Fiókkezelés |} Inaktivitás jelentkezzen ki

**AC-2 (5)** a szervezet megköveteli, hogy a felhasználók jelentkeznek, amikor [hozzárendelés: szervezet által meghatározott időszak várt tétlen vagy jelentkezzen ki a leírás].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend hozhat létre olyan házirendet, amely a felhasználók jelentkezzen ki, ha azok várhatóan egy adott időn belül (vagy egyéb tényezők) inaktívnak lennie. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-7a"></a>A NIST 800-53 AC-2 (7) sémaszintjén szabályozása

#### <a name="account-management--role-based-schemes"></a>Fiókkezelés |} Szerepkör-alapú rendszerek

**(7) AC-2 sémaszintjén** a szervezet hoz létre és felügyeli a jogosultsággal rendelkező felhasználói fiókok szerint rendezi a rendszer engedélyezett információelérés és a jogosultságok a szerepkörök a szerepköralapú hozzáférés-sémát.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a következő rendszer fióktípus: Azure Active Directory-felhasználók, a Windows operációs rendszer felhasználók, az SQL Server szolgáltatásfiókja. Az Azure Active Directory jogosultságot megvalósított szerepköralapú hozzáférés-vezérlés használatával rendelhet a felhasználói szerepkörök; Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni. Ezek a szerepkör-alapú rendszerek bővíthető az ügyfél kritikus igényeinek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-7b"></a>A NIST 800-53 AC-2 (7) .b szabályozása

#### <a name="account-management--role-based-schemes"></a>Fiókkezelés |} Szerepkör-alapú rendszerek

**(7) AC-2 .b** a szervezet kiemelt szerepkör-hozzárendelések figyeli.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megvalósítja az OMS biztonsági naplózási megoldás azonosítása és irányítópulton. Ez az irányítópult lehetővé teszi, hogy a fiók kezelők figyelése információ rendszer fiókok használatát. Ez a megoldás kiemelt szerepkör-hozzárendelések jelentésének lehet lekérdezni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-7c"></a>A NIST 800-53 AC-2 (7) .c szabályozása

#### <a name="account-management--role-based-schemes"></a>Fiókkezelés |} Szerepkör-alapú rendszerek

**(7) AC-2 .c** a szervezetnek szüksége [hozzárendelés: szervezet által meghatározott műveleteket] Ha kiemelt szerepkör-hozzárendelések nem megfelelőek.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős művelet véve az ügyfél által felügyelt fiókot, ha kiemelt szerepkör-hozzárendelések nem megfelelő. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-9"></a>A NIST 800-53 vezérlő AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Fiókkezelés |} Korlátozások a megosztott / csoport fiókok használata

**AC-2 (9)** a szervezet csak lehetővé teszi a megfelelő megosztott/csoport fiókok használata [hozzárendelés: megosztott/csoportfiókoknak létrehozó szervezet által meghatározott feltételek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek megosztott/csoport fiókok engedélyezve vannak az Azure tervezetének által üzembe helyezett erőforrás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-10"></a>A NIST 800-53 vezérlő AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Fiókkezelés |} Megosztott / csoport fiók hitelesítő adat megszüntetése

**AC-2 (10)** az információs rendszer megosztott/csoport fiók hitelesítő adatainak ér véget, amikor a tagok a csoport elhagyására.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek megosztott/csoport fiókok engedélyezve vannak az Azure tervezetének által üzembe helyezett erőforrás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-11"></a>A NIST 800-53 vezérlő AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Fiókkezelés |} Használati feltételek

**AC-2 (11)** az adatokat a rendszer érvénybe lépteti [hozzárendelés: szervezet által meghatározott körülmények között és/vagy a használati feltételek] a(z) [hozzárendelés: szervezet által megadott információk rendszerfiókok].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend előfordulhat, hogy az Active Directory ki és beállított idő napi korlátozások vagy más fiók használati feltételek végrehajtásához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-12a"></a>A NIST 800-53 AC-2 (12) sémaszintjén szabályozása

#### <a name="account-management--account-monitoring--atypical-usage"></a>Fiókkezelés |} Figyelési / alapján ezúttal szokatlan használati fiók

**AC-2 (12) sémaszintjén** a szervezet figyeli információ rendszer fiókok számára [hozzárendelés: a rendellenes használati szervezet által meghatározott].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megvalósítja az OMS biztonsági és hitelesítési megoldás identitások és hozzáférések irányítópult. Ez az irányítópult lehetővé teszi, hogy a fiók kezelők hozzáférések elleni telepített erőforrások figyelése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-12b"></a>A NIST 800-53 AC-2 (12) .b szabályozása

#### <a name="account-management--account-monitoring--atypical-usage"></a>Fiókkezelés |} Figyelési / alapján ezúttal szokatlan használati fiók

**AC-2 (12) .b** a szervezet jelenti a rendellenes használati információk rendszer fiókokat [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének megvalósítja az OMS biztonsági és hitelesítési megoldás identitások és hozzáférések irányítópult. Ez az irányítópult fiók kezelők hozzáférések elleni telepített erőforrások figyelése engedélyezése. Ez a megoldás beállítható úgy, hogy küld riasztást, ha a rendellenes tevékenységek gyanús vagy más előre meghatározott események következnek be. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-2-13"></a>A NIST 800-53 vezérlő AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Fiókkezelés |} Tiltsa le a fiókok magas kockázatú egyéni felhasználók számára

**AC-2 (13)** a szervezeten belül lényeges kockázatot jelentő felhasználót letiltja [hozzárendelés: szervezet által meghatározott időszak] kockázat felderítés.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési szabályzat és eljárások hozhat létre a felhasználók számára a szervezet jelentős kockázatot jelentő fiókok letiltása feltételeit. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-3"></a>A NIST 800-53 vezérlő AC-3

#### <a name="access-enforcement"></a>Hozzáférés kényszerítése

**AC-3** az adatokat a rendszer érvénybe lépteti a jóváhagyott engedélyek logikai erőforrások elérése érdekében információkat és a rendszer megfelel-e megfelelő hozzáférés-vezérlési házirendeket.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének kikényszeríti a logikai hozzáférési engedélyek kényszeríti ki az Azure Active Directory felhasználók hozzárendelése szerepkörökhöz, Active Directory-felhasználók biztonsági csoportokra való hozzárendelésével szerepköralapú hozzáférés-vezérlés használatával, és a Windows operációs rendszer szintű szabályozza. Az Azure Active Directory-szerepkörök hozzárendelve a felhasználókhoz, vagy csoportok az Azure erőforrás-, csoport vagy előfizetés erőforrásokhoz való logikai hozzáférés szabályozása. Active Directory biztonsági csoportokat az operációs rendszer szintű erőforrások és a funkciók való logikai hozzáférést. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-4"></a>A NIST 800-53 vezérlő AC-4

#### <a name="information-flow-enforcement"></a>Információk folyamata kényszerítése

**AC-4** az adatokat a rendszer érvénybe lépteti a rendszerből, és alapján összekapcsolt rendszerek közötti információáramlást szabályozásának jóváhagyott engedélyek [hozzárendelés: szervezet által megadott információk folyamata Alkalmazásvezérlési házirendek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének kikényszeríti információk folyamata korlátozások Alkalmazásátjáró, amelyben erőforrások telepítése, alhálózatok alkalmazott hálózati biztonsági csoportok segítségével és a belső terheléselosztót. Hálózati biztonsági csoportok biztosítására, hogy az információáramlás engedélyezett szabályok alapján erőforrások között. Alkalmazás-átjáró és a terheléselosztó dinamikusan irányíthatja a forgalmat egy adott erőforráshoz való jóváhagyott szerepkörök alapján. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-4-8"></a>A NIST 800-53 vezérlő AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Információk folyamata kényszerítési |} Biztonsági házirend szűrők

**AC-4 (8)** az adatokat a rendszer érvénybe lépteti adatokat folyamata vezérlő a [hozzárendelés: szervezet által meghatározott biztonsági házirend szűrők] folyamat vezérlő döntéseinek alapjaként [hozzárendelés: szervezet által meghatározott információáramlás].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a felelősek az információk folyamatvezérlés ügyfél telepített erőforrások belül. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-4-21"></a>A NIST 800-53 vezérlő AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Információk folyamata kényszerítési |} Információáramlás fizikai / logikai elkülönítése

**AC-4 (21)** az információs rendszer logikailag vagy fizikailag az információáramlás elválasztja [hozzárendelés: és/vagy a szervezet által meghatározott mechanizmusok technikák] megvalósítása [hozzárendelés: szervezet által meghatározott típusú színkivonatok szükséges adatok].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ügyfél telepített erőforrások belül információáramlás elválasztó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-5a"></a>A NIST 800-53 vezérlő AC-5.a

#### <a name="separation-of-duties"></a>Feladataik elválasztását igénylik

**AC-5.a** elválasztja a szervezet [hozzárendelés: egyének szervezet által meghatározott feladatai].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős feladataik elválasztását igénylik az ügyfél által felügyelt fiókok között. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-5b"></a>A NIST 800-53 vezérlő AC-5.b

#### <a name="separation-of-duties"></a>Feladataik elválasztását igénylik

**AC-5.b** a szervezet dokumentumok egyének feladataik elválasztását igénylik.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős dokumentálásáért feladataik elválasztását igénylik az ügyfél által felügyelt fiókok között. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-5c"></a>A NIST 800-53 vezérlő AC-5.c

#### <a name="separation-of-duties"></a>Feladataik elválasztását igénylik

**AC-5.c** a szervezet adatokat rendszer hozzáférési engedélyek feladataik elválasztását igénylik támogatásához határozza meg.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének hajt végre, amelyen beállítható úgy, hogy a szervezeti követelményeknek megfelelően azon különálló szerepköralapú hozzáférés-vezérlés. Az Azure Active Directory jogosultságot megvalósított szerepköralapú hozzáférés-vezérlés használatával rendelhet a felhasználói szerepkörök; Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-6"></a>A NIST 800-53 vezérlő AC-6

#### <a name="least-privilege"></a>Legalacsonyabb jogosultsági szint

**AC-6** a szervezet védelmi funkciókat alkalmaz, így csak hitelesített fér hozzá a felhasználók (vagy a felhasználók nevében eljáró folyamatok) hozzárendelt tevékenység megfelelően szervezeti feladatok elvégzéséhez szükséges legalacsonyabb jogosultsági szint elve és üzleti funkciók.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a szerepköralapú hozzáférés-vezérlést az korlátozhatja a felhasználókat csak explicit módon hozzárendelt jogosultságok. Az Azure Active Directory jogosultságot megvalósított szerepköralapú hozzáférés-vezérlés használatával rendelhet a felhasználói szerepkörök; Active Directory jogosultságot szerepköralapú hozzáférés-vezérlés segítségével felhasználókat rendelhet a biztonsági csoportokat lehet létrehozni.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-1"></a>A NIST 800-53 vezérlő AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Legalacsonyabb jogosultsági szint |} Engedélyezi a hozzáférést a biztonsági funkciók

**(1) AC-6** a szervezet explicit módon engedélyezi a hozzáférést [hozzárendelés: (telepítve hardveres, szoftveres és belső vezérlőprogramja) szervezet által meghatározott biztonsági funkciók és biztonsági vonatkozó információt].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások hozhat létre egy hozzáférés-engedélyezési folyamat, amely tartalmazza a biztonsági funkciók eléréséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-2"></a>A NIST 800-53 vezérlő AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Legalacsonyabb jogosultsági szint |} Jogosulatlan hozzáférést nem a biztonsággal kapcsolatos funkciók

**AC-6 (2)** a szervezet megköveteli, hogy a információk rendszerfiókok vagy szerepkörök hozzáféréssel rendelkező felhasználói [hozzárendelés: szervezet által meghatározott biztonsági funkciók vagy biztonsági vonatkozó információkat], használja a megfelelő jogosultságokkal nem rendelkező fiókok vagy szerepkörök, amikor nem a biztonsággal kapcsolatos funkciók eléréséhez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Vállalati szintű hozzáférés-vezérlési szabályzat az ügyfél megfelelő jogosultságokkal nem rendelkező fiókok használatára, nem a biztonsággal kapcsolatos funkciók elérésekor felhasználók lehet szükség. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-3"></a>A NIST 800-53 vezérlő AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Legalacsonyabb jogosultsági szint |} Hálózati hozzáférés, a kiemelt parancsok

**AC-6 (3)** a szervezet engedélyezi a hálózati hozzáférést [hozzárendelés: kiemelt parancsok szervezet által meghatározott] csak a [hozzárendelés: szervezet által meghatározott működési igényeknek vonatkozóan] és a hozzáférés indoklása dokumentumokat a az adatokat a rendszer biztonsági tervezése.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend határozhatnak meg kiemelt parancsok, amelyek a hálózaton keresztül érhető el. Megjegyzés: Az ügyfelek nem Azure-infrastruktúra fizikai hozzáféréssel rendelkezik. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-5"></a>A NIST 800-53 vezérlő AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Legalacsonyabb jogosultsági szint |} Kiemelt jogosultságú fiókok

**[5] AC-6** a szervezet korlátozza a információk rendszer kiemelt jogosultságú fiókok [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend határozhatnak meg kiemelt jogosultságú fiókok használatára vonatkozó korlátozások. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-7a"></a>A NIST 800-53 AC-6 (7) sémaszintjén szabályozása

#### <a name="least-privilege--review-of-user-privileges"></a>Legalacsonyabb jogosultsági szint |} Tekintse át a felhasználói jogosultságok

**(7) AC-6 sémaszintjén** a szervezet értékelést [hozzárendelés: szervezet által meghatározott gyakorisága] jogosultságaival [hozzárendelés: szervezet által meghatározott szerepkörök vagy felhasználói osztályok] ilyen jogosultsággal szükség érvényesítéséhez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói fiókok ügyfél által felügyelt felhasználói jogosultságok áttekintése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-7b"></a>A NIST 800-53 AC-6 (7) .b szabályozása

#### <a name="least-privilege--review-of-user-privileges"></a>Legalacsonyabb jogosultsági szint |} Tekintse át a felhasználói jogosultságok

**(7) AC-6 .b** a szervezet ismét kiosztja, vagy eltávolítja a jogosultságokat, ha szükséges, megfelelő a szervezeti kritikus/üzleti igényeinek megfelelően.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél újbóli hozzárendelése vagy eltávolítása az ügyfél által felügyelt fiók megfelelő jogosultságokkal felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-8"></a>A NIST 800-53 vezérlő AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Legalacsonyabb jogosultsági szint |} A kód végrehajtása a jogosultsági szintek

**AC-6 (8)** megakadályozza, hogy az adatokat a rendszer [hozzárendelés: szervezet által definiált egyéni] futtatásának felhasználók végrehajtása a szoftver-nál magasabb jogosultsági szinten.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a szerepköralapú hozzáférés-vezérlést az korlátozhatja a felhasználókat csak explicit módon hozzárendelt jogosultságok. Virtuális gép operációs rendszer szintű védelmet nem engedélyezik a szoftvert a szoftver végrehajtása felhasználók magasabb jogosultsági szinten hajtható végre. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-9"></a>A NIST 800-53 vezérlő AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Legalacsonyabb jogosultsági szint |} A kiemelt funkciók használatának naplózása

**AC-6 (9)** az információs rendszer naplózás jogosultsági szintű funkciók végrehajtását.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az OMS a Naplóelemzés szolgáltatás megvalósítja. Telepített virtuális gépek és az Azure storage-fiókok diagnosztika is, amelyeknek nincs csatlakoztatott Naplóelemzési biztosítására jogosultsági szintű funkciók végrehajtását ellenőrzi, hogy. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-6-10"></a>A NIST 800-53 vezérlő AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Legalacsonyabb jogosultsági szint |} Jogosulatlan megtiltja jogosultsági szintű funkciók végrehajtása

**AC-6 (10)** az adatokat a rendszer megakadályozza, hogy a megfelelő jogosultságokkal nem rendelkező felhasználók tiltása játsszák ki, hogy kiemelt funkciók végrehajtása, vagy biztonsági óvintézkedéseket/ellenintézkedések módosítása megvalósítva.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a szerepköralapú hozzáférés-vezérlést az korlátozhatja a felhasználókat csak explicit módon hozzárendelt jogosultságok.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-7a"></a>A NIST 800-53 vezérlő AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Sikertelen bejelentkezési kísérletek

**AC-7.a** az adatokat a rendszer érvénybe lépteti legfeljebb [hozzárendelés: szervezet által megadott szám] egymást követő érvénytelen bejelentkezési kísérletek során a felhasználó egy [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure portál korlátok egymást követő érvénytelen bejelentkezési kísérletek felhasználók. A csoportházirendet alkalmazza az összes virtuális gépet az Azure tervezetének által központilag telepített operációs rendszer szintjén. A házirend korlátozza a felhasználók egy 15 perces időszakon belül legfeljebb három egymást követő érvénytelen bejelentkezési kísérletek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-7b"></a>A NIST 800-53 vezérlő AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Sikertelen bejelentkezési kísérletek

**AC-7.b** az adatokat a rendszer automatikusan [kijelölés: zárolja a fiókot/csomópont egy [hozzárendelés: szervezet által meghatározott időszakra vonatkozó]; zárolja a fiókot/csomópont mindaddig, amíg a rendszergazda által kiadott; késlelteti a következő bejelentkezési kérelem [megfelelően Hozzárendelés: szervezet által meghatározott késleltetés algoritmus]] Ha túllépik a sikertelen kísérletek maximális számát.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure-portálon fiókok zárolja, egymást követő érvénytelen bejelentkezési kísérlet felhasználók után. A csoportházirendet alkalmazza az összes virtuális gépet az Azure tervezetének által központilag telepített operációs rendszer szintjén. A házirend fiókok zárolja három órán keresztül a felhasználók által a három egymást követő érvénytelen bejelentkezési kísérlet után. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-7-2"></a>A NIST 800-53 vezérlő AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Sikertelen bejelentkezési kísérletek |} Kiürítése / mobilkészülékről

**AC-7 (2)** az információs rendszer pon/törlés adatait [hozzárendelés: mobileszközök szervezet által meghatározott] alapján [hozzárendelés: szervezet által meghatározott követelmények vagy eljárások kiürítése/törlése] után [hozzárendelése: szervezet által megadott szám] eszköz egymást követő, sikertelen bejelentkezési kísérletek.

**Feladatkörök:**`Not Applicable`

|||
|---|---|
| **Ügyfél** | Mobil eszközök nincsenek telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a mobileszközök az Azure határon belül. A vezérlő így nem tulajdonság Microsoft Azure vonatkozik. |


 ## <a name="nist-800-53-control-ac-8a"></a>A NIST 800-53 vezérlő AC-8.a

#### <a name="system-use-notification"></a>Rendszerértesítő használata

**AC-8.a** az adatokat a rendszer megjeleníti a felhasználók számára [hozzárendelés: szervezet által meghatározott rendszer értesítési üzenetet, vagy a transzparens használja] előtt a rendszer, amely megfelelő Szövetségi konzisztens adatvédelmi és biztonsági közlemények való hozzáférés biztosítása törvényi, végrehajtó rendelések, irányelvek, házirendek, szabályzat, szabványokat, és útmutatást és, hogy felhasználók elérik-e egy USA állapotok Kormányzati információk rendszer; információk rendszer használati ellenőrizhető legyen, rögzített, és ellenőrzés; az információs rendszer jogosulatlan használata tiltott és büntetőjogi vonatkoznak vagy civil eljárást; és az információk használatára figyelésével és rögzítésével hozzájárul jelzi.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend a bejelentkezés előtt a felhasználóknak megjelenített használata rendszerértesítőként valósítja meg. Megjegyzés: Az Azure szerkezeti terve valósítja meg egy példa Rendszerértesítő használja. Az ügyfél ezt a szöveget a szervezet és/vagy szabályozó szervezet követelményeinek teljesítéséhez kell szerkeszteni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-8b"></a>A NIST 800-53 vezérlő AC-8.b

#### <a name="system-use-notification"></a>Rendszerértesítő használata

**AC-8.b** az adatokat a rendszer megőrzi a értesítési üzenet vagy egy szalagcím képernyőn mindaddig, amíg a felhasználók megerősíti a használati feltételeket, és jelentkezzen be, vagy további elérni az adatokat a rendszer explicit műveletek végrehajtása.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend a bejelentkezés előtt a felhasználóknak megjelenített használata rendszerértesítőként valósítja meg. A felhasználó megerősíti kell ahhoz, hogy jelentkezzen be az értesítést. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-8c"></a>A NIST 800-53 vezérlő AC-8.c

#### <a name="system-use-notification"></a>Rendszerértesítő használata

**AC-8.c** nyilvánosan elérhető rendszerekhez az információk rendszer jeleníti meg a rendszer adatok használata [hozzárendelés: szervezet által megadott feltételek], további hozzáférést; megelőzően megjeleníti hivatkozásokat, ha vannak ilyenek, figyelés, a rögzítés, vagy naplózás, amely konzisztensek adatvédelmi szálláslehetőségeinek az ilyen rendszerekre, amelyek általában nem engedik az adott tevékenységek; és a jogosult használatát, a rendszer leírását tartalmazza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős használatát rendszerértesítőként megjelenítése ügyfél telepített összes nyilvánosan elérhető erőforrás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-10"></a>A NIST 800-53 vezérlő AC-10

#### <a name="concurrent-session-control"></a>Egyidejű munkamenet-vezérlés

**AC 10-es** az információs rendszer korlátozza az egyidejű munkamenetek minden [hozzárendelés: szervezet által meghatározott fiók és/vagy a fiók típusa] való [hozzárendelés: szervezet által megadott szám].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az operációs rendszer házirendjét az Azure tervezetének által központilag telepített virtuális gépek. A házirend valósítja meg az egyidejű munkamenet korlátozások (két munkamenet). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-11a"></a>A NIST 800-53 vezérlő AC-11.a

#### <a name="session-lock"></a>Munkamenet zárolása

**AC-11.a** az adatokat a rendszer megakadályozza a további elérését, a rendszer által kezdeményezése után a munkamenet-zárolási [hozzárendelés: szervezet által meghatározott időszak] tétlen vagy a felhasználó egy kérelem fogadásakor.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend az RDP-munkamenetekhez valósítja meg az inaktivitási zár. Felhasználók manuális kezdeményezhet a zárolást. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-11b"></a>A NIST 800-53 vezérlő AC-11.b

#### <a name="session-lock"></a>Munkamenet zárolása

**AC-11.b** az adatokat a rendszer megőrzi a munkamenet zárolási mindaddig, amíg a felhasználói hozzáférés meghatározott azonosítási és hitelesítési eljárások segítségével helyreállítja a csatolást.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend az RDP-munkamenetekhez valósítja meg az inaktivitási zár. Felhasználók ismét hitelesítenie kell magát a munkamenet zárolásának feloldásához.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-11-1"></a>A NIST 800-53 vezérlő AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Munkamenet-Lock |} Minta-elrejtés jeleníti meg

**(1) AC-11** az információs rendszer elfedi, a munkamenet zárolás keresztül nyilvánosan megtekinthető képének képernyőjén korábban látható információt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend az RDP-munkamenetekhez valósítja meg az inaktivitási zár. A munkamenet zárolási elfedi korábban látható információt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-12"></a>A NIST 800-53 vezérlő AC – 12

#### <a name="session-termination"></a>Munkamenet befejezését

**AC – 12** az adatokat a rendszer automatikusan után felhasználói munkamenet leállítása [hozzárendelés: szervezet által meghatározott feltételek vagy a munkamenet leválasztása igénylő kiváltó események].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A távoli asztali munkamenetgazda konfigurálása az Azure tervezetének által telepített Windows virtuális gépek beállítható úgy, hogy a szervezet munkamenet lezárás követelményeknek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-12-1a"></a>A NIST 800-53 vezérlő AC – 12 (1) sémaszintjén

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Munkamenet befejezését |} A felhasználó által kezdeményezett Logouts / üzenet megjelenítése

**(1) AC – 12 sémaszintjén** az információs rendszer kijelentkezési képességet biztosít a felhasználó által kezdeményezett kommunikáció munkamenetek, amikor eléréséhez hitelesítés esetén [hozzárendelés: szervezet által megadott információk erőforrások].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure portál és a virtuális gép telepített operációs rendszerek által a Azure tervezetének használja a kijelentkezési kezdeményezésének engedélyezése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-12-1b"></a>A NIST 800-53 vezérlő AC – 12 (1) .b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Munkamenet befejezését |} A felhasználó által kezdeményezett Logouts / üzenet megjelenítése

**(1) AC – 12 .b** az adatokat a rendszer explicit kijelentkezési jelenik meg a felhasználók számára, amely jelzi, hitelesített kommunikációs munkamenetek megbízható megszüntetését.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure portál és a virtuális gép telepített operációs rendszerek által a Azure tervezetének használja a kijelentkezési kezdeményezésének engedélyezése. A kijelentkezési folyamat biztosít arra utal, hogy a felhasználók számára, hogy a munkamenet meg lett szakítva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-14a"></a>A NIST 800-53 vezérlő AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Azonosítás és hitelesítés nélkül engedélyezett műveletek

**AC-14.a** azonosítja a szervezet [hozzárendelés: szervezet által meghatározott felhasználói műveletek], amely a végrehajtható azonosítási és hitelesítési szervezeti feladatok/üzleti funkciók konzisztens nélkül az információk rendszer.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős alapján azonosítja az ügyfél telepített erőforrások azonosítási és hitelesítési (pl., például megtekint egy nyilvánosan elérhető weblap) nélkül végrehajtható műveletek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-14b"></a>A NIST 800-53 vezérlő AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Azonosítás és hitelesítés nélkül engedélyezett műveletek

**AC-14.b** a szervezet dokumentumokat, és itt támogató profilkategóriák a biztonsági csomagban információs rendszer azonosítási és hitelesítési nem igénylő felhasználói műveletek.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói dokumentáció az azonosítási és hitelesítési ügyfél telepített erőforrások nem igénylő felhasználói műveletek felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-17a"></a>A NIST 800-53 vezérlő AC-17.a

#### <a name="remote-access"></a>Távelérés

**AC-17.a** a szervezet létesít, és a használattal kapcsolatos korlátozások, konfigurációs vagy kapcsolódási követelményeihez és megvalósítási útmutatás az egyes engedélyezett távoli hozzáférési dokumentumokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend határozhatnak meg távelérési használati korlátozások. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-17b"></a>A NIST 800-53 vezérlő AC-17.b

#### <a name="remote-access"></a>Távelérés

**AC-17.b** a szervezet engedélyezi a távoli hozzáférést az információs rendszer ilyen kapcsolatok engedélyezése előtt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások a távoli elérés engedélyezési folyamat hozhat létre. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-1"></a>A NIST 800-53 vezérlő AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Távoli hozzáférés |} Automatikus figyelési / szabályozása

**(1) AC-17** az információs rendszer figyeli, és távelérési módszerek szabályozza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az információs rendszer az Azure portálon keresztül egy jumpbox távoli asztali kapcsolaton keresztül, és egy ügyfél megvalósított webalkalmazás révén távoli hozzáférést biztosít. Az Azure portál és a távoli asztali munkamenetekhez keresztül fér hozzá naplóz, és OMS révén figyelhető. Az ügyfél távoli hozzáférés-vezérléssel szükség esetén, a webalkalmazásnak kell megvalósítania. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-2"></a>A NIST 800-53 vezérlő AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Távoli hozzáférés |} Bizalmas védelméről / integritási titkosítással

**(2) AC-17** az információs rendszer valósítja meg a távelérés munkamenetek sértetlenségét és bizalmasságát védelme érdekében titkosítási mechanizmus.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure-portálon, a távoli asztali kapcsolat és a webes alkalmazás átjáró, beleértve az Azure Blueprint által telepített erőforrásokhoz való távoli hozzáférés biztosított TLS használatával. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-3"></a>A NIST 800-53 vezérlő AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Távoli hozzáférés |} Felügyelt hozzáférést ellenőrzési pontok

**AC-17 (3)** az adatokat a rendszer továbbítja a keresztül minden távoli hozzáférések [hozzárendelés: szervezet által megadott szám] felügyelt hálózati hozzáférési ellenőrzési pontok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Távoli elérés az Azure tervezetének által telepített fiktív webalkalmazás Alkalmazásátjáró keresztül történik. Egyéb erőforrások táveléréséhez egy jumpbox keresztül történik. Nincsenek más nyilvánosan elérhető végpontok. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-4a"></a>A NIST 800-53 AC-17 (4) sémaszintjén szabályozása

#### <a name="remote-access--privileged-commands--access"></a>Távoli hozzáférés |} Jogosultságokat igénylő parancsok / hozzáférés

**(4) AC-17 sémaszintjén** a szervezet engedélyezi a kiemelt parancsok és biztonsági-információk csak a távoli hozzáférést keresztül hozzáférést végrehajtása [hozzárendelés: szervezet által meghatározott igények].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend határozhatnak meg, hogy távolról is elérhető, és adja meg a profilkategóriák kiemelt parancsok. Megjegyzés: Az ügyfelek hozzáférése nincs közvetlen hálózati Azure-infrastruktúra. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-4b"></a>A NIST 800-53 AC-17 (4) .b szabályozása

#### <a name="remote-access--privileged-commands--access"></a>Távoli hozzáférés |} Jogosultságokat igénylő parancsok / hozzáférés

**(4) AC-17 .b** a szervezet hozzáférés indoklása dokumentumokat a biztonsági csomagban az információk rendszerhez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend határozhatnak meg, hogy távolról is elérhető, és adja meg a profilkategóriák kiemelt parancsok. Megjegyzés: Az ügyfelek hozzáférése nincs közvetlen hálózati Azure-infrastruktúra. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-17-9"></a>A NIST 800-53 vezérlő AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Távoli hozzáférés |} Kapcsolat bontása / hozzáférés letiltása

**AC-17 (9)** a szervezet lehetővé teszi a gyors bontja a kapcsolatot, vagy tiltsa le a távoli hozzáférés információk belül [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az információs rendszer az Azure portálon keresztül, egy jumpbox keresztül távoli asztali kapcsolaton keresztül, és a webes alkalmazás segítségével távoli hozzáférést biztosít. Ha egy Azure Active Directory-fiókot le van tiltva, vagy eltávolítja, az Azure portál elérésére azonnal megszakad. Hasonlóképpen ha egy virtuális gép operációs rendszer szintű fiók le van tiltva, vagy eltávolítja, a jumpbox távoli asztali hozzáférésének le van választva azonnal. Az ügyfelek meg kell valósítania a távoli hozzáférés a webes alkalmazás leválasztása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-18a"></a>A NIST 800-53 vezérlő AC-18.a

#### <a name="wireless-access"></a>Vezeték nélküli hozzáférés

**AC-18.a** a szervezet használattal kapcsolatos korlátozások, a konfiguráció/kapcsolat követelményeket és a megvalósítás útmutató a vezeték nélküli hozzáférési határoz meg.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure használattal kapcsolatos korlátozások, a konfiguráció/kapcsolat követelményeket és a megvalósítás útmutató a hálózati biztonsági szabvány, amely explicit módon letiltja a Microsoft Azure környezetben vezeték nélküli használata vezeték nélküli hozzáféréshez határoz meg. |


 ## <a name="nist-800-53-control-ac-18b"></a>A NIST 800-53 vezérlő AC-18.b

#### <a name="wireless-access"></a>Vezeték nélküli hozzáférés

**AC-18.b** a szervezet engedélyezi a vezeték nélküli hozzáférést a információs rendszer ilyen kapcsolatok engedélyezése előtt.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a Microsoft Azure adatközpontjaiban belül vezeték nélküli hozzáférést. |


 ### <a name="nist-800-53-control-ac-18-1"></a>A NIST 800-53 vezérlő AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Vezeték nélküli hozzáférés |} Hitelesítési és titkosítási

**AC-18-ra (1)** az információs rendszer védi a vezeték nélküli hozzáférést a rendszerhez hitelesítésével [kijelölés (egy vagy több): felhasználók; eszközök] és a titkosítás.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a vezeték nélküli hozzáférés a Microsoft Azure-környezeten belül. |


 ### <a name="nist-800-53-control-ac-18-3"></a>A NIST 800-53 vezérlő AC 18. (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Vezeték nélküli hozzáférés |} Tiltsa le a vezeték nélküli hálózat

**(3) AC-18** a szervezet letiltja, amikor nem készült, vezeték nélküli hálózati információkat rendszerösszetevők kiállítási és üzembe helyezés előtt belső ágyazott lehetőségeket.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a vezeték nélküli hozzáférés a Microsoft Azure-környezeten belül. |


 ### <a name="nist-800-53-control-ac-18-4"></a>A NIST 800-53 vezérlő AC 18. (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Vezeték nélküli hozzáférés |} Korlátozza a felhasználók konfigurációk

**(4) AC-18** a szervezet azonosítja, és kifejezetten engedélyezi a felhasználók külön konfigurálni a vezeték nélküli hálózati funkciói.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a vezeték nélküli hozzáférés a Microsoft Azure-környezeten belül. |


 ### <a name="nist-800-53-control-ac-18-5"></a>A NIST 800-53 vezérlő AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Vezeték nélküli hozzáférés |} Antennák / Transmission Power szintjei

**AC 18. [5]** a szervezet rádió antennák kiválasztása, és új átviteli teljesítményszintek a valószínűsége annak, hogy használható jelek fogadhatók szervezet által szabályozott határain kívül csökkentése érdekében.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs vezeték nélküli hozzáférés telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a vezeték nélküli hozzáférés a Microsoft Azure-környezeten belül. |


 ## <a name="nist-800-53-control-ac-19a"></a>A NIST 800-53 vezérlő AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Hozzáférés-vezérlés a mobileszközök kezeléséhez

**AC-19.a** a szervezet használattal kapcsolatos korlátozások, a konfigurációs követelmények, a kapcsolat követelményeket és a megvalósítás útmutató a szervezet által felügyelt mobileszközök határoz meg.

**Feladatkörök:**`Not Applicable`

|||
|---|---|
| **Ügyfél** | Nincsenek ügyfél által felügyelt mobil eszközök telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a mobileszközök az Azure határon belül. A vezérlő így nem tulajdonság Microsoft Azure vonatkozik. |


 ## <a name="nist-800-53-control-ac-19b"></a>A NIST 800-53 vezérlő AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Hozzáférés-vezérlés a mobileszközök kezeléséhez

**AC-19.b** a szervezet engedélyezi a szervezeti adatok rendszerekre mobileszközök kapcsolat.

**Feladatkörök:**`Not Applicable`

|||
|---|---|
| **Ügyfél** | Nincsenek ügyfél által felügyelt mobil eszközök telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a mobileszközök az Azure határon belül. A vezérlő így nem tulajdonság Microsoft Azure vonatkozik. |


 ### <a name="nist-800-53-control-ac-19-5"></a>A NIST 800-53 vezérlő AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Hozzáférés-vezérlés mobileszközökhöz |} Eszköz teljes / tároló-alapú titkosítás

**AC-19 (5)** alkalmaz a szervezet [kijelölés: teljes eszköztitkosítás; tároló titkosítása] védelme érdekében a titkosítás és az adatok integritását a [hozzárendelés: szervezet által meghatározott mobileszközök].

**Feladatkörök:**`Not Applicable`

|||
|---|---|
| **Ügyfél** | Nincsenek ügyfél által felügyelt mobil eszközök telepített Azure rendszer hatókörén belül. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a mobileszközök az Azure határon belül. A vezérlő így nem tulajdonság Microsoft Azure vonatkozik. |


 ## <a name="nist-800-53-control-ac-20a"></a>A NIST 800-53 vezérlő AC-20.a

#### <a name="use-of-external-information-systems"></a>Külső adatokat rendszerek használata

**AC-20.a** a szervezet hoz létre a használati feltételeket, működő, a tulajdonos, más szervezetekkel létrehozott megbízhatósági kapcsolata konzisztens és/vagy karbantartása külső információs rendszerekkel, így jogosult személyek az információs rendszer eléréséhez információk külső rendszerekből.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend használatára vonatkozó rendelkezés felhő szolgáltatásajánlatok FedRAMP alatt is tartalmazhat. Azure engedélyezte egy ideiglenes hitelesítési működéséhez (P-ATO) által a FedRAMP közös engedélyezési Board (JAB) engedélyezése az Azure felhőszolgáltatások használatát beszerzési állami intézményekhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-20b"></a>A NIST 800-53 vezérlő AC-20.b

#### <a name="use-of-external-information-systems"></a>Külső adatokat rendszerek használata

**AC-20.b** a szervezet hoz létre a használati feltételeket, működő, a tulajdonos, más szervezetekkel létrehozott megbízhatósági kapcsolata konzisztens és/vagy karbantartása külső információs rendszerekkel, így jogosult személyek feldolgozni, tárolni vagy továbbítását szervezet által szabályozott információk külső rendszerekből.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend használatára vonatkozó rendelkezés felhő szolgáltatásajánlatok FedRAMP alatt is tartalmazhat. Azure engedélyezte egy ideiglenes hitelesítési működéséhez (P-ATO) által a FedRAMP közös engedélyezési Board (JAB) engedélyezése az Azure felhőszolgáltatások használatát beszerzési állami intézményekhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-20-1"></a>A NIST 800-53 vezérlő AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Külső adatokat rendszerek használata |} Jogosult használatát vonatkozó korlátozások

**(1) AC-20** a szervezet lehetővé teszi a felhatalmazott személyek segítségével egy külső információs rendszer az információs rendszer eléréséhez vagy feldolgozni, tárolni vagy továbbít a szervezet által felügyelt információkat, csak akkor, ha a szervezet ellenőrzi a a külső rendszer a szervezet adatokat biztonsági házirendet, és a biztonsági tervnek; a szükséges biztonsági ellenőrzések végrehajtása vagy a külső adatokat rendszert üzemeltető szervezeti entitással jóváhagyott rendszer kapcsolat vagy feldolgozási megállapodások megőrzi.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél nagyvállalat informatikai csoportja előfordulhat, hogy ellenőrizze a felhőalapú szolgáltatás szolgáltató megfelelőségi információk biztonsági követelményeit, és használni a hozzárendelt felhő szolgáltatásajánlatok vállalati jóváhagyás. Azure egy ideiglenes hitelesítési működéséhez (P-ATO) által a FedRAMP közös engedélyezési Board (JAB) rendelkezik. Azure FedRAMP jóváhagyott harmadik féltől származó assessment szervezetek (3PAO) FedRAMP biztonsági ellenőrzést és egyéb követelményeinek való megfelelés ellenőrzése megfelelőségét ellenőrizni kell. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ac-20-2"></a>A NIST 800-53 vezérlő AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Külső adatokat rendszerek használata |} Hordozható tárolóeszközök

**(2) AC-20** a szervezet [kijelölés: korlátozza; Ez a beállítás letiltja] által felhatalmazott személyek külső adatokat rendszerek hordozható tárolóeszközre szervezet által felügyelt eszköz.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft nem teszi lehetővé az ügyfél által felügyelt hordozható tárolóeszközök a Microsoft Azure-környezeten belül. |


 ## <a name="nist-800-53-control-ac-21a"></a>A NIST 800-53 vezérlő AC-21.a

#### <a name="information-sharing"></a>Információk megosztása

**AC-21.a** a szervezet információk megosztásához, ellenőrizze, hogy a megosztási partner hozzárendelt hozzáférési engedélyek felel meg az információkat a vonatkozó hozzáférési korlátozásokat a hitelesített felhasználóknak engedélyezésével elősegíti a [hozzárendelése: szervezet által megadott információk megosztása körülmények között, ahol szükség-e felhasználói megítélése].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés-vezérlési házirend kapcsolatos információk megosztása rendelkezéseket tartalmazhatnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-21b"></a>A NIST 800-53 vezérlő AC-21.b

#### <a name="information-sharing"></a>Információk megosztása

**AC-21.b** alkalmaz a szervezet [hozzárendelés: szervezet által meghatározott automatikus mechanizmusok, vagy manuálisan végrehajtott folyamatokat] felhasználók segít a információk megosztása/együttműködés döntések meghozatala során.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy vállalati szintű információk megosztása döntési támogatási funkció. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-22a"></a>A NIST 800-53 vezérlő AC-22.a

#### <a name="publicly-accessible-content"></a>Nyilvánosan elérhető tartalom

**AC-22.a** a szervezet egy nyilvánosan elérhető információkat rendszerre információk jogosult személyek jelöli meg.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások nyilvánosan elérhető információk jogosult személyek jelölhet ki. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-22b"></a>A NIST 800-53 vezérlő AC-22.b

#### <a name="publicly-accessible-content"></a>Nyilvánosan elérhető tartalom

**AC-22.b** a szervezet betanítja felhatalmazott személyek annak érdekében, hogy nyilvánosan elérhető információk csak bizalmasként adatokat tartalmazza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél jogosult nyilvánosan elérhető információk az egyéni felhasználók számára a vállalati szintű képzési hivatkozhatnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-22c"></a>A NIST 800-53 vezérlő AC-22.c

#### <a name="publicly-accessible-content"></a>Nyilvánosan elérhető tartalom

**AC-22.c** a szervezet ellenőrzi, hogy a nyilvánosan elérhető információkat rendszerbe, győződjön meg arról, hogy bizalmasként adatok nem tartalmazza a könyvelési előtt javasolt tartalmát.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások tartalomhoz javasolt, hogy a program egy nyilvánosan elérhető rendszer felülvizsgálati folyamat hozhat létre. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ac-22d"></a>A NIST 800-53 vezérlő AC-22.d

#### <a name="publicly-accessible-content"></a>Nyilvánosan elérhető tartalom

**AC-22.d** a szervezet ellenőrzi, hogy a tartalmat a nyilvánosan elérhető információkat rendszeren bizalmasként információt [hozzárendelés: szervezet által meghatározott gyakoriság] és az ilyen adatokat eltávolítja, ha a felderített.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű hozzáférés ellenőrzési eljárások hozhat létre egy folyamatot, időszakosan ellenőrizhetjük a nyilvánosan elérhető rendszerek közzé tartalom. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |

