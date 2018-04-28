---
title: Ajánlott eljárások Azure DDoS-védelem & referencia-architektúrában |} Microsoft Docs
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
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS-védelem: Ajánlott eljárások és hivatkozhat architektúrája

Ez a dokumentum célja informatikai döntéshozók és biztonsági csoporthoz. A tesztkörnyezet az Azure-ral, hálózati és biztonsági várt.

Elosztott szolgáltatásmegtagadásos (DDoS-) tervez rugalmassági van szükség, tervezése és kialakítása során függően számos különböző. Ez a dokumentum a következő gyakorlati tanácsokat tervezése során az alkalmazások az Azure-ban, a rugalmasságot DDoS-támadások ellen.

## <a name="types-of-attacks"></a>Jellegű támadások

DDoS egy alkalmazás-erőforrásokat lefoglalhat használt támadás típusú. A cél, hogy az alkalmazás rendelkezésre állásának és jogos kérelmek kezelésének képessége hatással. Támadások egyre több kifinomult és nagyobb méretű és a hatása. DDoS-támadások tud célozható tetszőleges végpontot, amely nyilvánosan elérhető az interneten keresztül.

Azure biztosít folyamatos védelem DDoS-támadások ellen. Ez a védelem integrálva van az Azure platformon alapértelmezés szerint, és minden további költség nélkül. A platform megadott DDoS-védelem core mellett is tudunk nevű új ajánlat "[Azure DDoS védelem szabványos](https://azure.microsoft.com/services/ddos-protection/)", amely hálózati támadások elleni speciális DDoS megoldás képességeket biztosít, és automatikusan beállítva, hogy az adott Azure-erőforrások védelme. Védelem használata egyszerű ahhoz, hogy az új virtuális hálózatok létrehozása során. Kezdeti létrehozás után is elvégezhető, és nem kell alkalmazás vagy erőforrás módosítani.

![](media/azure-ddos-best-practices/image1.png)

DDoS-támadások széles körű lehet sorolni három (3) más kategóriák

### <a name="volumetric-attacks"></a>-Es támadások

-Es támadások a leggyakoribb DDoS-támadások. -Es támadások találgatásos támadások, a hálózati és a szállítási réteg célzó. Azok megkísérlik az erőforrások lefoglalhat ilyen hálózati kapcsolatokon. Több fertőzött rendszer használata esetén az ilyen támadások általában számára a hálózati rétegek látszólag jogos forgalom jelentős mennyiségű kéréssekkel. Ilyen típusú támadások különböző hálózati szintű protokollok, például az Internet Control Message Protocol (ICMP), a User Datagram Protocol (UDP) és a Transmission Control Protocol (TCP) használnak.

