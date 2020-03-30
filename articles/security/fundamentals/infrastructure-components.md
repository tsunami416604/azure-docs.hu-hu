---
title: Az Azure információs rendszer összetevői és határai
description: Ez a cikk a Microsoft Azure architektúrájának és felügyeletének általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727201"
---
# <a name="azure-information-system-components-and-boundaries"></a>Az Azure információs rendszer összetevői és határai
Ez a cikk az Azure architektúrájának és felügyeletének általános leírását tartalmazza. Az Azure rendszerkörnyezet a következő hálózatokból áll:

- Microsoft Azure éles hálózat (Azure-hálózat)
- Microsoft vállalati hálózat (corpnet)

E hálózatok üzemeltetéséért és karbantartásáért külön informatikai csapatok felelősek.

## <a name="azure-architecture"></a>Azure architektúra
Az Azure egy felhőalapú számítástechnikai platform és infrastruktúra az alkalmazások és szolgáltatások létrehozásához, üzembe helyezéséhez és kezeléséhez adatközpontok hálózatán keresztül. A Microsoft kezeli ezeket az adatközpontokat. A megadott erőforrások száma alapján az Azure virtuális gépeket (VM-eket) hoz létre az erőforrás-szükséglet alapján. Ezek a virtuális gépek egy Azure-hipervizoron futnak, amelyet a felhőben való használatra terveztek, és nem érhető el a nyilvánosság számára.

Minden Azure fizikai kiszolgálócsomóponton van egy hipervizor, amely közvetlenül fut a hardveren. A hipervizor egy csomópontot változó számú vendég virtuális gépre oszt. Minden csomópont nak van egy gyökér virtuális gépe is, amely a gazdaoperációs rendszert futtatja. A Windows tűzfal minden virtuális gépen engedélyezve van. A szolgáltatásdefiníciós fájl konfigurálásával meghatározhatja, hogy mely portok címezhetők meg. Ezek a portok az egyetlenek, nyitott és címezhető, belső vagy külső. A lemezhez és a hálózathoz való minden forgalmat és hozzáférést a hipervizor és a gyökér operációs rendszer közvetít.

A gazdarétegen az Azure virtuális gépek a legújabb Windows Server testreszabott és megerősített verzióját futtatják. Az Azure a Windows Server olyan verzióját használja, amely csak a virtuális gépek üzemeltetéséhez szükséges összetevőket tartalmazza. Ez javítja a teljesítményt és csökkenti a támadási felületet. A számítógép határait a hipervizor kényszeríti, ami nem függ az operációs rendszer biztonságától.

### <a name="azure-management-by-fabric-controllers"></a>Azure-kezelés hálóvezérlők által

Az Azure-ban a fizikai kiszolgálókon (blades/csomópontokon) futó virtuális gépek körülbelül 1000 fürtcsoportokba vannak csoportosítva. A virtuális gépeket egymástól függetlenül kezeli egy kibővített és redundáns platformszoftver-összetevő, a fabric controller (FC).

Minden FC kezeli a fürtben futó alkalmazások életciklusát, és az ellenőrzése alatt álló hardver állapotát felügyeli és figyeli. Ez fut vegetatív műveletek, például a virtuális gép példányok reincarnating kifogástalan kiszolgálókon, amikor megállapítja, hogy a kiszolgáló nem sikerült. Az FC alkalmazáskezelési műveleteket is hajt végre, például alkalmazások üzembe helyezését, frissítését és horizontális felskálázását.

Az adatközpont fürtökre van osztva. A fürtök fc szinten elkülönítik a hibákat, és megakadályozzák, hogy bizonyos hibaosztályok azon a fürtön kívüli kiszolgálókat érintenek, amelyben előfordulnak. Egy adott Azure-fürtöt kiszolgáló FC-fürtök fc-fürtbe vannak csoportosítva.

### <a name="hardware-inventory"></a>Hardverleltár

Az FC leltárt készít az Azure hardver- és hálózati eszközeiről a rendszerindítási konfigurációs folyamat során. Az Azure éles környezetébe belépő új hardver- és hálózati összetevőknek követniük kell a rendszerindítási konfigurációs folyamatot. Az FC felelős az adatcenter.xml konfigurációs fájlban felsorolt teljes készlet kezeléséért.

### <a name="fc-managed-operating-system-images"></a>FC által felügyelt operációs rendszer lemezképei

Az operációs rendszer csapata az Azure éles környezetében az összes gazdagépre és vendég virtuális gépre telepített lemezképeket biztosít virtuális merevlemezek formájában. A csapat ezeket az alapképeket egy automatikus offline létrehozási folyamaton keresztül készíti el. Az alaplemezkép az operációs rendszer egy olyan verziója, amelyben a kernel és más alapvető összetevők módosultak és optimalizáltak az Azure-környezet támogatása érdekében.

A hálóáltal felügyelt operációsrendszer-lemezképeknek három típusa van:

