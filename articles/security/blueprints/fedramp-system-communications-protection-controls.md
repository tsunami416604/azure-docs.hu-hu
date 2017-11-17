---
title: "FedRAMP Azure tervezetének Automation - rendszer és a kommunikáció védelme"
description: "Webalkalmazások FedRAMP – a rendszer és a kommunikáció védelme"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 059576e28b0b74700b5e4fbad06e74606c893af7
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.
    
    

# <a name="system-and-communications-protection-sc"></a>Rendszer és a kommunikáció védelme (SC)

## <a name="nist-800-53-control-sc-1"></a>A NIST 800-53 vezérlő SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Rendszer- és kommunikációs védelmi házirend- és eljárások

**SC-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy rendszer- és kommunikációs védelmi házirendje, amely célú, a hatókör, a szerepkörök, a feladatkörei, a felügyeleti megoldást előfizetési, koordinálásának szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a rendszer és a kommunikációs védelmi házirendet és a kapcsolódó rendszer és a kommunikáció védelmi vezérlők; végrehajtásának megkönnyítése ellenőrzi, és frissíti az aktuális rendszer és a kommunikáció védelmi házirend [hozzárendelés: szervezet által meghatározott gyakorisága]; és a rendszer és a kommunikáció védelmi eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer és a kommunikáció védelmi házirendet és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-2"></a>Az NIST 800-53 vezérlő SC-2.

#### <a name="application-partitioning"></a>Alkalmazás particionálás

**SC-2** az információs rendszer felhasználói funkciója (például a felhasználói felület szolgáltatások) elválasztja információk rendszer felügyeleti funkciót.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének elválasztja felhasználói funkció rendszer felügyeleti funkció a logikai hozzáférés-vezérlést és a rendszer-architektúra érvényesítése keresztül. Felhasználói funkció ügyfél telepített webes alkalmazás felületek korlátozódik. Rendszer-felügyeleti funkciók felületek nem azonosak a felhasználói felületek elembe. Minden felügyeleti kapcsolat biztonságos megerősített (jumpbox) található gazdagépet a hálózati biztonsági csoportszabályok felügyeleti alhálózat megfelelő éles erőforrásokhoz való hozzáférés korlátozása keresztül történik. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-3"></a>A NIST 800-53 vezérlő SC-3

#### <a name="security-function-isolation"></a>Biztonsági függvény elkülönítés

**SC-3** az információs rendszer elkülöníti a biztonsági funkciók a nem a biztonsággal kapcsolatos funkciók.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépeket az Azure tervezetének által telepített Windows operációs rendszer futtatását. Windows fenntartja a külön végrehajtási tartományok az egyes futó folyamatokhoz tartozó, egy titkos virtuális címtartomány hozzárendelése minden folyamat. Ezen felül a megoldás megvalósít egy architektúra és hozzáférési vezérlők úgy tervezték, hogy a biztonsági funkciók elkülönítése, amennyiben szükséges. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-4"></a>A NIST 800-53 vezérlő SC-4

#### <a name="information-in-shared-resources"></a>Megosztott információk

**SC-4** az adatokat a rendszer megakadályozza, hogy a jogosulatlan és a nem kívánt információkat átviteli keresztül megosztott rendszererőforrásokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépeket az Azure tervezetének által telepített Windows operációs rendszer futtatását. Az operációs rendszer kezeli az erőforrások (például memória, tároló) úgy, hogy az információk csak a felhasználók és a megfelelő engedélyekkel rendelkező szerepkörök érhető el. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-5"></a>A NIST 800-53 vezérlő SC-5

#### <a name="denial-of-service-protection"></a>Letiltja a védelmi szolgáltatás