A leggyakrabban használt hálózati réteg DDoS-támadások: TCP SZIN elárasztás, az ICMP echo, a UDP elárasztás, a DNS és a NTP erősítési támadás. Ilyen típusú támadások segítségével nemcsak megszakadhat a szolgáltatás, hanem az, a több nefarious és célzott hálózati behatolás egy smokescreen. A legutóbbi-es támadás például a [Memcached biztonsági rés](https://www.wired.com/story/github-ddos-memcached/) , amely befolyásolja a Githubon. A támadás UDP-port 11211 céloz, és létrehozott 1.35 Tb/s támadás kötet.

### <a name="protocol-attacks"></a>Protokoll támadások

Protokoll cél alkalmazásprotokollokon támadásokat. Próbálják infrastruktúra eszközök például tűzfalak, az alkalmazás-kiszolgálókat és a terheléselosztók az összes elérhető erőforrást felhasználják. Protokoll támadások használja a csomagok, amelyek nem megfelelően formázott vagy protokoll rendellenességeket tartalmaznak. Ilyen támadások több nyitott kérelmek, kiszolgálók és egyéb kommunikációs eszközök válasz és a csomag válaszra való várakozás elküldésével fog működni. A cél megkísérli a nyitott kérelmek felé, ami a célzott rendszer összeomlását válaszolni.

A leggyakoribb a protokoll-alapú DDoS-támadások példája TCP SZIN árvíz. A támadás TCP SZIN kérelmek több cél felé irányítja a rendszer, és ne terhelje tovább, akkor is használható. A cél, hogy ellenőrizze a cél nem válaszol. 2016 DIN leállás mellett alkalmazásszintű támadás alatt állt TCP SZIN árvizek célzó DIN tartozó DNS-kiszolgálók 53-as port is.

### <a name="resource-attacks"></a>Erőforrás támadások

Erőforrás támadások az alkalmazási rétegre célként. Háttér-folyamatok azok indítás, annak érdekében, hogy az ne terhelje tovább a célrendszeren. Erőforrás a normál keres, de ez végzi processzorigényes lekérdezi a kiszolgálóhoz való visszaélés forgalom támadásokat. A szükséges erőforrások lefoglalhat forgalom mennyiségének viszonylag alacsonyabb, mint a más típusú támadások. Az erőforrás-támadások nem különböztethetők meg jogos forgalmat, így nehezen észlelhető akkor. A leggyakrabban használt erőforrás támadások HTTP/HTTPS- és DNS szolgáltatások esetén.

## <a name="shared-responsibility-in-the-cloud"></a>Megosztott felelős a felhőben

A növekvő típusok és képzettsége támadások elleni védelmi részletes stratégia szükséges. Biztonsági megosztott feladata az ügyfél és a Microsoft között. Microsoft hivatkozik, mint egy [megosztott felelősségi modell](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Az alábbi ábra a részlege felelős.

![](media/azure-ddos-best-practices/image2.png)

Azure-ügyfél kihasználhassa az ajánlott eljárások áttekintését és felépítése globálisan elosztott alkalmazások és a következő tesztelése sikertelen.

## <a name="fundamental-best-practices"></a>Alapvető ajánlott eljárások

DDoS-támadások és a megnövekedhet. A következő szakasz előíró útmutatást DDoS rugalmas szolgáltatások létrehozása az Azure-kiszolgálón.

### <a name="design-for-security"></a>Biztonság – tervezés

Az ügyfelek biztosítania kell biztonsági prioritást során az alkalmazás, a tervezési és megvalósítási történő központi telepítése és üzemeltetése teljes életciklusát. Az alkalmazások, amelyek lehetővé teszik egy viszonylag kis mennyiségű kialakított kérést egy túlságosan mennyiségű erőforrást, ami azt eredményezi, hogy szolgáltatáskimaradás használandó hibák is rendelkezhetnek. Microsoft Azure-on futó szolgáltatás védelme érdekében ügyfelek kell alaposan megismernie az alkalmazás architektúráját, valamint kell összpontosítania, a [szoftver minőségű 5 oszlopok](https://docs.microsoft.com/azure/architecture/guide/pillars).
Az ügyfelek tipikus forgalommal, érdemes tudni a az alkalmazás és más alkalmazások és a nyilvános internethez kitett szolgáltatásvégpontokra közötti kapcsolat modell.

Továbbá rugalmasak magának az alkalmazásnak irányuló szolgáltatásmegtagadást kezelni egy alkalmazás biztosítva van kiemelkedő fontosságú. Biztonság és adatvédelem az Azure platformon, kezdve be van építve a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx). Az SDL biztonsági címek minden fejlesztési fázisában, és biztosítja, hogy Azure folyamatosan frissül még biztonságosabbá teszi.

### <a name="design-for-scalability"></a>Méretezhetőség kialakítása

A méretezhetőség a rendszernek a megnövekedett terhelés kezelésére vonatkozó képessége. Az ügyfelek kell tervezése az alkalmazások [horizontálisan méretezhető](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) felerősített terhelés, különösen a DDoS-támadások esemény az igény kielégítésére. Ha az alkalmazás attól függ, hogy a szolgáltatás egyetlen példányát, a hibaérzékeny pontot hoz létre. Kiépítés több példány növeli a rugalmasságot és a méretezhetőséget.

A [Azure App Service](../app-service/app-service-value-prop-what-is.md), jelölje be egy [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , amely több példányt biztosít. Azure-szolgáltatásokhoz, konfigurálhatja a szerepkörök használandó [több példány](../cloud-services/cloud-services-choose-me.md). A [Azure virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), győződjön meg arról, hogy a virtuális gép architektúrája tartalmazza az egynél több virtuális gép és az egyes virtuális gépek szerepel egy [rendelkezésre állási csoport](../virtual-machines/virtual-machines-windows-manage-availability.md). Azt javasoljuk, [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) automatikus skálázás képességeinek.

### <a name="defense-in-depth"></a>Mélyreható védelmet

Mélyreható védelmet mögött lényege különböző védelmi stratégiák kockázatkezelés. Rétegez egy alkalmazás biztonsági védelmekkel csökkenti egy sikeres támogatás esélyét. Azt javasoljuk, hogy az ügyfelek valósítja meg az Azure platform beépített képességeit keresztül az alkalmazások biztonságos tervekhez.

Például a támadás veszélyét növeli a méretet, vagy az alkalmazás támadási felületét. Felületének csökkentése érdekében ajánlott keresztül engedélyezett lezárja-e a kitett IP cím terület & figyelő portot, amelyek nem szükségesek a terheléselosztók ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[App átjáró](../application-gateway/application-gateway-create-probe-portal.md)) vagy keresztül [hálózati biztonsági csoportok (NSG).](../virtual-network/virtual-networks-nsg.md)
Használhat [címkék szolgáltatás](/virtual-network/security-overview.md) és [biztonsági csoportok](/virtual-network/security-overview.md) minimalizálása érdekében a biztonsági szabály létrehozásához összetettségét, és a hálózati biztonság beállítása egy alkalmazás struktúra természetes bővítménye.

Az ügyfelek telepítse az Azure-szolgáltatások egy [virtuális hálózati](../virtual-network/virtual-networks-overview.md) amikor csak lehetséges. Ez lehetővé teszi, hogy a szolgáltatás erőforrásainak kommunikációja áthaladjon magánhálózati IP-címét. Egy virtuális hálózatot az Azure szolgáltatás forgalmát forrás IP-cím nyilvános IP-címek alapértelmezés szerint használja. Használatával [szolgáltatás végpontjait](../virtual-network/virtual-network-service-endpoints-overview.md) vált, szolgáltatás-forgalom virtuális hálózati Magáncímeket használják a forrás IP-címek, ha egy virtuális hálózatot az Azure-szolgáltatás eléréséhez.

Az ügyfél helyszíni tehesse erőforrásaik az Azure-ban első megtámadott erőforrások gyakran látható. Azure csatlakozik egy helyszíni környezetben, azt javasoljuk, hogy elérhetővé tegyék a helyszíni erőforrások a nyilvános internethez minimálisra csökkenthető. A méretezés és az Azure fejlett DDoS-védelem funkcióinak használatához, üzembe helyezése az Azure-ban a jól ismert nyilvános entitásokat. Mivel ezek nyilvánosan elérhető entitások gyakran egy cél DDoS-támadások, a helyszíni erőforrások gyakorolt hatás üzembe őket az Azure-ban csökkenti.

## <a name="azure-ddos-protection"></a>Az Azure DDoS-védelem

Azure két adja meg a hálózati támadások (3 & 4 réteg) - DDoS alapszintű és a szabványos DDoS-védelem a védelem DDoS service offerings rendelkezik. 

### <a name="azure-ddos-basic-protection"></a>Az Azure alapvető DDoS-védelem

Alapszintű védelmi integrálva van az Azure alapértelmezés szerint a további költség nélkül. A teljes skála és Azure globálisan telepített hálózati kapacitása biztosít szolgálhat közös hálózati réteg támadások elleni keresztül mindig a forgalom figyelési és valós idejű megoldás. DDoS Basic védelem nélküli felhasználói konfigurációs vagy az alkalmazás módosítását igényli. Alapszintű DDoS-védelem által védett összes Azure-szolgáltatások többek között a PaaS szolgáltatások, például az Azure DNS-ben.

![](media/azure-ddos-best-practices/image3.png)

Alapszintű Azure DDoS-védelem szoftver- és hardverkonfigurációiról összetevőkből áll. A szoftver vezérlő vezérlősík úgy dönt, mikor, ahol, és milyen kell kormányzott, elemzése, és távolítsa el a támadás forgalom hardver készülékek keresztül. A vezérlő vezérlősík teszi ehhez a döntéshez alapuló infrastruktúra kiterjedő DDoS-védelem *házirend*, amely statikusan beállította és egységesen alkalmazza az összes Azure-ügyfél számára.

Például a DDoS-védelem házirend azt szabja meg, milyen adatforgalma, a védelem nem lehet *indított* (Ez azt jelenti, hogy a bérlői forgalom irányítani kell a tisztítási készülékek keresztül), és ezt követően hogyan a tisztítási készülékek kell *mérséklése* a támadás.

DDoS védelem alapszintű Azure-szolgáltatás irányuló infrastruktúra védelmét és az Azure platformon védelméről. Megszünteti a forgalmat, amikor az meghaladja a, hogy ez hatással lehet egy több-bérlős környezetben több ügyfél jelentős mértékben. Nem biztosít lehetőséget, vagy ügyfél testreszabott házirendeket.

### <a name="azure-ddos-standard-protection"></a>Az Azure szabványos DDoS-védelem

Standard védelem DDoS megoldás speciális funkciókat is biztosítanak, és van beállítva, hogy az adott Azure-erőforrások virtuális hálózat védelme automatikusan. Egyszerű ahhoz, hogy minden új vagy meglévő virtuális hálózat és az szükséges, alkalmazás vagy erőforrás-változása. Azt az alapvető szolgáltatás, így a naplózást, a riasztás és a telemetriai adatok keresztül több előnye is van. Az alábbiakban leírt van a kulcs differentiators Azure DDoS védelem szabványos szolgáltatás.

#### <a name="adaptive-realtime-tuning"></a>Adaptív valós idejű hangolása

Az Azure DDoS védelem alapszintű service felhasználók védelme érdekében, de csak védje azokat más ügyfelek befolyásolása érdekében. Ha egy szolgáltatás ki van építve, egy tipikus kötet jogos bejövő forgalmat, amely kisebb, mint például a *eseményindító arány* infrastruktúra kiterjedő DDoS védelem házirend egy adott ügyfélhez erőforrások DDoS támadást léphet észrevétlenül. Általában összetett jellege legutóbbi támadások (például több vektoros DDoS-), valamint az alkalmazás-specifikus viselkedésmódok bérlő hívás /-ügyfél, testre szabott adatvédelmi szabályzatok.
Mindez két insights segítségével:

1. Az automatikus tanulás /-ügyfél (-IP) réteg 3 vagy 4 forgalmat, és
2. Minimalizálja a téves fényében, hogy Azure méretezési lehetővé teszi, hogy felvegye jelentős mennyiségű forgalom.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS védelem telemetriai, figyelés és riasztás

DDoS védelem standard elérhetővé kell tenni keresztül részletes telemetria [Azure figyelő](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) DDoS-támadások időtartama alatt. Riasztási bármely az Azure-figyelő metrikák DDoS-védelem által konfigurálható. Naplózási integrálható Splunk (az Azure Event Hubs), az Azure Naplóelemzés és az Azure Storage speciális elemzés a Azure figyelő Diagnostics felületén keresztül.

##### <a name="ddos-mitigation-policies"></a>DDoS megoldás házirendek

Az Azure portálon kattintson **figyelő** \> **metrikák**. Az a **metrikák** képernyőn, válassza ki az erőforráscsoportot, nyilvános IP-cím erőforrás típusa és az Azure nyilvános IP-címe. DDoS mérőszámok alapján az elérhető mérőszámok ablaktáblán látható lesz.

DDoS védelem szabványos vonatkozik **három autotuned megoldás házirendek (TCP SZIN, TCP és UDP)** minden nyilvános IP-cím a védett erőforrás, a vnetben, amely DDoS engedélyezve van. A metrika kiválasztásával megtekintheti a házirend küszöbértékek **"Bejövő csomagok való DDoS megoldás"**.

![](media/azure-ddos-best-practices/image7.png)

A házirend küszöbértékek a gépi tanulási a alapú hálózati forgalom profilkészítési keresztül automatikusan konfigurált. Támadás alatt lévő IP-cím DDoS megoldás akkor következik be, csak akkor, ha a házirend küszöbérték elérése esetén.

##### <a name="under-ddos-attack"></a>DDoS támadás

Ha a nyilvános IP-cím támadás alatt áll, a metrika "alatt DDoS támadásokkal vagy nem" érték ki-és 1, a megoldás a támadás forgalom végezzük.

![](media/azure-ddos-best-practices/image8.png)

Azt javasoljuk, hogy ez a metrika, ha van egy aktív DDoS megoldás értesítést szeretne kapni a riasztás konfigurálása a nyilvános IP-cím végre.

További információkért lásd: [kezelése Azure DDoS védelem szabványos az Azure portál használatával](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Erőforrás támadások

Adott erőforrás támadásokkal szemben, az alkalmazás rétegben, az ügyfelek, konfigurálni kell webes alkalmazás tűzfalat (WAF) segítségével biztonságos webes alkalmazásokhoz. WAF megvizsgálja a bejövő webes forgalom blokkolása SQL alkalommal idegen, DDoS és más réteg 7 támadásoknak. Azure biztosít [Alkalmazásátjáró szolgáltatásként WAF](../application-gateway/application-gateway-web-application-firewall-overview.md) a webalkalmazások a gyakori biztonsági réseket és biztonsági rések központosított védelemre. Nincsenek elérhető, akkor lehetnek megfelelőbbek, az Ön igényeinek keresztül Azure partnerektől származó WAF ajánlatok fajtáinak a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Webalkalmazási tűzfalak még akkor is, amelyek ki vannak téve-es & állapot Erőforrásfogyás támadásokkal szemben. Erősen ajánlott DDoS Standard védelmet a virtuális hálózaton WAF-es elleni védelméhez bekapcsolásával & protokoll támadásokat. További információkért lásd: a referencia architektúra szakasz.

### <a name="protection-planning"></a>Védelem tervezése

Tervezése és előkészítése fontosságúak megérteni, hogyan hajtsa végre a rendszer a DDoS-támadások során. A tervezést és előkészítést kíván is alakítsa ki egy incidenskezelési válaszadási tervet.

Az ügyfelek győződjön meg arról, hogy Standard DDoS-védelem engedélyezve van a virtuális hálózat az internet felől elérhető végpontok. DDoS-riasztások konfigurálása segít egy állandó tartsuk követheti az összes lehetséges támadás a infrastruktúra. Az ügyfelek egymástól függetlenül figyelje az alkalmazásaikat. Az alkalmazás normál működésének megértése szükséges. Lépéseket kell tenni, ha az alkalmazás viselkedik a DDoS-támadások során a várt módon.

#### <a name="ddos-attacks-orchestration"></a>DDoS-támadások vezénylési

Ajánlott a feltételezéseket hogyan a szolgáltatások válaszolni a támadás, még mielőtt akkor fordul elő, rendszeres szimulációja végzett tesztelése. Tesztelés során ellenőrzi, hogy a szolgáltatások és alkalmazások továbbra is a kívánt módon működik, és nem a végfelhasználói élmény megszakadását van. Azonosítsa a technológia és a folyamat szempontból hézagok, és azokat a DDoS válasz stratégia. Egy általános javasoljuk, hogy az ilyen tesztek kerülnek végrehajtásra átmeneti környezetek vagy a nem csúcsidőre éles környezetbe történő gyakorolt hatásának minimalizálása érdekében.

Azt a közösen [BreakingPoint felhő](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre az illesztőfelület Ha Azure-ügyfél hozhat létre a forgalom elleni DDoS-védelem engedélyezve szimulációja nyilvános végpontjai. [Töréspont felhő](https://www.ixiacom.com/products/breakingpoint-cloud) szimuláció lehetővé teszi:

- Ellenőrizze, hogy hogyan Microsoft Azure DDoS-védelem védelmet nyújt az Azure-erőforrások DDoS-támadások

- Az incidensekre adott reakciók folyamat DDoS támadás optimalizálása

- A dokumentum DDoS megfelelőségi

- A hálózati biztonsági csoportok képzése

Számítógépes egy állandó innováció védelmet igénylő folyamatos túlélésért folyó. Azure DDoS Standard védelem egy a legkorszerűbb kínál a legkorszerűbb egyre DDoS-támadások mérséklése egy hatékony megoldás rendelkező ügyfelek számára.

## <a name="components-of-a-ddos-response-strategy"></a>DDoS válasz stratégia összetevők

A legtöbb esetben a DDoS-támadások célja az Azure-erőforrások, nincs minimális beavatkozás szükséges egy végfelhasználói tükrözik. Továbbra is DDoS tartalmazó megoldás a szervezete incidensválaszra stratégia részeként biztosítja az üzletmenet folytonossága minimális hatással van.

### <a name="microsoft-threat-intelligence"></a>Microsoft fenyegetésfelderítési adataival

A Microsoft által használt egy kiterjesztett biztonsági Közösség, amely támogatja a Microsoft online services, Microsoft-partnerek és az internetes biztonsági Közösségben kapcsolatok együttes ismerete kiterjedt fenyegetés eszközintelligencia hálózattal rendelkezik. Kritikus fontosságú infrastruktúra-szolgáltatóként a Microsoft megkapja a korai figyelmeztetéseket kapcsolatos fenyegetések, megtudta, más Microsoft online services és a Microsoft globális ügyfelei fenyegetésfelderítési adataival alap vesz igénybe. Az összes Microsoft fenyegetésfelderítési adataival van építve vissza az Azure DDoS-védelem mindegyikére.

Továbbá a Microsoft Digital Crimes Unit (DCU) DDoS-támadások elleni botnetek, egy közös forrás parancs és a vezérlő sértő stratégiák hajt végre.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Hajtsa végre a kockázat kiértékelésekor az Azure-erőforrások

rendkívül fontos tudni, hogy egy folyamatos DDoS-támadások elleni a kockázat körét. Az ügyfelek rendszeresen kérdezze meg magukat: milyen új nyilvánosan elérhető Azure-erőforrások védelmét kell? Van-e a szolgáltatás hibáinak hibaérzékeny pontot? Hogyan szolgáltatások el lehet különíteni a támadás során továbbra is szolgáltatások elérhetővé tételére érvényes ügyfelek gyakorolt hatást? Vannak-e virtuális hálózatok, ahol DDoS védelem szabványos engedélyezni kell, de nem? A szolgáltatás aktív vannak a feladatátvételi különféle régiókban?

### <a name="customer-ddos-response-team"></a>Ügyfél DDoS csoportnak

DDoS válasz csapat biztosításához a támadás gyors és hatékony válasz egy fő lépés. Ügyfelek különböző ügyfelek a szervezetben, akik fog felügyeletéért megtervezése és végrehajtása meg kell határoznia. DDoS csoportnak kell lennie az Azure DDoS védelem szabványos szolgáltatás alapos ismerete, és azonosítása és a támadás összehangolása a különböző belső és külső ügyfelek, beleértve a Microsoft támogatási csoport kiküszöböléséhez apt kell lennie szükség esetén.

A Microsoft azt javasolja, amely DDoS csoportnak tervezési és a szimuláció gyakorlatokat, beleértve a érint, a szolgáltatás rendelkezésre állása és folytonosságának megtervezése normál részeként. 

### <a name="during-an-attack"></a>A támadás során

Az Azure DDoS védelem szabványos fog azonosításában és kiküszöbölésében DDoS-támadások felhasználói beavatkozás nélkül. Tájékoztatást kaphat nincs egy aktív megoldás védett nyilvános IP-címhez, akkor [olyan riasztást konfigurálhat](../virtual-network/ddos-protection-manage-portal.md) a metrika a "alatt DDoS támadásokkal vagy nincs". Tekintse át a további, és létre riasztásokat a kívánt módon működjenek a más DDoS metrikáihoz tudni, hogy a skála a támadás, eldobott forgalom, stb.

#### <a name="when-to-contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft támogatási szolgálatához

- Ha a DDoS-támadások során azt találja, hogy a teljesítmény, a védett erőforrás súlyosan csökken, mert, vagy az erőforrás nem érhető el.

- Ha úgy gondolja, hogy a DDoS-védelem szolgáltatás nem várt módon viselkedik. DDoS-védelem szolgáltatás csak kezdeményezése megoldás, ha az alábbi feltételek teljesülése esetén:

    - A metrika értékét "való DDoS megoldás TCP/TCP SZIN/UDP-házirend verziója alacsonyabb, mint a védett nyilvános IP-cím erőforrás fogadott.

- Ha ismeri, kívánja irányítja a hálózati forgalom jelentősen növelheti tervezett ugrásszerű esemény rendelkezik.

- Ha egy szereplő rendelkezik veszélyeztetett elindíthatja a DDoS-támadások az erőforrásokon.

Kritikus fontosságú üzleti érintő problémákat, hozzon létre egy súlyossági-A [támogatja a jegy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>POST támadás lépései

Célszerű mindig egy futás utáni támadás után ne, és állítsa be újra a DDoS válasz stratégia, igény szerint stratégiát. Vegye figyelembe a következőkre:

- Lett nincs fennakadást a szolgáltatás vagy a végfelhasználói élmény méretezhető architektúra erőforráshiány?

- Mely alkalmazások vagy szolgáltatások jelentkezett a legtöbb?

- Hogyan hatékony volt a DDoS válasz stratégia, és hogyan azt javítható további?

Ha azt gyanítja, hogy egy DDoS támadás alatt áll értesítse, míg a normál Azure támogatási csatornákon keresztül.

## <a name="ddos-protection-reference-architectures"></a>DDoS védelem referencia architektúra

DDoS védelem szabványos úgy van kialakítva, [vannak telepítve, a virtuális hálózat számára.](../virtual-network/virtual-network-for-azure-services.md) Egyéb szolgáltatások esetén az alapértelmezett alapvető DDoS-védelem érvényesek. A megadott hivatkozás architektúrák az alábbiakban leírt architektúra mintákat csoportosított forgatókönyvek, szerint vannak rendezve.

### <a name="virtual-machine-windowslinux-workloads"></a>Virtuális gép (Windows/Linux) munkaterhelések

#### <a name="application-running-on-load-balanced-vms"></a>Alkalmazás fut a terhelés eloszlik a virtuális gépek

Ez a referenciaarchitektúra bevált eljárásokat mutat be, amelyekkel a méretezhetőség és a rendelkezésre állás javítása érdekében több Windows rendszerű virtuális gép (VM) futtatható egy terheléselosztó mögötti méretezési csoportban. Ez az architektúra bármely állapot nélküli alkalmazások és szolgáltatások, például webkiszolgálót használható.

![](media/azure-ddos-best-practices/image9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Az architektúra egyetlen nyilvános IP-címet használ, és az internetes forgalom terheléselosztón keresztül jut el a virtuális gépekhez. DDoS védelem szabványos engedélyezve van az Azure (internet) terheléselosztó, amely rendelkezik a nyilvános IP-cím hozzárendelve a virtuális hálózaton.

A terheléselosztó osztja el a Virtuálisgép-példányok bejövő Internet kéréseket. Virtuálisgép-méretezési készlet lehetővé teszi a bejövő vagy kimenő tolja virtuális gépek számát manuálisan vagy automatikusan előre meghatározott szabályok alapján. Ez azért fontos, ha az erőforrás DDoS támadás alatt áll. Tekintse meg a [cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), a referencia-architektúrában olvashat.

#### <a name="applications-running-on-windows-n-tier"></a>Futó Windows N szintű alkalmazásokhoz

Egy N szintű architektúra számos módon implementálható. Az ábrán egy tipikusnak mondható 3 szintű webalkalmazás látható. Ez az architektúra épül [futtassa a virtuális gépek elosztott terhelésű a méretezhetőség és a rendelkezésre állási](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![](media/azure-ddos-best-practices/image10.png)

A fenti architektúrában szabványos DDoS-védelem engedélyezve van a virtuális hálózaton. Minden nyilvános IP-címek a virtuális hálózat kap Layer3/Layer4 DDoS-védelem. A réteg 7 védelem Application Gateway WAF termékváltozatban kell telepíteni. Tekintse meg [ez](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) cikkben olvashat a referencia-architektúrában.

#### <a name="paas-web-application"></a>A PaaS-webalkalmazás

A referencia-architektúrában jeleníti meg az Azure App Service-alkalmazást futtat egy régió. Ez az architektúra látható bevált gyakorlatok a webes alkalmazás esetén állítsa be [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) és [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Készenléti régió feladatátvételi forgatókönyvek be van állítva.

![](media/azure-ddos-best-practices/image11.png)

A TRAFFIC Manager irányítja a bejövő kérelmek alkalmazás átjárónak a régiók egyikéhez sem. A normál működés során az Alkalmazásátjáró aktív régióban irányítja a kérelmeket. Ha az adott régióban, a Traffic Manager feladatátadás az Alkalmazásátjáró készenléti régióban.

Biztosítására, hogy a webalkalmazásnak szánt internetről minden forgalom a [alkalmazás átjáró nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) a Traffic Manager keresztül. Ebben a forgatókönyvben, az app service (webalkalmazások) önmagában nem közvetlenül kívülről elérhető, és Alkalmazásátjáró védi. Javasoljuk, hogy az alkalmazás átjáró WAF SKU konfigurálása (akadályozza) réteg 7 (HTTP vagy HTTPS/webes szoftvercsatorna) támadások elleni védelem érdekében. Emellett a webalkalmazások vannak konfigurálva [fogadja el a forgalom csak az Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-címet.

Tekintse meg [ez](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) cikkben olvashat a referencia-architektúrában.

### <a name="mitigation-for-non-web-paas-services"></a>A megoldás nem webes PaaS szolgáltatások

#### <a name="hdinsight-on-azure"></a>Az Azure HDInsight

A referencia-architektúrában jeleníti meg a DDoS védelem majd konfigurálása [HDInsight-fürt](https://docs.microsoft.com/azure/hdinsight/) az Azure-on. Ellenőrizze, hogy a HDInsight-fürthöz kapcsolódó virtuális hálózatot, és DDoS-védelem engedélyezve van a virtuális hálózatban kell.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

Ebben az architektúrában a HDInsight-fürthöz az internetről érkező adatforgalmat a HDInsight átjáró terheléselosztóhoz társított nyilvános IP-cím van átirányítva. Az átjáró terheléselosztó ezután elküldi a forgalmat az átjárócsomópontokkal vagy a feldolgozó csomópontok közvetlenül. Megadott szabványos DDoS-védelem engedélyezve van a HDInsight virtuális hálózaton, az összes nyilvános IP-címek a virtuális hálózat kap Layer3/Layer4 DDoS-védelem. A fenti referencia-architektúrában N-réteg/több-Region hivatkozás architektúrák kombinálva.

A referencia-architektúrában a további részletekért tekintse meg a [kiterjesztése Azure HDInsight Azure virtuális hálózat használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentációját.

### <a name="azure-api-management"></a>Az Azure API management

A referencia-architektúrában nyilvános végpontja védelmet nyújt a [API management](../api-management/api-management-key-concepts.md) erőforrás-közzétételi API-k az ügyfél számára a szervezeten kívül. A külső virtuális hálózatot DDoS-védelem engedélyezése APIM kell telepíteni.

![](media/azure-ddos-best-practices/image15.png)

A külső virtuális hálózat konfigurálása, az API Management gateway & fejlesztői portálon található, a nyilvános interneten keresztül egy nyilvános terheléselosztó. Ebben az architektúrában szabványos DDoS-védelem engedélyezve van a virtuális hálózati APIM külső virtuális hálózat. Továbbítódik az internetről a nyilvános IP-címére a APIM, amelyek Layer3/Layer4 hálózati támadások elleni védelmét. Réteg 7 HTTP/HTTPs lánctámadások elleni védelem érdekében, konfigurálhatja az alkalmazás átjárót WAF módban.

A virtuális hálózaton található, telepítve van, és a DDoS védelem szabványos megmarad a további szolgáltatások listája [Itt](../virtual-network/virtual-network-for-azure-services.md). DDoS védelem szabványos csak támogatja az Azure Resource Manager erőforrásokat. *A nyilvános IP-cím a VNETEN belül injected alkalmazás Service környezetben (ASE) telepítési alapértelmezés szerint nem támogatott.* ASE környezet védelme a részletekért tekintse meg ezt a szakaszt.

## <a name="next-steps"></a>További lépések

* [Az Azure DDoS védelem termék oldalát](https://azure.microsoft.com/services/ddos-protection/)

* [Az Azure DDoS védelem Blog](http://aka.ms/ddosblog)

* [Az Azure DDoS-védelem dokumentációja ](../virtual-network/ddos-protection-overview.md)
