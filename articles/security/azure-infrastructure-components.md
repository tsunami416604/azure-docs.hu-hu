---
title: Az Azure információk rendszerösszetevők és határok
description: Ez a cikk a Microsoft Azure-architektúra és felügyeleti általános leírását tartalmazza.
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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102337"
---
# <a name="azure-information-system-components-and-boundaries"></a>Az Azure információk rendszerösszetevők és határok
Ez a cikk a Microsoft Azure-architektúra és felügyeleti általános leírását tartalmazza. A rendszer az Azure környezetben a következő hálózatok tevődik össze:

- A Microsoft Azure éles hálózattól (Azure-hálózat)
- Microsoft Corporate hálózaton (vállalati hálózat)

Külön informatikai csapata hozza létre a tevékenységek és karbantartás az Azure-hálózat és a vállalati hálózat hálózatok felelősek.

## <a name="azure-architecture"></a>Azure architektúra
Microsoft Azure a felhő számítási platform és infrastruktúra létrehozása, telepítése, és kezelését alkalmazások és szolgáltatások a Microsoft által kezelt adatbázisok hálózaton keresztül. Az ügyfelek által megadott erőforrások számától függően a Azure virtuális gépek erőforrás igények alapján hoz létre. Futtassa a Microsoft Azure hipervizor, amely az a virtuális gépeken használja a felhőben, és nem érhető el a nyilvánosság számára.

Minden Azure fizikai kiszolgáló-csomóponton nincs olyan Hipervizorra, amely közvetlenül a hardver felett fut. Hipervizor felosztja egy csomópont egy Vendég virtuális gépek (VM) változó számú. Minden csomópont is rendelkezik egy különös "Gyökér" virtuális gép, a gazda operációs Rendszerével futtató. A Windows tűzfal engedélyezve van minden egyes virtuális gépen. A csak nyitott és megcímezhető, belső vagy külső, portjait explicit módon definiálva az a szolgáltatásdefiníciós fájlból, az ügyfél által konfigurált portokat. Minden forgalom, és a lemezek és a hálózati hozzáférést a hipervizor és a gyökér operációs rendszer által közvetített.

Az állomás rétegben, a legújabb Windows Server testreszabott és a megerősített verziójának Azure virtuális gépeken. Microsoft Azure egy stripped-down verzióját használja, a Windows Server, amely csak a virtuális gépek gazdagépre szükséges összetevőket tartalmazza. Ez történik, a teljesítmény javítása érdekében, és a támadási felület csökkentése érdekében. A hipervizor, amely nem függ az operációs rendszer biztonsági gép határok érvényesíti.

**Az Azure-kezelést úgy Hálóvezérlők (FCs)**: az Azure, a (paneleken/csomópontokat) fizikai kiszolgálókon futó virtuális gép "fürt" körülbelül 1000 vannak csoportosítva. A virtuális gépek egymástól függetlenül kiterjesztett és a redundáns platform szoftver összetevőt az FC kezeli.

Minden egyes FC a fürt, és kiosztja a futó alkalmazások életciklusát felügyeli, és az ellenőrzése alatt a hardver állapotát figyeli. Végrehajtja a mindkét vegetatív műveletek, például a Virtuálisgép-példány megfelelő kiszolgálókon reincarnating, amikor meghatározza, hogy a kiszolgáló nem. Az FC telepítése, frissítése és alkalmazások kiterjesztése például alkalmazás-felügyeleti műveletek is végez.

A datacenter fürtök van osztva. Fürtök elkülöníteni az FC szintjén hibák, előfordulhat, hogy bizonyos hibák osztályok érintő kiszolgálók túl a fürt, amely akkor fordulhat elő. Egy adott Azure-fürttel átadott FCs az FC-fürt vannak csoportosítva.

**Hardver készlet**: Azure hardver- és hálózati eszközökről lett előkészítve a rendszerindítási konfigurációs folyamat során, és részletes ismertetését lásd: a datacenter.xml konfigurációs fájlt. Bármely új hardver- és hálózati összetevők írja be az Azure éles környezetben a rendszerindítási konfigurációs folyamat után kell következnie. Az FC a teljes leltár datacenter.xml konfigurációs fájlban felsorolt felügyeletéért felelős.

**FC-felügyelet alatt álló operációs rendszer**: az operációs rendszer team Ez a témakör az operációs rendszer lemezképet a virtuális merevlemezek (VHD) formájában telepített összes gazdagép és Vendég virtuális gépek az Azure éles környezetben. Az operációs rendszer csapat hoz létre ezek "Base képek" egy automatizált offline létrehozási folyamat során. A kiinduló lemezkép az operációs rendszer a a kernel és egyéb alapvető összetevői rendelkezik lett módosítva, és az Azure környezetbe támogatására optimalizált egy verziója telepítve.

A háló által felügyelt operációsrendszer-lemezképek három típusa van:

- Gazdagép operációs rendszer – az állomás operációs rendszere egy testreszabott állomás virtuális gépeken futó operációs rendszert
- Az operációs rendszer natív – natív OS bérlők (például az Azure Storage) futó, amelynek nincs Generációazonosítón
- Vendég operációs rendszer – a Vendég virtuális gépeken futó vendég operációs rendszer

A gazdagép és a natív FC-felügyelet alatt álló operációs rendszerek használhatók a felhőben, és nincsenek nyilvánosan elérhető.

**Állomás és az operációs rendszer natív**: állomás operációs rendszer és az operációs rendszer natív megerősített operációsrendszer-lemezképek üzemeltető a háló ügynökök (be), és a számítási csomópont (fut, az első virtuális gép a csomóponton) és tárhely csomópontot. A használatának előnyeit optimalizált állomás képek talál, és a natív operációs rendszer, hogy csökkenti a támadási felületét API-k vagy nagy biztonsági kockázatot jelentenek, és növeli az operációs rendszer csökkentése nem használt összetevők által elérhetővé tett. Ilyen csökkenteni erőforrásigénnyel operációs rendszer csak az Azure szükséges összetevőket. Ez javítja a teljesítményt, és csökkenti a támadási felületet.

**Vendég operációs rendszer**: Azure belső összetevőinek vendég operációs rendszer virtuális gépeken futó eltérően külső felhasználók Remote Desktop Protocol (RDP) futtatásához nincs lehetőség van. A referenciakonfiguráció konfigurációs beállításainak változtatásai halad át a módosítást, és felszabadíthatja a felügyeleti folyamat szükséges.

## <a name="azure-datacenters"></a>Azure-adatközpontok
A Microsoft felhőalapú infrastruktúra, valamint műveletek (MCIO) csapat felügyeli a Microsoft fizikai infrastruktúra és az Adatközpont létesítményekben az összes Microsoft online szolgáltatáshoz. MCIO felelős elsősorban belül az adatközpontokban a fizikai és környezeti vezérlők kezelése, valamint a kezelése és a támogató külső szegélyhálózati hálózati eszközök (a peremhálózati útválasztók és az Adatközpont útválasztókat). MCIO felelős is az Adatközpont állványok operációs rendszer minimális kiszolgálói hardvere beállítása. Az Azure-ral nincs közvetlen interakció rendelkeznek.

## <a name="service-management--service-teams"></a>Szolgáltatás-felügyelet & csoportokkal
Támogatja az Azure-szolgáltatás a "Service csoportok" néven ismert mérnöki csoportokat kezeli. A csoportok egy Azure támogatása területe felelős. Minden csoport biztosítania kell egy mérnököt elérhető 24 x 7 vizsgálatához és a szolgáltatás hibáinak megoldásához. Csoportokkal, alapértelmezés szerint nincs fizikai érje el a hardvert, az Azure-ban működik.

Csoportok a következők:

- Alkalmazásplatform
- Azure Active Directory
- Azure Compute
- Az Azure Net
- Mérnöki csapathoz Felhőszolgáltatások
- ISSD: biztonsági
- Multifactor Authentication (Többtényezős hitelesítés)
- SQL Database
- Storage

## <a name="types-of-users"></a>Felhasználók típusai
Minden Azure belső felhasználók kategóriába sorolt, amely definiálja a hozzáférésüket ügyféladatokat (hozzáférés vagy nincs hozzáférése) bizalmassági szintű alkalmazott állapotuk rendelkeznek. Az alkalmazottak (vagy alvállalkozói) a Microsoft minősülnek belső felhasználók. Minden felhasználó külső felhasználók minősülnek. Az Azure-ba (engedélyezési engedély hitelesítési bekövetkezte után) a felhasználói jogosultságok az alábbi táblázat ismerteti:

| Szerepkör | Belső vagy külső | Érzékenységi szint | Engedélyezett jogosultságok és a szerepkörökben elvégzett feladatok | Hozzáférés típusa
| --- | --- | --- | --- | --- |
| Az Azure Datacenter visszafejtés | Belső | Nem lehet hozzáférni ügyféladatok | A helyszíni; a fizikai biztonság kezeléséhez Tartson őrjáratok mindkét az adatközpontban, és minden belépési pontot; figyelése Hajtsa végre a kíséreti szolgáltatások esetében bejövő és kimenő biztosíthat általános szolgáltatások (étkezési lehetőségek, tisztítás) vagy az informatikai munkahelyi; az adatközponton belül az egyes nem-bejelölve datacenter személyzet Tartson rendszeres figyelése és karbantartása hálózati hardver; Hajtsa végre az incidenskezelés és a javítás / csere munkahelyi segítségével számos különféle eszközre; Tartson rendszeres figyelése és karbantartása a fizikai hardverről a adatközpontokban; Hozzáférés az igény szerinti környezetet tulajdonság tulajdonosai. Képes a törvényszéki vizsgálatokat, naplózás a jelentést, és kötelező biztonsági képzés & házirend követelményeinek; megkövetelése Működési tulajdonjoga és kritikus fontosságú biztonsági eszközök, például a Képolvasók és a napló gyűjtemény karbantartása. | A környezet állandó hozzáférés |
| A Microsoft Azure incidens osztályozás (gyors válasz mérnökök) | Belső | Felhasználói adatok | Infrastruktúra-műveletek, támogatást és Azure mérnöki csapat; közti kommunikáció felügyeletét Platform incidenseket, problémákat és szolgáltatáskérések osztályozhatja. | Csak az idő a hozzáférést a környezet - nem vevő rendszerek korlátozott állandó hozzáféréssel rendelkező |
| A Microsoft Azure telepítési mérnökök | Belső | Felhasználói adatok | Hajtsa végre a központi telepítés/frissítés a webplatform-összetevők, szoftverek és ütemezett konfigurációs módosítások elősegítésére a Microsoft Azure. | Csak az idő a hozzáférést a környezet - nem vevő rendszerek korlátozott állandó hozzáféréssel rendelkező |
| A Microsoft Azure kimaradás ügyfélszolgálathoz (bérlő) | Belső | Felhasználói adatok | Hibakeresés és platform kimaradások és hibák diagnosztizálása egyes számítási bérlők és a Microsoft Azure-fiókok; Hibák elemzése és kritikus javításokat meghajtó platform/ügyfélnek, meghajtó műszaki fejlesztései között támogatása. | Csak az idő a hozzáférést a környezet - nem vevő rendszerek korlátozott állandó hozzáféréssel rendelkező |
| A Microsoft Azure élő webhelyet Engineers (figyelés mérnökök) & incidens | Belső | Felhasználói adatok | A felderítésére és diagnosztikai eszközökkel; platform állapotfigyelő kiküszöböléséhez kiszolgálófelügyeletért felelnek A kötet illesztőprogramok javítások meghajtó, kimaradások eredő elemek javítása, és kimaradás visszaállítási műveletek segítik. | Csak az idő a hozzáférést a környezet - nem vevő rendszerek korlátozott állandó hozzáféréssel rendelkező |
|Microsoft Azure-ügyfelek | Külső | – | N/A | – |

Azure használja egyedi azonosítóként hitelesítéséhez a szervezeti felhasználók és a felhasználók (vagy szervezeti felhasználók nevében eljáró folyamatok) összes eszközök vagy eszközökre, amelyek az Azure környezetbe részei.

**A Microsoft Azure – belső hitelesítési**: Azure belső összetevői közötti kommunikáció a TLS-titkosítással védett. A legtöbb esetben az x.509 szabványú tanúsítványokban önaláírt. Kivételek tanúsítványokat használ, amely érhető el. az Azure-hálózatot kívül a kapcsolatok, valamint a FCs történik. FCs rendelkezik a hitelesítésszolgáltatói (CA) a megbízható legfelső szintű hitelesítésszolgáltató által támogatott Microsoft Certificate által kibocsátott tanúsítványokat. Ez lehetővé teszi, hogy könnyen tanúsítványváltást nyilvános kulcsok FC. FC nyilvános kulcsok ezenkívül a Microsoft fejlesztői eszközök használja, hogy a fejlesztők küldje el az új alkalmazás-lemezképek, ha azok titkosítottak FC nyilvános kulccsal rendelkező minden beágyazott titkos kulcsok védelmében.

**A Microsoft Azure hardver Device Authentication**: az FC fenn használt hitelesítő adatokat (kulcsok és/vagy jelszó) hitelesítse magát a különböző hardvereszközök az ellenőrzése alatt. Szállítására, megőrzése, és használja a hitelesítő adatokat használja a rendszer az Azure a fejlesztők, a rendszergazdák és a biztonsági mentési szolgáltatások/személyzet hozzáférés megakadályozása bizalmas, bizalmas vagy titkos információk célja.

A titkosítás nyilvános kulccsal FC beállítása és az FC újrakonfigurálása a FC fő identitása alapján alkalommal hálózati hardvereszközök eléréséhez használt hitelesítő adatok átvitele. Hitelesítő adatokat lekérni, és az FC visszafejteni, amikor jogcímadatokat őket.

**Hálózati eszközök**: hálózati szolgáltatási fiókok vannak konfigurálva, hogy a hálózati eszközök (útválasztók, kapcsolók és terheléselosztók) hitelesítést a Microsoft Azure-ügyfél engedélyezése az Azure-hálózat csoport által.

## <a name="secure-service-administration"></a>Biztonságos felügyeleti szolgáltatás
A Microsoft Azure operations csoporthoz kell használnia a biztonságos felügyeleti munkaállomás (fűrészek; ügyfelek is hasonló a vezérlőelemeket kell megvalósítani a Privileged Access munkaállomásokon vagy láb). A FŰRÉSZ megoldás, az neves ajánlott külön rendszergazdai és felhasználói fiókok használata a felügyeleti csoporthoz való kiterjesztését. Ez az eljárás egy külön-külön hozzárendelt rendszergazdai fiókot, amely a felhasználó általános jogú felhasználói fiók nem használja. LÁTOTT buildek az adott fiók elválasztási gyakorlat, adja meg a megbízható munkaállomás a kényes fiókok.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
