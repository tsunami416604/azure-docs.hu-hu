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
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789717"
---
Az iontárgyak internete (IoT) egyedülálló biztonsági, adatvédelmi és megfelelőségi kihívásokat jelent a vállalkozások számára világszerte. A hagyományos kibertechnológiával ellentétben, ahol ezek a problémák a szoftverek és azok megvalósításának módja körül forognak, az IoT arra vonatkozik, hogy mi történik, ha a kiber- és a fizikai világok konvergálnak. Az IoT-megoldások védelme megköveteli az eszközök biztonságos kiépítését, az eszközök és a felhő közötti biztonságos kapcsolatot, valamint a felhőben történő biztonságos adatvédelmet a feldolgozás és a tárolás során. Az ilyen funkciók ellen azonban erőforrás-korlátozott eszközök, a központi telepítések földrajzi eloszlása és a megoldáson belüli eszközök nagy száma.

Ez a cikk azt ismerteti, hogy az IoT-megoldásgyorsítók hogyan biztosítanak biztonságos és privát dolgok internetének felhőalapú megoldását. A megoldásgyorsítók teljes körű megoldást kínálnak, a biztonság az alapoktól kezdve minden fázisba bevan építve. A Microsoftnál a biztonságos szoftverek fejlesztése a szoftverfejlesztés gyakorlatának része, amely a Microsoft több évtizedes tapasztalatában gyökerezik a biztonságos szoftverek fejlesztésében. Ennek biztosítása érdekében a Biztonságfejlesztési Életciklus (SDL) az alapvető fejlesztési módszertan, amelyhez számos infrastruktúra-szintű biztonsági szolgáltatás kapcsolódik, mint például az Operational Security Assurance (OSA) és a Microsoft Digital Crimes Unit, Microsoft biztonsági válaszközpont és a Microsoft Kártevők Elleni Központja.

A megoldásgyorsítók olyan egyedi funkciókat kínálnak, amelyek egyszerűvé és átláthatóvá teszik az IoT-eszközökről származó adatok kiépítését, csatlakoztatását és tárolását. Ez a cikk az Azure IoT-megoldásgyorsítók biztonsági funkciókat és telepítési stratégiákat vizsgálja a biztonság, az adatvédelem és a megfelelőségi kihívások kezelése érdekében.

## <a name="introduction"></a>Bevezetés

A dolgok internete (IoT) a jövő hulláma, amely azonnali és valós lehetőségeket kínál a vállalkozásoknak a költségek csökkentésére, a bevételek növelésére és üzleti tevékenységük átalakítására. Számos vállalkozás azonban a biztonsággal, az adatvédelemmel és a megfelelőségsel kapcsolatos aggályok miatt nem szívesen telepíti az IoT-t a szervezetében. Az egyik fő aggodalomra ad okot az IoT-infrastruktúra egyedisége, amely egyesíti a kiber- és fizikai világokat, és súlyosbítja az e két világban rejlő egyéni kockázatokat. Az IoT biztonsága az eszközökön futó kód integritásának biztosítására, az eszköz- és felhasználói hitelesítés biztosítására, az eszközök (valamint az eszközök által generált adatok) egyértelmű tulajdonjogának meghatározására, valamint a kiber- és fizikai támadásokkal szembeni ellenálló képességre vonatkozik.

Aztán ott van a magánélet kérdése. A vállalatok átláthatóságot szeretnének az adatgyűjtéssel kapcsolatban, mint például az adatgyűjtés és -ok, ki láthatja, ki ellenőrzi a hozzáférést, és így tovább. Végül, vannak általános biztonsági kérdések a berendezés mellett az emberek működtetése őket, és kérdések fenntartása ipari szabványok nak való megfelelés.

Tekintettel a biztonsággal, az adatvédelemmel, az átláthatósággal és a megfelelőségi aggályokra, továbbra is kihívást jelent a megfelelő IoT-megoldásszolgáltató kiválasztása. Az IoT-szoftverek és -szolgáltatások különböző gyártók által nyújtott egyes darabjainak összefűzése hiányosságokat okoz a biztonság, az adatvédelem, az átláthatóság és a megfelelőség terén, amelyeket nehéz lehet észlelni, nem hogy kijavítani. A megfelelő IoT-szoftverek és -szolgáltatók kiválasztása azon alapul, hogy olyan szolgáltatókat keresnek, amelyek széles körű tapasztalattal rendelkeznek a szolgáltatások futtatásában, amelyek függőleges ekés földrajzi területeken is átnyúlnak, de biztonságos és átlátható módon is képesek skálázni. Hasonlóképpen, ez segít a kiválasztott szolgáltató, hogy több évtizedes tapasztalattal rendelkezik a fejlődő biztonságos szoftver fut milliárd gépek világszerte, és képesek értékelni a fenyegetés táj által jelentett ez az új világ az Internet of Things.

## <a name="secure-infrastructure-from-the-ground-up"></a>Biztonságos infrastruktúra az alapoktól kezdve

A [Microsoft Cloud](https://azure.microsoft.com) infrastruktúra 127 országban/régióban több mint egymilliárd ügyfelet támogat. A Microsoft több évtizedes tapasztalatára támaszkodva a vállalati szoftverek gyártása és a világ legnagyobb online szolgáltatásainak futtatása során a Microsoft Cloud magasabb szintű fokozott biztonságot, adatvédelmet, megfelelőséget és fenyegetéscsökkentési gyakorlatokat biztosít mint amit a legtöbb ügyfél önmagában el tudna érni.

A [biztonsági fejlesztési életciklus (SDL)](https://www.microsoft.com/sdl/) kötelező vállalati szintű fejlesztési folyamatot biztosít, amely biztonsági követelményeket ágyaz be a teljes szoftveréletciklusba. Annak érdekében, hogy az operatív tevékenységek ugyanolyan szintű biztonsági gyakorlatot kövessenek, az SDL szigorú biztonsági irányelveket alkalmaz, amelyeket a Microsoft működési biztonsági garancia (OSA) folyamata lefektetett. A Microsoft együttműködik külső könyvvizsgáló cégekkel is annak folyamatos ellenőrzése érdekében, hogy megfelel-e megfelelőségi kötelezettségeinek, és a Microsoft széles körű biztonsági erőfeszítéseket tesz kiválósági központok létrehozásával, beleértve a Microsoft Digitális Bűncselekmények Osztályát is, Microsoft Security Response Center és a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – biztonságos IoT-infrastruktúra a vállalkozásszámára

A Microsoft Azure teljes körű felhőalapú megoldást kínál, amely az integrált felhőszolgáltatások – analitika, gépi tanulás, tárolás, biztonság, hálózatkezelés és web – folyamatosan növekvő gyűjteményét ötvözi az iparág vezető kötelezettségvállalásával a az adatok védelme. A Microsoft [feltételezi, hogy](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) a biztonsági incidensek elleni stratégia egy *dedikált, szoftverbiztonsági szakértőkből* álló csapatot használ, akik támadásokat szimulálnak, tesztelik az Azure észlelési képességét, védelmet nyújtanak az újonnan megjelenő fenyegetések ellen, és helyreállíthatják a támadásokat. A Microsoft [globális incidenselhárítási](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) csapata éjjel-nappal azon dolgozik, hogy enyhítse a támadások és a rosszindulatú tevékenységek hatásait. A csapat az incidensek kezelésére, kommunikációjára és helyreállítására vonatkozóan megállapított eljárásokat követi, és felderíthető és kiszámítható felületeket használ a belső és külső partnerekkel.

A Microsoft rendszerei folyamatos behatolásészlelést és -megelőzést, szolgáltatástámadások megelőzését, rendszeres behatolási tesztelést és kriminalisztikai eszközöket biztosítanak, amelyek segítenek azonosítani és enyhíteni a fenyegetéseket. [A többtényezős hitelesítés](../articles/active-directory/authentication/multi-factor-authentication.md) további biztonsági réteget biztosít a végfelhasználók számára a hálózathoz való hozzáféréshez. Az alkalmazás és a gazdaszolgáltató számára pedig a Microsoft hozzáférést biztosít, figyel, kártevőirtó, biztonsági résvizsgálat, javítások és konfigurációkezelés.

A megoldásgyorsítók kihasználják az Azure platformba beépített biztonságot és adatvédelmet, valamint az SDL és OSA folyamatokat az összes Microsoft-szoftver biztonságos fejlesztéséhez és működtetéséhez. Ezek az eljárások infrastruktúra-védelmet, hálózatvédelmet, valamint identitás- és felügyeleti jellemzőket biztosítanak, amelyek alapvető fontosságúak bármely megoldás biztonsága szempontjából.

Az [IoT-megoldásgyorsítókon](../articles/iot-fundamentals/iot-introduction.md) belüli [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) egy teljes körűen felügyelt szolgáltatást kínál, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt az IoT-eszközök és az Azure-szolgáltatások, például az [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) és az Azure [Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) között az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés használatával.

Az Azure IoT-megoldásgyorsítókba beépített biztonsági és adatvédelmi funkciók legjobb kommunikációja érdekében ez a cikk a csomag három elsődleges biztonsági területre bontja a csomagot.

![Azure IoT-megoldásgyorsítók](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Biztonságos eszközkiépítés és hitelesítés

A megoldásgyorsítók biztonságossá teszik az eszközöket, amíg azok a terepen vannak, és minden eszközhöz egyedi identitáskulcsot biztosítanak, amelyet az IoT-infrastruktúra az eszközzel való kommunikációra használhat működés közben. A folyamat gyorsan és egyszerűen beállítható. A felhasználó által kiválasztott eszközazonosítóval létrehozott kulcs az eszköz és az Azure IoT Hub közötti összes kommunikációban használt jogkivonat alapját képezi.

Az eszközazonosítók társíthatók egy eszközhöz a gyártás során (azaz egy hardvermegbízhatósági modulban villoghatnak), vagy használhatnak egy meglévő rögzített identitást proxyként (például CPU-sorozatszámok). Mivel az azonosító adatok módosítása az eszközön nem egyszerű, fontos logikai eszközazonosítókat bevezetni arra az esetre, ha az alapul szolgáló eszköz hardvere megváltozik, de a logikai eszköz ugyanaz marad. Bizonyos esetekben az eszközidentitás társítása az eszköz központi telepítésének időpontjában történhet (például egy hitelesített helyszíni mérnök fizikailag konfigurál egy új eszközt, miközben kommunikál a megoldás háttérkiszolgálójával). Az [Azure IoT Hub identitásjegyzék](../articles/iot-hub/iot-hub-devguide.md) biztonságos tárolását biztosítja az eszközidentitások és a biztonsági kulcsok a megoldáshoz. Az egyes vagy eszközidentitások csoportjai hozzáadhatók egy engedélyezési listához vagy egy tiltólistához, amely lehetővé teszi az eszközhozzáférés teljes körű szabályozását.

Az Azure IoT Hub hozzáférés-vezérlési szabályzatok a felhőben lehetővé teszi kitágítása és letiltása bármely eszköz identitását, így lehetővé teszi, hogy leválasztja az eszközt egy IoT-üzembe helyezés, ha szükséges. Ez a társítás és az eszközök szétválása az egyes eszközidentitásokon alapul.

Az eszköz további biztonsági funkciói a következők:

* Az eszközök nem fogadnak el kéretlen hálózati kapcsolatokat. Az összes kapcsolatot és útvonalat csak kimenő módon hozzák létre. Ahhoz, hogy egy eszköz parancsot kapjon a háttérrendszertől, az eszköznek kapcsolatot kell kezdeményeznie a feldolgozandó függőben lévő parancsok ellenőrzéséhez. Miután az eszköz és az IoT Hub közötti kapcsolat biztonságosan létrejött, a felhőből az eszközre és az eszközről a felhőbe küldött üzenetek transzparens módon küldhetők.

* Az eszközök csak olyan jól ismert szolgáltatásokhoz csatlakoznak vagy hoznak létre útvonalakat, amelyekkel társviszonyban vannak, például egy Azure IoT Hubhoz.

* A rendszerszintű engedélyezés és hitelesítés eszközönkénti identitásokat használ, így a hozzáférési hitelesítő adatok és engedélyek szinte azonnal visszavonhatók.

### <a name="secure-connectivity"></a>Biztonságos kapcsolat

Az üzenetküldés tartóssága minden IoT-megoldás fontos jellemzője. A parancsok és/vagy az eszközökről történő adatok fogadásának szükségességét hangsúlyozza az a tény, hogy az IoT-eszközök az interneten keresztül vagy más hasonló hálózatokon keresztül csatlakoznak, amelyek megbízhatatlanok lehetnek. Az Azure IoT Hub az üzenetekre adott nyugták rendszerén keresztül kínálja a felhő és az eszközök közötti üzenetküldés tartósságát. Az üzenetek további tartóssága az Üzenetek az IoT Hubban legfeljebb hét napig a telemetriai adatok és két nap parancsok gyorsítótárazásával érhető el.

A hatékonyság fontos az erőforrások megőrzésének és az erőforrásokkal korlátozott környezetben való működésének biztosításához. A HTTPS (HTTP Secure), a népszerű http protokoll iparági szabványnak megfelelő biztonságos verziója az Azure IoT Hub támogatásával hatékony kommunikációt tesz lehetővé. Az Azure IoT Hub által támogatott Advanced Message Queuing protocol (AMQP) és message queuing telemetriai átvitel (MQTT) nem csak az erőforrás-felhasználás hatékonyságára, hanem a megbízható üzenetkézbesítésre is szolgál. 

A méretezhetőség hez lehetővé kell tenni az eszközök széles körével való biztonságos együttműködést. Az Azure IoT hub biztonságos kapcsolatot biztosít mind az IP-kompatibilis, mind a nem IP-kompatibilis eszközökhöz. Az IP-kompatibilis eszközök biztonságos kapcsolaton keresztül képesek közvetlenül csatlakozni és kommunikálni az IoT Hubbal. A nem IP-kompatibilis eszközök erőforrás-korlátozottak, és csak rövid távkommunikációs protokollokon, például Zwave, ZigBee és Bluetooth protokollokon keresztül csatlakoznak. A helyszíni átjáró ezekaz eszközök összesítésére szolgál, és protokollfordítást hajt végre a felhővel való biztonságos kétirányú kommunikáció lehetővé tétele érdekében.

A kapcsolat további biztonsági szolgáltatásai a következők:

* Az eszközök és az Azure IoT Hub, illetve az átjárók és az Azure IoT Hub közötti kommunikációs útvonal az iparági szabványnak megfelelő Transport Layer Security (TLS) használatával van biztosítva az X.509 protokollhasználatával hitelesített Azure IoT Hubbal.

* Annak érdekében, hogy megvédje az eszközöket a kéretlen bejövő kapcsolatok, az Azure IoT Hub nem nyit semmilyen kapcsolatot az eszközzel. Az eszköz minden kapcsolatot kezdeményez.

* Az Azure IoT Hub tartósan tárolja az eszközöket, és megvárja, amíg az eszköz csatlakozik. Ezek a parancsok két napig tárolódnak, így a tápellátás vagy a kapcsolat miatt szórványosan csatlakozó eszközök is megkapják ezeket a parancsokat. Az Azure IoT Hub minden eszközhöz egy eszközre vonatkozó várólistát tart fenn.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Biztonságos feldolgozás és tárolás a felhőben

A megoldásgyorsítók a titkosított kommunikációtól a felhőben történő adatfeldolgozásig segítenek az adatok biztonságának megőrzésében. Rugalmasságot biztosít a biztonsági kulcsok további titkosításának és felügyeletének megvalósításához.

Az Azure Active Directory (AAD) használatával a felhasználói hitelesítés és engedélyezés, az Azure IoT-megoldásgyorsítók biztosíthatnak egy házirend-alapú engedélyezési modell adatok a felhőben, amely lehetővé teszi a könnyű hozzáférés-kezelés, amely naplózható és felülvizsgálható. Ez a modell azt is lehetővé teszi, hogy közel azonnali visszavonása az adatokhoz a felhőben, és az Azure IoT-megoldás gyorsítók csatlakozik.

Miután az adatok a felhőben, akkor lehet feldolgozni és tárolni bármely felhasználó által definiált munkafolyamatban. Az adatok egyes részeihez való hozzáférést az Azure Active Directory szabályozza, a használt tárolási szolgáltatástól függően.

Az IoT-infrastruktúra által használt összes kulcs biztonságos tárolóban tárolódik a felhőben, és a kulcsok újbóli kiépítésének lehetősége esetén áttekinthető. Az adatok tárolhatók az [Azure Cosmos DB-ben](../articles/cosmos-db/introduction.md) vagy [az SQL-adatbázisokban,](../articles/sql-database/sql-database-faq.md)lehetővé téve a kívánt biztonsági szint meghatározását. Emellett az Azure lehetővé teszi az adatokhoz való hozzáférés figyelésének és naplózásának módját, hogy figyelmeztesse Önt a behatolásra vagy a jogosulatlan hozzáférésre.

## <a name="conclusion"></a>Összegzés

A dolgok internete a dolgaiddal kezdődik – azokkal a dolgokkal, amelyek a legfontosabbak a vállalkozások számára. Az IoT elképesztő értéket tud nyújtani egy vállalkozásnak a költségek csökkentésével, a bevételek növelésével és az üzleti tevékenység átalakításával. Az átalakítás sikere nagyban függ a megfelelő IoT-szoftverek és -szolgáltató kiválasztásától. Ez azt jelenti, hogy olyan szolgáltatót kell találni, amely nem csak az üzleti igények és követelmények megértésével katalizálja ezt az átalakulást, hanem olyan szolgáltatásokat és szoftvereket is biztosít, amelyek biztonsággal, adatvédelemmel, átláthatósággal és megfelelőséggel készültek, mint fő tervezési szempontokat. A Microsoft széles körű tapasztalattal rendelkezik a biztonságos szoftverek és szolgáltatások fejlesztésében és telepítésében, és továbbra is vezető szerepet tölt be a dolgok internetének új korszakában.

A megoldásgyorsítók beépített biztonsági intézkedéseket hoznak létre, lehetővé téve az eszközök biztonságos felügyeletét a hatékonyság javítása érdekében, az innovációt lehetővé tevő működési teljesítmény növelését, valamint a vállalkozások átalakítására fejlett adatelemzési megoldásokat alkalmazva. A biztonság, a több biztonsági funkciók és a tervezési minták réteges megközelítésével a megoldásgyorsítók segítenek egy olyan infrastruktúra üzembe helyezésében, amely megbízható bármely vállalkozás átalakításához.

## <a name="additional-information"></a>További információ

Minden megoldásgyorsító létrehozza az Azure-szolgáltatások példányait, például:

* [**Azure IoT Hub:**](https://azure.microsoft.com/services/iot-hub/)Az átjáró, amely összeköti a felhőt az eszközökkel. Hubonként több millió kapcsolatra méretezhető, és hatalmas mennyiségű adatot dolgozhat fel az eszközönkénti hitelesítéstámogatással, amely segít a megoldás biztonságossá tétele érdekében.

* [**Azure Cosmos DB:**](https://azure.microsoft.com/services/cosmos-db/)A méretezhető, teljes indexelt adatbázis-szolgáltatás félig strukturált adatok, amely kezeli a metaadatokat a kihelyezett eszközök, például attribútumok, konfigurációs és biztonsági tulajdonságok. Az Azure Cosmos DB nagy teljesítményű és nagy átviteli sebességű feldolgozást, séma-független adatindexelést és gazdag SQL-lekérdezési felületet kínál.

* [**Azure Stream Analytics:**](https://azure.microsoft.com/services/stream-analytics/)Valós idejű adatfolyam-feldolgozás a felhőben, amely lehetővé teszi, hogy gyorsan fejlesszen ki és telepítsen egy alacsony költségű elemzési megoldást az eszközök, érzékelők, infrastruktúra és alkalmazások valós idejű elemzéseinek feltárására. A teljes körűen felügyelt szolgáltatásból származó adatok bármely kötetre skálázhatók, miközben továbbra is nagy átviteli, alacsony késésű és rugalmasság érhető el.

* [**Azure App Services:**](https://azure.microsoft.com/services/app-service/)Felhőalapú platform, amely hatékony webes és mobilalkalmazásokat hozhat létre, amelyek bárhol csatlakozhatnak az adatokhoz; a felhőben vagy a helyszínen. Vonzó mobilalkalmazásokat készíthet iOS, Android és Windows rendszerre. Integrálja szoftverét szolgáltatásként (SaaS) és vállalati alkalmazásokkal, és minden hatóel őket képes kapcsolattal több tucat felhőalapú szolgáltatással és vállalati alkalmazással. Kódoljon kedvenc nyelvén és IDE-.NET, Node.js, PHP, Python vagy Java nyelven, hogy minden eddiginél gyorsabban hozhat webalkalmazásokat és API-kat.

* [**Logic Apps:**](https://azure.microsoft.com/services/app-service/logic/)Az Azure App Service Logikai alkalmazások funkciója segít integrálni az IoT-megoldást a meglévő üzletági rendszerekbe, és automatizálni a munkafolyamatokat. A Logic Apps lehetővé teszi a fejlesztők számára, hogy olyan munkafolyamatokat tervezzenek, amelyek egy eseményindítóból indulnak ki, majd egy sor lépést hajtanak végre – olyan szabályokat és műveleteket, amelyek hatékony összekötőket használnak az üzleti folyamatokba való integráláshoz. A Logic Apps beépített kapcsolatot biztosít az SaaS, a felhőalapú és a helyszíni alkalmazások hatalmas ökoszisztémájához.

* [**Azure Blob storage:**](https://azure.microsoft.com/services/storage/)Megbízható, gazdaságos felhőalapú tárhely az eszközök által a felhőbe küldött adatok számára.