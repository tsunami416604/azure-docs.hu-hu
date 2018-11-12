---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 14e8eb7c73a1e2cf5047410d3571008c1cd1e1ca
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289274"
---
# <a name="security-for-internet-of-things-iot-from-the-ground-up"></a>Biztonsági eszközök internetes hálózatához (IoT) létrehozása az alapoktól

A dolgok Internete (IoT) a vállalkozások világszerte egyedi biztonsági, adatvédelmi és megfelelőségi kihívást jelent. Ellentétben a hagyományos kibertámadások technológia, ahol a ezeket a problémákat a szoftvert, és hogyan valósul köré szerveződik IoT vonatkozik, mi történik, ha az internetes és a fizikai világ lesz. Biztonságos kiépítés eszközök, ezek az eszközök és a felhőben és a biztonságos adatvédelem a felhőben feldolgozásra és tárolásra során közötti biztonságos kapcsolatot biztosító IoT-megoldások védelme szükséges. Azonban működik a funkció, olyan eszközök erőforrás korlátozott, központi telepítések földrajzi eloszlása és egy nagy számú egy megoldáson belül.

Ez a cikk bemutatja, hogyan az IoT-megoldásgyorsítók adjon meg egy biztonságos és személyes eszközök internetes hálózata felhőalapú megoldás. A megoldásgyorsítók teljes körű teljes körű megoldást, az alapjaitól kezdve minden szakaszában épített biztonsági révén. A Microsoft biztonságos szoftverfejlesztés része a szoftverfőmérnöke gyakorlat, a Microsoft évtizedes hardverből hosszú biztonságos szoftverfejlesztés élménye. Ennek biztosítására, biztonságos fejlesztési Életciklussal (SDL) az eligazodást fejlesztési módszertanába, infrastruktúra-szintű biztonsági szolgáltatások, például garanciában (OSA) és a Microsoft Digital Crimes Unit, a Microsoft számos szolgáltatással párosítva Security Response Centerből, és a Microsoft Malware Protection Center.

A megoldásgyorsítók egyedi funkciókat kínálnak, a márka kiépítését, csatlakozik és egyszerű és átlátható IoT-eszközökről származó adatok tárolására, és az összes, a legtöbb biztonságos. Ez a cikk az Azure IoT megoldás megoldásgyorsítók biztonsági funkciók megvizsgálja, és üzembe helyezési stratégiáknak felelnek annak biztosítása érdekében a biztonsági, adatvédelmi és megfelelőségi kihívások foglalkozik.

## <a name="introduction"></a>Bevezetés

A dolgok Internete (IoT) a jövőben azonnali vállalkozások ajánlat és a való életből vett lehetőségek csökkentheti a költségeket, növelheti a bevételt és a vállalkozás átalakítására hulláma. Számos vállalat azonban is szívesen üzembe helyezése IoT biztonsági, adatvédelmi és megfelelőségi szempontok miatt a szervezetben. Egy nagyobb pont aggodalomra ad okot a egyedi-e az IoT infrastruktúra, amely egyesíti az internetes és a fizikai világ együtt, ezek két világ egyes kockázatokat főneveket származik. Az IoT biztonsági vonatkozik, eszközökön futó kódot sértetlenségének biztosítása eszköz- és felhasználói hitelesítést biztosító eszközök (valamint eszközök által létrehozott adatok) törölje tulajdonjogának meghatározása és folyamatban van az internetes és a fizikai támadások rugalmas.

Ezt követően a probléma van az adatvédelem. Vállalatok szeretné átláthatóság vonatkozó adatgyűjtést, a gyűjtött adatok alatt, és miért, akik láthatják őket, akik szabályozza a hozzáférést, és így tovább. Végezetül vannak a berendezés mellett az azokat üzemeltető személyek általános biztonsági problémák és karbantartása az iparági szabványok megfelelőségi problémákat.

