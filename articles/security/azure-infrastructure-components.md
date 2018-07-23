---
title: Az Azure information rendszerösszetevők és határok
description: Ez a cikk a Microsoft Azure-architektúra és a felügyeleti általános leírása.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b2e8ef232e1b25c7d000f4683830ff2e188047fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186476"
---
# <a name="azure-information-system-components-and-boundaries"></a>Az Azure information rendszerösszetevők és határok
Ez a cikk az Azure-architektúra és a felügyeleti általános leírása. A rendszer az Azure-környezet az alábbi hálózatok tevődik össze:

- A Microsoft Azure éles hálózati környezetben (az Azure-hálózat)
- A Microsoft vállalati hálózaton (vállalati hálózat)

Műveletek és karbantartás ezeket a hálózatokat felelős külön IT-csoportoknak.

## <a name="azure-architecture"></a>Azure-architektúra
Az Azure felhőalapú számítástechnikai platform és infrastruktúra létrehozását, telepítését, és kezelését alkalmazások és szolgáltatások adatközpontok hálózaton keresztül is. Ezek az adatközpontok a Microsoft felügyeli. A megadott erőforrások száma alapján, az Azure létrehozza a virtuális gépek (VM), erőforrás-igényei alapján. Ezek a virtuális gépek futtatását az Azure hipervizor, amely a felhőben, és nem érhető el a nyilvános.

Minden Azure fizikai kiszolgáló-csomóponton nincs, amely közvetlenül a hardver felett fut hipervizoron. A hipervizor a Vendég virtuális gépek változó számú csomópont osztja fel. Minden egyes csomópontot egy legfelső szintű virtuális gép, amely a gazda operációs rendszer fut is tartalmaz. Windows tűzfal engedélyezve van az egyes virtuális Gépeken. Meghatározhatja, hogy mely portok címezhető úgy konfigurálja a szolgáltatásdefiníciós fájlban. Ezeket a portokat csak azok a nyitott és címezhetővé válnak, belsőleg és külsőleg. Az összes forgalom és a lemez és a hálózati hozzáférést a hipervizor és a gyökérszintű operációs rendszer által közvetített.

A gazdagép rétegben futtassa egy testre szabott és a megerősített verzióját a legújabb Windows Server Azure virtuális gépeken. Az Azure, amely csak virtuális gépek gazdagépre szükséges összetevőket tartalmazza a Windows Server olyan verzióját használja. Ez növeli a teljesítményt, és csökkenti a támadási felületet. Gép határokat a hipervizor, amely nem függ az operációs rendszer biztonsági tartat be.

### <a name="azure-management-by-fabric-controllers"></a>Az Azure management by hálóvezérlők

Az Azure-ban (panelek/csomópontokat) fizikai kiszolgálókon futó virtuális gépek fürtök körülbelül 1000 vannak csoportosítva. A virtuális gépek egymástól függetlenül kezeli a hálóvezérlő (FC) nevű horizontálisan felskálázott és redundáns platform szoftverösszetevő.

Minden egyes FC a fürt, és kiosztja a futó alkalmazások életciklusát felügyeli, és az ellenőrzése alatt a hardver állapotát figyeli. Autonóm mozgások műveletek, például a Virtuálisgép-példányok kifogástalan állapotú kiszolgálók reincarnating, ha azt állapítja meg, hogy a kiszolgáló nem futtatja. Az FC is végez az alkalmazás-felügyeleti műveletek, például a központi telepítése, frissítése és alkalmazások kiterjesztése.

Az Adatközpont fürtök oszlik. Fürtök elkülönítése hibák az FC-szinten, és megakadályozza, hogy bizonyos osztályokat a hibák túl a fürt, amelyben történnek kiszolgálók érintő. FCs, amely egy adott Azure-fürtben kiszolgálása egy Szálcsatornás fürt vannak csoportosítva.

### <a name="hardware-inventory"></a>Hardverleltár

Az FC Azure hardver- és hálózati eszközöket leltárát előkészíti a rendszerindítási konfigurációs folyamat során. Minden olyan új hardver- és hálózati összetevők írja be az Azure éles környezetben a rendszerindítási konfigurációs eljárást kell követnie. Az FC feladata a teljes készlet datacenter.xml konfigurációs fájlban felsorolt kezelése.

### <a name="fc-managed-operating-system-images"></a>FC-felügyelt operációsrendszer-lemezképek

Az operációs rendszer csapat lemezképet, a képernyőn a virtuális merevlemezek, az összes gazdagép és Vendég virtuális gépek az Azure éles környezetben üzembe helyezett biztosít. A csapat egy offline automatizált összeállítási folyamatból keresztül ezek alaplemezképek hoz létre. Az alap rendszerképet az operációs rendszer a a kernel és egyéb alapvető összetevői rendelkezik lett módosítva, és az Azure-környezet támogatására optimalizált verziója is.

Háló által felügyelt operációsrendszer-lemezképek három típusa van:

- Gazdagép: Egy testreszabott operációs rendszert futtató virtuális gépek gazdagépen.
- Natív: Egy natív operációs rendszert futtat, a bérlők számára (például Azure Storage). Ez az operációs rendszer nem rendelkezik minden olyan hipervizorra.
- Vendég: A Vendég operációs rendszer, amely a Vendég virtuális gépeket.

