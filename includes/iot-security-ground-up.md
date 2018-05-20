---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c1ba6cb5b01c86c64fd6134c592829050bbb226d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Az eszközök internetes hálózatát biztonsági másolatot az alapoktól

A az eszközök internetes hálózatát (IoT) működő vállalkozásoknak világszerte egyedi biztonsági, adatvédelmi és megfelelőségi kihívást jelent. Hagyományos számítógépes technológia, ahol a problémák szoftver, és hogyan megvalósított köré szerveződik, eltérően IoT vonatkozik, mi történik, ha a számítógépes és a fizikai világot átszervezését. Az IoT-megoldások védelmét biztosítja az eszközöket, ezek az eszközök és a felhőben és a feldolgozás és a tárolás során a felhőben biztonságos adatvédelem közötti biztonságos kapcsolat biztonságos kiépítése igényel. Eszközök fejlesztésének ilyen funkció, azonban a következők erőforrás-korlátozott eszközök, központi telepítések földrajzi eloszlása, és számos olyan megoldást belüli eszközök.

Ez a cikk ismerteti, hogyan az IoT-megoldás gyorsítók biztonságban az eszközök internetes hálózatát felhőalapú megoldást nyújt. A megoldás gyorsítók egy teljes végpont megoldás az alapoktól minden szakaszban beépített biztonsági biztosításához. A Microsoft fejlesztett biztonságos szoftver része a szoftver mérnöki gyakorlat, feltört eszköz, a Microsoft évtizedekben hosszú élménye biztonságos szoftver fejlesztése. Ennek biztosítására, biztonságos fejlesztési Életciklussal (SDL) eligazodást fejlesztési módszert, az infrastruktúra-szintű biztonsági szolgáltatásokat, például működési biztonsági megbízhatósági (OSA) és a Microsoft digitális milyen egység, Microsoft rengeteg alapján kialakulhat Security Response Center, és a Microsoft Malware Protection Center.

A megoldás gyorsítók egyedi lehetőségeket kínálunk, hogy ellenőrizze a kiépítési, csatlakozik, és az egyszerű és átlátható az IoT-eszközökről származó adatok tárolja, és minden, a legtöbb biztonságáról. Ez a cikk az Azure IoT-megoldás gyorsítók funkciókat megvizsgálja, és a központi telepítési stratégiák biztonsági, adatvédelmi és megfelelőségi kihívást biztosításához tárgyalja.

## <a name="introduction"></a>Bevezetés

A az eszközök internetes hálózatát (IoT) a jövőben, azonnali vállalatok számára az ajánlat és valós lehetőségek csökkentheti a költségeket, bevételek növeléséhez, és az üzleti wave. Számos vállalat azonban nem határozatlanok miatt biztonsági, adatvédelmi és megfelelőségi kapcsolatos szervezetük IoT telepítéséhez. A fő pont érintő az IoT-infrastruktúra, amely egyesíti a számítógépes és fizikai világot együtt, ezek két világot egyedi kockázatok összetételéhez egyediségi származik. Az IoT biztonsági vonatkozik az eszközökön futó kód sértetlenségének biztosítása és felhasználói hitelesítés, meghatározása eszközöket (valamint az eszközök által létrehozott adatok) egyértelmű tulajdonjogát, de a rugalmas számítógépes és fizikai támadások.

Ezt követően a probléma van az adatvédelem. A vállalatok szeretné átláthatóság vonatkozó adatok gyűjtése a alatt gyűjtött adatok, és miért, aki láthatja azt, aki szabályozza a hozzáférést, és így tovább. Végezetül vannak a készülék, valamint az azokat üzemeltető személyek általános biztonsági problémákat, és az ipari szabványok megfelelőségi fenntartásának.