Adja meg a biztonsági, adatvédelmi, átláthatóság és megfelelőségi problémák, a megfelelő IoT-megoldás szolgáltató választása marad kihívást. Biztonsági, adatvédelmi, átláthatóság és megfelelőséget, amelyeket nehéz észlelni, hát alone javítsa ki lehet hiányosságok összefűzheti egyéni IoT-szoftver és a szolgáltatások különböző kereskedőktől származó adatokat mutatja be. A megfelelő IoT-szoftver és szolgáltatás szolgáltató alapján szolgáltatók,-szolgáltatások, amelyek ívelhet át több referenciaegyenesen és földrajzi területről, de is képes, méretezhető, biztonságos és átlátható módon széles körű tapasztalatokkal rendelkeznek keresése a kiválasztott. Ehhez hasonlóan ez segít a kiválasztott szolgáltató évtizedes tapasztalatot mondhat biztonságos szoftverfejlesztés gépek világszerte több milliárd fut, és lehetővé teszi a fenyegetések világának az új rengeteg a dolgok Internete által jelentett értékeljük rendelkeznek.

## <a name="secure-infrastructure-from-the-ground-up"></a>Biztonságos infrastruktúra létrehozása az alapoktól

A [Microsoft Cloud](https://azure.microsoft.com) infrastruktúra támogatja a több mint egymilliárd ügyfelek 127 országban. Rajz a Microsoft által több évtizedes tapasztalatait nagyvállalati szoftverek készítése és a világ legnagyobb online szolgáltatásai futtató, a Microsoft Cloud biztosít fokozott biztonsági, adatvédelmi, megfelelőségi és fenyegetés magasabb szintű kockázatcsökkentési eljárások mint az ügyfelek többsége saját maguk is el lehet érni.

A [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/) biztosít a vállalati szintű fejlesztési folyamat során kötelezően, amely a biztonsági követelményeknek a teljes szoftveréletciklusba beágyazza. Győződjön meg arról, hogy üzemeltetési tevékenységek kövesse az ajánlott biztonsági eljárások azonos szintű érdekében SDL szigorú biztonsági irányelveket elrendezve a Microsoft garanciában (OSA) folyamat használja. A Microsoft emellett együttműködik a külső auditáló cégekkel folyamatos ellenőrzés, hogy megfelel-e megfelelőségi kötelezettségeit, és a Microsoft kompetenciaközpontjai széles körű biztonsági kompetenciaközpontok, a Microsoft Digital Crimes Unit, beleértve a létrehozása munkája alapján A Microsoft Security Response Center és Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>A Microsoft Azure – biztonságos IoT-infrastruktúrát a vállalkozása számára

Egyet, amely ötvözi az folyamatosan gyarapodó integrált felhőszolgáltatások átfogó felhőalapú megoldást kínál a Microsoft Azure – elemzés, gépi tanulás, tárolási, biztonsági, hálózati és webes – az védelméhez olyan piacvezető elkötelezettséget és az adatok védelme. A Microsoft [feltételezése](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) stratégiát használ egy dedikált *csapat* szimulálása támadások, a tesztelés képes észlelni az Azure biztonsági szakértők szoftvert, az újonnan felbukkanó fenyegetésekkel szemben, és helyreállítása kapcsolatos problémák esetén. A Microsoft [globális incidenskezelő](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) csapat működik, arra a támadások és kártékony tevékenységek által okozott hatások mérsékléséhez. A csapat elfogadott incidenskezelés, kommunikációs és helyreállítási eljárásokat követi, és felderíthető és kiszámítható felületeket használja a belső és külső partnerekkel.

A Microsoft rendszerekben folyamatos behatolásérzékelési és megelőzéséről, szolgáltatás támadások megelőzése, rendszeres behatoláspróbák és nyomkereső eszközökkel, amelyek segítenek azonosítani és elhárítani a fenyegetéseket. [A multi-factor authentication](../articles/active-directory/authentication/multi-factor-authentication.md) egy extra a végfelhasználók hozzáférhessenek a hálózati biztonsági réteget nyújt. És az alkalmazás és a gazdagép-szolgáltató, a Microsoft kínál a hozzáférés-vezérlés, figyelés, kártevőirtó, biztonsági rések keresése, javításokat és konfigurációkezelés.

A megoldásgyorsítók biztonsági és adatvédelmi a biztonságos fejlesztésre és az összes Microsoft-szoftverek művelethez SDL és OSA folyamatok mellett az Azure platform beépített előnyeit. Ezek az eljárások infrastruktúra védelmét, a hálózati védelem és a biztonsági megoldások alapvető identitás- és felügyeleti funkciók biztosítanak.

A [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) belül a [IoT-megoldásgyorsítók](../articles/iot-fundamentals/iot-introduction.md) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi például IoT-eszközök és az Azure-szolgáltatások közötti megbízható és biztonságos kétirányú kommunikációt nyújt [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) és [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés használatával.

A legjobb kommunikációhoz, biztonság és adatvédelem az Azure IoT-megoldásgyorsítók épített, ez a cikk a három elsődleges biztonsági területre a csomag felszámolja.

![Azure IoT-megoldásgyorsítók](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Device Provisioning Service és a hitelesítés biztonságossá tétele

A megoldásgyorsítók eszközök biztonságos, amíg azok meg a mező azáltal, hogy minden egyes eszközhöz, amely az eszköz kommunikálni, amíg folyamatban van az IoT-infrastruktúra által használható egy egyedi kulcs. A folyamat a gyors és könnyű őket beállítani. A létrehozott kulcsot a felhasználó által kiválasztott Eszközazonosítót használó alapját egy tokent, az eszköz és az Azure IoT Hub közötti minden kommunikáció során használt.

Az eszközazonosítókat társítható egy eszközhöz folyamán gyártás (amelyet a hardveres megbízhatósági modul van, fellobban), vagy használhat egy meglévő rögzített identitás (például CPU sorozatszámok) proxyként. Mivel a módosítása a azonosító adatokat az eszközön nem egyszerű, fontos vezeti be a logikai eszköz azonosítóját, abban az esetben a mögöttes hardver értesítse, de a logikai eszközt változatlan marad. Bizonyos esetekben egy új eszközidentitást közötti társítás fordulhat elő, eszköz üzembe helyezéskor (például egy hitelesített mérnök fizikailag konfigurálja egy új eszközt a megoldás háttérrendszere folytatott kommunikáció során). A [Azure IoT Hub-identitásjegyzék](../articles/iot-hub/iot-hub-devguide.md) eszközidentitások és a egy megoldás esetében a biztonsági kulcsok biztonságos tárolást biztosít. Személy vagy csoport eszközidentitások lehet hozzáadni az engedélyezési listán, vagy egy tiltólista engedélyezése az eszközhozzáférés teljes vezérlését.

Az Azure IoT Hub hozzáférés-vezérlési házirendeket a felhőben az aktiválás és az eszközidentitás, így szüntesse meg az eszközön az IoT-környezet szükség esetén a letiltás engedélyezése. Ez a társítás és eszközök társításának minden eszközidentitáshoz alapul.

További biztonsági funkciói a következők:

* Eszközök nem fogadja el kéretlen hálózati kapcsolatokat. Csak kimenő el azok létesíthet kapcsolatokat és útvonalakat. Egy eszköz parancsot fogadjon a háttérrendszer az eszköz kell kapcsolatot kezdeményeznie a függőben lévő feldolgozandó parancsok ellenőrzéséhez. Miután az eszköz és az IoT Hub között biztonságos létrejön a kapcsolat, üzenetküldés a felhőből az eszközre és eszközről a felhőbe az elküldött transzparens módon.

* Eszközök csak csatlakozni, vagy ezekkel létesíthetnek útvonalat jól ismert, amellyel társított szolgáltatásokhoz, például egy Azure IoT hubot.

* Rendszerszintű engedélyezés és hitelesítés eszközönkénti identitásokon, így a hozzáférési hitelesítő adataival és engedélyeivel közel használata – azonnali visszavonható.

### <a name="secure-connectivity"></a>Biztonságos kapcsolatok

Üzenetküldési tárolt bármely IoT-megoldás egyik fontos szolgáltatása. Az a tény, hogy az IoT-eszközök csatlakoznak-e az interneten vagy más hasonló hálózatokhoz, amelyek nem megbízható hálózathatáron aláhúzott kell tartósan parancsokat, illetve közvetítése adatokat fogadjon az eszközökről. Az Azure IoT Hub üzenetkezelés a felhőben és az eszközöknek az üzenetek maximális kézbesítésszáma nyugták rendszerek közötti tartósságot biztosít. Üzenetküldési szolgáltatásokhoz további tartóssága üzeneteket az IoT hub legfeljebb hét napja, a telemetriai adatok és parancsok két nap gyorsítótárazásával érhető el.

Az erőforrások és a műveletet olyan erőforrás-okból korlátozott környezet megóvása érdekében fontos, hatékonyságát. HTTPS (HTTP Secure), a népszerű http-protokoll biztonságos iparági szabványnak megfelelő verziója támogatja az Azure IoT Hub és hatékony kommunikáció engedélyezése. Speciális Message Queuing Protocol (AMQP) és a Message Queuing Telemetry Transport (MQTT), Azure IoT Hub által támogatott úgy tervezték, nem csupán a hatékonyság szempontjából erőforrás-használat, de üzenet megbízható kézbesítését. 

Méretezhetőség biztonságosan együttműködni eszközök számos lehetősége van szükség. Az Azure IoT hub lehetővé teszi biztonságos kapcsolat IP-kompatibilis és nem IP-kompatibilis eszközökön is. IP-kompatibilis eszközök képesek közvetlenül csatlakozhat, és az IoT Hub kommunikálni biztonságos kapcsolaton keresztül. Nem alkalmas eszközök erőforrás korlátozott, és csak rövid távolság kommunikációs protokollok, például Zwave ZigBee és Bluetooth keresztül. Helyszíni átjáró ezeknek az eszközöknek összesítésére szolgál, és a protokollátjárók protokollfordítást végeznek a felhő biztonságos kétirányú kommunikáció engedélyezéséhez.

Kapcsolat további biztonsági szolgáltatások a következők:

* Eszközök és az Azure IoT Hub között, vagy átjárókat és az Azure IoT Hub közötti kommunikációs útvonal az Azure IoT Hub X.509-protokoll használatával hitelesíti szabványos Transport Layer Security (TLS) használatával lett biztonságossá téve.

* Annak érdekében, hogy az eszközök védelme a kéretlen bejövő kapcsolatokat, az Azure IoT Hub nem nyitható meg az eszköz minden olyan kapcsolatot. Az eszköz összes kapcsolatot kezdeményez.

* Az Azure IoT Hub tartósan tárolja az üzeneteket az eszközöket, és megvárja, amíg az eszköz csatlakozhat. Ezeket a parancsokat az áramellátási vagy csatlakozási problémákat, ezek a parancsok fogadására miatt szórványosan, kapcsolódó eszközök engedélyezése két napig tárolódnak. Az Azure IoT Hub egy eszközre irányuló várólista minden egyes eszközhöz fenntart.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Biztonságos feldolgozására és tárolására, a felhőben

A titkosított kommunikációs feldolgozási adataihoz a felhőben a a megoldásgyorsítók adatok biztonsága érdekében. Rugalmasan valósíthatják meg a további titkosítási és biztonsági kulcsok kezelését biztosítja.

Felhasználói hitelesítés és engedélyezés az Azure Active Directory (AAD) használja, az Azure IoT-megoldásgyorsítók megadhat egy engedélyezési házirend-alapú modell adatok a felhőben, egyszerű hozzáférés-kezelés, amely ellenőrzi, és tekintse át engedélyezése. Ez a modell is lehetővé teszi a hozzáférést adataihoz a felhőben, valamint az eszköz csatlakozik az Azure IoT-megoldásgyorsítók közel azonnali visszavonás.

Amint adatok a felhőben, feldolgozása, és minden olyan felhasználó által definiált munkafolyamat tárolva. Minden egyes részére, amelyben az adatok hozzáférésének az Azure Active Directoryval, attól függően, a storage szolgáltatás használt.

Az IoT-infrastruktúra által használt összes kulcs lehetővé teszi az vihetők át a arra az esetre érvénybe kell kulcsok biztonságos tárolása a felhőben tárolódnak. Adatok tárolhatók [Azure Cosmos DB](../articles/cosmos-db/introduction.md) vagy a [SQL-adatbázisok](../articles/sql-database/sql-database-faq.md), engedélyezése a a szükséges biztonsági szintjét. Az Azure emellett figyelése és a naplózási minden hozzáférés az adatokhoz, riasztást küld minden olyan behatolásvédelmi, és a jogosulatlan hozzáféréssel.

## <a name="conclusion"></a>Összegzés

Az IOT-kezdődik a dolgokat – a vállalkozások a leginkább fontos dolog. IoT is értékteremtés lenyűgöző üzleti költségek csökkentése, növelje a bevétel és üzleti átalakítása. Az átalakítás sikeres nagymértékben függ a megfelelő IoT szoftver és szolgáltatás-szolgáltató kiválasztásáról. Ez azt jelenti, hogy egy szolgáltató, amely nem csak az átalakítás ismertetése üzleti szükségletei és igényei szerint catalyzes, de is biztosít a szolgáltatások és a beépített biztonsági, adatvédelmi, átláthatóság és megfelelőségi fő tervezési megfontolások a szoftver keresése. A Microsoft a fejleszteni és üzembe helyezni a biztonságos szoftverek és szolgáltatások széles körű ismeretekkel rendelkezik, és vezető továbbra is az új IOT-élettartamát.

A megoldásgyorsítók biztonsági intézkedéseket a kialakításból fakadóan javíthatják a hatékonyságukat, az eszközök biztonságos figyelés engedélyezése meghajtó működési teljesítmény innovációnak, és a alkalmazni a fejlett adatelemzés vállalkozás átalakítására hozhat létre. A biztonsági, több biztonsági funkciókat és tervezési minták a rétegelt megközelítést a megoldásgyorsítók segítségével üzembe helyezése olyan infrastruktúra, amely megbízható bármely vállalkozás átalakítására.

## <a name="additional-information"></a>További információ

Egyes megoldásgyorsítók hoz létre példányok az Azure-szolgáltatások, például:

* [**Az Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): az átjáró a felhőben csatlakozó eszközöket. Eszközönkénti hitelesítés támogatását, így megoldását biztonságos a hubot és a folyamat nagy mennyiségű adatot-nként több millió skálázhatja.

* [**Az Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): méretezhető, teljes körűen indexelt adatbázis-szolgáltatás, amely felügyeli az eszközök metaadatait, részben strukturált adatok számára hozza létre, például az attribútumokat, konfigurációs és biztonsági tulajdonságait. Az Azure Cosmos DB kínál a nagy teljesítményű és nagy átviteli sebességű feldolgozására, sémafüggetlen indexelését, adatok és a egy részletes SQL-lekérdezési felületet.

* [**Az Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): valós idejű streamfeldolgozás a felhőben, amely lehetővé teszi, hogy gyors fejlesztése és üzembe helyezése egy alacsony költségű analytics megoldás a valós idejű feltárásában eszközök, érzékelők, az infrastruktúra és alkalmazások . E teljes körűen felügyelt szolgáltatás az adatokat tetszőleges adatmennyiséghez a nagy átviteli sebességet, közel valós idejű és rugalmasság méretezheti.

* [**Az Azure App Services**](https://azure.microsoft.com/services/app-service/): egy felhőalapú platform, amely hatékony webes és mobilalkalmazások bárhol adatokat; a felhőben vagy a helyszínen. Vonzó alkalmazások készítése iOS, Android és Windows rendszerre. Integrálható a szoftvert, mint a szoftverszolgáltatások (SaaS) és a vállalati alkalmazásait-az-a – azonnali kapcsolódás több tucat felhőalapú szolgáltatáshoz és vállalati alkalmazáshoz. A kedvenc nyelvét és IDE – .NET, Node.js, PHP, Python vagy Java-webalkalmazások és API-k, minden eddiginél gyorsabban készíthet.

* [**A Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): az Azure App Service Logic Apps funkciója segít integrálni az IoT-megoldás a meglévő üzleti rendszerekhez és a munkafolyamatok automatizálása. A Logic Apps segítségével a fejlesztők olyan egy eseményindítóval kezdődnek, majd végrehajtanak bizonyos lépéseket munkafolyamatokat – szabályok és műveletek, amely integrálható az üzleti folyamatok hatékony összekötők használatával. A Logic Apps-az-– azonnali kapcsolatok beépítésével az SaaS, felhőalapú, és kínál a helyszíni alkalmazásokat.

* [**Az Azure Blob storage**](https://azure.microsoft.com/services/storage/): megbízható, gazdaságos felhőalapú tárolás az adatok, amelyek az eszközök küldenek a felhőbe.