A gazdagép és a natív FC-felügyelt operációs rendszereket a felhőben való használatra készültek, és nem nyilvánosan elérhető-e.

#### <a name="host-and-native-operating-systems"></a>Gazdagép és a natív operációs rendszerek

Gazdagép és a natív a háló ügynökök működtetésére, és a egy számítási csomóponton (fut, az első virtuális gép a csomóponton) és a tárolási csomópontok futó megerősített operációsrendszer-lemezképeket. Állomás és a natív optimalizált alaplemezképek használatának előnye, hogy csökkenti a támadási API-k vagy a nem használt összetevők által elérhetővé tett. Ezek nagy biztonsági kockázatot jelentenek, és növelje az erőforrás-igényű, az operációs rendszer. Csökkentett erőforrás-igényű operációs rendszerek csak az Azure-bA szükséges összetevőket tartalmazza.

#### <a name="guest-operating-system"></a>Vendég operációs rendszer

A vendég operációs rendszer virtuális gépeken futó Azure belső összetevőket Remote Desktop Protocol futtatásához nincs lehetőség van. A referenciakonfiguráció konfigurációs beállításokhoz módosításokat kell haladjon végig a módosítás, és a kibocsátási folyamat.

## <a name="azure-datacenters"></a>Azure-adatközpontok
A Microsoft felhő- és műveletek (MCIO) a fizikai infrastruktúra és az Adatközpont eszközökkel, a Microsoft online Services kezeli. MCIO felelős elsősorban az adatközpontokon belül a fizikai és környezeti vezérlők kezelése, valamint a kezelése és a külső szegélyhálózat-alapú hálózati eszközök (például a peremhálózati útválasztói és a datacenter útválasztók) támogatására. MCIO is feladata a minimális kiszolgálói operációs rendszer nélküli hardver az Adatközpont állványokra a beállítása. Nem Azure-ral való közvetlen kommunikáció az ügyfél rendelkezik.

## <a name="service-management-and-service-teams"></a>A szolgáltatások felügyelete és csoportok
Különböző mérnöki csoportok, csoportok, más néven a támogatást az Azure szolgáltatás kezelése. Minden egyes szolgáltatás csapat felelős Azure támogatása egy adott területre. Minden egyes szolgáltatás csapat vizsgálhatók és oldhatók meg a hibákat a szolgáltatásban elérhető 24 x 7 mérnökként kell tennie. Csoportokkal, alapértelmezés szerint nincs a hardvert, az Azure-ban működő fizikai hozzáférésre.

A csoportok a következők:

- Alkalmazásplatform
- Azure Active Directory
- Azure Compute
- Az Azure-háló
- Mérnöki csapathoz Felhőszolgáltatások
- ISSD: biztonság
- Multifactor Authentication (Többtényezős hitelesítés)
- SQL Database
- Storage

## <a name="types-of-users"></a>Felhasználók típusai
Az alkalmazottak (vagy alvállalkozók), a Microsoft kell tekinteni a belső felhasználók. Minden egyéb felhasználó külső felhasználók tekintendők. Minden Azure belső felhasználó rendelkezik, amely meghatározza a hozzáférése a vásárlói adatokhoz (hozzáférés vagy nincs hozzáférés) egy bizalmassági szintű kategorizált alkalmazott állapotukat. Az Azure-ba (engedélyezési engedély a hitelesítés végrehajtása után) a felhasználói jogosultságok az alábbi táblázat ismerteti:

| Szerepkör | Belső vagy külső | Érzékenységi szint | Engedélyezett jogosultságok és elvégzett feladatok | Hozzáférés típusa
| --- | --- | --- | --- | --- |
| Az Azure-adatközpont mérnök | Belső | Nincs hozzáférés a vásárlói adatokhoz | Kezelheti a helyszíni fizikai biztonságát. Tartson őrjáratok az Adatközpont-ból, és figyelheti az összes belépési pont. Általános szolgáltatások (például gyorsétkezés, vagy tisztítás) vagy az adatközponton belül informatikai munkahelyi személyzet kíséreti bA és az egyes adatközpont-ból nem törli. Végezhet rendszeres figyelése és karbantartása hálózati hardver. Incidensek kezelése és a javítás/csere munka elvégzéséhez különböző eszközök használatával. Végezhet rendszeres figyelése és karbantartása az adatközpontok fizikai hardver. Hozzáférés a lakástulajdonosok igény szerinti környezetet. Képes a törvényszéki vizsgálatokat végez, az incidensekkel kapcsolatos jelentéseket jelentkezzen és szükséges kötelező biztonsági képzés és a házirend követelményeinek. Működési tulajdonosi és a kritikus fontosságú eszközök, például a Képolvasók és naplógyűjtés karbantartását. | Állandó hozzáférést a környezethez. |
| Az Azure incidens osztályozási (gyors válasz mérnökök) | Belső | Hozzáférés a vásárlói adatokhoz | MCIO, a támogatást és a mérnöki munkacsoportok közötti kommunikáció felügyeletét. Osztályozási platform incidenseket, a üzembe helyezési problémák és a szolgáltatáskéréseket. | Igény a hozzáférést a nem vevő rendszerek korlátozott állandó hozzáférést a környezet. |
| Azure-beli mérnökök | Belső | Hozzáférés a vásárlói adatokhoz | Üzembe helyezése és frissítése a webplatform-összetevők, szoftverek és ütemezett konfigurációs módosítások Azure támogatásához. | Igény a hozzáférést a nem vevő rendszerek korlátozott állandó hozzáférést a környezet. |
| Azure-ügyfél szolgáltatáskimaradás támogatási (bérlő) | Belső | Hozzáférés a vásárlói adatokhoz | Hibakeresés és a platform leállásainak és a hibák diagnosztizálása az egyes számítási bérlők és az Azure-fiókokon. Hibák elemzése. Kritikus javításokat meghajtó platform vagy az ügyfélnek, és technikai fejlesztései átívelő, támogatást. | Igény a hozzáférést a nem vevő rendszerek korlátozott állandó hozzáférést a környezet. |
| (Figyelés mérnökök) élő webhelyet az Azure-mérnökök és incidens | Belső | Hozzáférés a vásárlói adatokhoz | Diagnosztizálásához és platform egészségügyi diagnosztikai eszközök használatával. Meghajtó a kötet illesztőprogramok esetén alkalmazott javítások, valamilyen okból kimaradás lép elemek javítása és szolgáltatáskimaradás helyreállítására szolgáló műveletek támogatásával segíti. | Igény a hozzáférést a nem vevő rendszerek korlátozott állandó hozzáférést a környezet. |
|Az Azure-ügyfelek | Külső | – | N/A | – |

Az Azure egyedi azonosítók használatával hitelesíti a szervezeti felhasználók és ügyfelek (vagy a szervezeti felhasználók nevében eljáró folyamatok). Ez vonatkozik az összes eszközre és az Azure-környezet részét képező eszközök.

### <a name="azure-internal-authentication"></a>Az Azure belső hitelesítés

Az Azure belső összetevői közötti kommunikáció a TLS-titkosítás védi. A legtöbb esetben önaláírt X.509-tanúsítványokat. Kivételt,-kapcsolatokkal, amelyek az Azure-hálózaton kívülről elérhető tanúsítványok, az FCs a tanúsítványokat is. FCs van egy Microsoft-Certificate a hitelesítésszolgáltató (CA), amely használatával a megbízható legfelső szintű hitelesítésszolgáltató által kibocsátott tanúsítványokat. Ez lehetővé teszi a nyilvános kulcsok FC egyszerűen kell leváltani. Ezenkívül a Microsoft fejlesztői eszközök FC nyilvános kulcsok használata. Amikor a fejlesztők új alkalmazás-lemezképek, rendszer titkosítja a képeket egy FC nyilvános kulccsal rendelkező beágyazott titkos kulcsok védelme érdekében.

### <a name="azure-hardware-device-authentication"></a>Azure-beli hardveres eszközhitelesítés

Az FC fenntart egy készletét használt hitelesítő adatokat (kulcsok és/vagy jelszavakat) önmaga hitelesítéséhez a különböző hardvereszközök az ellenőrzése alatt. A Microsoft ezekkel a hitelesítő adatokkal való hozzáférés letiltása a rendszert használja. Pontosabban az átviteli adatmegőrzés és használja ezeket az adatokat szolgál, hogy az Azure-fejlesztők számára, a rendszergazdák és a biztonsági mentési szolgáltatások és személyek hozzáférési bizalmas, bizalmas vagy titkos információkhoz.

A Microsoft az FC fő identitás nyilvános kulcs alapján titkosítást használ. Ez akkor történik, FC beállítása és az FC újrakonfigurálás időpontokban, a hálózati hardvereszközöket eléréséhez használt hitelesítő adatok átviteléhez. Az FC van szüksége a hitelesítő adatokat, amikor az FC kérdezi le, és visszafejti őket.

### <a name="network-devices"></a>Hálózati eszközök

Az Azure hálózatkezelésért felelős csapat úgy konfigurálja a hálózati szolgáltatás fiókok egy Azure-ügyfél hálózati eszközökre (útválasztók, kapcsolók és terheléselosztók) hitelesítést.

## <a name="secure-service-administration"></a>Biztonságos felügyeleti szolgáltatás
Az Azure műveletei csoporthoz kell használnia a biztonságos rendszergazdai munkaállomások (saw). Ügyfeleink az emelt hozzáférési szintű munkaállomások segítségével Megvalósíthat hasonló szabályozza. Az saw a felügyeleti munkatársak segítségével egy külön-külön hozzárendelt rendszergazdai fiók, amely elkülönül az a felhasználó általános jogú felhasználói fiókot. A SAW épül, amely a fiók elkülönítése eljárás megbízható munkaállomásokat azáltal, hogy ezeket a bizalmas fiókok esetében.

## <a name="next-steps"></a>További lépések
A Microsoft nem biztonságossá tétele az Azure-infrastruktúra kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
