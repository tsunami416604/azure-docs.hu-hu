---
title: Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - rendszer és adatintegritást
description: FedRAMP webes alkalmazások Automation - rendszer és adatintegritást
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206981"
---
# <a name="system-and-information-integrity-si"></a>Rendszer- és adatintegritást (SI)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-si-1"></a>A NIST 800-53 vezérlő SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Rendszer és a Kódintegritási szabályzat információk és eljárások

**SI-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy rendszer- és házirend célja, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás orvosló a koordinációs szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a rendszer és a információk kódintegritási szabályzat és a társított rendszer és a információk integritási vezérlők; végrehajtásának megkönnyítése ellenőrzi, hogy és a jelenlegi rendszer- és a kódintegritási szabályzat frissítések [hozzárendelés: szervezet által meghatározott gyakorisága]; és rendszer- és integritásának eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer- és a kódintegritási szabályzat és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-2a"></a>A NIST 800-53 vezérlő SI-2.a

#### <a name="flaw-remediation"></a>Hiányosság a szervizelési

**SI-2.a** a szervezet azonosítja, jelentéseket és adatokat a rendszer hiba javítja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az Automation & vezérlő megoldást a Windows virtuális gépek ebben az architektúrában telepített frissítések állapotának nyomon követését. Az irányítópultról a frissítéskezelés csempe megjeleníti a telepített Windows-kiszolgálók hibája: korrigálás állapota. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-2b"></a>A NIST 800-53 vezérlő SI-2.b

#### <a name="flaw-remediation"></a>Hiányosság a szervizelési

**SI-2.b** a szervezet teszteli, hatékonyságát és a lehetséges hatásai telepítés előtt hibája szervizelése kapcsolódó szoftver és a belső vezérlőprogram frissítéseket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói tesztelés hibája szervizelési hatékonysága és az ügyfél telepített erőforrás telepítés előtti lehetséges hatásai kapcsolódó frissítéseket felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-2c"></a>A NIST 800-53 vezérlő SI-2.c

#### <a name="flaw-remediation"></a>Hiányosság a szervizelési

**SI-2.c** a szervezeten belül biztonsági megfelelő szoftver és a belső vezérlőprogram frissítést telepít [hozzárendelés: szervezet által meghatározott időszak] kiadásában a frissítéseket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A tervezetének által telepített Windows virtuális gépek automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is telepíti az Automation & vezérlő megoldás, amelyen keresztül a központi telepítést is létrehozható kiszolgálókra történő központi telepítéséhez javítások Windows szükség esetén. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-2d"></a>A NIST 800-53 vezérlő SI-2.d

#### <a name="flaw-remediation"></a>Hiányosság a szervizelési

**SI-2.d** a szervezet magában foglalja a hiányosság a szervizelési be a szervezeti konfigurációfelügyeleti folyamat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős a konfigurációkezelés a hiányosság a szervizelési is beleértve. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-2-1"></a>A NIST 800-53 vezérlő SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Hiányosság a szervizelési |} Központi kezelés

**(1) SI-2** a szervezet központilag kezeli a hiányosság szervizelési folyamat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az Automation & vezérlő megoldást a Windows virtuális gépek ebben az architektúrában telepített frissítések állapotának nyomon követését. Az irányítópultról a frissítéskezelés csempe megjeleníti a telepített Windows-kiszolgálók hibája: korrigálás állapota. Szoftverfrissítés-telepítések kiszolgálókra történő központi telepítéséhez javítások Windows szükség esetén is létrehozható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-2-2"></a>A NIST 800-53 vezérlő SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Hiányosság a szervizelési |} Automatizált hibája: korrigálás állapota

