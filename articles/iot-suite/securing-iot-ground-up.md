---
title: "Biztonságossá tétele a az eszközök internetes hálózatát az alapoktól mentése |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft Azure IoT Suite beépített biztonsági szolgáltatásai"
services: 
suite: iot-suite
documentationcenter: 
author: YuriDio
manager: timlt
editor: 
ms.assetid: 10252dfa-8313-4a97-9bd6-a3f1345dd3be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 5979e5e4baa385cc0aaff5f1277f2a0f7492c426
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Az eszközök internetes hálózatát biztonsági másolatot az alapoktól
A az eszközök internetes hálózatát (IoT) működő vállalkozásoknak világszerte egyedi biztonsági, adatvédelmi és megfelelőségi kihívást jelent. Hagyományos számítógépes technológia, ahol a problémák szoftver, és hogyan megvalósított köré szerveződik, eltérően IoT vonatkozik, mi történik, ha a számítógépes és a fizikai világot átszervezését. Az IoT-megoldások védelmét biztosítja az eszközöket, ezek az eszközök és a felhőben és a feldolgozás és a tárolás során a felhőben biztonságos adatvédelem közötti biztonságos kapcsolat biztonságos kiépítése igényel. Eszközök fejlesztésének ilyen funkció, azonban a következők erőforrás-korlátozott eszközök, központi telepítések földrajzi eloszlása, és számos olyan megoldást belüli eszközök.

Ez a cikk ismerteti, hogyan a Microsoft Azure IoT Suite biztonságban az eszközök internetes hálózatát felhőmegoldást biztosít. Az Azure IoT Suite egy teljes végpont megoldás az alapoktól minden szakaszban beépített biztonsági nyújt. A Microsoft fejlesztett biztonságos szoftver része a szoftver mérnöki eljárás szerint az évtizedekben a feltört hosszú élménye biztonságos szoftver fejlesztése. Ennek biztosítására, biztonságos fejlesztési Életciklussal (SDL) eligazodást fejlesztési módszert, az infrastruktúra-szintű biztonsági szolgáltatásokat, például működési biztonsági megbízhatósági (OSA) és a Microsoft digitális milyen egység, Microsoft rengeteg alapján kialakulhat Security Response Center, és a Microsoft Malware Protection Center. 

Az Azure IoT Suite egyedi funkciókat, amelyek IoT-eszközökről származó létesítési való csatlakozás és tárolni adatok egyszerű és átlátható nyújtja, és minden, a legtöbb biztonságáról. Ebben a cikkben azt vizsgálja meg az Azure IoT Suite funkciókat, és a központi telepítési stratégiák biztonsági, adatvédelmi és megfelelőségi kihívást biztosításához tárgyalja. 

## <a name="introduction"></a>Bevezetés
A az eszközök internetes hálózatát (IoT) a jövőben, azonnali vállalatok számára az ajánlat és valós lehetőségek csökkentheti a költségeket, bevételek növeléséhez, és az üzleti wave. Számos vállalat azonban nem határozatlanok miatt biztonsági, adatvédelmi és megfelelőségi kapcsolatos szervezetük IoT telepítéséhez. A fő pont érintő az IoT-infrastruktúra, amely egyesíti a számítógépes és fizikai világot együtt, ezek két világot egyedi kockázatok összetételéhez egyediségi származik. Az IoT biztonsági vonatkozik az eszközökön futó kód sértetlenségének biztosítása és felhasználói hitelesítés, meghatározása eszközöket (valamint az eszközök által létrehozott adatok) egyértelmű tulajdonjogát, de a rugalmas számítógépes és fizikai támadások. 

Ezt követően a probléma van az adatvédelem. A vállalatok szeretné átláthatóság vonatkozó adatok gyűjtése a alatt gyűjtött adatok, és miért, aki láthatja azt, aki szabályozza a hozzáférést, és így tovább. Végezetül vannak a készülék, valamint az azokat üzemeltető személyek általános biztonsági problémákat, és az ipari szabványok megfelelőségi fenntartásának.

A biztonsági, adatvédelmi, átláthatóság és megfelelőségi szempontok miatt a megfelelő IoT-megoldás szolgáltató választása marad kihívást. Szeretne összefűzni IoT szoftver- és más szállítóktól által nyújtott szolgáltatások egyes adatra vezet be a biztonsági, adatvédelmi, átláthatóság és megfelelőségi, akkor annak észleléséhez, hát alone javítsa ki a hézagok meghatározása érdekében. A jobb oldali IoT szoftver és szolgáltatás szolgáltató alapján rendelkező szolgáltatások, amelyek referenciaegyenesen és földrajzi eloszthatja, de is lehetővé válik a biztonságos és transzparens módon szerzett, kiterjedt tapasztalatunkat szolgáltatók keresése a választott. Hasonlóképpen segít a kiválasztott szolgáltató évtizedekben gépek világszerte több milliárd futó biztonságos szoftver fejlesztése a felhasználói felület, és képes a fenyegetésekről alkotott képet az eszközök internetes hálózatát a lehetőségek új világának által jelentett értékeljük rendelkezésére.

## <a name="secure-infrastructure-from-the-ground-up"></a>Biztonságos infrastruktúra alapoktól
A [Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) infrastruktúra támogatja a több mint egymilliárd ügyfelek 127 országokban. A vállalati szoftverek kialakításához és futtatásához a legnagyobb online szolgáltatások közül a világ évtizedekben-hosszú tapasztalatunk rajzolási, nyújtunk fokozott biztonsági, adatvédelmi, megfelelőségi és fenyegetés nagyobb mértékű megoldás eljárásokat, mint a legtöbb ügyfél sikerült a saját elérése.

A [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/) biztonsági követelményeket a teljes szoftver életciklus beágyazó kötelező vállalati szintű fejlesztési folyamat nyújt. Annak biztosítására, operatív tevékenységek kövesse az azonos szintű biztonsági eljárásokat, szigorú biztonsági irányelvek jelenjen meg az operatív biztonsági megbízhatósági (OSA) folyamat használjuk. Azt is együttműködnek külső naplózási vállalkozások folyamatos ellenőrzés, hogy a Microsoft megfelel-e a kötelezettségek és széles körű biztonsági erőfeszítéseket képességekkel, beleértve a Microsoft digitális milyen egység, Microsoft Security középpontja kell létrehozni azt végezhetnek Válasz Center és a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>A Microsoft Azure - biztonságos IoT-infrastruktúra a vállalkozása számára
A Microsoft Azure biztosít egy teljes felhőalapú megoldáson, egyet, amely egyesíti az integrált felhőszolgáltatások folyamatosan növekvő gyűjteménye – analytics, a gépi tanulás, a tárolás, a biztonsági, a hálózat és a webes – a védelmi iparágvezető kötelezettséget és adatvédelmi adatait. A [szabálysértésnek minősülő feltételezik](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) stratégia használ egy dedikált "red" szoftver biztonsági szakértői csoport, akiknek támadások szimulálása tesztelés lehetővé teszi az Azure észleli, megjelenő fenyegetéseket, és elleni megszegése helyreállíthatók. A [globális incidensválasz](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) team works éjjel támadások és a rosszindulatú tevékenységhez által okozott hatások mérsékléséhez. A team elfogadott incidenskezelés kommunikációra és helyreállítási eljárásokat a következő, és a belső és külső partnerekkel felderíthető és kiszámítható felületeket használja.

Rendszer adja meg a folyamatos behatolásérzékelési és megelőzési, szolgáltatás támadás megelőzése, rendszeres behatolás tesztelése és törvényszéki eszközök, amelyek segítenek azonosítani és elhárítani a fenyegetéseket. [A multi-factor authentication](../multi-factor-authentication/multi-factor-authentication.md) végfelhasználók hozzáférhessenek a hálózati biztonsági további réteget biztosít. És az alkalmazás és az állomás-szolgáltató, a hozzáférés-vezérlés, figyelés, kártevőirtó, biztonsági rések keresése, javítások és konfigurációkezelés kínálunk.

A Microsoft Azure IoT Suite kihasználja a biztonsági és adatvédelmi beépítve az SDL és OSA folyamatok biztonságos fejlesztési és az összes Microsoft szoftver művelet együtt az Azure platformon. Ezekkel az eljárásokkal infrastruktúra védelme, hálózatvédelmi és alapvető biztonsági megoldások identitás- és felügyeleti funkciókat biztosítanak. 

A [Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md) belül a [IoT Suite](iot-suite-what-is-azure-iot.md) teljes körűen felügyelt szolgáltatást, amely lehetővé teszi az IoT-eszközök és az Azure-szolgáltatások közötti megbízható és biztonságos kétirányú kommunikáció például [ Az Azure Machine Learning](../machine-learning/studio/what-is-machine-learning.md) és [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) eszközönkénti hitelesítő adatokat és a hozzáférés-vezérlés használatával.

A biztonság és adatvédelem az Azure IoT Suite épített legjobb kommunikációhoz, azt már bontásban a csomag a három elsődleges biztonsági területre. 

![Azure IoT Suite](media/securing-iot-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Kiépítés biztonságos eszköz és a hitelesítés
Az Azure IoT Suite titkosítja az eszközök, amíg azok ki a mezőben egy egyedi identitása kulcs az egyes eszközök, bár ez a művelet az eszköz kommunikáljon az IoT-infrastruktúra által felhasználható megadásával. A folyamat gyorsan és egyszerűen állíthat be. A felhasználó által kiválasztott eszközazonosítójú létrehozott kulcs az eszköz és az Azure IoT Hub közötti összes kommunikáció során használt jogkivonat alapját.

Eszközazonosítókat társítható egy eszközt (például Flash memóriájában rögzített hardver megbízhatósági modulban) gyártási, vagy használhat egy meglévő rögzített identity (például CPU sorozatszámokat) proxy-ként. Mivel a módosítása a azonosító adatokat az eszköz nem egyszerű, fontos bevezetni logikai eszköz azonosítóját, abban az esetben, ha az alapul szolgáló eszköz hardvermódosításokat, de a logikai eszköz változatlan marad. Bizonyos esetekben egy eszközidentitás társítását (azaz egy hitelesített mező mérnököt fizikailag konfigurálja egy új eszközt a megoldás háttéralkalmazással való kommunikáció során) eszközt a központi telepítéskor fordulhat elő. A [Azure IoT Hub identitásjegyzékhez](../iot-hub/iot-hub-devguide.md) eszköz identitások és a biztonsági kulcsok megoldás biztonságos tárolására szolgál. Személy vagy eszköz identitások csoportok felveheti egy engedélyezési lista vagy egy tiltólista engedélyezése a teljes felügyeletet gyakorolhat az eszközök elérést.

Az Azure IoT Hub hozzáférés-vezérlési házirendeket a felhőben engedélyezése aktiválás és az eszközidentitás, biztosítva az eszköz szükség esetén az IoT-telepítéséből társítását letiltása. A társítás, és az eszközök disassociation minden eszközidentitás alapul.

További eszközök biztonsági funkciói a következők:

* Eszközök nem fogadja el a nem kért hálózati kapcsolatokat. Csak kimenő módon azok kapcsolódó összes kapcsolatok és útvonalakat. Egy eszköz úgy, hogy egy parancs fogadjon a háttér az eszköz feldolgozni a függőben lévő parancsok kereséséhez kapcsolatot kell kezdeményeznie. Ha létrejön a kapcsolat az eszköz és az IoT-központ között biztonságosan, üzenetküldési a felhőből az eszközre, és az eszköz a felhőre transzparens módon is elküldhetők.  
* Eszközök csak csatlakoztassa, vagy a létrehozásához használt azok vannak társviszonyban, például az Azure IoT-központ jól ismert szolgáltatások útvonalakat.
* A hitelesítési és engedélyezési rendszerszintű használja eszközönkénti identitások hozzáférési hitelesítő adatok és az engedélyek közelében-azonnal visszavonható.

### <a name="secure-connectivity"></a>Biztonságos kapcsolat
Az üzenetküldési tartóssági az IoT-megoldás egyik fontos szolgáltatása. Adatvagyonának parancsok biztosításához és/vagy eszközök fogadhat adatokat kell, hogy az IoT-eszközök csatlakoznak-e az interneten, vagy más hasonló hálózatok, ami megbízhatatlan lehet a tény aláhúzott. Azure IoT-központ az üzenetküldési és üzenetek válaszul nyugták rendszert eszközök közötti tartósságot biztosít. További tartóssági üzenetküldéshez üzenetet az IoT Hub gyorsítótárazásával a telemetriai adatok és a két napot ki parancsokat hét napig érhető el.

Hatékonyságát fontos erőforrásokat és a műveletet olyan erőforrás-korlátozott környezetben megőrzéséhez. Azure IoT Hub, hatékony kommunikáció engedélyezése HTTPS (HTTP Secure), a népszerű http protokoll a szabványos biztonságos verzióját támogatja. Speciális Message Queuing protokoll (AMQP) és a Message Queuing Telemetriai Transport (MQTT), Azure IoT-központ által támogatott úgy vannak kialakítva, nem csak a hatékonyság erőforrás használja, de megbízható üzenetkézbesítést tekintetében. 

Méretezhetőség megbizonyosodhat eszközök széles biztonságosan együttműködni. Az Azure IoT-központ lehetővé teszi, hogy az IP-t használó mind a nem IP-engedélyezett eszközök biztonságos kapcsolatot. IP-t használó eszközök el tudják közvetlenül csatlakozzanak, és az IoT Hub kommunikálni egy biztonságos kapcsolaton keresztül. A nem IP-kompatibilis eszközök erőforrás-korlátozott, és csak rövid távolság kommunikációs protokollokat, például Zwave ZigBee vagy Bluetooth-on keresztül. A mező átjáró ezeknek az eszközöknek összesítésére szolgál, és a protokollfordításhoz a felhővel biztonságos kétirányú kommunikációhoz végez.

Kapcsolat további biztonsági funkciói a következők:

* Eszközök és az Azure IoT-központ között, vagy átjárók és az Azure IoT Hub, kommunikációs elérési használatával lett biztonságossá téve szabványos Transport Layer Security (TLS) az Azure IoT Hub hitelesített X.509 protokoll használatával.
* Eszközök védelme a kéretlen bejövő kapcsolatokat, Azure IoT Hub nem nyitható meg a kapcsolat az eszközre. Az eszköz összes kapcsolatot kezdeményez. 
* Azure IoT Hub tartósan tárolja az üzeneteket az eszközök és az eszközt, amellyel vár. Ezek a parancsok engedélyezése miatt power vagy kapcsolódási problémákat, ezek a parancsok fogadására időnként, csatlakozó eszközök két napig tárolódnak. Az Azure IoT Hub fenntartja az egyes eszközök eszközönkénti várólista.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Biztonságos feldolgozása és tárolása a felhőben
A titkosított kommunikáció feldolgozási adatok a felhőben az Azure IoT Suite segít a biztonságos. További titkosítási és a biztonsági kulcsok kezelése végrehajtásához rugalmasságot biztosít. Azure Active Directory (AAD) használ a felhasználói hitelesítés és engedélyezés, Azure IoT Suite biztosíthat egy csoportházirend-alapú engedélyezési modellt az adatok a felhőben naplózva, és tekintse át, mennyire egyszerű a hozzáférés felügyeletének engedélyezésére. Ez a modell is lehetővé teszi, hogy a felhőben tárolt adatainak eléréséhez, valamint az Azure IoT Suite csatlakoztatott eszközök szinte azonnali visszavonás.

Ha adatok a felhőben, feldolgozni, és bármely felhasználó által definiált munkafolyamat tárolja. Minden az adatok egy részét a hozzáférést az Azure Active Directoryval, attól függően, hogy a társzolgáltatás használt vezérlik.

Az IoT-infrastruktúra által használt összes kulcsok segítségével abban az esetben kell kulcsokat újra kiosztott váltása a biztonsági tároló, a felhőben vannak tárolva. Adatok tárolhatók [Azure Cosmos DB](../cosmos-db/introduction.md) vagy [SQL-adatbázisok](../sql-database/sql-database-faq.md), engedélyezi a szükséges biztonsági szintjét. Emellett Azure biztosítja az figyelésére és az adatokat, hogy riasztást küldjön, bármely behatolás az elérésére, vagy a jogosulatlan hozzáférés naplózása.

## <a name="conclusion"></a>Összegzés
Az eszközök internetes hálózatát kezdődik-e a dolgok – a legfontosabb dolgokról szolgáltatnak a vállalkozások számára. Az IoT biztosíthat elképesztő érték üzleti költségek csökkentése, árbevétel növelését, és üzleti átalakítása. Sikerült az átalakítás nagymértékben függ a megfelelő IoT szoftver és szolgáltatás szolgáltató kiválasztásához. Ez azt jelenti, hogy olyan szolgáltatót, amely nem csak az átalakítás által az üzleti igények ismertetése és követelmények catalyzes, de emellett a szolgáltatások és szoftverek biztonsági, adatvédelmi, átláthatóság és megfelelőségi fő tervezési megfontolások a következővel keresése. A Microsoft szerzett, kiterjedt tapasztalatunkat fejlesztésük és biztonságos szoftverek és szolgáltatások telepítése és egy vezető továbbra is az új élettartamát az eszközök internetes hálózatát. 

A Microsoft Azure IoT Suite hoz létre a biztonsági intézkedéseket úgy lett kialakítva, javíthatják a hatékonyságukat, meghajtó működési teljesítmény innováció engedélyezése, és a speciális elemzés vállalatok átalakítására alkalmaz az eszközök biztonságos figyelés engedélyezése. A réteges megközelítésének biztonsági, több biztonsági funkcióiról és kialakítási minta felé, az Azure IoT Suite segítségével olyan infrastruktúra, amely lehet megbízható átalakítását, minden üzleti telepítése. 

## <a name="additional-information"></a>További információ
Minden Azure IoT Suite előkonfigurált megoldás hoz létre az Azure-szolgáltatások, például a következő osztályt:

* [**Az Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): az átjáró, amely összeköti a felhő "dolgokhoz". Eszköz hitelesítést támogató segít a megoldás biztonságos hub és a folyamat nagy mennyiségű adatot kapcsolatok több millió méretezheti.
* [**Az Azure Cosmos DB**](https://azure.microsoft.com/services/documentdb/): egy méretezhető, teljes mértékben indexelt dokumentumadatbázis-szolgáltatás, amely felügyeli az eszközök metaadatok félig strukturált adatok ellátásához, például az attribútumokat, a konfiguráció és a biztonsági tulajdonságait. Cosmos DB nagy teljesítményű és nagy átviteli feldolgozás, a séma-független indexelő adatokat, és egy részletes SQL-lekérdezési felületet kínál.
* [**Az Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): valós idejű streamfeldolgozó a felhőben, amely lehetővé teszi, hogy a gyors fejlesztésére és alacsony költségű analytics megoldást nyújt a valós idejű betekintést az eszközök, érzékelőket, infrastruktúra és alkalmazások telepítése . A teljes körűen felügyelt szolgáltatás adatainak megőrzése mellett nagy átviteli sebességet, alacsony késéssel és rugalmasság méretezhető, azonosíthatja a kötettel.
* [**Az Azure App Services**](https://azure.microsoft.com/services/app-service/): egy felhőalapú platform hozhat létre hatékony webes és mobilalkalmazások bárhol adatokat; a felhőben, vagy a helyszínen. Vonzó alkalmazások készítése iOS, Android és Windows rendszerre. A szoftver egy szolgáltatott szoftverként (SaaS) és a felhő alapú szolgáltatások több tucatnyi out-of-az-box kapcsolattal rendelkező vállalati alkalmazások és a vállalati alkalmazások integrálása. A kedvenc nyelvi és IDE az – .NET, Node.js, PHP, Python vagy Java-webalkalmazások és API-k minden eddiginél gyorsabban létrehozásához.
* [**A Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): A Logic Apps szolgáltatás az Azure App Service segítségével integrálható a meglévő üzleti rendszereken az IoT-megoldásból és munkafolyamat-folyamatok automatizálása. A Logic Apps segítségével a fejlesztők olyan munkafolyamatokat alakíthatnak ki, amely egy és majd végrehajtanak bizonyos lépéseket – szabályok és műveleteit, amelyekkel az üzleti folyamatok integrálása hatékony összekötők. A Logic Apps kínál a túlnyomó ökoszisztémájának alkalmazásával az SaaS, a felhő alapú out-of-az-box kapcsolat és a helyszíni alkalmazások.
* [**Az Azure blob storage**](https://azure.microsoft.com/services/storage/): az eszközök elküldik a felhőbe adatok megbízható, gazdaságos felhőalapú tárhelyre.

## <a name="next-steps"></a>Következő lépések
Az IoT-megoldásból biztosításával kapcsolatos további tudnivalókért lásd:

* [Az IoT ajánlott biztonsági eljárások][lnk-security-best-practices]
* [Az IoT-biztonsági architektúrája][lnk-security-architecture]
* [Az IoT üzembe][lnk-security-deployment]

[lnk-security-best-practices]: iot-security-best-practices.md
[lnk-security-architecture]: iot-security-architecture.md
[lnk-security-deployment]: iot-suite-security-deployment.md

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive-overview]
* [Gyakran ismételt kérdések az IoT Suite-ról][lnk-faq]

Az IoT-központ biztonsági olvashat [IoT-központ való hozzáférés szabályozása] [ lnk-devguide-security] az IoT Hub fejlesztői útmutató.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