A biztonsági, adatvédelmi, átláthatóság és megfelelőségi szempontok miatt a megfelelő IoT-megoldás szolgáltató választása marad kihívást. Szeretne összefűzni IoT szoftver- és más szállítóktól által nyújtott szolgáltatások egyes adatra vezet be a biztonsági, adatvédelmi, átláthatóság és megfelelőségi, akkor annak észleléséhez, hát alone javítsa ki a hézagok meghatározása érdekében. A jobb oldali IoT szoftver és szolgáltatás szolgáltató alapul, amelyeken fut a szolgáltatásokat, amelyek referenciaegyenesen és földrajzi eloszthatja, de is lehetővé válik a biztonságos és transzparens módon szerzett, kiterjedt tapasztalatunkat szolgáltatók keresése a választott. Hasonlóképpen segít a kiválasztott szolgáltató évtizedekben gépek világszerte több milliárd futó biztonságos szoftver fejlesztése a felhasználói felület, és képes a fenyegetésekről alkotott képet az eszközök internetes hálózatát a lehetőségek új világának által jelentett értékeljük rendelkezésére.

## <a name="secure-infrastructure-from-the-ground-up"></a>Biztonságos infrastruktúra alapoktól

A [Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) infrastruktúra támogatja a több mint egymilliárd ügyfelek 127 országokban. A Microsoft évtizedekben-hosszú kapcsolatos vállalati szoftverek kialakításához és futtatásához a legnagyobb online szolgáltatások közül a világ rajzolási, a Microsoft Cloud tanácsokat fokozott biztonsági, adatvédelmi, megfelelőségi és fenyegetés nagyobb mértékű megoldás mint a legtöbb ügyfél saját maguk is el lehet érni.

A [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/) biztonsági követelményeket a teljes szoftver életciklus beágyazó kötelező vállalati szintű fejlesztési folyamat nyújt. Annak biztosítására, operatív tevékenységek kövesse az azonos szintű biztonsági eljárásokat, SDL szigorú biztonsági irányelvek jelenjen meg a Microsoft üzemeltetési biztonsági megbízhatósági (OSA) folyamat használja. Microsoft naplózási külső cégek folyamatos ellenőrzés, hogy a megfelelőségi teljesíti, és a Microsoft az átfogó biztonsági erőfeszítéseket képességekkel, beleértve a Microsoft digitális milyen egység, az erőforrások létrehozásával kapcsolatba lép a is működik Microsoft Security Response Center, és a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>A Microsoft Azure - biztonságos IoT-infrastruktúra a vállalkozása számára

A Microsoft Azure biztosít egy teljes felhőalapú megoldáson, egyet, amely egyesíti az integrált felhőszolgáltatások folyamatosan növekvő gyűjteménye – analytics, a gépi tanulás, a tárolás, a biztonsági, a hálózat és a webes – a védelmi iparágvezető kötelezettséget és adatvédelmi adatait. A Microsoft [szabálysértésnek minősülő feltételezik](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) stratégia használ egy dedikált *piros team* szimulálása támadások, a tesztelés az Azure képességét, hogy észlelni biztonsági szakértők szoftver, a megjelenő fenyegetéseket, és elleni helyreállítása a behatolás. A Microsoft [globális incidensválasz](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) team works éjjel támadások és a rosszindulatú tevékenységhez által okozott hatások mérsékléséhez. A team elfogadott incidenskezelés kommunikációra és helyreállítási eljárásokat a következő, és a belső és külső partnerekkel felderíthető és kiszámítható felületeket használja.

A Microsoft systems adja meg, folyamatos behatolásérzékelési és megelőzési, szolgáltatás támadás megelőzése, rendszeres behatolás tesztelése és törvényszéki eszközök, amelyek segítenek azonosítani és elhárítani a fenyegetéseket. [A multi-factor authentication](../articles/active-directory/authentication/multi-factor-authentication.md) végfelhasználók hozzáférhessenek a hálózati biztonsági további réteget biztosít. Valamint az alkalmazás és a gazdagép-szolgáltató, a Microsoft hozzáférés-vezérlés, figyelés, kártevőirtó, biztonsági rések keresése, javítások és konfigurációkezelés.

A megoldás gyorsítók biztonsági és adatvédelmi biztonságos fejlesztési és az összes Microsoft szoftver művelet SDL és OSA folyamatok együtt az Azure platformon épített előnyeit. Ezekkel az eljárásokkal infrastruktúra védelme, hálózatvédelmi és alapvető biztonsági megoldások identitás- és felügyeleti funkciókat biztosítanak.