**SI-2 (2)** a szervezet funkcióit használja az automatikus mechanizmusok [hozzárendelés: szervezet által meghatározott gyakoriság] információk rendszerösszetevők tekintetében hiányosság a szervizelési állapotának meghatározásához.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az Automation & vezérlő megoldást a Windows virtuális gépek ebben az architektúrában telepített frissítések állapotának nyomon követését. Minden felügyelt Windows-számítógép esetében naponta kétszer történik vizsgálat. A rendszer 15 percenként lekérdezi a Windows API utolsó frissítésének időpontját, hogy meghatározza, megváltozott-e az állapot, és ha igen, megfelelőségi vizsgálatot kezdeményez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-2-3a"></a>A NIST 800-53 SI-2 (3) sémaszintjén szabályozása

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Hiányosság a szervizelési |} A hiba javítása idő / Referenciaalapokban korrekciós műveletek

**(3) SI-2 sémaszintjén** a szervezet közötti hiba azonosítása és a hiányosság a szervizelési időt méri.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél ügyfél telepített erőforrások belül felmérésével hibái felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-2-3b"></a>A NIST 800-53 SI-2 (3) .b szabályozása

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Hiányosság a szervizelési |} A hiba javítása idő / Referenciaalapokban korrekciós műveletek

**(3) SI-2 .b** a szervezet létesít [hozzárendelés: szervezet által meghatározott referenciaalapok] korrekciós műveletek hajthatók végre.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a hiányosság a szervizelési folyamatok vállalati szintű referenciaalapok hivatkozhatnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-3a"></a>A NIST 800-53 vezérlő SI-3.a

#### <a name="malicious-code-protection"></a>Rosszindulatú kódot védelme

**SI-3.a** a szervezet funkcióit használja a kártékony kódot védelmi mechanizmusok információk rendszer be- és kilépés észleli, és a kártékony kódokkal felszámolása pontokat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-3b"></a>A NIST 800-53 vezérlő SI-3.b

#### <a name="malicious-code-protection"></a>Rosszindulatú kódot védelme

**SI-3.b** minden új kiadásokat szervezeti konfiguráció-kezelési házirend és eljárások érhetők el a szervezet frissíti az kártékony kódot védelmi mechanizmust.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. A bővítmény automatikusan frissíti a kiadásban elérhető legyen, mindkét a kártevőirtó motor és a védelmi aláírások van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-3c"></a>A NIST 800-53 vezérlő SI-3

#### <a name="malicious-code-protection"></a>Rosszindulatú kódot védelme

**SI-3** a szervezetnél konfigurálva van a kártevő kód védelmi mechanizmusokat végezheti el az adatokat a rendszer rendszeres vizsgálatokat [hozzárendelés: szervezet által meghatározott gyakoriság] és a valós idejű vizsgálat a külső forrásból származó fájlok [kijelölés (egy vagy több). végpont; hálózati kilépést pontok], a fájlok letöltését követően megnyitásakor vagy szervezeti biztonsági előírás tiltja; megfelelően hajtotta végre és [kijelölés (egy vagy több): blokkolja a kártékony kódokkal; karanténba helyezése, kártékony kódot; küldési riasztás rendszergazdának; [Hozzárendelés: a művelet a szervezet által meghatározott]] észlelési rosszindulatú kód válaszul.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. A bővítmény összetevőjére valós időben és rendszeres időközönként (hetente), automatikusan mindkét a kártevőirtó motor és a védelmi aláírások frissítése és automatikus javítási műveleteket van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-3d"></a>A NIST 800-53 vezérlő SI-3.d

#### <a name="malicious-code-protection"></a>Rosszindulatú kódot védelme

**SI-3.d** a szervezet az téves fogadását címek kártékony kódot észlelési és felszámolására és az eredményül kapott célgyűjtemény rendelkezésre állását a információs rendszer alatt.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős kártékony kódot elleni védelem. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-3-1"></a>A NIST 800-53 vezérlő SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Rosszindulatú kódot védelmi |} Központi kezelés

**SI-3 (1)** a szervezet központilag kezeli a kártevő kód védelmi mechanizmust.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. Azure lehetővé teszi egy központi tekintse át a kártevőirtó megoldást az aktuális állapotát. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-3-2"></a>A NIST 800-53 vezérlő SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Rosszindulatú kódot védelmi |} Az automatikus frissítések

**SI-3 (2)** az adatokat a rendszer automatikusan frissíti a rosszindulatú kódot védelmi mechanizmust.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. A bővítmény automatikusan frissíti a kiadásban elérhető legyen, mindkét a kártevőirtó motor és a védelmi aláírások van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-3-7"></a>A NIST 800-53 vezérlő SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Rosszindulatú kódot védelmi |} Nonsignature-alapú észlelése

**(7) SI-3** az adatokat a rendszer kártékony kódot nonsignature alapú észlelési módszerek valósítja meg.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti az összes telepített Windows virtuális gép a Microsoft Antimalware virtuálisgép-bővítmény készletével megvalósított állomásalapú kártevőirtó védelmet. A bővítmény heurisztikus észlelés végrehajtására van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4a"></a>A NIST 800-53 vezérlő SI-4.a

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.a** a szervezet figyeli a információs rendszer támadások és a potenciális támadások összhangban utaló [hozzárendelés: szervezet által meghatározott célkitűzések figyelése]; és a jogosulatlan helyi, hálózati és távoli kapcsolatok száma.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a Naplóelemzési és a biztonsági és hitelesítési megoldás. Ez a megoldás révén átfogó képet kaphat a biztonságot, a támadások és a potenciális támadások mutatók. A biztonsági és naplózási irányítópult érhetők el adatok használata telepített megoldásokkal telepített erőforrások biztonsági állapotának magas szintű betekintést nyújt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4b"></a>A NIST 800-53 vezérlő SI-4.b

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.b** a szervezet azonosítja az információk rendszer jogosulatlan használata [hozzárendelés: szervezet által meghatározott technikák és módszerek].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a biztonsági és hitelesítési megoldás. A azonosítása és hozzáférési tartományi egy irányítópultot, amelynek információk identitás rendszerállapotáról, beleértve a bejelentkezés sikertelen bejelentkezési kísérletek számát és a fiókokat, amelyeket a rendszer az aktuális száma áttekintését biztosítja. Ez az irányítópult a rendelkezésre álló információk segít a potenciális gyanús tevékenység azonosítása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4c"></a>A NIST 800-53 vezérlő SI-4.c

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.c** a szervezet által telepített stratégiailag rendszerből információkat gyűjthet a szervezet által meghatározott alapvető információk; és a rendszerben, bizonyos típusú tranzakciók nyomon követésére ad hoc helyeken figyelési eszközök a szervezet számára fontos.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a Naplóelemzési és a biztonsági és hitelesítési megoldás. A biztonsági és naplózási irányítópult érhetők el adatok használata telepített megoldásokkal, beleértve a virtuális gép operációs rendszer figyelési adatok betekintést telepített erőforrások biztonsági állapotának magas szintű betekintést nyújt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4d"></a>A NIST 800-53 vezérlő SI-4.d

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.d** a szervezet védi a behatolás-figyelési származó információ eszközeit a jogosulatlan hozzáférés, módosítását és törlését.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Logikai hozzáférés-vezérlés segítségével védheti a tervezetének belül figyelési információit a jogosulatlan hozzáférés, módosítását és törlését. Az Azure Active Directory vezérlésről jóváhagyott logikai szerepköralapú csoporttagságok használatával. Lehet, hogy a figyelési adatok megtekintése és figyelési eszközök ezen engedélyeket igénylő felhasználók korlátozni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4e"></a>A NIST 800-53 vezérlő SI-4.e

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.e** A szervezet heightens figyelése, ha arra utal, hogy a megnövekedett kockázata szervezeti műveletek és eszközök, egyéni felhasználók számára, más szervezetek vagy a nemzeti bűnüldözési információ, alapján rendszer szintje az eszközintelligencia-információkat, vagy más hiteles információforrás.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások figyeléséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4f"></a>A NIST 800-53 vezérlő SI-4.f

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.f** a szervezet szerzi be a jogi véleményével információk Rendszerfigyelő vonatkozó Szövetségi törvényeket, végrehajtó rendelések, irányelvek, vagy a szabályzat tevékenységek tekintetében.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások figyeléséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-4g"></a>A NIST 800-53 vezérlő SI-4.g