**SC-5** az információs rendszer ellen, vagy korlátozza a következő típusú szolgáltatásmegtagadási támadások hatásainak: [hozzárendelés: szolgáltatásmegtagadási támadások, illetve az adatforrások ilyen adatokat való hivatkozások szervezet által meghatározott típusú] által alkalmazó [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti, amely tartalmazza a webalkalmazási tűzfal, és betölti a terheléselosztási képességeit Alkalmazásátjáró. Egy méretezhető rendelkezésre állási csoport webes réteg, adatbázis-rétegből, és az Active Directory támogatása a telepített virtuális gépek vannak telepítve. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-6"></a>A NIST 800-53 vezérlő SC-6

#### <a name="resource-availability"></a>Erőforrás-elérhetőséget

**SC-6** az információs rendszer erőforrások rendelkezésre állását védi lefoglalásával [hozzárendelés: szervezet által meghatározott erőforrások] által [kijelölés (egy vagy több); prioritás; kvóta; [Hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket]].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépeket az Azure tervezetének által telepített Windows operációs rendszer futtatását. Minden Windows-folyamat biztosít a program végrehajtásának szükséges erőforrásokat. Erőforrás-prioritásának az operációs rendszer kezeli. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-7a"></a>A NIST 800-53 vezérlő SC-7.a

#### <a name="boundary-protection"></a>Határ védelme

**SC-7.a** az információs rendszer figyelők és -vezérlők kommunikációs, a rendszer a külső határ és a kulcs belső határain belül a rendszer.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének Alkalmazásátjáró telepíti, terheléselosztóhoz, és konfigurálja a hálózati biztonsági csoportszabályok vezérlésére commutations határok külső és belső alhálózatok között. Engedélyezi az ügyfél figyelését az OMS Naplóelemzési által gyűjtött Alkalmazásátjáró, a terheléselosztóhoz, és a hálózati biztonsági csoport eseménye és a diagnosztikai naplókat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-7b"></a>A NIST 800-53 vezérlő SC-7.b

#### <a name="boundary-protection"></a>Határ védelme

**SC-7.b** a információkat rendszer megvalósítja az nyilvánosan elérhető rendszer összetevőinek alhálózatai [kijelölés: fizikailag; logikailag] különíteni a belső szervezeti hálózatokhoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózati architektúrát erőforrásokat. Alhálózatok logikailag el egymástól az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok közötti alkalmazott hálózati biztonsági csoportszabályok (pl., külső forgalom nem érik el az adatbázist, kezelési, vagy az Active Directory alhálózatok). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-7c"></a>A NIST 800-53 vezérlő SC-7.c

#### <a name="boundary-protection"></a>Határ védelme

**SC-7.c** az információs rendszer csatlakozik a külső hálózatokat vagy információs rendszerekkel használt csak az megfelelően egy szervezeti biztonsági architektúrája a határ Kulcsvédelmi eszközök álló felügyelt felületeken keresztül.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének ügyfél telepített webalkalmazás külső kapcsolatok kezelésére Alkalmazásátjáró telepíti. Külső kapcsolatok felügyeleti hozzáférés egy megerősített állomás korlátozódik, vagy a telepített felügyeleti alhálózatot a hálózati biztonsági szabály külső kapcsolatok jumpbox engedélyezett IP-címek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-3"></a>A NIST 800-53 vezérlő SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Határ védelmi |} Hozzáférési pontok

**SC-7 (3)** a szervezet az adatokat a rendszer a külső hálózati kapcsolatok számának korlátozása.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti két nyilvános IP-cím: egyet az Alkalmazásátjáró; társított a felügyeleti megerősített gazdagéphez tartozó egyik / jumpbox. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-4a"></a>A NIST 800-53 vezérlő SC-7 (4) sémaszintjén

#### <a name="boundary-protection--external-telecommunications-services"></a>Határ védelmi |} Külső távközlési szolgáltatások

**SC-7 (4) sémaszintjén** a szervezete bevezet egy felügyelt felület minden egyes külső telekommunikációs szolgáltatás.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti két nyilvános IP-cím: egyet az Alkalmazásátjáró; társított a felügyeleti megerősített gazdagéphez tartozó egyik / jumpbox. Szoftveralapú hálózatkezelés keresztül engedélyezve van a konfigurációkezelővel kezelését. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-4b"></a>A NIST 800-53 vezérlő SC-7 (4) .b

#### <a name="boundary-protection--external-telecommunications-services"></a>Határ védelmi |} Külső távközlési szolgáltatások

**SC-7 (4) .b** a szervezet hoz létre az egyes felügyelt felület forgalom folyamata szabályzat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti két nyilvános IP-cím: egyet az Alkalmazásátjáró; társított a felügyeleti megerősített gazdagéphez tartozó egyik / jumpbox. Szoftveralapú hálózatkezelés keresztül engedélyezve van a konfigurációkezelővel kezelését. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-4c"></a>A NIST 800-53 vezérlő SC-7 (4) .c

#### <a name="boundary-protection--external-telecommunications-services"></a>Határ védelmi |} Külső távközlési szolgáltatások

**SC-7 (4) .c** a szervezet a titkosítás és az egyes csatolókra között továbbított információk integritására – védi.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A webes alkalmazás átjáró által az Azure tervezetének telepített egy HTTPS figyelő, ebből következő titkosítás és a kommunikációs munkamenetek integritás van konfigurálva. Távoli asztali kapcsolatokat a jumpbox is titkosított bizalmas és sértetlenségét. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-4d"></a>A NIST 800-53 vezérlő SC-7 (4) .d

#### <a name="boundary-protection--external-telecommunications-services"></a>Határ védelmi |} Külső távközlési szolgáltatások

**SC-7 (4) .d** a szervezet minden kivételt a forgalom adatfolyam házirend támogató kritikus/üzleti igényeknek és időtartama, amelyen a kell dokumentumokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem felelősek datacenter-műveletek (távközlési tartalmaznak). Minden telekommunikációs szolgáltatás a megadott és a Microsoft Azure által kezelt. Ez a vezérlő az Azure-ból örökölt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-4e"></a>A NIST 800-53 vezérlő SC-7 (4) .e

#### <a name="boundary-protection--external-telecommunications-services"></a>Határ védelmi |} Külső távközlési szolgáltatások

**SC-7 (4) .e** A szervezet ellenőrzi, hogy a forgalom adatfolyam házirend kivételeit [hozzárendelés: szervezet által meghatározott gyakoriság], és eltávolítja a kivételeket, amelyek egy explicit kritikus/üzleti igények által már nem támogatott.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem felelősek datacenter-műveletek (távközlési tartalmaznak). Minden telekommunikációs szolgáltatás a megadott és a Microsoft Azure által kezelt. Ez a vezérlő az Azure-ból örökölt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-5"></a>A NIST 800-53 vezérlő SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Határ védelmi |} Alapértelmezés szerint megtagadási / által kivétel engedélyezése

**SC-7 (5)** az információk rendszernek felügyelt felületeket hálózati kommunikációs forgalom alapértelmezés szerint letiltja, és lehetővé teszi a hálózati kommunikációs forgalom kivétel (azaz az összes megtagadása, engedélyezik a kivétel).

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Alkalmazott hálózati biztonsági csoportokhoz az Azure tervezetének által telepített szabálykészletek egy megtagadási alapértelmezés szerint séma használatával vannak konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-7"></a>A NIST 800-53 vezérlő SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Határ védelmi |} A távoli eszközök vegyes Alagútkezelés tiltása

**SC-7 (7)** adatokat a rendszer, a távoli eszköz együtt megakadályozza, hogy az eszköz egyszerre a rendszer nem távoli kapcsolatok létrehozásáról és néhány más erőforrásokhoz a külső hálózatokat-kapcsolaton keresztül kommunikál.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű távoli eszközkonfigurációs szabályzatot foglalkozhat vegyes alagútkezelést. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-8"></a>A NIST 800-53 vezérlő SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Határ védelmi |} Irányíthatja a forgalmat a hitelesített Proxy kiszolgálókon

**SC-7 (8)** információk rendszerútvonalakra [hozzárendelés: szervezet által meghatározott belső kommunikációs forgalom] való [hozzárendelés: külső hálózatok szervezet által meghatározott] keresztül hitelesített proxykiszolgáló felügyelt felületeket.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős útválasztási ügyfél által megadott adatokat egy hitelesített proxykiszolgálót segítségével egy külső hálózathoz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-10"></a>A NIST 800-53 vezérlő SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Határ védelmi |} Megakadályozza a jogosulatlan Exfiltration

**SC-7 (10)** a szervezet megakadályozza, hogy az adatok illetéktelen exfiltration keresztül felügyelt felületeket.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős megakadályozza az adatok illetéktelen exfiltration keresztül felügyelt felületeket. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-12"></a>A NIST 800-53 vezérlő SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Határ védelmi |} Gazdagép-alapú védelem

**SC-7 (12)** a szervezet valósít meg [hozzárendelés: állomásalapú határ szervezet által meghatározott védelmi mechanizmus], [hozzárendelés: szervezet által megadott információk rendszerösszetevők].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által központilag telepített virtuális gépek vannak konfigurálva, a gazdagép alapú tűzfal engedélyezve van. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-13"></a>A NIST 800-53 vezérlő SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Határ védelmi |} Biztonsági eszközök izolálása / mechanizmusok / összetevőinek támogatásához

**SC-7 (13)** a szervezet elkülöníti [hozzárendelés: szervezet által megadott információk biztonsági eszközöket, a mechanizmusok, és a támogatási összetevőket] az egyéb belső információ rendszerösszetevők fizikailag megvalósításával az alhálózatokra külön a rendszer más összetevőitől felügyelt felületet.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti az ügyfél telepítését információk biztonsági eszközök és a támogatás összetevői különálló felügyeleti alhálózattal architektúrát erőforrásokat. Alhálózatok logikailag el hálózati biztonsági csoportszabályok egymástól. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-18"></a>A NIST 800-53 vezérlő SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Határ védelmi |} Biztonságos sikertelen

**SC-7 (18)** a információk rendszer biztonságosan sikertelen határ védelmi eszköz egy operatív meghibásodása esetén.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Nincsenek telepített Azure rendszer hatókörén belül fizikai határ védelmi eszközök. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure földrajzilag különálló és a redundáns-átjáró kiszolgálókon és az SSL VPN telepíti. Amikor egy átjáró rendszer sikertelen, biztonságosan nem sikerül, és hozzáférés a környezet korlátozott. Ahhoz, hogy a kapcsolatot a Microsoft Azure-környezethez, a felhasználó egy aktív Microsoft Azure által kezelt átjárókiszolgálóra külön kapcsolatot kell létesítenie. <br /> Emellett ha meghibásodik a Microsoft Azure hálózati eszközöket (beleértve a peremhálózati útválasztók, hozzáférési útválasztók, terheléselosztók, összesítési kapcsolók és TORS), az érintett áramkör válik csatlakozik, így biztonságosan sikertelen. A Microsoft Azure hálózati eszköz hibája nem vezethet, vagy adja meg az eszköz a rendszer a külső adatokat okozhat, és nem jogosulatlan információk kiadás engedélyez hibát. A beépített redundanciát lehetővé teszi, hogy a Microsoft Azure eszközök rendelkezésre állási befolyásolása nélkül sikertelen lesz. |


 ### <a name="nist-800-53-control-sc-7-20"></a>A NIST 800-53 vezérlő SC-7 (20.)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Határ védelmi |} Dinamikus elkülönítési / elkülönítése

**SC-7 (20)** az információs rendszer lehetővé teszi a dinamikusan izolátumának/elkülönítse [hozzárendelés: szervezet által megadott információk rendszerösszetevők] más összetevők, a rendszer.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél győződjön meg arról, hogy a rendszer rendelkezik-e a funkció dinamikusan elkülöníteni az ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-7-21"></a>A NIST 800-53 vezérlő SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Határ védelmi |} Az információk rendszerösszetevők elkülönítési

**SC-7 (21)** a szervezet védett határ védelmi mechanizmus külön [hozzárendelés: szervezet által megadott információk rendszerösszetevők] támogató [hozzárendelés: szervezet által meghatározott feladatok és/vagy üzleti funkciók].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózati architektúrát erőforrásokat. Alhálózatok logikailag elválasztott hálózati biztonsági csoport szabályokat az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok között. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-8"></a>A NIST 800-53 vezérlő SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Átviteli titkosítás és integritás

**SC-8** védi az adatokat a rendszer a [kijelölés (egy vagy több): bizalmas; integritási] az átvitt adatok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | ((1) végrehajtása SI-8 megfelel ennek a vezérlő követelménynek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-8-1"></a>A NIST 800-53 vezérlő SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Átviteli titkosítás és integritásának |} Kriptográfiai vagy egy másik fizikai védelem

**SC-8 (1)** a információk rendszer megvalósítja a titkosítási mechanizmus [kijelölés (egy vagy több): jogosulatlan információk közzétételének megakadályozása; információk módosításai észlelése] átvitel közben kivéve, ha más módon védi [hozzárendelése: szervezet által megadott alternatív fizikai védelmi szolgáltatás].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének konfigurálja az erőforrások csak biztonságos protokollok használatával kommunikálni. Az Alkalmazásátjáró WAF összetevőjét fogadjanak észlelésében külső használja a HTTPS és a TLS és a háttérkészlet csak a HTTPS és a TLS protokollt használó kommunikációra van konfigurálva. SQL Server csak a HTTPS és a TLS protokollt használó kommunikációra van konfigurálva. Távoli asztali szolgáltatások biztonságos kapcsolatok használatára van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-10"></a>A NIST 800-53 vezérlő SC-10

#### <a name="network-disconnect"></a>Hálózati kapcsolat bontása

**SC-10** az adatokat a rendszer megszakítja a hálózati kapcsolatot, a kommunikációs munkamenet befejezése után a munkamenet vagy után társított [hozzárendelés: szervezet által meghatározott időszak] tétlen.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Távoli asztali munkamenetekhez hitelesítés Active Directory kezeli. Ha az Active Directory felhasználó számára le van tiltva a hozzáférés, távoli munkamenetek azonnal megszűnik. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-12"></a>A NIST 800-53 vezérlő SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Titkosítási kulcs létrehozása és kezelése

**SC-12** a szervezet hoz létre és kezeli a titkosítási kulcsokat az alkalmazásban lévő összhangban az információs rendszer szükséges titkosítási [hozzárendelés: a kulcsok létrehozásának, terjesztési, szervezet által meghatározott követelmények tárolási, hozzáférési és megsemmisítése].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének egy Azure Key Vault telepíti. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault 140-2 2. szint hardveres biztonsági modul (HSM) Kulcslétrehozási funkció hozhat létre a kulcsokat a FIPS használatával. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-12-1"></a>A NIST 800-53 vezérlő SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Titkosítási kulcs létrehozásához és felügyeleti |} Rendelkezésre állás

**SC-12 (1)** a szervezeti felhasználók titkosítási kulcsok a adatvesztés információk tart fenn.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Key Vault fogja tárolni a titkosítási kulcsok és titkos az Azure tervezetének szerepel. Key Vault leegyszerűsíti a kulcskezelési folyamatot, a kulcsok feletti titkosítsa az adatokat. A következő hitelesítők Key Vault vannak tárolva: az Azure-telepítés fiók, a virtuális gép rendszergazdai jelszót, az SQL Server szolgáltatásfiók jelszavát a jelszó. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-12-2"></a>A NIST 800-53 vezérlő SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Titkosítási kulcs létrehozásához és felügyeleti |} Szimmetrikus kulcsok

**SC-12 (2)** a szervezet hoz létre, vezérli, és elosztja a szimmetrikus titkosítási kulcsok használatával [kijelölés: NIST FIPS-kompatibilis; Kulcskezelés NSA jóváhagyott] technológia és a folyamatok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Key Vault segítségével eredményez, vezérlését és terjesztheti kriptográfiai kulcsokat. Az Azure Key Vault 140-2 2. szint hardveres biztonsági modul (HSM) Kulcslétrehozási funkció hozhat létre a kulcsokat a FIPS használatával. Kulcsok tárolja és kezeli az Azure Key Vault biztonságosan titkosított tárolókra belül. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-12-3"></a>A NIST 800-53 vezérlő SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Titkosítási kulcs létrehozásához és felügyeleti |} Aszimmetrikus kulcsok

**SC-12 (3)** a szervezet hoz létre, vezérli, és elosztja az aszimmetrikus titkosítási kulcsok használatával [kijelölés: NSA jóváhagyott kulcsfontosságú technológia és a folyamatok; jóváhagyott osztály 3 PKI-tanúsítványok vagy elhelyezett kulcskezelő anyagokat; nyilvános kulcsokra épülő infrastruktúra osztály 3 vagy 4 osztály tanúsítványokat és a hardveres biztonsági jogkivonatokat, amelyek a felhasználó titkos kulcs védelme jóváhagyott].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős előállító, szabályozása és terjesztése az aszimmetrikus titkosítási kulcsok, (ha azokat használja felhasználói telepített erőforrások belül). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-13"></a>A NIST 800-53 vezérlő SC-13

#### <a name="cryptographic-protection"></a>Titkosítási védelem

**SC-13** megvalósítja az adatokat a rendszer [hozzárendelés: szervezet által meghatározott kriptográfiai használ, és minden szükséges titkosítási típus] vonatkozó Szövetségi törvényeket, végrehajtó rendelések irányelveknek, házirendek, szabványok – megfelelően és szabványnak.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Windows-hitelesítést, a távoli asztal és a BitLocker által az Azure tervezetének kell használni. Ezek az összetevők beállítható úgy, hogy a titkosítási modulok FIPS 140 érvényesítése támaszkodnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-15a"></a>A NIST 800-53 vezérlő SC-15.a

#### <a name="collaborative-computing-devices"></a>Együttműködési számítástechnikai eszközök

**SC-15.a** az információs rendszer ez a beállítás letiltja a Távoli aktiválás együttműködési számítási eszközök az alábbi kivételekkel: [hozzárendelés: Távoli aktiválás esetén engedélyezni kell a szervezet által meghatározott kivételek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek telepítve az Azure tervezetének részeként együttműködési számítási eszközök. Megjegyzés: Fizikai együttműködési eszközök telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-15b"></a>A NIST 800-53 vezérlő SC-15.b

#### <a name="collaborative-computing-devices"></a>Együttműködési számítástechnikai eszközök

**SC-15.b** a információt rendszer biztosít explicit utalhat, hogy használja a felhasználók fizikailag jelen az eszközök számára.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek telepítve az Azure tervezetének részeként együttműködési számítási eszközök. Megjegyzés: Fizikai együttműködési eszközök telepített Azure rendszer hatókörén belül van. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-17"></a>A NIST 800-53 vezérlő SC-17

#### <a name="public-key-infrastructure-certificates"></a>Nyilvános kulcsú infrastruktúra tanúsítványait

**SC-17** a nyilvános kulcsú tanúsítványokat állít ki a szervezet egy [hozzárendelés: szervezet által meghatározott tanúsítvány-házirend] vagy jut hozzá a nyilvános kulcsú tanúsítványokat egy jóváhagyott-szolgáltatótól.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy vállalati szintű nyilvános kulcsokra épülő infrastruktúra a tanúsítványok kiállítását hivatkozhatnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-18a"></a>A NIST 800-53 vezérlő SC-18.a

#### <a name="mobile-code"></a>Mobil kódot

**SC-18.a** a szervezet elfogadható és elfogadhatatlan mobil kódnak és a mobil kódnak technológiák határozza meg.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer és a kommunikációs védelmi eljárások határozhatnak meg elfogadható és elfogadhatatlan mobil kódot. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-18b"></a>A NIST 800-53 vezérlő SC-18.b

#### <a name="mobile-code"></a>Mobil kódot

**SC-18.b** a szervezet létesít végrehajtási útmutató elfogadható mobil kódnak és a mobil kódnak technológiák és használattal kapcsolatos korlátozásokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű rendszer és a kommunikációs védelmi eljárások mobil kódnak használatának korlátozásai hozhat létre. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-18c"></a>A NIST 800-53 vezérlő SC-18.c

#### <a name="mobile-code"></a>Mobil kódot

**SC-18.c** a szervezet engedélyezi, figyeli, és információt rendszerből mobil kódnak használatát szabályozza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy vállalati szintű folyamat engedélyezési, megfigyelésének és mobil kódnak használatát irányítását. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-19a"></a>A NIST 800-53 vezérlő SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.a** a szervezet használattal kapcsolatos korlátozások és megvalósítási útmutató a hang / Internet Protocol (VoIP) technológiák az információs rendszer való megosztása kárt okozhat, ha ártó szándékkal használja alapján hoz létre.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek nincsenek hang / internet protocol technológiák telepítése az Azure tervezetének részeként. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-19b"></a>A NIST 800-53 vezérlő SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.b** a szervezet engedélyezi, figyeli, és információt rendszerből VoIP használatát szabályozza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek nincsenek hang / internet protocol technológiák telepítése az Azure tervezetének részeként. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-20a"></a>A NIST 800-53 vezérlő SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Biztonságos neve / cím névfeloldási szolgáltatás (mérvadó forrás)

**SC-20.a** az információs rendszer biztosít további adatok forrása hitelesítési és sértetlenségének ellenőrzése összetevők a mérvadó névfeloldási adatokat a rendszer külső nevét és címét névfeloldási lekérdezéseket válaszul adja vissza.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős egy biztonságos nevét és címét névfeloldási szolgáltatás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-20b"></a>A NIST 800-53 vezérlő SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Biztonságos neve / cím névfeloldási szolgáltatás (mérvadó forrás)

**SC-20.b** az információs rendszer lehetővé teszi a gyermekzónákhoz és (Ha a gyermek támogatja a biztonságos névfeloldási szolgáltatást) biztonsági állapotának ellenőrzése a szülő és gyermek tartományok közötti megbízhatósági láncot engedélyezésére, amikor részeként működő elosztott, hierarchikus névtér.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős egy biztonságos nevét és címét névfeloldási szolgáltatás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-21"></a>A NIST 800-53 vezérlő SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Biztonságos neve / cím (rekurzív vagy gyorsítótárazása) névfeloldási szolgáltatás

**SC-21** az információs rendszer kér, és a nevét és címét feloldási válaszokat a rendszer kap mérvadó forrásból származó adatok eredetének hitelesítését és a rendszerintegritás-ellenőrzést végez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél le tudja kérni és az adatok eredetének hitelesítését és az adatok integritásának ellenőrzését végre nevét és címét feloldási válaszok mérvadó forrásból származó ügyfél telepített erőforrások konfigurálása az felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-22"></a>A NIST 800-53 vezérlő SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektúra és a kiépítés neve / cím névfeloldási szolgáltatás

**SC-22** az információs rendszerekkel használt, amelyek együttesen biztosítanak a szervezet nevét és címét névfeloldási szolgáltatás hibatűrő, és a belső/külső Szerepszétválasztás megvalósításához.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős győződjön meg arról, hogy a cím ügyfél telepített erőforrások névfeloldási szolgáltatásokat nyújtó rendszerek hibatűrő, és a belső/külső Szerepszétválasztás megvalósításához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-23"></a>A NIST 800-53 vezérlő SC-23

#### <a name="session-authenticity"></a>Munkamenet eredetiséget

**SC-23** az adatokat a rendszer kommunikációs munkamenetek hitelességének védelmet nyújt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure-portálon, a távoli asztali kapcsolat és a webes alkalmazás átjáró, beleértve az Azure Blueprint által telepített erőforrásokhoz való távoli hozzáférés biztosított TLS használatával. TLS eredetiséget biztosít a munkamenet szintű kommunikáció. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-23-1"></a>A NIST 800-53 vezérlő SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Munkamenet eredetiséget |} Jelentkezzen ki a munkamenet-azonosítók érvénytelenné válnak

**SC-23 (1)** az információs rendszer érvényteleníti a munkamenet-azonosítók felhasználó kijelentkezik vagy más munkamenet befejezését követően.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure-portálon, a távoli asztali kapcsolat és a webes alkalmazás átjáró, beleértve az Azure Blueprint által telepített erőforrásokhoz való távoli hozzáférés biztosított TLS használatával. Az Azure portál és a távoli asztali munkamenetekhez érvénytelenné válnak a munkamenet-azonosítók után jelentkezzen ki. Webes munkamenet érvénytelenítési Azure Application Gateway - webes alkalmazás tűzfalat (WAF) szabályok ki. A WAF hajtja végre a munkamenet időkorlátja 30 perc (szervezet egyedi szabályok konfigurálható feladás egy vagy több központi telepítési) az ügyfélről inaktivitás után, és a cookie-k affinitás munkamenetenként vonatkozik. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-24"></a>A NIST 800-53 vezérlő SC-24

#### <a name="fail-in-known-state"></a>Ismeretlen állapotú sikertelen

**SC-24** az adatokat a rendszer nem tudja a [hozzárendelés: szervezet által meghatározott ismert-állapot] a(z) [hozzárendelés: hibák szervezet által meghatározott típusú] megőrzi az [hozzárendelés: szervezet által meghatározott állapotadatairól] nem.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél egy ismert állapotban ügyfél telepített erőforrások sikertelen biztosításáért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-28"></a>A NIST 800-53 vezérlő SC-28

#### <a name="protection-of-information-at-rest"></a>Adatok inaktív védelme

**SC-28** védi az adatokat a rendszer a [kijelölés (egy vagy több): bizalmas; integritási], [hozzárendelés: szervezet által megadott információk aktívan].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | SC-28 (1) végrehajtása megfelel ennek a vezérlő követelménynek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-sc-28-1"></a>A NIST 800-53 vezérlő SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Inaktív adatok védelmének |} Titkosítási védelem

**SC-28 (1)** a információk rendszer megvalósítja a jogosulatlan és módosításának megakadályozása érdekében titkosítási mechanizmus [hozzárendelés: szervezet által megadott információk] az [hozzárendelés: szervezet által megadott információk rendszer összetevők].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által központilag telepített virtuális gépek valósítja meg az adatok titkosítása a titkosítás és integritásának inaktív adatok védelmét. A Windows Azure lemez titkosítása a BitLocker Windows-szolgáltatás segítségével van megvalósítva. SQL Server átlátszó adatok titkosítás (TDE), amely végrehajtja a valós idejű titkosítási és visszafejtési adatok és naplófájlok nyugalmi információk védelme érdekében van konfigurálva. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet. Ügyfél választhatja azt, hogy a tárolt adatok integritását a további alkalmazásszintű vezérlőelemeket kell megvalósítani. Az Azure tervezetének által telepített összes tárolási BLOB sértetlenségét és bizalmasságát használata Azure Storage szolgáltatás titkosítási (SSE) védett. SSE megvédi az Azure storage-fiókok, 256 bites AES titkosítással belül inaktív adatok. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-sc-39"></a>A NIST 800-53 vezérlő SC-39

#### <a name="process-isolation"></a>Folyamat elkülönítési

**SC-39** az adatokat a rendszer fenntartja az összes végrehajtás alatt álló folyamat külön végrehajtási tartományhoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A virtuális gépeket az Azure tervezetének által telepített Windows operációs rendszer futtatását. Windows fenntartja a külön végrehajtási tartományok az egyes futó folyamatokhoz tartozó, egy titkos virtuális címtartomány hozzárendelése minden folyamat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |
