---
title: Azure DDoS-védelem ajánlott eljárások és architektúrák hivatkozhat |} Microsoft Docs
description: További tudnivalók arról, hogyan használja a naplózási adatokat az alkalmazással kapcsolatos részletes dcu.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: b802c7b96bd8d0cfa56347d45542495caf69d7e4
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824710"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS-védelem: Ajánlott eljárások és architektúrák hivatkozik

Ez a cikk az informatikai döntéshozók és biztonsági csoporthoz. Azt várja, hogy ismeri az Azure-ral, hálózati és biztonsági.

Elosztott szolgáltatásmegtagadásos (DDoS-) tervez rugalmassági van szükség, tervezése és kialakítása során függően számos különböző. Ez a cikk a következő gyakorlati tanácsokat tervezése során az alkalmazások az Azure-ban, a rugalmasságot DDoS-támadások ellen.

## <a name="types-of-attacks"></a>Jellegű támadások

DDoS olyan támadástípus, amely megpróbálja lefoglalhat alkalmazás-erőforrásokat. A cél, hogy az alkalmazások rendelkezésre állásáról és képességét a jogos tanúsítványigénylések hatással. Támadások egyre több kifinomult és nagyobb méretű és a hatása. DDoS-támadások tud célozható tetszőleges végpontot, amely nyilvánosan elérhető az interneten keresztül.

Azure biztosít folyamatos védelem DDoS-támadások ellen. Ez a védelem integrálva van az Azure platformon alapértelmezés szerint, és nem kapcsolódik további költség. 

DDoS-védelem a platform alapszolgáltatásai mellett [Azure DDoS védelem szabványos](https://azure.microsoft.com/services/ddos-protection/) hálózati támadások elleni speciális DDoS megoldás képességeket biztosít. Az adott Azure-erőforrások védelme automatikusan terveztek. Védelem használata egyszerű ahhoz, hogy az új virtuális hálózatok létrehozása során. Létrehozása után is elvégezhető, és nem kell alkalmazás vagy erőforrás módosítani.

![Azure DDoS-védelem szerepét a támadó ügyfelek és a virtuális hálózat védelme](media/azure-ddos-best-practices/image1.png)

DDoS-támadások három kategóriába sorolhatók:-es, protokoll, és az erőforrás.

### <a name="volumetric-attacks"></a>-Es támadások

-Es támadások a leggyakoribb típus DDoS-támadások. -Es támadások találgatásos támadások, a hálózati és a szállítási réteg célzó. Próbálják lefoglalhat erőforrásokhoz, mint a hálózati kapcsolat. 

Ilyen támadások gyakran több fertőzött rendszer használnak a hálózati rétegek látszólag jogos forgalommal kéréssekkel. Hálózati szintű protokollok, például az Internet Control Message Protocol (ICMP), a User Datagram Protocol (UDP) és a Transmission Control Protocol (TCP) használnak.

A leggyakrabban használt hálózati réteg DDoS-támadások elárasztás, ICMP echo, elárasztás, DNS, UDP, TCP SZIN és NTP erősítési támadásokat. Ilyen típusú támadások segítségével nemcsak megszakadhat a szolgáltatás, hanem az, a több nefarious és célzott hálózati behatolás egy smokescreen. A legutóbbi-es támadás például a [Memcached biztonsági rés](https://www.wired.com/story/github-ddos-memcached/) , amely hatással a Githubon. A támadás UDP-port 11211 céloz, és létrehozott 1.35 Tb/s támadás kötet.

### <a name="protocol-attacks"></a>Protokoll támadások

Protokoll cél alkalmazásprotokollokon támadásokat. Próbálják infrastruktúra eszközök, például tűzfalakat, alkalmazás-kiszolgálókat, az összes elérhető erőforrást felhasználják, és terheléselosztókat. Protokoll támadások használja a csomagok, amelyek nem megfelelően formázott vagy protokoll rendellenességeket tartalmaznak. Ilyen támadások működik nagy számú nyitott kérelmek, hogy a kiszolgálók és egyéb kommunikációs eszközök fogadja a hívást, és várjon, amíg a csomag válaszra való küldésével. A cél megkísérli a nyitott kérelmek, a rendszer összeomlását felé, ami válaszolni.

A leggyakoribb a protokoll-alapú DDoS-támadások példája TCP SZIN árvíz. A támadás több TCP SZIN kérelmeket próbál ne terhelje tovább a cél. A cél, hogy ellenőrizze a cél nem válaszol. A 2016 DIN leállás mellett TCP SZIN állt alkalmazásszintű támadás alatt elárasztja, hogy célzott 53-as port DIN tartozó DNS-kiszolgálók.

### <a name="resource-attacks"></a>Erőforrás támadások

Erőforrás támadások az alkalmazási rétegre célként. Háttér-folyamatok azok indítás, annak érdekében, hogy az ne terhelje tovább a rendszer. Erőforrás a normál keres, de ez végzi processzorigényes lekérdezi a kiszolgálóhoz való visszaélés forgalom támadásokat. Szükséges erőforrások lefoglalhat forgalom mennyisége nem éri el, amely a más típusú támadások. Az erőforrás-támadások jogos forgalmat, így nehezen észlelése nem különböztethetők akkor. A leggyakrabban használt erőforrás támadások HTTP/HTTPS- és DNS szolgáltatások esetén.

## <a name="shared-responsibility-in-the-cloud"></a>Megosztott felelős a felhőben

Egy védelmi-stratégia segít a növekvő különböző és képzettsége támadások elleni. Biztonsági megosztott feladata az ügyfél és a Microsoft között. Microsoft meghívja ezt a [megosztott felelősségi modell](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Az alábbi ábrán láthatók az osztály a felelősségi:

![Az Azure és az ügyfél feladatkörei](media/azure-ddos-best-practices/image2.png)

Azure-ügyfél hasznot húzhatnak a Microsoft ajánlott eljárások áttekintését és felépítése globálisan elosztott alkalmazásokat, amelyek tervezhetők meg és tesztelése sikertelen.

## <a name="fundamental-best-practices"></a>Alapvető ajánlott eljárások

A következő szakaszok ad DDoS-rugalmas szolgáltatások létrehozásához, Azure előíró útmutatást.

### <a name="design-for-security"></a>Biztonság – tervezés

Győződjön meg arról, hogy biztonsági prioritást során az alkalmazás, a tervezési és megvalósítási történő központi telepítése és üzemeltetése teljes életciklusát. Az alkalmazások, amelyek lehetővé teszik egy viszonylag kis mennyiségű kérést egy túlságosan mennyiségű erőforrást, ami azt eredményezi, hogy szolgáltatáskimaradás használandó hibák is rendelkezhetnek. 

A Microsoft Azure-on futó szolgáltatás védelme érdekében meg kell alapos ismerete szükséges az alkalmazás-architektúra pedig a a [szoftver minőségű öt oszlopok](https://docs.microsoft.com/azure/architecture/guide/pillars).
Tudnia kell tipikus forgalommal, az alkalmazás és más alkalmazások és a végpontok, amelyek ki vannak téve a nyilvános internethez közötti kapcsolat modell.

Annak biztosítása, hogy egy alkalmazás rugalmasak magának az alkalmazásnak megcélzó szolgáltatásmegtagadást kezeli az egyik legfontosabb. Az Azure platformon, kezdve beépített biztonság és adatvédelem a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx). Az SDL biztonsági címek minden fejlesztési fázisában, és biztosítja, hogy Azure folyamatosan frissül még biztonságosabbá teszi.

### <a name="design-for-scalability"></a>Méretezhetőség kialakítása

A méretezhetőség azt, hogy a rendszer megnövekedett terhelés kezelése. Az alkalmazások kell tervezési [horizontálisan méretezhető](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) egy felerősített terhelés, kifejezetten a DDoS-támadások esetén az igény kielégítésére. Ha az alkalmazás attól függ, hogy a szolgáltatás egyetlen példányát, a hibaérzékeny pontot hoz létre. A rendszer több példány kiosztás révén, rugalmasabb, és több méretezhető.

A [Azure App Service](../app-service/app-service-value-prop-what-is.md), jelölje be egy [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , amely több példányt biztosít. Azure-szolgáltatásokhoz, konfigurálhatja a szerepkörök használandó [több példány](../cloud-services/cloud-services-choose-me.md). A [Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), győződjön meg arról, hogy a virtuális gép (VM) architektúra tartalmazza az egynél több virtuális gép és az egyes virtuális gépek szerepel egy [rendelkezésre állási csoport](../virtual-machines/virtual-machines-windows-manage-availability.md). Azt javasoljuk, [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) automatikus skálázás képességeinek.

### <a name="defense-in-depth"></a>Mélyreható védelmet

Mélyreható védelmet mögött lényege kockázatkezelést különböző védelmi stratégiák használatával. Rétegez egy alkalmazás biztonsági védelmekkel csökkenti egy sikeres támogatás esélyét. Azt javasoljuk, hogy megvalósítása biztonságos tervekhez az alkalmazások az Azure platformon beépített képességei használatával.

Például növeli a támadások veszélye méretű (*terület surface*) az alkalmazás. Engedélyezett használatával elérhető IP-címterület bezárása, és nem szükséges azokat a terheléselosztókat a portokat figyeli csökkentheti a támadási felületét ([Azure terheléselosztó](../load-balancer/load-balancer-get-started-internet-portal.md) és [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Hálózati biztonsági csoportokkal (NSG-k)](../virtual-network/security-overview.md) , amelyek egy másik csökkenteni a támadási felületet.
Használhat [címkék szolgáltatás](/virtual-network/security-overview.md#service-tags) és [biztonsági csoportok](/virtual-network/security-overview.md#application-security-groups) minimalizálása érdekében a biztonsági szabályok létrehozásához és konfigurálásához a hálózati biztonság, mint az alkalmazás struktúra természetes bővítménye összetettségét.

Az Azure-szolgáltatásokat telepítsen egy [virtuális hálózati](../virtual-network/virtual-networks-overview.md) amikor csak lehetséges. Ez az eljárás lehetővé teszi, hogy a szolgáltatás erőforrásainak kommunikációja áthaladjon magánhálózati IP-címét. Egy virtuális hálózatot az Azure szolgáltatás forgalmát forrás IP-cím nyilvános IP-címek alapértelmezés szerint használja. Használatával [szolgáltatás végpontjait](../virtual-network/virtual-network-service-endpoints-overview.md) vált, a virtuális hálózati Magáncímeket használják a forrás IP-címek, ha azok próbál hozzáférni az Azure-szolgáltatás egy virtuális hálózati forgalmát.

Ügyfelek helyszíni együtt az Azure-ban erőforrásaik első megtámadott erőforrások gyakran látható. Kapcsolat a helyszíni környezet az Azure-ba, javasoljuk, hogy elérhetővé tegyék a helyszíni erőforrások a nyilvános internethez minimálisra csökkenthető. A méretezés és az Azure fejlett DDoS védelem funkcióinak használatához, üzembe helyezése az Azure-ban a jól ismert nyilvános entitásokat. Mivel ezek nyilvánosan elérhető entitások gyakran egy cél DDoS-támadások, a helyszíni erőforrások gyakorolt hatás üzembe őket az Azure-ban csökkenti.

## <a name="azure-offerings-for-ddos-protection"></a>DDoS-védelem Azure-szolgáltatásokkal

Azure rendelkezik két DDoS szolgáltatásra vonatkozó ajánlattal kapcsolatban, hogy a hálózati támadásoktól (3. és 4. réteg) védelmet nyújt: DDoS védelem egyszerű és szabványos DDoS-védelem. 

### <a name="ddos-protection-basic"></a>DDoS védelem Basic

Alapszintű védelmi integrálva van az Azure alapértelmezés szerint a további költség nélkül. A méretezés és a globálisan üzembe helyezett Azure hálózati kapacitása biztosít a forgalom folyamatos figyelési és valós idejű megoldás révén közös hálózati rétegbeli támadások elleni védelmet. DDoS védelem alapvető nincs felhasználói konfiguráció vagy az alkalmazás módosítását igényli. DDoS védelem alapvető megvédi az összes Azure-szolgáltatások, többek között a PaaS szolgáltatások, például az Azure DNS-ben.

![Az Azure-hálózatot képviselő alakot képezi rendelkező szöveg "globális DDoS megoldás jelenléte" és "Vezető DDoS megoldás kapacitás"](media/azure-ddos-best-practices/image3.png)

Az Azure-ban alapvető DDoS-védelem szoftver- és hardverkonfigurációiról összetevőkből áll. A szoftver vezérlő vezérlősík úgy dönt, mikor, ahol, és milyen kell kormányzott, elemzése, és távolítsa el a támadás forgalom hardver készülékek keresztül. A vezérlő vezérlősík teszi ehhez a döntéshez alapuló infrastruktúra kiterjedő DDoS-védelem *házirend*. Ez a házirend statikusan beállította és egységesen alkalmazza az összes Azure-ügyfél számára.

Például a DDoS-védelem házirend azt szabja meg, milyen adatforgalma, a védelem nem lehet *elindul.* (Ez azt jelenti, hogy a bérlői forgalom kell útválasztása készülékek tisztítási.) A házirend határozza meg majd, a háttértisztítás készülékek kell hogyan *mérséklése* a támadás.

Az Azure DDoS védelem alapszintű service irányuló védelmét az infrastruktúra és az Azure platformon védelméről. Megszünteti a forgalmat, amikor az meghaladja a, hogy ez hatással lehet egy több-bérlős környezetben több ügyfél jelentős mértékben. Nem biztosít lehetőséget, vagy ügyfél testreszabott házirendeket.

### <a name="ddos-protection-standard"></a>DDoS védelem Standard

Standard szintű védelmet továbbfejlesztett DDoS megoldás szolgáltatásokat biztosítja. Az adott Azure-erőforrások virtuális hálózat védelme érdekében automatikusan terveztek. Védelmi egyszerű ahhoz, hogy minden új vagy meglévő virtuális hálózat, és azt nem kell alkalmazás vagy erőforrás módosítani. Azt az alapszintű service, beleértve a naplózást, a riasztás és a telemetriai adatok keresztül több előnye is van. Az alábbi szakaszok felsorolják a kulcsfontosságú szolgáltatásokat az Azure DDoS védelem szabványos szolgáltatás.

#### <a name="adaptive-real-time-tuning"></a>Adaptív valós idejű hangolása

A DDoS védelem alapszintű Azure szolgáltatás segítségével az ügyfelek védelmét, és megelőzheti a hatások más ügyfelek számára. Ha egy szolgáltatás ki van építve, egy tipikus kötet jogos bejövő forgalmat, amely kisebb, mint például a *eseményindító arány* az infrastruktúra kiterjedő DDoS-védelem házirend, egy adott ügyfélhez erőforrások DDoS támadást mehet észrevétlenül. Általában legutóbbi támadások (például több vektoros DDoS-) összetettsége és a bérlők az alkalmazás-specifikus viselkedését hívás /-ügyfél, testre szabott adatvédelmi szabályzatok. A szolgáltatás a Testreszabás két insights segítségével éri el:

- Egy ügyfél (-IP) forgalmi minták automatikus tudomást réteg 3 vagy 4.

- Minimalizálja a téves, figyelembe véve, hogy a skála Azure lehetővé teszi, hogy felvegye a jelentős mennyiségű forgalom.

![DDoS védelem szabványos működése, a házirend. generációs"körben ábrája](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS-védelem telemetriai, figyelés, és a riasztás

DDoS védelem szabványos közzététele keresztül részletes telemetria [Azure figyelő](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) DDoS-támadások időtartama. Az Azure-figyelő metrikák DDoS-védelem használó bármelyikét riasztásokat lehet beállítani. Naplózási Splunk (az Azure Event Hubs), az Azure Naplóelemzés és az Azure Storage a Azure figyelő Diagnostics felületén keresztül speciális elemzésekre szolgáló integrálva.

##### <a name="ddos-mitigation-policies"></a>DDoS megoldás házirendek

Válassza ki az Azure-portálon **figyelő** > **metrikák**. Az a **metrikák** ablaktáblában jelölje ki az erőforráscsoportot, válasszon ki egy erőforrástípust a **nyilvános IP-cím**, és válassza ki az Azure nyilvános IP-címe. DDoS metrikák láthatók a **elérhető** ablaktáblán.

DDoS védelem szabványos három autotuned megoldás házirendet alkalmaz (TCP SZIN TCP és UDP) minden egyes nyilvános IP-cím a védett erőforrás DDoS engedélyezve van a virtuális hálózatban. A metrika kiválasztásával megtekintheti a házirend küszöbértékek **bejövő csomagok való DDoS megoldás**.

![Elérhető és a metrikák diagram](media/azure-ddos-best-practices/image7.png)

A házirend küszöbértékek a machine learning-alapú hálózati forgalom profilkészítési keresztül automatikusan konfigurált. Támadás alatt lévő IP-cím DDoS megoldás akkor következik be, csak akkor, ha a házirend küszöbérték elérése esetén.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>A metrika DDoS támadás alatt lévő IP-cím

Ha a nyilvános IP-cím a támadásoknak, a metrika értékének **alatt DDoS támadás, vagy nem** DDoS-védelem megoldás végez a támadás forgalom 1-re változik.

!["A DDoS támadásokkal vagy nem" metrika és diagram](media/azure-ddos-best-practices/image8.png)

Azt javasoljuk, hogy ez a mérőszám a riasztás konfigurálása. Majd értesítést is, ha egy aktív DDoS-megoldás a nyilvános IP-cím végre.

További információkért lásd: [kezelése Azure DDoS védelem szabványos az Azure portál használatával](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Webalkalmazási tűzfal erőforrás támadások

Adott erőforrás támadásokkal szemben, az alkalmazás rétegben, konfigurálnia kell a webalkalmazási tűzfal (WAF) segítségével biztonságos webes alkalmazások. Egy WAF megvizsgálja a bejövő webes forgalom blokkolása SQL-utasítások, parancsfájlok, DDoS és egyéb réteg 7 támadások többhelyes. Azure biztosít [Alkalmazásátjáró szolgáltatásként WAF](../application-gateway/application-gateway-web-application-firewall-overview.md) a webalkalmazások a gyakori biztonsági réseket és biztonsági rések központosított védelemre. Nincsenek elérhető, akkor lehetnek megfelelőbbek, az Ön igényeinek keresztül Azure partnertől más WAF ajánlatokat a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

-Es és state Erőforrásfogyás támadások még webalkalmazási tűzfalak. Erősen ajánlott a virtuális hálózaton WAF-es elleni védelme szabványos DDoS-védelem engedélyezése és protokoll támadásokat. További információkért lásd: a [DDoS-védelem hivatkozás architektúrák](#ddos-protection-reference-architectures) szakasz.

### <a name="protection-planning"></a>Védelem tervezése

Tervezése és előkészítése fontosságúak megérteni, hogyan hajtsa végre a rendszer a DDoS-támadások során. Ennek érdekében egy incidenskezelési válaszadási tervet tervezése.

Ha DDoS védelem szabványos, győződjön meg arról, hogy a virtuális hálózat internet felé néző végpontok engedélyezve van. DDoS-riasztások konfigurálása segít folyamatosan figyelje a infrastruktúra bármely potenciális támadások. 

Az alkalmazások egymástól függetlenül kell figyelni. Ismerje meg az alkalmazás normál működését. Ha az alkalmazás viselkedik a DDoS-támadások során elvárt működésének előkészítése.

#### <a name="testing-through-simulations"></a>Keresztül szimulációja tesztelése

Ajánlott a módját a szolgáltatások végzett rendszeres szimulációja válaszol a támadás feltételezéseket tesztelése. Tesztelés során ellenőrzi, hogy a szolgáltatások és alkalmazások továbbra is a kívánt módon működik, és nincs a felhasználói felületet nem működőképes. Azonosítsa a technológia és a folyamat szempontból hézagok, és bele azokat a DDoS válasz stratégia. Azt javasoljuk, hogy az ilyen tesztek végrehajtása átmeneti környezet vagy a csúcsidőn kívül az éles környezetbe hatás minimalizálása érdekében.

Azt a közösen [BreakingPoint felhő](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felület, ahol Azure-ügyfél hozhat létre a forgalom elleni szimulációja nyilvános végpontok DDoS-védelem engedélyezve van. Használhatja a [BreakingPoint felhő](https://www.ixiacom.com/products/breakingpoint-cloud) a szimuláció:

- Ellenőrizze, hogy hogyan Azure DDoS-védelem megóvja az Azure-erőforrások DDoS-támadások.

- Az incidensekre adott reakciók folyamat DDoS támadás optimalizálásához.

- A dokumentum DDoS megfelelőségét.

- A hálózati biztonsági csoportok betanításához.

Számítógépes állandó innováció védelmet igényel. Az Azure DDoS Standard védelem egy-az-a-legkorszerűbb egyre DDoS-támadások mérséklése egy hatékony megoldás kínál.

## <a name="components-of-a-ddos-response-strategy"></a>DDoS válasz stratégia összetevők

A DDoS-támadások, amely általában célozza az Azure-erőforrások felhasználó szempontból minimális beavatkozást igényelnek. Továbbra is DDoS beépítése az incidensválasz-stratégia részeként megoldás segítségével az üzletmenet folytonossága hatás minimalizálása érdekében.

### <a name="microsoft-threat-intelligence"></a>Microsoft fenyegetésfelderítési adataival

Microsoft kiterjedt fenyegetés eszközintelligencia hálózattal rendelkezik. Ez a hálózat használ egy kiterjesztett biztonsági Közösség, amely támogatja a Microsoft online services, Microsoft-partnerek és az internetes biztonsági Közösségben kapcsolatok együttes ismerete. 

Kritikus fontosságú infrastruktúra-szolgáltatóként a Microsoft megkapja a korai figyelmeztetéseket fenyegetésekkel kapcsolatos. Microsoft összegyűjti fenyegetésfelderítési adataival, az online szolgáltatások és a globális felhasználói alapja. Microsoft magában foglalja a fenyegetésfelderítési adataival mindegyikét újra üzembe a Azure DDoS-védelem termékek.

A Microsoft Digital Crimes Unit (DCU) is, elleni botnetek sértő stratégiák hajt végre. Botnetek közös forrás parancs és a vezérlő DDoS-támadások.

### <a name="risk-evaluation-of-your-azure-resources"></a>Azure-erőforrások kockázat kiértékelésekor

rendkívül fontos tudni, hogy egy folyamatos DDoS-támadások elleni a kockázat körét. Rendszeres időközönként megválaszolnia: 
- Milyen új nyilvánosan elérhető Azure-erőforrások védelmét van szüksége?

- Van-e a szolgáltatás olyan hibaérzékeny pontot? 

- Hogyan szolgáltatások el lehet különíteni a támadás során továbbra is szolgáltatások elérhetővé tételére érvényes ügyfelek gyakorolt hatást?

- Vannak-e virtuális hálózatok, ahol DDoS védelem szabványos engedélyezni kell, de nem? 

- A szolgáltatás aktív vannak a feladatátvételi különféle régiókban?

### <a name="customer-ddos-response-team"></a>Ügyfél DDoS csoportnak

DDoS válasz csapat ez válaszol a támadást, gyors és hatékony legfontosabb lépés. Azonosítsa az ügyfelek a szervezetben, akik fog felügyeletéért megtervezése és végrehajtása. A DDoS válasz csapat alaposan ismerje meg az Azure DDoS védelem szabványos szolgáltatás. Győződjön meg arról, hogy a csapat azonosítása és csökkenthető a támadás belső és külső használó ügyfelek számára, többek között a Microsoft támogatási csoport egyeztetve.

DDoS-válasz csapata javasoljuk szimuláció gyakorlatokat használhatja a szolgáltatás rendelkezésre állása és folytonosságának megtervezése normál részeként. Gyakorlatokban mérettesztelés kell tartalmaznia.

### <a name="alerts-during-an-attack"></a>Riasztások a támadás során

Azure DDoS védelem szabványos azonosítja, és csökkenti a DDoS-támadások felhasználói beavatkozás nélkül. Tájékoztatást kaphat nincs egy aktív megoldás védett nyilvános IP-címhez, akkor [olyan riasztást konfigurálhat](../virtual-network/ddos-protection-manage-portal.md) a metrika a **alatt DDoS támadás, vagy nem**. Ha szeretné tudni, hogy a skála a támadás, eldobott forgalom és egyéb részletek más DDoS metrikák figyelmeztetések létrehozása.

#### <a name="when-to-contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft támogatási szolgálatához

- A DDoS-támadások során látnia, hogy a védett erőforrás teljesítménye jelentősen csökkent, vagy az erőforrás nem érhető el.

- Úgy gondolja, hogy a DDoS-védelem szolgáltatás nem várt módon viselkedik. 

  A DDoS-védelem szolgáltatás elindul a megoldás, ha csak a metrika értékét **való DDoS megoldás TCP/TCP SZIN/UDP-házirend** alacsonyabb, mint a védett nyilvános IP-erőforrás fogadott.

- Arra készül, egy ugrásszerű esemény, amely jelentősen növeli a hálózati forgalmat.

- Egy szereplő rendelkezik veszélyeztetett elindíthatja a DDoS-támadások az erőforrásokon.

A támadásokkal szemben, amelyek a kritikus fontosságú üzleti hatással van, hozzon létre egy súlyossági-A [támogatja a jegy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>A támadás utáni lépéseket

Célszerű mindig egy futás utáni támadás után ne, és szükség szerint módosítsa a DDoS válasz stratégia stratégiát. Vegye figyelembe a következőkre:

- Lett nincs fennakadást a szolgáltatás vagy a felhasználói élmény méretezhető architektúra erőforráshiány?

- Mely alkalmazások vagy szolgáltatások jelentkezett a legtöbb?

- Hogyan hatékony volt a DDoS válasz stratégia, és hogyan fejleszthető?

Ha azt gyanítja, hogy Ön egy DDoS támadás alatt áll, az ilyen normál Azure támogatási csatornáit keresztül.

## <a name="ddos-protection-reference-architectures"></a>DDoS-védelem referencia architektúra

DDoS védelem szabványos úgy van kialakítva, [szolgáltatások a virtuális hálózat üzembe helyezett](../virtual-network/virtual-network-for-azure-services.md). Más szolgáltatások az alapértelmezett DDoS védelem alapszintű service vonatkozik. A következő hivatkozás architektúrák csoportosított architektúra mintákat forgatókönyvek, szerint vannak rendezve.

### <a name="virtual-machine-windowslinux-workloads"></a>Virtuális gép (Windows/Linux) munkaterhelések

#### <a name="application-running-on-load-balanced-vms"></a>Elosztott terhelésű virtuális gépeken futó alkalmazás

A referencia-architektúrában több Windows virtuális gép egy terheléselosztó mögött rendelkezésre állás és méretezhetőség javítása érdekében állítsa be a skála futó bevált gyakorlatok csoportja jeleníti meg. Ez az architektúra bármely állapot nélküli alkalmazások és szolgáltatások, például webkiszolgálót használható.

![Az elosztott terhelésű virtuális gépeken futó alkalmazás referencia-architektúrában ábrája](media/azure-ddos-best-practices/image9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Egy egyetlen nyilvános IP-címet, és internetes forgalmat is csomagként küldhető el a virtuális gépek terheléselosztók keresztül. DDoS védelem szabványos engedélyezve van az Azure (internet) terheléselosztó, amely rendelkezik a nyilvános IP-cím hozzárendelve a virtuális hálózaton.

A terheléselosztó osztja el a Virtuálisgép-példányok bejövő internet kéréseket. Virtuálisgép-méretezési csoportok lehetővé teszik a bejövő vagy kimenő tolja virtuális gépek számát manuálisan vagy automatikusan előre meghatározott szabályok alapján. Ez azért fontos, ha az erőforrás DDoS támadás alatt áll. A referencia-architektúrában további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Fut a Windows N szintű alkalmazás

Egy N szintű architektúra számos módon implementálható. Az alábbi ábrán egy tipikus háromrétegű webalkalmazás. Ez az architektúra épít, a cikk [futtassa a virtuális gépek elosztott terhelésű a méretezhetőség és a rendelkezésre állási](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![A Windows N szintű futó alkalmazást a referencia-architektúrában ábrája](media/azure-ddos-best-practices/image10.png)

Ebben az architektúrában szabványos DDoS-védelem engedélyezve van a virtuális hálózaton. Minden nyilvános IP-címek a virtuális hálózat DDoS-védelem réteg 3 vagy 4 beolvasása. A réteg 7 védelem központi telepítése a WAF termékváltozatban Alkalmazásátjáró. A referencia-architektúrában további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS webalkalmazás

A referencia-architektúrában jeleníti meg az Azure App Service-alkalmazást futtat egy régió. Ez az architektúra látható használó webalkalmazás bevált gyakorlatok csoportja [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) és [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
A készenléti régió feladatátvételi forgatókönyvek be van állítva.

![A referencia-architektúrában PaaS webalkalmazás ábrája](media/azure-ddos-best-practices/image11.png)

Az Azure Traffic Manager irányítja a bejövő kérelmek alkalmazás átjárónak a régiók egyikéhez sem. A normál működés során az aktív terület az Alkalmazásátjáró irányítja a kérelmeket. Ha az adott régióban, Traffic Manager feladatátadás Application Gateway készenléti régióban.

Biztosítására, hogy az összes forgalom az internetről, a webalkalmazásnak szánt a [alkalmazás átjáró nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) Traffic Manager keresztül. Ebben a forgatókönyvben, az app service (webalkalmazás) önmagában nem közvetlenül kívülről elérhető, és Alkalmazásátjáró védi. 

Azt javasoljuk, hogy konfigurálja-e az alkalmazás átjáró WAF-Termékváltozat (akadályozza) réteg 7 (HTTP/HTTPS/WebSocket) támadások elleni védelme érdekében. Ezenkívül webalkalmazások vannak konfigurálva [csak az alkalmazás átjáró forgalom fogadására](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-címet.

A referencia-architektúrában kapcsolatos további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>A megoldás nem webes PaaS szolgáltatások

#### <a name="hdinsight-on-azure"></a>Az Azure HDInsight

A referencia-architektúrában látható DDoS védelem szabványa konfigurálása egy [Azure HDInsight-fürt](https://docs.microsoft.com/azure/hdinsight/). Győződjön meg arról, hogy a HDInsight-fürthöz kapcsolódó virtuális hálózatot, és hogy DDoS-védelem engedélyezve van-e a virtuális hálózaton.

!["HDInsight" és "A Speciális beállítások" ablaktáblák, virtuális hálózati beállításokkal](media/azure-ddos-best-practices/image12.png)

![DDoS védelem kiválasztása](media/azure-ddos-best-practices/image13.png)

Ebben az architektúrában az internetről a HDInsight-fürthöz rendelt továbbítódik a HDInsight átjáró terheléselosztóhoz társított nyilvános IP-címhez. Az átjáró terheléselosztó ezután elküldi a forgalmat az átjárócsomópontokkal vagy a feldolgozó csomópontok közvetlenül. Standard DDoS-védelem engedélyezve van a HDInsight virtuális hálózaton, mert az összes nyilvános IP-címek a virtuális hálózat első réteg 3 vagy 4 DDoS-védelem. A referencia-architektúrában az N szintű és több területi hivatkozás architektúrák kombinálva.

A referencia-architektúrában további információkért tekintse meg a [kiterjesztése Azure HDInsight Azure virtuális hálózat használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentációját.


> [!NOTE]
> Az Azure App Service-környezet PowerApps vagy az API Management része virtuális hálózatnak egy nyilvános IP-is natív módon nem támogatott.

## <a name="next-steps"></a>További lépések

* [Az Azure DDoS-védelem termék oldalát](https://azure.microsoft.com/services/ddos-protection/)

* [DDoS-védelem Azure blog](http://aka.ms/ddosblog)

* [Az Azure DDoS-védelem dokumentációja](../virtual-network/ddos-protection-overview.md)