#### <a name="information-system-monitoring"></a>Információ-rendszer figyelése

**SI-4.g** biztosít a szervezet [hozzárendelés: szervezet által megadott információk Rendszerfigyelő információt] való [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] [kijelölés (egy vagy több): szükség esetén; [Hozzárendelés: szervezet által meghatározott gyakoriság]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások figyeléséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-1"></a>A NIST 800-53 vezérlő SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Információk a rendszer figyelési |} Rendszerszintű behatolás rendszer

**SI-4 (1)** a szervezet kapcsolódik, és konfigurálja az egyes behatolás eszközök olyan információkat rendszerszintű behatolás észlelési rendszerbe.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások figyeléséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-2"></a>A NIST 800-53 vezérlő SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Információk a rendszer figyelési |} A valós idejű elemzési automatizált eszközök

**(2) SI-4** a szervezet automatizált eszközök támogatásához közel valós idejű elemzési események funkcióit használja.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a Naplóelemzési és a különböző felügyeleti megoldások, biztonsági és hitelesítési megoldását is beleértve. A Naplóelemzési közel valós idejű elemzési események biztosít a telepített erőforrások között. -Kezelési megoldás biztosítja biztonságot átfogó képet solution tartományok között. A Naplóelemzési érhetők el adatok használata telepített megoldásokkal telepített erőforrások biztonsági állapotát betekintést nyújt. A Naplóelemzési beállítható úgy, hogy a megadott feltételeknek megfelelő riasztásokat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-4"></a>A NIST 800-53 vezérlő SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Információk a rendszer figyelési |} Bejövő és kimenő kommunikációs forgalom

**SI-4 (4)** az információs rendszer figyeli a bejövő és kimenő kommunikációs forgalom [hozzárendelés: szervezet által meghatározott gyakoriság] szokatlan vagy jogosulatlan tevékenységek vagy feltételeket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások figyeléséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-5"></a>A NIST 800-53 vezérlő SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Információk a rendszer figyelési |} A rendszer riasztások

**SI-4 (5)** információk teljesítményriasztások [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] Ha fordulhat elő, a biztonság sérülését, vagy lehetőségét jelezhetik a következő jelzések: [hozzárendelés: szervezet által meghatározott biztonsági sérülés mutatók].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a különböző megoldások, biztonsági és hitelesítési megoldását is beleértve. A Naplóelemzési közel valós idejű elemzési események biztosít a telepített erőforrások között. -Kezelési megoldás biztosítja biztonságot átfogó képet solution tartományok között. A Naplóelemzési beállítható úgy, hogy a megadott feltételeknek megfelelő riasztásokat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-11"></a>A NIST 800-53 vezérlő SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Információk a rendszer figyelési |} Kommunikációs forgalom rendellenességeket elemzése

**SI-4 (11)** a szervezet elemzi az adatokat a rendszer a külső határ kimenő kommunikáció-forgalmat, és kijelölt [hozzárendelés: szervezet által meghatározott belső mutat, a rendszerben (például alhálózatok, alrendszereket)] számára Fedezze fel a rendellenességeket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél kommunikációs forgalom rendellenességek észlelését, az ügyfél telepített erőforrások elemzése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-14"></a>A NIST 800-53 vezérlő SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Információk a rendszer figyelési |} Vezeték nélküli behatolásérzékelési

**SI-4 (14)** a szervezet alkalmazza a vezeték nélküli behatolás rendszer engedélyezetlen vezeték nélküli eszközök azonosítására és észlelésére a támadási kísérletek és a potenciális támadások/megszegése az információk rendszerre.

**Feladatok:** `Azure Only`

|||
|---|---|
| **Ügyfél** | Nincs ügyfél által felügyelt hardver, beleértve a vezeték nélküli eszközök, az Azure adatközpontjaiban engedélyezett. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure rendszeresen figyeli a vezeték nélküli jelek engedélyezetlen negyedévente AC-18 leírtaknak megfelelően. <br /> A Microsoft Azure valósítja meg a vezérlő PaaS és a IaaS ügyfél nevében. |


 ### <a name="nist-800-53-control-si-4-16"></a>A NIST 800-53 vezérlő SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Információk a rendszer figyelési |} Figyelési információkat összefüggéseket

**SI-4 (16)** a szervezet korrelálja figyelési információkat rendszerben alkalmazott eszközök adatait.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének telepíti a Naplóelemzési és a különböző felügyeleti megoldások, biztonsági és hitelesítési megoldását is beleértve. A Naplóelemzési érhetők el adatok használata telepített megoldásokkal telepített erőforrások biztonsági állapotát betekintést nyújt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-18"></a>A NIST 800-53 vezérlő SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Információk a rendszer figyelési |} Forgalom elemzése / átalakítása Exfiltration

**SI-4 (18)** a szervezet elemzi a külső határt a információs rendszer (azaz a rendszer szegélyhálózati) és a kimenő kommunikáció forgalmat [hozzárendelés: a rendszerben (pl. alrendszereket, mutat szervezet által meghatározott belső alhálózatok)] adatok átalakítása exfiltration észleléséhez.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős az ügyfél telepített erőforrások kommunikációs forgalom elemzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-19"></a>A NIST 800-53 vezérlő SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Információk a rendszer figyelési |} Vajon nagyobb kockázattal egyéni felhasználók számára

**SI-4 (19)** a szervezet valósít meg [hozzárendelés: további figyelési szervezet által meghatározott] által azonosított egyéni felhasználók számára a [hozzárendelés: szervezet által meghatározott források], amelyek egy nagyobb kockázati szinteket illetően.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős figyelési használhatják, akik nagyobb kockázattal jár. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-20"></a>A NIST 800-53 vezérlő SI-4 (20.)

#### <a name="information-system-monitoring--privileged-users"></a>Információk a rendszer figyelési |} Megfelelő jogosultsággal rendelkező felhasználók

**SI-4 (20)** a szervezet valósít meg [hozzárendelés: további figyelési szervezet által meghatározott] jogosultsággal rendelkező felhasználók.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél figyelését a megfelelő jogosultsággal rendelkező felhasználók felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-22"></a>A NIST 800-53 vezérlő SI-4 (22-es)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Információk a rendszer figyelési |} Nem hitelesített hálózati szolgáltatások

**SI-4 (22-es)** az információk rendszer észleli a hálózati szolgáltatások, amelyek nem engedélyezett vagy jóvá [hozzárendelés: szervezet által meghatározott engedélyezési vagy a jóváhagyási folyamat] és [kijelölés (egy vagy több): naplózás; riasztások [hozzárendelése: szervezet által meghatározott tanácsadási csoporthoz vagy a szerepkörök]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél nem hitelesített hálózati szolgáltatások észlelése felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-23"></a>A NIST 800-53 vezérlő SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Információk a rendszer figyelési |} Gazdagép-alapú eszközök

**SI-4 (23)** a szervezet valósít meg [hozzárendelés: figyelési mechanizmusok állomásalapú szervezet által meghatározott], [hozzárendelés: szervezet által megadott információk rendszerösszetevők].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének figyelési adatait gyűjti a telepített erőforrások a gazdagép-alapú figyelési képességek adatait is beleértve. A Microsoft Monitoring Agent telepítve van az összes Windows virtuális gépek Naplóelemzési és más felügyeleti megoldás által használt figyelési adatok gyűjtéséért felelős ügyfélfeladatot. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-4-24"></a>A NIST 800-53 vezérlő SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Információk a rendszer figyelési |} Az illetéktelen behatolásoknak mutatók

**SI-4 (24)** az adatokat a rendszer felderíti, gyűjti, továbbítja, és használja az illetéktelen behatolásoknak mutatók.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős felderítésével, gyűjtése, terjesztése és az illetéktelen behatolásoknak ügyfél telepített erőforrások mutatók használatával. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-5a"></a>A NIST 800-53 vezérlő SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Biztonsági riasztások, tanácsadók és irányelvek

**SI-5.a** a szervezet kap információkat rendszer biztonsági riasztásokat, tanácsadók és az irányelvek [hozzárendelés: külső szervezetek szervezet által meghatározott] alapján.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági riasztásokat, tanácsadók és ügyfél telepített erőforrásokat (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között) irányelvek felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-5b"></a>A NIST 800-53 vezérlő SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Biztonsági riasztások, tanácsadók és irányelvek

**SI-5.b** a szervezet belső biztonsági riasztásokat, tanácsadók és irányelvek generál, szükségesnek.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági riasztásokat, tanácsadók és az ügyfél telepített erőforrások irányelvek felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-5c"></a>A NIST 800-53 vezérlő SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Biztonsági riasztások, tanácsadók és irányelvek

**SI-5.c** a szervezet biztonsági riasztásokat, tanácsadók és irányelvek terjeszti: [kijelölés (egy vagy több): [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök]; [Hozzárendelés: a szervezeten belüli szervezet által meghatározott elemek]; [Hozzárendelés: a külső szervezetek szervezet által meghatározott]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági riasztásokat, tanácsadók és az ügyfél telepített erőforrások irányelvek felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-5d"></a>A NIST 800-53 vezérlő SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Biztonsági riasztások, tanácsadók és irányelvek

**SI-5.d** a szervezet valósítja meg a biztonsági irányelveknek megfelelően a meghatározott idő keretek, vagy a kiállító szervezetet meg nem felelés mértékének értesíti.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági riasztásokat, tanácsadók és az ügyfél telepített erőforrások irányelvek felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-5-1"></a>A NIST 800-53 vezérlő SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Biztonsági riasztások, tanácsadók és irányelvek |} Automatizált riasztások és tanácsadók

**SI-5 (1)** a szervezet funkcióit használja az automatizált mechanizmusokat, ellenőrizze a biztonsági figyelmeztetés és tanácsadó információk érhetők el a szervezetben.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági riasztásokat, tanácsadók és az ügyfél telepített erőforrások irányelvek felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-6a"></a>A NIST 800-53 vezérlő SI-6.a

#### <a name="security-function-verification"></a>Biztonsági függvény ellenőrzése

**SI-6.a** a információk rendszer ellenőrzi a helyes működését [hozzárendelés: szervezet által meghatározott biztonsági funkciók].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős biztonsági függvény ellenőrzési ügyfél telepített erőforrásokat (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-6b"></a>A NIST 800-53 vezérlő SI-6.b

#### <a name="security-function-verification"></a>Biztonsági függvény ellenőrzése

**SI-6.b** az adatokat a rendszer ezt az ellenőrzést hajt végre [kijelölés (egy vagy több): [hozzárendelés: szervezet által meghatározott rendszer átmeneti állapotok]; parancs; a megfelelő jogosultsággal rendelkező felhasználó esetén [Hozzárendelés: szervezet által meghatározott gyakoriság]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági függvény ellenőrzési ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-6c"></a>A NIST 800-53 vezérlő SI 6.c lépést

#### <a name="security-function-verification"></a>Biztonsági függvény ellenőrzése

**SI-6.c lépést** az adatokat a rendszer értesíti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] sikertelen biztonsági ellenőrzés tesztet hajt végre.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági függvény ellenőrzési ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-6d"></a>A NIST 800-53 vezérlő SI-6.d

#### <a name="security-function-verification"></a>Biztonsági függvény ellenőrzése

**SI-6.d** az adatokat a rendszer [kijelölés (egy vagy több): az adatokat a rendszer leállítja; újraindítja a információs rendszer; [Hozzárendelés: alternatív elemek szervezet által meghatározott]] Ha rendellenességeket felderítése.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél biztonsági függvény ellenőrzési ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-7"></a>A NIST 800-53 vezérlő SI-7

#### <a name="software-firmware-and-information-integrity"></a>Szoftver, a belső vezérlőprogram és az adatintegritást

**SI-7** a szervezet védett sértetlenségének ellenőrzése eszközök észlelni a jogosulatlan változtatása [hozzárendelés: szervezet által definiált egyéni, a belső vezérlőprogram és az információk].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépek a tervezetének által telepített Windows operációs rendszert futtató. Windows biztosít a valós idejű fájl sértetlenségének ellenőrzése, a védelem és a Windows vagy a Windows Resource védelem (WRP) funkció engedélyezett Windows rendszer frissítések részeként telepített core rendszer fájlok helyreállítását. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-7-1"></a>A NIST 800-53 vezérlő SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Szoftver, a belső vezérlőprogram és az adatintegritást |} Integritás-ellenőrzést

**SI-7 (1)** a információk rendszer hajt végre a integritás-ellenőrzést [hozzárendelés: szervezet által definiált egyéni, a belső vezérlőprogram és az információk] [kijelölés (egy vagy több): indításkor;: [hozzárendelés: átmeneti állapotok szervezet által meghatározott vagy biztonsági kapcsolódó események]; [Hozzárendelés: szervezet által meghatározott gyakoriság]].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépek a tervezetének által telepített Windows operációs rendszert futtató. Windows biztosít a valós idejű fájl sértetlenségének ellenőrzése, a védelem és a Windows vagy a Windows Resource védelem (WRP) funkció engedélyezett Windows rendszer frissítések részeként telepített core rendszer fájlok helyreállítását. WRP lehetővé teszi a valós idejű integritás-ellenőrzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-7-2"></a>A NIST 800-53 vezérlő SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Szoftver, a belső vezérlőprogram és az adatintegritást |} Integritás megsértésének automatizált értesítések

**(2) SI-7** a szervezet funkcióit használja az automatikus értesítést biztosító eszközök [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] után azok az eltérések felfedezése sértetlenségének ellenőrzése során.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépek a tervezetének által telepített Windows operációs rendszert futtató. Windows biztosít a valós idejű fájl sértetlenségének ellenőrzése, a védelem és a Windows vagy a Windows Resource védelem (WRP) funkció engedélyezett Windows rendszer frissítések részeként telepített core rendszer fájlok helyreállítását.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-7-5"></a>A NIST 800-53 vezérlő SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Szoftver, a belső vezérlőprogram és az adatintegritást |} Integritás megsértésének automatikus válasz

**SI-7 (5)** az adatokat a rendszer automatikusan [kijelölés (egy vagy több): az adatokat a rendszer leállítja az információs rendszer újraindul; valósítja meg [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket]] Ha integritási megsértésének van felderített.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata automatikusan ad választ az integritási megsértésének ügyfél telepített erőforrások belül. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-7-7"></a>A NIST 800-53 vezérlő SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Szoftver, a belső vezérlőprogram és az adatintegritást |} Észlelési és válasz integrációját.

**SI-7 (7)** a szervezet a szerződés magában foglalja a jogosulatlan észlelését [hozzárendelés: a információk rendszer biztonságot érintő módosításainak szervezet által meghatározott] be a szervezeti incidensválasz-funkció.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél szoftver- és integritásának ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-7-14"></a>A NIST 800-53 vezérlő SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Szoftver, a belső vezérlőprogram és az adatintegritást |} Bináris vagy gépi végrehajtható kód

**SI-7 (14)** a szervezet nem engedélyezi a korlátozott vagy nem garancia és a forráskód; nélkül forrásból származó bináris vagy számítógép-végrehajtható kód használata, és biztosítja a forrás-kód követelmények csak a kötelező kritikus kivételek / működési követelmények és a engedélyező hivatalos jóváhagyásával.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer- és integritásának eljárások beszerzése a forráskód bináris vagy gépi végrehajtható kód néhány forrásból követelmények hozhat létre. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-8a"></a>A NIST 800-53 vezérlő SI-8.a

#### <a name="spam-protection"></a>Levélszemét-védelem

**SI-8.a** a szervezet alkalmazta a levélszemét védelmi mechanizmusok pontokon információk rendszer be- és kilépés észlelésére, és a művelet végrehajtása a kéretlen üzeneteket.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek a tervezetének részeként levelezési kiszolgálók. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-8b"></a>A NIST 800-53 vezérlő SI-8.b

#### <a name="spam-protection"></a>Levélszemét-védelem

**SI-8.b** a szervezet frissítések levélszemét védelmi mechanizmus Ha új kiadásokat szervezeti konfiguráció-kezelési házirend és eljárások érhetők el.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek a tervezetének részeként levelezési kiszolgálók. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-8-1"></a>A NIST 800-53 vezérlő SI-8 (1)

#### <a name="spam-protection--central-management"></a>Levélszemét-védelmi |} Központi kezelés

**(1) SI-8** a szervezet központilag kezeli a levélszemét védelmi mechanizmust.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek a tervezetének részeként levelezési kiszolgálók. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-si-8-2"></a>A NIST 800-53 vezérlő SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Levélszemét-védelmi |} Az automatikus frissítések

**(2) SI-8** az adatokat a rendszer automatikusan frissíti a levélszemét védelmi mechanizmust.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek a tervezetének részeként levelezési kiszolgálók. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-10"></a>A NIST 800-53 vezérlő SI 10-es

#### <a name="information-input-validation"></a>Információk bemeneti érvényesítése

**SI 10-es** az adatokat a rendszer ellenőrzi érvényességét [hozzárendelés: szervezet által megadott információk bemenet].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős információk bemenet-ellenőrzéshez ügyfél telepített erőforrásokat (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-11a"></a>A NIST 800-53 vezérlő SI-11.a

#### <a name="error-handling"></a>Hibakezelés

**SI-11.a** a információk rendszer által létrehozott hibaüzeneteket, amelyek adja meg a szükséges javítási műveleteket adatokat anélkül hogy felfedné ellenfelek kihasználható információkat.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrások kereskedelmi operációs rendszerek és alkalmazások alkalmaz. A szoftver ágazatban kialakult bevált gyakorlaton használatával győződjön meg arról, bizalmas információ nem látható a hibaüzenetekben látható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-11b"></a>A NIST 800-53 vezérlő SI-11.b

#### <a name="error-handling"></a>Hibakezelés

**SI-11.b** az információs rendszer felfedi hibaüzenetek csak a [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrások kereskedelmi operációs rendszerek és alkalmazások alkalmaz. A szoftver ágazatban kialakult bevált gyakorlaton használatával adja meg a hibaüzeneteket, amelyek a használatát, az üzenetet fogadó környezetében. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-12"></a>A NIST 800-53 vezérlő SI – 12

#### <a name="information-handling-and-retention"></a>Információk kezelésére és a megőrzési

**SI – 12** kezeli, és mindaddig megőrzi az adatokat a rendszer és a kimeneti adatainak megfelelően vonatkozó Szövetségi törvényeket, a rendszer a szervezet vezetői rendeléseket irányelvek, házirendek, szabványok, szabványokat, és működési követelményeinek.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős kezelésére, és az ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között), és ezeket az erőforrásokat a kimeneti adatainak adatainak megőrzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-si-16"></a>A NIST 800-53 vezérlő SI – 16

#### <a name="memory-protection"></a>Memóriavédelem

**SI-16** megvalósítja az adatokat a rendszer [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] az memória jogosulatlan kód végrehajtása elleni védelméhez.

**Feladatok:** `Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépek a tervezetének által telepített Windows operációs rendszert futtató. Windows rendelkezik védelem megakadályozza a kód végrehajtása korlátozott memória helyeken: nem hajtható végre (NX), a cím terület elrendezés Véletlenszerűsítésének (ASLR) és az Adatvégrehajtás megakadályozása (DEP). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |
