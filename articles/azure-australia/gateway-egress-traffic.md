---
title: A kimenő forgalom szabályozása az Azure Ausztráliában
description: Kulcsfontosságú elemek a kimenő forgalom szabályozásához az Azure-ban a biztonságos internetes átjárókkal kapcsolatos ausztráliai kormányzati követelmények teljesítése érdekében
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602086"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>A kimenő forgalom szabályozása az Azure Ausztráliában

Az IKT-rendszerek biztonságossá tételének alapvető összetevője a hálózati forgalom szabályozása. A kommunikáció korlátozása csak a rendszer működéséhez szükséges forgalomra, ami csökkenti a kompromisszumos veszélyt. Az alkalmazások és szolgáltatások által kommunikáló külső rendszerek láthatósága és szabályozása a feltört rendszerek észlelése, illetve a megkísérelt vagy sikeres kiszűrése. Ez a cikk tájékoztatást nyújt arról, hogyan működik a kimenő (kimenő) hálózati forgalom az Azure-ban, és javaslatokat tesz a hálózati biztonsági vezérlők megvalósítására egy olyan internetkapcsolattal rendelkező rendszer számára, amely az ausztrál Cyber Security Center (ASCS) szolgáltatáshoz igazodik. A fogyasztói útmutatás és a ASCS információs biztonsági kézikönyvének (ISM) szándéka.

## <a name="requirements"></a>Követelmények

A nemzetközösségi rendszerek általános biztonsági követelményei az ISM-ben vannak meghatározva. Ahhoz, hogy támogassa a nemzetközösségi entitásokat a hálózati biztonság megvalósításában _, a ASCs közzétette a ASCs védelmet: A hálózati szegmentálás és elkülönítés_megvalósítása, valamint a felhőalapú környezetekben a rendszerek biztonságossá tétele érdekében a ASCs közzétette a _felhőalapú számítástechnikai biztonságot a bérlők számára_.

A ASCS-dokumentumok körvonalazzák a hálózati biztonság megvalósításának és a forgalom szabályozásának kontextusát, és gyakorlati ajánlásokat nyújtanak a hálózat kialakításához és konfigurálásához.

Az Azure-beli kimenő forgalom szabályozásának következő főbb követelményei a ASCS-dokumentumokban vannak meghatározva.