A [Azure IoT Hub](../articles/iot-hub/iot-hub-what-is-iot-hub.md) belül a [IoT-megoldás gyorsítók](../articles/iot-accelerators/iot-accelerators-what-is-azure-iot.md) teljes körűen felügyelt szolgáltatást, amely lehetővé teszi, mint az IoT-eszközök és az Azure-szolgáltatások közötti megbízható és biztonságos kétirányú kommunikáció [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) és [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) eszközönkénti hitelesítő adatokat és a hozzáférés-vezérlés használatával.

A biztonság és adatvédelem az Azure IoT-megoldás gyorsítók épített legjobb kommunikációhoz, ez a cikk a három elsődleges biztonsági területre suite megszakad.

![Azure IoT-megoldásgyorsítók](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Kiépítés biztonságos eszköz és a hitelesítés

A megoldás gyorsítók biztonságos eszközök amikor ki a mezőben egy egyedi identitása kulcs az egyes eszközök, bár ez a művelet az eszköz kommunikáljon az IoT-infrastruktúra által felhasználható megadásával. A folyamat gyorsan és egyszerűen állíthat be. A felhasználó által kiválasztott eszközazonosítójú létrehozott kulcs az eszköz és az Azure IoT Hub közötti összes kommunikáció során használt jogkivonat alapját.

Eszköz azonosítóját (amely Flash van, memóriájában rögzített hardver megbízhatósági modulban) gyártási lehet eszközhöz társított, vagy használhat egy meglévő rögzített identity (például CPU sorozatszámokat) proxyként. Mivel a módosítása a azonosító adatokat az eszköz nem egyszerű, fontos bevezetni logikai eszköz azonosítóját, abban az esetben, ha az alapul szolgáló eszköz hardvermódosításokat, de a logikai eszköz változatlan marad. Bizonyos esetekben egy eszközidentitás társítását fordulhat elő, eszköz központi telepítéskor (például egy hitelesített mező mérnököt fizikailag konfigurálja egy új eszközt a megoldás háttéralkalmazással való kommunikáció során.). A [Azure IoT Hub identitásjegyzékhez](../articles/iot-hub/iot-hub-devguide.md) eszköz identitások és a biztonsági kulcsok megoldás biztonságos tárolására szolgál. Személy vagy eszköz identitások csoportok felveheti egy engedélyezési lista vagy egy tiltólista engedélyezése a teljes felügyeletet gyakorolhat az eszközök elérést.

Az Azure IoT Hub hozzáférés-vezérlési házirendeket a felhőben engedélyezése aktiválás és az eszközidentitás, biztosítva az eszköz szükség esetén az IoT-telepítéséből társítását letiltása. A társítás, és az eszközök disassociation minden eszközidentitás alapul.

További eszközök biztonsági funkciói a következők:

* Eszközök nem fogadja el a nem kért hálózati kapcsolatokat. Csak kimenő módon azok kapcsolódó összes kapcsolatok és útvonalakat. Egy eszköz úgy, hogy egy parancs fogadjon a háttér az eszköz feldolgozni a függőben lévő parancsok kereséséhez kapcsolatot kell kezdeményeznie. Ha létrejön a kapcsolat az eszköz és az IoT-központ között biztonságosan, üzenetküldési a felhőből az eszközre, és az eszköz a felhőre transzparens módon is elküldhetők.
* Eszközök csak csatlakoztassa, vagy a létrehozásához használt azok vannak társviszonyban, például az Azure IoT-központ jól ismert szolgáltatások útvonalakat.
* A hitelesítési és engedélyezési rendszerszintű használja eszközönkénti identitások hozzáférési hitelesítő adatok és az engedélyek közelében-azonnal visszavonható.

### <a name="secure-connectivity"></a>Biztonságos kapcsolat

Az üzenetküldési tartóssági az IoT-megoldás egyik fontos szolgáltatása. Adatvagyonának parancsok biztosításához és/vagy eszközök fogadhat adatokat kell, hogy az IoT-eszközök csatlakoznak-e az interneten, vagy más hasonló hálózatok, amely nem megbízható a tény aláhúzott. Azure IoT-központ az üzenetküldési és üzenetek válaszul nyugták rendszert eszközök közötti tartósságot biztosít. További tartóssági üzenetküldéshez üzenetet az IoT Hub gyorsítótárazásával a telemetriai adatok és a két napot ki parancsokat hét napig érhető el.

Hatékonyságát fontos erőforrásokat és a műveletet olyan erőforrás-korlátozott környezetben megőrzéséhez. Azure IoT Hub, hatékony kommunikáció engedélyezése HTTPS (HTTP Secure), a népszerű http protokoll a szabványos biztonságos verzióját támogatja. Speciális Message Queuing protokoll (AMQP) és a Message Queuing Telemetriai Transport (MQTT), Azure IoT-központ által támogatott úgy vannak kialakítva, nem csak a hatékonyság erőforrás használja, de megbízható üzenetkézbesítést tekintetében. 

Méretezhetőség megbizonyosodhat eszközök széles biztonságosan együttműködni. Az Azure IoT-központ lehetővé teszi, hogy az IP-t használó mind a nem IP-engedélyezett eszközök biztonságos kapcsolatot. IP-t használó eszközök el tudják közvetlenül csatlakozzanak, és az IoT Hub kommunikálni egy biztonságos kapcsolaton keresztül. A nem IP-kompatibilis eszközök erőforrás-korlátozott, és csak rövid távolság kommunikációs protokollokat, például Zwave ZigBee vagy Bluetooth-on keresztül. A mező átjáró ezeknek az eszközöknek összesítésére szolgál, és a protokollfordításhoz a felhővel biztonságos kétirányú kommunikációhoz végez.

Kapcsolat további biztonsági szolgáltatások a következők:

* Eszközök és az Azure IoT-központ között, vagy átjárók és az Azure IoT Hub, kommunikációs elérési használatával lett biztonságossá téve szabványos Transport Layer Security (TLS) az Azure IoT Hub hitelesített X.509 protokoll használatával.
* Eszközök védelme a kéretlen bejövő kapcsolatokat, Azure IoT Hub nem nyitható meg a kapcsolat az eszközre. Az eszköz összes kapcsolatot kezdeményez.
* Azure IoT Hub tartósan tárolja az üzeneteket az eszközök és az eszközt, amellyel vár. Ezek a parancsok engedélyezése miatt power vagy kapcsolódási problémákat, ezek a parancsok fogadására időnként, csatlakozó eszközök két napig tárolódnak. Az Azure IoT Hub fenntartja az egyes eszközök eszközönkénti várólista.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Biztonságos feldolgozása és tárolása a felhőben

A titkosított kommunikáció a felhőben tárolt adatainak feldolgozását a a megoldás gyorsítók adatok biztonsága érdekében. További titkosítási és a biztonsági kulcsok kezelése végrehajtásához rugalmasságot biztosít.

Azure Active Directory (AAD) használ a felhasználói hitelesítés és engedélyezés, a Azure IoT-megoldás gyorsítók egy csoportházirend-alapú engedélyezési modellt az adatok a felhőben naplózva, és tekintse át, mennyire egyszerű a hozzáférés felügyeletének engedélyezésére biztosíthat. Ez a modell is lehetővé teszi, hogy a felhőben tárolt adatainak eléréséhez, valamint az Azure IoT-megoldás gyorsítók csatlakoztatott eszközök szinte azonnali visszavonás.

Ha adatok a felhőben, feldolgozni, és bármely felhasználó által definiált munkafolyamat tárolja. Minden az adatok egy részét a hozzáférést az Azure Active Directoryval, attól függően, hogy a társzolgáltatás használt vezérlik.

Az IoT-infrastruktúra által használt összes kulcs váltása abban az esetben lehet újra kiépíteni kell kulcsok segítségével biztonságos tárolóban, a felhőben vannak tárolva. Adatok tárolhatók [Azure Cosmos DB](../articles/cosmos-db/introduction.md) vagy [SQL-adatbázisok](../articles/sql-database/sql-database-faq.md), engedélyezi a szükséges biztonsági szintjét. Emellett Azure biztosítja az figyelésére és az adatokat, hogy riasztást küldjön, bármely behatolás az elérésére, vagy a jogosulatlan hozzáférés naplózása.

## <a name="conclusion"></a>Összegzés

Az eszközök internetes hálózatát kezdődik-e a dolgok – a legfontosabb dolgokról szolgáltatnak a vállalkozások számára. Az IoT biztosíthat elképesztő érték üzleti költségek csökkentése, árbevétel növelését, és üzleti átalakítása. Sikerült az átalakítás nagymértékben függ a megfelelő IoT szoftver és szolgáltatás szolgáltató kiválasztásához. Ez azt jelenti, hogy olyan szolgáltatót, amely nem csak az átalakítás által az üzleti igények ismertetése és követelmények catalyzes, de emellett a szolgáltatások és szoftverek biztonsági, adatvédelmi, átláthatóság és megfelelőségi fő tervezési megfontolások a következővel keresése. A Microsoft szerzett, kiterjedt tapasztalatunkat fejlesztésük és biztonságos szoftverek és szolgáltatások telepítése és egy vezető továbbra is az új élettartamát az eszközök internetes hálózatát.

A megoldás gyorsítók hozhat létre biztonsági intézkedéseket a Tervező, eszközök javíthatják a hatékonyságukat, biztonságos figyelés engedélyezése meghajtó működési teljesítmény innováció engedélyezése, és a speciális elemzés alkalmaz átalakítására vállalatok számára. A réteges megközelítésének biztonsági, több biztonsági szolgáltatásokat és kialakítási minta felé, és a megoldás gyorsítók súgó olyan infrastruktúra, amely megbízható átalakítását, minden üzleti telepítése.

## <a name="additional-information"></a>További információ

Minden egyes megoldásgyorsító létrehozza Azure-szolgáltatáshoz, például:

* [**Az Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): az átjáró a felhőben csatlakozó eszközöket. Eszköz hitelesítést támogató segít a megoldás biztonságos hub és a folyamat nagy mennyiségű adatot kapcsolatok több millió méretezheti.
* [**Az Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): egy méretezhető, teljes mértékben indexelt dokumentumadatbázis-szolgáltatás, amely felügyeli az eszközök metaadatok félig strukturált adatok ellátásához, például az attribútumokat, a konfiguráció és a biztonsági tulajdonságait. Azure Cosmos DB nagy teljesítményű és nagy átviteli feldolgozás, a séma-független indexelő adatokat, és egy részletes SQL-lekérdezési felületet kínál.
* [**Az Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): valós idejű streamfeldolgozó a felhőben, amely lehetővé teszi, hogy a gyors fejlesztésére és alacsony költségű analytics megoldást nyújt a valós idejű betekintést az eszközök, érzékelőket, infrastruktúra és alkalmazások telepítése . A teljes körűen felügyelt szolgáltatás adatainak megőrzése mellett nagy átviteli sebességet, alacsony késéssel és rugalmasság méretezhető, azonosíthatja a kötettel.
* [**Az Azure App Services**](https://azure.microsoft.com/services/app-service/): egy felhőalapú platform hozhat létre hatékony webes és mobilalkalmazások bárhol adatokat; a felhőben, vagy a helyszínen. Vonzó alkalmazások készítése iOS, Android és Windows rendszerre. A szoftver egy szolgáltatott szoftverként (SaaS) és a felhő alapú szolgáltatások több tucatnyi out-of-az-box kapcsolattal rendelkező vállalati alkalmazások és a vállalati alkalmazások integrálása. A kedvenc nyelvi és IDE az – .NET, Node.js, PHP, Python vagy Java-webalkalmazások és API-k minden eddiginél gyorsabban létrehozásához.
* [**A Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): A Logic Apps szolgáltatás az Azure App Service segítségével integrálható a meglévő üzleti rendszereken az IoT-megoldásból és munkafolyamat-folyamatok automatizálása. A Logic Apps segítségével a fejlesztők olyan munkafolyamatokat alakíthatnak ki, amely egy és majd végrehajtanak bizonyos lépéseket – szabályok és műveleteit, amelyekkel az üzleti folyamatok integrálása hatékony összekötők. A Logic Apps kínál a túlnyomó ökoszisztémájának alkalmazásával az SaaS, a felhő alapú out-of-az-box kapcsolat és a helyszíni alkalmazások.
* [**Az Azure blob storage**](https://azure.microsoft.com/services/storage/): az eszközök elküldik a felhőbe adatok megbízható, gazdaságos felhőalapú tárhelyre.