- Állomás: A gazdagép virtuális gépeken futó testreszabott operációs rendszer.
- Natív: A natív operációs rendszer, amely fut a bérlők (például az Azure Storage). Ennek az operációs rendszernek nincs hipervizora.
- Vendég: Vendég operációs rendszer, amely a vendég virtuális gépeken fut.

A gazdagép és a natív FC által felügyelt operációs rendszerek a felhőben való használatra vannak tervezve, és nyilvánosan nem érhetők el.

#### <a name="host-and-native-operating-systems"></a>Gazda- és natív operációs rendszerek

A gazdagép és a natív megerősített operációsrendszer-lemezképek, amelyek a hálóügynököket üzemeltetik, és egy számítási csomóponton (a csomóponton első virtuális gépként fut) és tárolócsomópontokon futnak. Az állomás és a natív optimalizált alaplemezképek használatának előnye, hogy csökkenti az API-k vagy a nem használt összetevők által kitett felületet. Ezek magas biztonsági kockázatot jelenthetnek, és növelhetik az operációs rendszer lábnyomát. A csökkentett helyigényű operációs rendszerek csak az Azure-hoz szükséges összetevőket tartalmazzák.

#### <a name="guest-operating-system"></a>Vendég operációs rendszer

A vendég operációs rendszer virtuális gépein futó Azure belső összetevőinek nincs lehetősége a Távoli asztali protokoll futtatására. Az alapkonfigurációs konfigurációs beállítások módosításait végig kell menni a módosítási és kiadási kezelési folyamaton.

## <a name="azure-datacenters"></a>Azure adatközpontok
A Microsoft Cloud Infrastructure and Operations (MCIO) csapata kezeli a Microsoft összes online szolgáltatásának fizikai infrastruktúráját és adatközponti létesítményeit. Az MCIO elsősorban az adatközpontokon belüli fizikai és környezeti vezérlők kezeléséért, valamint a külső peremhálózati eszközök (például peremhálózati útválasztók és adatközpont-útválasztók) kezeléséért és támogatásáért felelős. Az MCIO felelős az adatközpont állványain lévő minimális kiszolgálóhardver beállításáért is. Az ügyfelek nem folytatnak közvetlen interakciót az Azure-ral.

## <a name="service-management-and-service-teams"></a>Szolgáltatáskezelő és szervizcsapatok
Különböző mérnöki csoportok, más néven a szolgáltatási csapatok, az Azure-szolgáltatás támogatását kezelik. Minden szolgáltatási csapat felelős az Azure támogatási területért. Minden szervizcsapatnak a hét minden nap 24 órájában elérhetővé kell tennie egy mérnököt a szolgáltatás hibáinak kivizsgálásához és megoldásához. A szervizcsapatok alapértelmezés szerint nem rendelkeznek fizikai hozzáféréssel az Azure-ban működő hardverhez.

A szervizcsapatok a következők:

- Alkalmazási platform
- Azure Active Directory
- Azure Compute
- Azure Net
- Felhőmérnöki szolgáltatások
- ISSD: Biztonság
- Multifactor Authentication (Többtényezős hitelesítés)
- SQL Database
- Storage

## <a name="types-of-users"></a>Felhasználók típusai
A Microsoft alkalmazottai (vagy alvállalkozói) belső felhasználónak minősülnek. Az összes többi felhasználó külső felhasználónak minősül. Minden Azure belső felhasználók az alkalmazotti állapot kategorizálva egy érzékenységi szint, amely meghatározza az ügyféladatokhoz való hozzáférés (hozzáférés vagy nincs hozzáférés). Az Azure felhasználói jogosultságait (hitelesítés után engedélyezési engedély) az alábbi táblázat ismerteti:

| Szerepkör | Belső vagy külső | Érzékenységi szint | Engedélyezett jogosultságok és végrehajtott funkciók | Hozzáférés típusa
| --- | --- | --- | --- | --- |
| Az Azure adatközpont mérnöke | Belső | Nincs hozzáférés az ügyféladatokhoz | A helyiségek fizikai biztonságának kezelése. Járőrözést végezhet az adatközpontba, és figyelheti az összes belépési pontot. Az adatközpontba be- és kikísérhetnek bizonyos nem engedélyezett személyzetet, akik általános szolgáltatásokat (például étkezést vagy takarítást) vagy az adatközponton belüli informatikai munkát nyújtanak. A hálózati hardverek rendszeres monitorozása és karbantartása. Számos eszköz használatával végezhetincidens-kezelést és hibajavítást. Végezzen rutinfelügyeletet és a fizikai hardver karbantartását az adatközpontokban. Hozzáférés a környezethez igény szerint az ingatlantulajdonosoktól. Alkalmas törvényszéki vizsgálatok elvégzésére, incidensjelentések naplózására, valamint kötelező biztonsági képzés re és szakpolitikai követelmények előírására. A kritikus biztonsági eszközök, például a képolvasók és a naplógyűjtés üzemeltetési tulajdonjoga és karbantartása. | Állandó hozzáférés a környezethez. |
| Azure-incidensek osztályozása (gyorsreagálású mérnökök) | Belső | Hozzáférés az ügyféladatokhoz | Az MCIO, a támogatási és a mérnöki csapatok közötti kommunikáció kezelése. A platformincidensek, a telepítési problémák és a szolgáltatási kérelmek osztályozása. | Just-in-time hozzáférés a környezethez, korlátozott állandó hozzáférést biztosít a nem ügyfél rendszerek. |
| Az Azure üzembe helyezési mérnökei | Belső | Hozzáférés az ügyféladatokhoz | Az Azure-t támogató platform-összetevőket, szoftvereket és ütemezett konfigurációs módosításokat telepítheti és frissítheti. | Just-in-time hozzáférés a környezethez, korlátozott állandó hozzáférést biztosít a nem ügyfél rendszerek. |
| Az Azure-ügyfelek kimaradási támogatása (bérlő) | Belső | Hozzáférés az ügyféladatokhoz | Hibakeresés és a platform kimaradások és hibák az egyes számítási bérlők és az Azure-fiókok. Elemezzük a hibákat. A platformra vagy az ügyfélre mutató kritikus javításokat, valamint a teljes körű technikai fejlesztéseket. | Just-in-time hozzáférés a környezethez, korlátozott állandó hozzáférést biztosít a nem ügyfél rendszerek. |
| Az Azure élő webhelymérnökei (monitoring mérnökök) és az incidensek | Belső | Hozzáférés az ügyféladatokhoz | Diagnosztizálhatja és csökkentheti a platform állapotát diagnosztikai eszközök használatával. Meghajtó javítások kötet-illesztőprogramok, javítási elemek eredő kimaradások, és segít kimaradás helyreállítása műveleteket. | Just-in-time hozzáférés a környezethez, korlátozott állandó hozzáférést biztosít a nem ügyfél rendszerek. |
|Azure-ügyfelek | Külső | N/A | N/A | N/A |

Az Azure egyedi azonosítókat használ a szervezeti felhasználók és az ügyfelek (vagy a szervezeti felhasználók nevében eljáró folyamatok) hitelesítésére. Ez az Azure-környezet részét képezi összes eszközre és eszközre vonatkozik.

### <a name="azure-internal-authentication"></a>Azure belső hitelesítés

Az Azure belső összetevői közötti kommunikáció TLS titkosítással védett. A legtöbb esetben az X.509 tanúsítványok önaláírtak. Az Azure-hálózaton kívülről elérhető kapcsolatokkal rendelkező tanúsítványok kivételt képeznek, csakúgy, mint az FCs-ek tanúsítványai. Az fc-k olyan Microsoft hitelesítésszolgáltató (CA) által kiállított tanúsítványokkal rendelkeznek, amelyet egy megbízható legfelső szintű hitelesítésszolgáltató támogat. Ez lehetővé teszi az FC nyilvános kulcsok könnyen átgörgethető. Ezenkívül a Microsoft fejlesztői eszközei FC nyilvános kulcsokat használnak. Amikor a fejlesztők új alkalmazásképeket küldenek be, a rendszerképek egy FC nyilvános kulccsal vannak titkosítva a beágyazott titkos kulcsok védelme érdekében.

### <a name="azure-hardware-device-authentication"></a>Azure hardvereszköz-hitelesítés

Az FC hitelesítő adatokat (kulcsokat és/vagy jelszavakat) tart fenn, amelyek az ellenőrzése alatt álló különböző hardvereszközök hitelesítéséhez szolgálnak. A Microsoft egy rendszert használ a hitelesítő adatokhoz való hozzáférés megakadályozására. Pontosabban, a szállítás, a megőrzési és a hitelesítő adatok használata célja, hogy megakadályozza az Azure-fejlesztők, rendszergazdák és a biztonsági mentési szolgáltatások és a személyzet bizalmas, bizalmas vagy személyes adatokhoz való hozzáférését.

A Microsoft titkosítást használ az FC fő identitásának nyilvános kulcsa alapján. Ez az FC beállítási és fc-újrakonfigurálási ideje alatt történik, a hálózati hardvereszközök eléréséhez használt hitelesítő adatok átviteléhez. Amikor az FC-nek szüksége van a hitelesítő adatokra, az FC lekéri és visszafejti azokat.

### <a name="network-devices"></a>Hálózati eszközök

Az Azure hálózati csapat konfigurálja a hálózati szolgáltatás fiókok lehetővé teszi az Azure-ügyfél hitelesítését a hálózati eszközök (útválasztók, kapcsolók és terheléselosztók).

## <a name="secure-service-administration"></a>Biztonságos szolgáltatásfelügyelet
Az Azure-műveletek személyzetének biztonságos rendszergazdai munkaállomások (SAWs) használatához kell használnia. Az ügyfelek hasonló vezérlőket valósíthatnak meg emelt szintű hozzáférésű munkaállomások használatával. A licenciarendszerű járművek esetén a felügyeleti személyzet a felhasználó általános jogú felhasználói fiókjától elkülönülő, egyénileg hozzárendelt felügyeleti fiókot használ. A SAW a fiókelkülönítési gyakorlatra épül azáltal, hogy megbízható munkaállomást biztosít az ilyen bizalmas fiókok számára.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