Leírás|Source
--------------- |------------------
A **hálózati szegmentálás és elkülönítés megvalósítása**, például n szintű architektúra használata, gazdagép-alapú tűzfalakkal és hálózati hozzáférés-vezérléssel a bejövő és kimenő hálózati kapcsolat korlátozására csak a szükséges portok és protokollok használatával.| [Felhő-számítástechnika bérlők számára](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Megfelelően nagy sávszélességű, kis késleltetésű és megbízható hálózati kapcsolat implementálása** a bérlő (beleértve a bérlő távoli felhasználóit) és a felhőalapú szolgáltatás között a bérlő rendelkezésre állási követelményeinek kielégítése érdekében  | [ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Technológiák alkalmazása több, mint a hálózati rétegben**. Az egyes gazdagépeket és hálózatokat a gyakorlatilag felügyelhető legalacsonyabb szinten kell szegmentálni és elkülöníteni, ahol lehetséges. A legtöbb esetben ez az adatkapcsolati rétegre vonatkozik, amely akár az alkalmazás rétegét is tartalmazza; érzékeny környezetekben azonban célszerű lehet fizikai elkülönítést megadni. A gazdagép-alapú és a hálózatra kiterjedő mértékeket kiegészítő módon kell üzembe helyezni, és központilag figyelni kell. Csak a tűzfal vagy a biztonsági berendezés implementálása, mert az egyetlen biztonsági mérték nem elegendő. |[ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Használja a legalacsonyabb jogosultsági szintű és a szükséges**alapelveket. Ha egy gazdagépnek, szolgáltatásnak vagy hálózatnak nem kell kommunikálnia egy másik gazdagéptel, szolgáltatással vagy hálózattal, nem szabad engedélyezni a következőt:. Ha egy gazdagép, szolgáltatás vagy hálózat csak egy másik gazdagépre, szolgáltatásra vagy hálózatra van szüksége egy adott porton vagy protokollon, csak a portokra és protokollokra kell korlátozni. Ezen alapelvek hálózaton keresztüli elfogadásával kiegészíthető a felhasználói jogosultságok minimalizálása, és jelentősen növelhető a környezet általános biztonsági helyzete. |[ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
A **gazdagépek és hálózatok elkülönítése az üzleti műveleteknek való érzékenységük vagy kritikusság alapján**. Ennek része lehet különböző hardver vagy platformok használata a különböző biztonsági besorolások, biztonsági tartományok, illetve az egyes gazdagépek vagy hálózatok rendelkezésre állási/integritási követelményeitől függően. Külön felügyeleti hálózatok, valamint a sávon kívüli felügyeleti hálózatok fizikai elkülönítése érzékeny környezetekben. |[ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Azonosítsa, hitelesítse és engedélyezze az összes entitás hozzáférését az összes többi entitáshoz**. Minden felhasználónak, gazdagépnek és szolgáltatásnak hozzáféréssel kell rendelkeznie az összes többi felhasználóhoz, gazdagéphez és szolgáltatáshoz, és csak azokra korlátozódik, amelyekre a kijelölt feladatai vagy funkcióik végrehajtásához szükség van. Minden olyan örökölt vagy helyi szolgáltatást, amely megkerüli vagy leértékeli az azonosítási, hitelesítési és engedélyezési szolgáltatások erősségét, a lehető leghamarabb le kell tiltani, és szorosan figyelemmel kell kísérniük a használatot. |[ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
A letiltási **lista helyett a hálózati forgalom engedélyezési listájának implementálása**. Csak a jól ismert hálózati forgalom (azonosított, hitelesített és engedélyezett forgalom) hozzáférésének engedélyezése, nem pedig az ismert hibás hálózati forgalomhoz való hozzáférés (például egy adott címnek vagy szolgáltatásnak a blokkolása). A listák engedélyezésének engedélyezése esetén a házirendek kiváló biztonsági házirendet eredményeznek, és jelentősen javíthatják a kapacitást a potenciális hálózati behatolások észlelése és értékelése érdekében. |[ASCS-védelem: Hálózati szegmentálás és elkülönítés megvalósítása](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
Az **engedélyezett webhelyek engedélyezési listájának meghatározása és az összes nem listázott webhely blokkolása** hatékonyan eltávolítja a támadók által használt leggyakoribb adattovábbítási és kiszűrése-módszerek egyikét. Ha a felhasználók a különböző webhelyekhez vagy gyorsan változó webhelyekhez való hozzáférésre vonatkozó jogos követelménysel rendelkeznek; érdemes figyelembe vennie az ilyen implementáció költségeit. Még a viszonylag megengedhető engedélyezési lista is jobb biztonságot nyújt, mint a megtagadási listák vagy egyáltalán nem korlátozás, miközben továbbra is csökkenti a megvalósítási költségeket. Egy megengedő engedélyezési listára példa lehet a teljes ausztráliai altartomány, azaz a "*. au", illetve a felső 1 000-helyek engedélyezése az Alexa-hely rangsorában (a dinamikus tartománynévrendszer (DDNS) tartományok és egyéb nem megfelelő tartományok szűrése után).| [Ausztrál kormányzati információs biztonsági kézikönyv (ISM)](https://www.cyber.gov.au/ism)
|

Ez a cikk azokat az információkat és javaslatokat ismerteti, amelyekkel az Azure-környezetét elhagyó hálózati forgalom szabályozható. Az Azure-ban üzembe helyezett rendszereket az infrastruktúra-szolgáltatás (IaaS) és a szolgáltatásként szolgáló platform (Pásti) használatával fedi le.

Az [átjáró bejövő forgalmáról](gateway-ingress-traffic.md) szóló cikk a hálózati forgalomnak az Azure-környezetbe való beléptetését, a teljes hálózati vezérlési lefedettséget pedig a jelen cikkhez kapcsolódóan foglalkozik.

## <a name="architecture"></a>Architektúra

A kimenő forgalom megfelelő szabályozása érdekében a hálózati biztonság megtervezése és megvalósítása során meg kell ismernie, hogy a kimenő hálózati forgalom hogyan működik az Azure-ban mind a IaaS, mind a Péter-n keresztül. Ez a szakasz áttekintést nyújt arról, hogyan történik az Azure-ban üzemeltetett erőforrás által generált kimenő forgalom feldolgozása, és hogy milyen biztonsági vezérlők használhatók a korlátozáshoz és a forgalom szabályozásához.

### <a name="architecture-components"></a>Architektúra-összetevők

Az itt látható építészeti diagram megjeleníti azokat a lehetséges útvonalakat, amelyekkel a hálózati forgalom a virtuális hálózatban lévő alhálózatba helyezett rendszerből kilép. Egy virtuális hálózat forgalmát egy alhálózati szinten kezelik, és az útválasztási és biztonsági szabályok érvényesek a (z)-ben található erőforrásokra. A kimenő forgalomhoz kapcsolódó összetevők a rendszerekre, a hatékony útvonalakra, a következő ugrási típusokra, a biztonsági vezérlőkre és a Péter-ra irányuló adatokra vannak osztva.

![Architektúra](media/egress-traffic.png)

### <a name="systems"></a>Rendszerek

A rendszerek a virtuális hálózatok részét képező IP-alhálózaton belüli kimenő forgalmat előidéző Azure-erőforrások és kapcsolódó összetevők.

| Összetevő | Leírás |
| --- | ---|
|Virtuális hálózat (VNet) | A VNet az Azure-ban található alapvető erőforrás, amely platformot és határt biztosít az erőforrások üzembe helyezéséhez és a kommunikáció engedélyezéséhez. A VNet egy Azure-régióban található, és meghatározza az IP-címtartomány és az útválasztási határokat a VNet integrált erőforrásai, például a Virtual Machines számára.|
|Subnet | Az alhálózat egy IP-címtartomány, amely egy VNet belül jön létre. A hálózati szegmentálás VNet belül több alhálózat is létrehozható.|
|Hálózati adapter| A hálózati adapterek az Azure-ban található erőforrások. Csatolva van egy virtuális géphez, és a hozzá társított alhálózatból nem internetre irányítható IP-címet rendelt hozzá. Ez az IP-cím dinamikusan vagy statikusan van hozzárendelve Azure Resource Manageron keresztül.|
|Nyilvános IP-címek| A nyilvános IP-cím egy olyan erőforrás, amely a Microsoft tulajdonában lévő nyilvános, internetre irányítható IP-címeket foglal le a megadott régióból a virtuális hálózaton való használatra. Társítható egy adott hálózati adapterhez vagy a Pásti-erőforráshoz, amely lehetővé teszi az erőforrás számára, hogy kommunikáljon az internettel, a ExpressRoute és más Pásti-rendszerekkel.|
|

### <a name="routes"></a>Útvonalak

A kimenő forgalom elérési útja az adott erőforráshoz tartozó hatályos útvonalaktól függ, amely az összes lehetséges forrásból származó útvonalak és az Azure útválasztási logikájának alkalmazásából eredő útvonalak kombinációja.

| Összetevő | Leírás |
|--- | ---|
|Rendszerútvonalak| Az Azure automatikusan hoz létre rendszerútvonalakat, és a virtuális hálózatban lévő egyes alhálózatokhoz rendeli az útvonalakat. A rendszerútvonalak nem hozhatók létre és nem távolíthatók el, de néhány felülbírálható egyéni útvonalakkal. Az Azure alapértelmezés szerint létrehozza az egyes alhálózatok alapértelmezett rendszerútvonalait, és további opcionális alapértelmezett útvonalakat biztosít adott alhálózatokhoz vagy minden alhálózathoz, amikor bizonyos Azure-képességeket használ.|
|Service Endpoints – szolgáltatásvégpont| A szolgáltatási végpontok közvetlen, privát kimenő kapcsolatokat biztosítanak egy alhálózatból egy adott Pásti-képességhez. A szolgáltatás-végpontok, amelyek csak a Pásti-funkciók egy részhalmaza számára érhetők el, nagyobb teljesítményt és biztonságot biztosítanak a VNet-hez hozzáférő erőforrásokhoz.|
|Útvonaltáblák| Az útválasztási tábla egy Azure-beli erőforrás, amely olyan felhasználó által megadott útvonalak (UDR-EK) megadására hozható létre, amelyek kiegészítik vagy felülbírálják a Border Gateway Protocol használatával megszerzett rendszerútvonalakat és útvonalakat. Minden UDR megad egy hálózatot, egy hálózati maszkot és egy következő ugrást. Egy útválasztási tábla társítható egy alhálózathoz, és ugyanaz az útválasztási táblázat több alhálózathoz is társítható, de egy alhálózat csak nulla vagy egy útválasztási táblát tartalmazhat.|
|Border Gateway Protocol (BGP)| A BGP egy autonóm rendszerek közötti útválasztási protokoll. Az autonóm rendszer egy közös felügyelet alá tartozó hálózat vagy hálózatok csoportja, és közös útválasztási házirendekkel rendelkezik. A BGP az útválasztási információk autonóm rendszerek közötti cseréjére szolgál. A BGP virtuális hálózati átjárók használatával integrálható a virtuális hálózatba.|
|

### <a name="next-hop-types-defined"></a>Következő ugrási típusok definiálva

Az Azure-on belüli minden útvonal magában foglalja a hálózati tartományt és a társított alhálózati maszkot, valamint a következő ugrást, amely meghatározza a forgalom feldolgozásának módját.

Következő ugrási típus | Leírás
---- | ----
**Virtual Network** | Átirányítja a forgalmat a címtartomány között egy virtuális hálózat címterület területén belül. Az Azure létrehoz egy útvonalat a virtuális hálózat címterében meghatározott egyes címtartományoknak megfelelő címelőtaggal. Ha a virtuális hálózati címtartomány több címtartomány van meghatározva, az Azure minden címtartomány számára létrehoz egy egyedi útvonalat. Az Azure az egyes címtartományok számára létrehozott útvonalak használatával automatikusan irányítja a forgalmat az alhálózatok között egy VNet belül.
**Virtuális hálózatok közötti társviszony** | Ha a virtuális hálózat két virtuális hálózat között jön létre, az egyes virtuális hálózatok minden egyes címtartományból egy útvonal kerül a virtuális hálózatra, amelyhez a rendszer hozzárendeli azt. A rendszer a virtuális hálózatban lévő alhálózatokkal megegyező módon irányítja a forgalmat a virtuális hálózatok között.
**Virtuális hálózati átjáró** | Egy vagy több, a következő ugrási típusú virtuális hálózati átjáróval rendelkező útvonal hozzáadásakor a rendszer hozzáad egy virtuális hálózati átjárót egy virtuális hálózathoz. A benne foglalt útvonalak a helyi hálózati átjáró erőforráson és a BGP-n keresztül megszerzett útvonalakon vannak konfigurálva.
**Virtuális készülék** | A virtuális készülékek általában egy hálózati alkalmazást, például egy tűzfalat futtatnak. A virtuális berendezés lehetővé teszi a forgalom további feldolgozását, például szűrést, ellenőrzést vagy címfordítást. A virtuális készülék ugrási típusának minden útvonalán meg kell adnia egy következő ugrási IP-címet is.
**VirtualNetworkServiceEndpoint** | Egy adott szolgáltatás nyilvános IP-címei a VirtualNetworkServiceEndpoint következő ugrásával lesznek hozzáadva útvonalként egy alhálózathoz, amikor a szolgáltatási végpont engedélyezve van. A szolgáltatási végpontok engedélyezve vannak a virtuális hálózatban lévő egyes alhálózatokon lévő egyes szolgáltatásokhoz. Az Azure-szolgáltatások nyilvános IP-címei rendszeresen változnak. Az Azure automatikusan kezeli az útvonaltáblában lévő címeket a címek változásakor.
**Internet** | Az Internet következő ugrásával folytatott forgalom kilép a virtuális hálózatból, és automatikusan egy nyilvános IP-címen lesz lefordítva, akár a társított Azure-régióban elérhető dinamikus készletből, akár az adott erőforráshoz konfigurált nyilvános IP-cím használatával. Ha a cél címe az Azure egyik szolgáltatására vonatkozik, a rendszer a forgalmat közvetlenül az Azure gerinc hálózatán keresztül irányítja át, nem pedig az internet felé irányuló forgalmat. Az Azure-szolgáltatások közötti forgalom attól függetlenül sem halad át az interneten, hogy melyik Azure-régióban van a virtuális hálózat, vagy hogy mely Azure-régióban van üzembe helyezve az Azure-szolgáltatás példánya.
**Nincsenek** | A következő ugrással rendelkező forgalom el lett dobva. Az Azure a következő ugrás típusa nélkül hozza létre a rendszer alapértelmezett útvonalait a fenntartott címek előtagjaihoz. A következő ugrással rendelkező útvonalakat útválasztási táblázatok használatával is hozzáadhatja, hogy a forgalom ne legyen átirányítva az adott hálózatokra.
|

### <a name="security-controls"></a>Biztonsági vezérlők

Szabályozás | Leírás
----- | -----
**Hálózati biztonsági csoportok (NSG)** | A NSG az Azure-beli virtuális hálózati erőforrások felé és kívülre irányítja a forgalmat. A NSG szabályokat alkalmazhat az engedélyezett vagy megtagadott adatforgalomra, amely magában foglalja az Azure-on belüli, valamint az Azure és a külső hálózatok, például a helyszíni vagy az Internet közötti adatforgalmat. A NSG a virtuális hálózaton belüli alhálózatokra vagy az egyes hálózati adapterekre vonatkoznak.
**Azure Firewall** | A Azure Firewall felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely az Azure virtuális hálózati erőforrásait védi. Ez egy szolgáltatásként nyújtott teljesen állapotalapú tűzfal, beépített magas rendelkezésre állással és korlátlan felhőalapú skálázhatósággal. A Azure Firewall IP-címek, protokollok és portok alapján konfigurálhatók a hagyományos hálózati szűrési szabályokkal, de a teljes tartománynevek (FQDN), a szolgáltatási címkék és a beépített veszélyforrások felderítése alapján is támogatja a szűrést.
**Hálózati virtuális berendezés (NVA)** | A hálózati virtuális készülékek olyan virtuálisgép-adathordozók, amelyek hálózatkezelést, biztonságot és egyéb funkciókat biztosíthatnak az Azure-ban. A NVA támogatja a hálózati funkciókat és szolgáltatásokat a virtuális hálózatokban és az üzemelő példányokban lévő virtuális gépek formájában. A NVA a konkrét követelmények kezelésére, a felügyeleti és operatív eszközök integrálására, illetve a meglévő termékekkel való konzisztencia biztosítására használható. Az Azure számos olyan hálózati virtuális berendezést (például webalkalmazási tűzfalat, tűzfalakat, átjárókat/útválasztókat, alkalmazáskézbesítési vezérlőt és WAN-optimalizálót) támogat, amely külső gyártótól származik.
**Szolgáltatás-végponti házirendek (előzetes verzió)** | A Virtual Network szolgáltatás végpont-házirendjei lehetővé teszik a virtuális hálózati forgalom szűrését az Azure-szolgáltatásokra, így csak bizonyos Azure-szolgáltatásbeli erőforrásokat, a szolgáltatási végpontokat. A végpontszabályzatok lehetővé teszik az Azure-szolgáltatásokba irányuló virtuális hálózati forgalom részletes hozzáférés-vezérlését.
**Azure Policy** | Azure Policy egy Azure-szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére szolgál. Ezek a házirendek szabályok segítségével szabályozzák az üzembe helyezhető erőforrások típusát és az erőforrások konfigurációját. A szabályzatok használatával kényszeríthető a megfelelőség, ha megakadályozza az erőforrások telepítését, ha azok nem felelnek meg a követelményeknek, vagy a megfelelőségi állapot jelentésének figyelésére is használható.
|

### <a name="paas-egress"></a>Péter-i kijárat

A Pásti-erőforrások többsége nem eredményez kimenő forgalmat, de reagál a bejövő kérelmekre (például egy Application Gatewayra, tárhelyre, SQL Databasera stb.) vagy más erőforrásokból származó adatok továbbítására (például Service Bus és Azure Relay). A (z) és az Azure közötti hálózati kommunikációt, például a Storage-t és az SQL-adatbázisokat is App Services, a hálózati szegmentálás vagy a elkülönítés helyett az identitás és egyéb erőforrás-konfigurációs vezérlők biztosítják.

A virtuális hálózatba üzembe helyezett Pásti-erőforrások dedikált IP-címeket kapnak, és az útválasztási vezérlők és a NSG ugyanúgy érvényesek, mint a virtuális hálózat többi erőforrása. A virtuális hálózaton belül nem létező összes erőforrás olyan IP-címek készletét fogja használni, amelyek az erőforrás összes példányán meg vannak osztva, amelyek vagy a Microsoft-dokumentáción keresztül vannak közzétéve, vagy a Azure Resource Manager segítségével határozhatók meg.

## <a name="general-guidance"></a>Általános útmutatás

Az Azure-on belüli biztonságos megoldások kialakításához és létrehozásához kritikus fontosságú a hálózati forgalom megismerése és szabályozása, hogy csak az azonosított és engedélyezett kommunikációt lehessen megállapítani. Ennek az útmutatónak a célja, valamint a későbbi szakaszokban ismertetett összetevőkre vonatkozó útmutatás a [ASCs-védelemben ismertetett alapelvek alkalmazásához használható eszközök és szolgáltatások leírására szolgál. A hálózat szegmentálásának és elkülönítésének](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) megvalósítása az Azure-beli számítási feladatok között. Ez részletesen ismerteti, hogyan hozhat létre virtuális architektúrát az erőforrások biztonságossá tételéhez, ha nem lehet alkalmazni a helyszíni környezetben lehetséges hagyományos fizikai és hálózati vezérlőket.

### <a name="guidance"></a>Útmutatás

* A virtuális hálózatokra vonatkozó kimenő pontok számának korlátozása
* A rendszer alapértelmezett útvonalának felülbírálása minden olyan alhálózat esetében, amely nem igényel közvetlen kimenő kommunikációt az internethez
* Teljes hálózati architektúra kialakítása és megvalósítása az összes bejövő és kimenő pont Azure-erőforrásokhoz való azonosításához és szabályozásához
* Érdemes lehet sugaras hálózati kialakítást használni a virtuális hálózatokhoz a Microsoft Virtual adatközpont (VDC) dokumentációjában leírtaknak megfelelően.
* A beépített biztonsági képességekkel rendelkező termékek használata az internet felé irányuló kimenő kapcsolatokhoz (például Azure Firewall, hálózati virtuális berendezések vagy webproxyk)
* Használjon olyan identitás-vezérlőket, mint a szerepköralapú hozzáférés, a feltételes hozzáférés és a többtényezős hitelesítés (MFA) a hálózati konfigurációs jogosultságok korlátozásához
* A hálózati konfiguráció kulcsfontosságú elemeinek módosítását vagy törlését megakadályozó zárolások implementálása
* A Pásti üzembe helyezése a VNet integrált konfigurációjában a nagyobb elkülönítés és szabályozás érdekében
* ExpressRoute megvalósítása a helyszíni hálózatokkal való kapcsolathoz
* Virtuális magánhálózatok implementálása külső hálózatokkal való integrációhoz
* A Azure Policy használata a régiók és erőforrások csak azokra a korlátozására, amelyek szükségesek a rendszer működéséhez
* Az erőforrások alapkonfigurációjának biztonsági beállításainak kikényszerítása Azure Policy felhasználása
* Az Azure-on belüli hálózati forgalom naplózásának, naplózásának és láthatóságának Network Watcher és Azure Monitor kihasználása

### <a name="resources"></a>További források

Elem | Összekapcsolás
-----------| ---------
_Ausztrál szabályozási és szabályzatok megfelelőségi dokumentumai, beleértve a fogyasztói útmutatást_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure virtuális adatközpont_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_ASCS hálózati szegmentálás_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ASCS Cloud Security bérlők számára_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ASCS-információ biztonsági kézikönyve_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Összetevő-útmutatás

Ez a szakasz további útmutatást nyújt azokról az összetevőkről, amelyek az Azure-ban üzembe helyezett rendszerek kimenő forgalmára vonatkoznak. Az egyes szakaszok az adott összetevő célját ismertetik, és a dokumentációra és a konfigurációs útmutatókra mutató hivatkozásokat tartalmaznak, amelyek segítséget nyújtanak a tervezési és a létrehozási tevékenységekhez.

### <a name="systems-security"></a>Rendszerek biztonsága

Az Azure-ban lévő erőforrásokkal folytatott minden kommunikáció a Microsoft által karbantartott hálózati infrastruktúrán keresztül történik, amely csatlakozási és biztonsági funkciókat biztosít. A Microsoft az Azure platform és a hálózati infrastruktúra védelme érdekében automatikusan helyez el számos védelmet, és további képességeket biztosít az Azure-ban elérhető szolgáltatásokként a hálózati forgalom szabályozása és a hálózati szegmentálás kialakítása érdekében. szegregáció.

### <a name="virtual-network-vnet"></a>Virtuális hálózat (VNet)

A Virtual Networks az egyik alapvető építőeleme a hálózatkezelés számára az Azure-ban. A virtuális hálózatok határozzák meg az IP-címtartomány és az útválasztási határokat a különböző rendszerekben való használathoz. A virtuális hálózatok alhálózatokra vannak osztva, és a Virtual Network belüli összes alhálózatnak közvetlen hálózati útvonala van egymáshoz. A virtuális hálózati átjárók (ExpressRoute vagy VPN) használatával a virtuális hálózaton belüli rendszerek integrálható a helyszíni és külső környezetekkel, valamint az Azure által biztosított hálózati címfordítással (NAT) és a nyilvános IP-címek kiosztásával, a rendszerek segítségével csatlakozhat az internethez vagy más Azure-régióhoz és-szolgáltatásokhoz. A virtuális hálózatok megismerése és a kapcsolódó konfigurációs paraméterek és útválasztás létfontosságú a kimenő hálózati forgalom megismerése és szabályozása szempontjából.

Ahogy a virtuális hálózatok alkotják az alapszintű címtartomány és az útválasztási határt az Azure-ban, a hálózati szegmentálás és elkülönítés elsődleges építőelemeként is használhatók.

| Resource | Összekapcsolás |
| --- | --- |
| *Virtual Networks – áttekintés* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *A virtuális hálózatok útmutatójának megtervezése*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Virtual Network rövid útmutató létrehozása* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Az alhálózatok a hálózati szegmentálás és az Azure-beli elkülönítés kulcsfontosságú összetevői. Az alhálózatok a rendszerek közötti elkülönítés biztosítására használhatók. Az alhálózat egy olyan virtuális hálózaton belüli erőforrás, amelyben a NSG, az útválasztási táblák és a szolgáltatási végpontok vannak alkalmazva. Az alhálózatok a tűzfalszabályok és a hozzáférés-vezérlési listák forrás-és célhelyként is használhatók.

A virtuális hálózat alhálózatait úgy kell tervezni, hogy megfeleljenek a munkaterhelések és rendszerek követelményeinek. Az alhálózatok kialakításában vagy megvalósításában részt vevő személyeknek a hálózati szegmentálás ASCS iránymutatásai alapján kell meghatározniuk, hogy a rendszerek hogyan legyenek csoportosítva egy alhálózaton belül.

|Resource|Összekapcsolás|
|---|---|
|*Virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Hálózati adapter

A hálózati adapterek a virtuális gépekről érkező összes kimenő forgalom forrása. A hálózati adapterek lehetővé teszik az IP-címzés konfigurálását, valamint a NSG alkalmazására, illetve a NVA keresztüli útválasztási forgalom használatára. A virtuális gépek hálózati adaptereit úgy kell megtervezni és konfigurálni, hogy megfeleljenek az általános hálózati szegmentálási és elkülönítési célkitűzéseknek.

|Resource|Összekapcsolás|
|---|---|
|*Hálózati adapter létrehozása, módosítása vagy törlése* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Hálózati adapter IP-címzése*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>VNet integrált Pásti

A Péter nagyobb funkcionalitást és rendelkezésre állást biztosít, és csökkenti a felügyeleti terhelést, de megfelelő védelemmel kell rendelkeznie. A szabályozás növeléséhez, a hálózati szegmentálás betartatásához, illetve az alkalmazások és szolgáltatások biztonságos kilépési pontjának biztosításához számos, a virtuális hálózattal integrálható, nagy számú kilépési lehetőség is elérhető.

A Microsoft a rendszer-vagy alkalmazás-architektúra integrált részeként a Pásti használatával több mechanizmust biztosít a Pásti üzembe helyezéséhez egy virtuális hálózaton. Az üzembe helyezési módszer segítségével korlátozható a hozzáférés a belső rendszerekkel és alkalmazásokkal való kapcsolat és integráció biztosítása mellett. Ilyenek például a App Service környezetek, az SQL felügyelt példányai és egyebek.

Ha a Pétert olyan virtuális hálózatba helyezi üzembe, amelyben az útválasztási és a NSG vezérlők lettek implementálva, elengedhetetlen az erőforrás konkrét kommunikációs követelményeinek megértése, beleértve a Microsoft-szolgáltatásokból való felügyeletet, valamint a következő elérési utat: a kommunikációs forgalom akkor fog megjelenni, amikor a szolgáltatástól érkező kérésekre válaszol.

| Resource  | Összekapcsolás  |
| --- | --- |
| *Virtuális hálózat integrációja Azure-szolgáltatásokhoz* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Az alkalmazás integrálása Azure Virtual Network útmutatóval* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>Nyilvános IP-cím

A nyilvános IP-címek a virtuális hálózaton kívüli kommunikáció során használatosak. Ide tartozik a Pásti-erőforrások és az Internet következő ugrásával rendelkező útvonalak. A nemzetközösségi entitásoknak körültekintően kell megtervezniük a nyilvános IP-címek kiosztását, és csak olyan erőforrásokhoz kell azokat hozzárendelni, amelyeken valódi követelmény van. Általános tervezési gyakorlatként a nyilvános IP-címeket a virtuális hálózat, például Azure Firewall, VPN Gateway vagy hálózati virtuális berendezések számára felügyelt kimenő pontok számára kell lefoglalni.

|Resource|Összekapcsolás|
|---|---|
|*Nyilvános IP-címek áttekintése*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Nyilvános IP-cím létrehozása, módosítása vagy törlése* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Érvényes útvonalak

A tényleges útvonalak a rendszerútvonalak, a szolgáltatási végpontok, az útválasztási táblák és a BGP kombinációja, valamint az Azure útválasztási logikája együttes használatával meghatározott útvonalak. Ha a kimenő adatforgalmat a rendszer egy alhálózatról küldi ki, az Azure kiválaszt egy útvonalat a cél IP-cím alapján, a leghosszabb előtag-megfeleltetési algoritmus használatával. Ha több útvonal is tartalmazza ugyanazt a címelőtagot, akkor az Azure a következő prioritási elv alapján választ az útvonaltípusok közül:

1. Felhasználó által megadott útvonal
2. BGP-útvonal
3. Rendszerútvonal

Fontos megjegyezni, hogy a virtuális hálózattal, a virtuális hálózati kapcsolatokkal vagy a virtuális hálózati szolgáltatás-végpontokkal kapcsolatos forgalom rendszerútvonalai előnyben részesített útvonalak, még akkor is, ha a BGP-útvonalak pontosabbak.

Az Azure-beli útválasztási topológiák kialakításában vagy megvalósításában részt vevő személyeknek ismerniük kell, hogy az Azure hogyan irányítja a forgalmat, és olyan architektúrát fejlesszen, amely a szükséges biztonsággal és láthatósággal kiegyensúlyozza a rendszerek szükséges funkcióit. Ügyelni kell arra, hogy a környezet megfelelő módon megtervezze a túlzott beavatkozásokat és kivételeket az útválasztási viselkedésekben, mivel ez növeli az összetettséget, és nehezebbé és időigényesebb megoldásokkal próbálkozik.

| Resource | Összekapcsolás  |
| --- | --- |
| *Érvényes útvonalak megtekintése* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Rendszerútvonalak

A [](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes)rendszerútvonalak esetében a virtuális hálózatok kialakításában vagy megvalósításában részt vevő személyeknek ismerniük kell az alapértelmezett rendszerútvonalakat, valamint az útvonalak kiegészítéséhez vagy felülbírálásához rendelkezésre álló lehetőségeket.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A [szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) -végpontok egy alhálózaton való engedélyezése közvetlen kommunikációs útvonalat biztosít a kapcsolódó Pásti-erőforráshoz. Ez nagyobb teljesítményt és biztonságot biztosíthat, ha korlátozza a rendelkezésre álló kommunikációs útvonalat a szolgáltatáshoz. A szolgáltatási végpontok használata a lehetséges kiszűrése elérési utat mutatja be, mivel az alapértelmezett konfiguráció lehetővé teszi, hogy az alkalmazáshoz vagy rendszerhez szükséges konkrét példányok helyett hozzáférjen a Pásti szolgáltatás összes példányához.

A nemzetközösségi entitásoknak ki kell értékelniük a közvetlen hozzáférés biztosításával járó kockázatot, beleértve a nem használt elérési út valószínűségét és következményét.

A szolgáltatási végpontokhoz kapcsolódó lehetséges kockázatok csökkentése érdekében a szolgáltatás-végponti házirendeket implementálja, ahol lehetséges, vagy fontolóra veheti a szolgáltatási végpontok használatát egy Azure Firewall vagy NVA alhálózaton, és átirányíthatja a forgalmat az adott alhálózatról, ahol további a szűrés, a figyelés vagy a vizsgálat is alkalmazható.

|Resource|Összekapcsolás|
|---|---|
|*Oktatóanyag: A virtuális hálózati szolgáltatásbeli végpontokkal való hálózati hozzáférés korlátozása a Azure Portal használatával* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Útvonaltáblák

Az útválasztási táblázatok egy rendszergazda által konfigurált mechanizmust biztosítanak a hálózati forgalom vezérlésére az Azure-ban. Az útválasztási táblázatok segítségével átirányíthatja a forgalmat egy Azure Firewall vagy NVA, közvetlenül kapcsolódhat külső erőforrásokhoz, vagy felülbírálhatja az Azure rendszerútvonalait. Az útválasztási táblázatok használatával megakadályozható, hogy a virtuális hálózati átjárón keresztül megszerzett hálózatok elérhetők legyenek egy alhálózat erőforrásai számára a virtuális hálózati átjáró útvonal-propagálásának letiltásával.

|Resource|Összekapcsolás|
|---|---|
|*Útválasztási fogalmak – egyéni útvonalak* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Oktatóanyag: Hálózati forgalom irányítása* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

A BGP-t virtuális hálózati átjárók is felhasználhatják az útválasztási információk dinamikus, helyszíni vagy más külső hálózatokkal való cseréjére. A BGP a virtuális hálózatra vonatkozik, ha egy ExpressRoute virtuális hálózati átjárón keresztül konfigurálja a ExpressRoute, és ha engedélyezve van egy Azure-VPN Gateway.

Az Azure-ban a virtuális hálózatok és a virtuális hálózati átjárók kialakításában vagy megvalósításában részt vevő személyeknek időt kell igénybe venniük az Azure-beli BGP-hez elérhető viselkedési és konfigurációs lehetőségek megismerésére.

|Resource|Összekapcsolás|
|---|---|
|*Útválasztási fogalmak: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*ExpressRoute-útválasztási követelmények* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*A BGP és az Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Oktatóanyag: Helyek közötti VPN konfigurálása ExpressRoute Microsoft-partneri kapcsolaton keresztül* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Következő ugrási típusok

### <a name="virtual-network"></a>Virtuális hálózat

Az Virtual Network következő Ugrásával rendelkező útvonalakat a rendszer automatikusan hozzáadja a rendszerútvonalakhoz, de hozzáadhatók a felhasználó által megadott útvonalakhoz is, hogy a rendszer visszairányítsa a forgalmat a virtuális hálózatra olyan példányokban, ahol a rendszerútvonal felülbírálva van.

### <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

A VNet-társítás lehetővé teszi a két különálló virtuális hálózat közötti kommunikációt. A VNet-társítás konfigurálását minden virtuális hálózaton engedélyezni kell, de a virtuális hálózatoknak nem kell ugyanabban a régióban, előfizetésben vagy ugyanahhoz a Azure Active Directory (Azure AD) bérlőhöz társítaniuk.

A VNet-társítás konfigurálásakor kritikus fontosságú, hogy a VNet-társítás kialakításában vagy megvalósításában részt vevő személyek megértsék a négy társított konfigurációs paramétert, és azt, hogy azok hogyan vonatkoznak a társ egyes oldalaira:

1. **Virtuális hálózati hozzáférés engedélyezése:** Válassza az **engedélyezve** (alapértelmezett) lehetőséget a két virtuális hálózat közötti kommunikáció engedélyezéséhez. A virtuális hálózatok közötti kommunikáció engedélyezése lehetővé teszi, hogy a virtuális hálózathoz csatlakoztatott erőforrások ugyanazzal a sávszélességgel és késéssel kommunikáljanak egymással, mintha ugyanahhoz a virtuális hálózathoz csatlakoznak.
2. **Továbbított forgalom engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné egy olyan hálózati forgalom által *továbbított* forgalmat, amely nem a virtuális hálózatról származik – a virtuális hálózatra való átálláshoz egy társon keresztül. Ez a beállítás alapvető fontosságú a sugaras és küllős hálózati topológia megvalósításához.
3. **Átjáró továbbításának engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a társ virtuális hálózat felhasználja az ehhez a virtuális hálózathoz csatolt virtuális hálózati átjárót. *Engedélyezi, hogy az átjáró átvitele* engedélyezve legyen a virtuális hálózaton a virtuális hálózati átjáró erőforrással, de csak akkor érvényes, ha a *távoli átjárók használata* engedélyezve van a másik virtuális hálózaton.
4. **Távoli átjárók használata:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a virtuális hálózatról érkező forgalom olyan virtuális hálózati átjárón keresztül haladjon át, amelyhez a virtuális hálózathoz csatlakozik. A *távoli átjárók használata* virtuális hálózati átjáró nélkül engedélyezve van a virtuális hálózaton, és csak akkor érvényes, ha az *átjáró engedélyezése* lehetőség engedélyezve van a másik virtuális hálózaton.

|Resource|Összekapcsolás|
|---|---|
| Fogalmak: Társviszony létesítése virtuális hálózatok között                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Virtuális hálózati társak létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

A virtuális hálózati átjárók olyan mechanizmust biztosítanak a virtuális hálózatok integrálásához a külső hálózatokkal, mint a helyszíni környezetek, a partneri környezetek és más felhőalapú környezetek. A virtuális hálózati átjáró két típusa a ExpressRoute és a VPN.

#### <a name="expressroute-gateway"></a>ExpressRoute-átjáró

A ExpressRoute-átjárók a virtuális hálózatból egy helyszíni környezetbe helyeznek el egy kimenő pontot, és a biztonsági, rendelkezésre állási, pénzügyi és teljesítménybeli követelmények kielégítése érdekében üzembe kell helyezni őket. A ExpressRoute-átjárók meghatározott hálózati sávszélességet biztosítanak, és használati költségekkel járnak a telepítés után. A virtuális hálózatok csak egy ExpressRoute-átjáróval rendelkezhetnek, de ez több ExpressRoute-áramkörhöz is csatlakoztatható, és több virtuális hálózat is kihasználható a VNet-alapú kapcsolaton keresztül, ami lehetővé teszi a sávszélesség és a kapcsolatok megosztását. Ügyelni kell arra, hogy az útválasztást a helyszíni környezetek és a virtuális hálózatok között ExpressRoute-átjárók használatával konfigurálja, hogy az ismert, ellenőrzött hálózati kimenő pontok használatával biztosítható legyen a végpontok közötti kapcsolat. A ExpressRoute-átjárót használó Nemzetközösségi entitásoknak a ExpressRoute-alapú magánhálózatok használatával is telepíteniük kell a hálózati virtuális berendezéseket (NVA-ket) a VPN-kapcsolat létesítéséhez a helyszíni környezethez, hogy megfeleljenek a ASCS fogyasztói útmutatójának.

Fontos megjegyezni, hogy a ExpressRoute-átjárók korlátozásokkal rendelkeznek a BGP-n keresztül cserélt címtartományok, Közösségek és egyéb speciális konfigurációs elemek esetében.

| Resource  | Összekapcsolás  |
|---|---|
| A ExpressRoute-átjáró áttekintése | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Az ExpressRoute virtuális hálózati átjáróinak konfigurálása | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Az Azure VPN Gateway a virtuális hálózatról kimenő hálózati pontot biztosít egy külső hálózat számára a helyek közötti kapcsolat biztosításához. A VPN-átjárók meghatározott hálózati sávszélességet biztosítanak, és a telepítés után használati költségekkel járnak. Az VPN Gatewayt használó Nemzetközösségi entitásoknak biztosítaniuk kell, hogy az a ASCS-használati útmutatónak megfelelően legyen konfigurálva. A virtuális hálózatok csak egy VPN Gateway rendelkezhetnek, de ez több bújtatással is konfigurálható, és több virtuális hálózat is kihasználható a VNet-társításon keresztül, így több virtuális hálózat is megoszthatja a sávszélességet és a kapcsolatot. A VPN-átjárók az interneten keresztül vagy a ExpressRoute keresztül hozhatók a Microsoft-partneri kapcsolaton keresztül.

| Resource  | Összekapcsolás |
| --- | --- |
| VPN Gateway áttekintése| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| A VPN Gateway tervezése és kialakítása | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure-VPN Gateway az Azure Ausztráliában | [Azure-VPN Gateway az Azure Ausztráliában](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>A virtuális berendezés következő ugrása

A virtuális készülék következő ugrása lehetővé teszi a hálózati forgalom feldolgozását a virtuális hálózatokra alkalmazott Azure hálózati és útválasztási topológián kívül. A virtuális készülékek alkalmazhatják a biztonsági szabályokat, lefordítják a címeket, VPN-eket, proxy forgalmat vagy számos más funkciót. A virtuális berendezés következő ugrása az UDR keresztül lesz alkalmazva, és felhasználható arra, hogy átirányítsa a forgalmat egy Azure Firewallba, egyedi NVA, vagy Azure Load Balancer rendelkezésre állást több NVA. Ha az útválasztáshoz virtuális készüléket szeretne használni, akkor a társított hálózati adaptereket engedélyezni kell az IP-továbbításhoz.

| Resource  | Összekapcsolás |
| --- | ---|
| Útválasztási fogalmak: Egyéni útvonalak | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| IP-továbbítás engedélyezése vagy letiltása | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>A VirtualNetworkServiceEndpoint következő ugrása

A következő ugrási VirtualNetworkServiceEndpoint rendelkező útvonalak csak akkor lesznek hozzáadva, ha egy alhálózaton egy szolgáltatási végpont konfigurálva van, és nem állítható be manuálisan az útválasztási táblákon keresztül.

### <a name="next-hop-of-internet"></a>Az Internet következő ugrása

A következő ugrási Internet a nyilvános IP-címet használó erőforrások elérésére szolgál, amely magában foglalja az internetet, valamint a Pásti és az Azure-régiókban található Azure-szolgáltatásokat is. A következő ugrási Internet nem igényel alapértelmezett útvonalat (0.0.0.0/0) az összes hálózatra vonatkozóan, de lehetővé teszi az útválasztási útvonalak adott nyilvános szolgáltatásokhoz való használatát. Az Internet következő ugrását kell használni ahhoz, hogy útvonalakat lehessen hozzáadni az engedélyezett szolgáltatásokhoz és a rendszerfunkciók, például a Microsoft felügyeleti címei számára szükséges képességekhez.

Az Internet következő ugrásával hozzáadható szolgáltatások például a következők:

1. Kulcskezelő szolgáltatások a Windows aktiválásához
2. App Service Environment-kezelés

|Resource|Összekapcsolás|
|---|---|
| Kimenő kapcsolatok az Azure-ban | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Az egyéni Azure-útvonalak használata a KMS-Aktiválás engedélyezéséhez | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| App Service Environment zárolása  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>A következő ugrás nincs

A következő ugrás nem használható egy adott hálózatra irányuló kommunikáció megakadályozására. Ezzel szemben egy NSG, amely azt szabályozza, hogy a forgalom engedélyezett vagy letiltott egy elérhető hálózati elérési út bejárásakor, a egyik következő ugrása nem távolítja el teljesen a hálózati elérési utat. A következő ugrással rendelkező útvonalak létrehozásakor körültekintően kell eljárni, különösen akkor, ha a 0.0.0.0/0 alapértelmezett útvonalra alkalmazza őket, mivel ez nem szándékolt következményekkel járhat, és a problémák megoldásához bonyolult és időigényes problémákat okozhat.

## <a name="security"></a>Biztonság

A hálózati szegmentálás és a IaaS-és a Pásti-elkülönítési vezérlők megvalósítása a képességek biztonságossá tételével, valamint a felügyelt kommunikációs útvonalak olyan rendszerekkel való megvalósításával valósul meg, amelyek kommunikálni fognak a képesség.

A megoldások az Azure-ban való tervezése és kialakítása olyan logikai architektúra létrehozására szolgál, amely a teljes Azure-beli jelenlétben a hálózati erőforrások megértéséhez, szabályozásához és figyeléséhez szükséges. Ez a logikai architektúra az Azure platformon belül definiált szoftver, amely a hagyományos hálózati környezetekben megvalósított fizikai hálózati topológia helyét veszi igénybe.

A létrehozott logikai architektúrának meg kell adnia a használhatósághoz szükséges funkciókat, azonban meg kell adnia a biztonság és integritás számára szükséges láthatóságot és szabályozást is.

Ennek az eredménynek a megvalósítása a szükséges hálózati szegmentálási és elkülönítési eszközök megvalósításán alapul, de a hálózati topológia védelméhez és érvényesítéséhez, valamint ezeknek az eszközöknek a megvalósításához is.

### <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG)

A NSG egy alhálózat vagy egy adott hálózati adapter számára engedélyezett bejövő és kimenő forgalom megadására szolgálnak. A NSG konfigurálásakor a nemzetközösségi entitásoknak engedélyezési módszert kell használniuk, amelyben a szabályok úgy vannak konfigurálva, hogy engedélyezzék a szükséges forgalmat egy olyan alapértelmezett szabállyal, amely nem felel meg az adott engedélyezési utasításnak. A NSG tervezése és konfigurálása során ügyelni kell arra, hogy az összes szükséges bejövő és kimenő forgalom megfelelően legyen rögzítve. Ez magában foglalja a virtuális hálózatokon és a helyszíni környezetben használt összes magánhálózati IP-címtartomány azonosítását és megismerését, valamint a Microsoft bizonyos szolgáltatásait, például a Azure Load Balancer és a Pásti felügyeleti követelményeit. A NSG megtervezésében és megvalósításában részt vevő személyeknek is ismerniük kell a szolgáltatás-címkék és az alkalmazás-biztonsági csoportok használatát a részletes, szolgáltatás-és alkalmazásspecifikus biztonsági szabályok létrehozásához.

Fontos megjegyezni, hogy egy NSG alapértelmezett konfigurációja engedélyezi a kimenő forgalmat a virtuális hálózaton belüli és az összes nyilvános IP-cím között.

|Resource|Összekapcsolás|
|---|---|
|Hálózati biztonság áttekintése | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Hálózati biztonsági csoport létrehozása, módosítása vagy törlése | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall felhasználható egy sugaras hálózati topológia létrehozására és a központosított hálózati biztonsági vezérlők betartatására. A Azure Firewall a kimenő forgalomhoz szükséges ISM-adatforgalomra vonatkozó követelmények teljesítéséhez használhatók az engedélyezési lista megközelítésének megvalósításával, ahol csak a rendszerfunkciókhoz szükséges IP-címek, protokollok, portok és teljes tartománynevek engedélyezettek. A nemzetközösségi entitásoknak kockázatalapú megközelítéssel kell eldönteniük, hogy a Azure Firewall által biztosított biztonsági képességek elégségesek-e a követelményeiknek. Olyan esetekben, amikor a Azure Firewall által biztosított további biztonsági képességekre van szükség, a nemzetközösségi entitásoknak fontolóra kell venniük a NVA megvalósítását.

|Resource|Összekapcsolás|
|---|---|
|*Azure Firewall dokumentáció* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban Azure PowerShell használatával* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Hálózati virtuális berendezések (NVA)

A NVA használható a sugaras hálózati topológia létrehozásához, a kibővített vagy kiegészítő hálózati képességek biztosításához, vagy az Azure-alapú hálózati mechanizmusok alternatívájaként a helyszíni hálózati szolgáltatásokkal való ismerős és konzisztens támogatáshoz és felügyelethez. A NVA olyan konkrét biztonsági követelmények teljesítésére is alkalmazhatók, mint a; olyan forgatókönyvek, amelyekben a hálózati forgalomhoz, a HTTPS-visszafejtéshez, a tartalmi ellenőrzéshez, a szűréshez vagy más biztonsági funkciókhoz kapcsolódó személyazonossági ismeretekre van szükség. A NVA magas rendelkezésre állású konfigurációban kell üzembe helyezni, és a NVA kialakításában vagy megvalósításában részt vevő személyeknek az Azure-ban való üzembe helyezésre vonatkozó útmutatást a megfelelő gyártói dokumentációban olvashat.

|Resource|Összekapcsolás|
|---|---|
|*Magasan elérhető hálózati virtuális berendezések üzembe helyezése* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Szolgáltatás-végponti házirendek (előzetes verzió)

A szolgáltatás rendelkezésre állása alapján konfigurálja a szolgáltatás-végponti házirendeket, valamint az adatkiszűrése valószínűségének és hatásának biztonsági kockázatfelmérését. A szolgáltatás-végponti házirendeket az Azure Storage-nak kell figyelembe vennie, és a kapcsolódó kockázati profil alapján más szolgáltatások esetében eseti alapon kell kezelni azokat.

| Resource | Összekapcsolás  |
| --- | --- |
| *Szolgáltatás-végponti házirendek áttekintése* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Szolgáltatás-végponti házirend létrehozása, módosítása vagy törlése a Azure Portal használatával* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy az Azure-környezet logikai architektúrájának érvényesítésére és karbantartására szolgáló kulcsfontosságú összetevő. Az Azure-szolgáltatásokon keresztül számos szolgáltatás és kimenő hálózati forgalom elérési útja érhető el. Rendkívül fontos, hogy a nemzetközösségi entitások tisztában legyenek a környezetében meglévő erőforrásokkal és a rendelkezésre álló hálózati kimenő pontokkal. Annak biztosítása érdekében, hogy a nem engedélyezett hálózati kimenő pontok ne legyenek létrehozva az Azure-környezetben, a nemzetközösségi entitásoknak Azure Policy kell használniuk az üzembe helyezhető erőforrások típusának és az erőforrások konfigurálásának szabályozására. A gyakorlati példákban az erőforrások korlátozása csak a használatra engedélyezett és jóváhagyott, és az alhálózatokhoz NSG szükséges.

|Resource | Összekapcsolás|
|---|---|
|*Azure Policy áttekintése* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Engedélyezett erőforrástípusok – minta házirend* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*NSG kényszerítése az alhálózati minta házirendjében*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>Pásti kimenő képességek

A Péter-képességek lehetővé teszik a megnövekedett funkcionalitást és az egyszerűbb felügyeletet, de a hálózati szegmentálásra és elkülönítésre vonatkozó követelmények kezelése terén összetett szempontokat is bevezetnek. A Pásti-képességek általában nyilvános IP-címekkel vannak konfigurálva, és az internetről érhetők el.  Ha a saját rendszerein és megoldásain belül a Pásti-képességeket használja, ügyelni kell arra, hogy azonosítsa az összetevők közötti kommunikációs folyamatokat, és a hálózati biztonsági szabályok létrehozásával csak ezt a kommunikációt engedélyezze. A biztonság védelme érdekében a Pásti-képességeket titkosítással, hitelesítéssel és megfelelő hozzáférés-vezérléssel és engedélyekkel kell konfigurálni.  

### <a name="public-ip-for-paas"></a>Nyilvános IP-cím: Péter

A Péter-képességekhez tartozó nyilvános IP-címek a szolgáltatás által üzemeltetett vagy üzembe helyezett régió alapján vannak lefoglalva. A nyilvános IP-címek kiosztásának és a régióknak meg kell ismernie a szükséges hálózati biztonsági szabályokat és útválasztási topológiát a hálózati szegmentálás és az Azure Virtual Networks, a Pásti és a ExpressRoute, valamint a elkülönítés tekintetében. Internetkapcsolat. Az Azure IP-címeket foglal le az egyes Azure-régiók számára lefoglalt készletből. A Microsoft az egyes régiókban használt címeket letölti, amelyek rendszeres és ellenőrzött módon frissülnek. Az egyes régiókban elérhető szolgáltatások is gyakran változnak, mivel új szolgáltatások jelennek meg, vagy a szolgáltatások telepítése szélesebb körben történik. A nemzetközösségi entitásoknak rendszeresen át kell tekinteniük ezeket az anyagokat, és az Automation használatával szükség szerint karbantarthatók a rendszerek. Az egyes régiókban üzemeltetett szolgáltatások adott IP-címei a Microsoft ügyfélszolgálatával szerezhetők be.

| Resource | Összekapcsolás |
| --- | --- |
| *A Microsoft Azure adatközpont IP-tartományai*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Azure-szolgáltatások régiónként*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Ausztrália – Közép-Ausztrália – Közép-2, Ausztrália – Kelet, Ausztrália – délkeleti & termékek = mind](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Bejövő és kimenő IP-címek Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>További lépések

Összehasonlíthatja az általános architektúráját és kialakítását a közzétett [védett tervekkel a IaaS és a Pásti](https://aka.ms/au-protected)webalkalmazásokhoz.
