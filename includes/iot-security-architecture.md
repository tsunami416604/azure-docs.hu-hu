---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 61fb8380bcad7a30d822ab610f52e8515477d683
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56246915"
---
# <a name="internet-of-things-iot-security-architecture"></a>Eszközök internetes hálózata (IoT) biztonsági architektúra

A rendszer tervezésekor fontos, hogy a rendszer a potenciális fenyegetések, és megfelelő védelmet ennek megfelelően felvenni, mert a rendszer lett kialakítva, és lett tervezve. Fontos a kezdetektől a termék tervezhet a biztonságot szem, mivel egy támadó hogyan lehet tudni veszélyeztetheti a rendszer segítségével, győződjön meg arról, hogy megfelelő megoldások tudnivalók a rendszer az elejétől.

## <a name="security-starts-with-a-threat-model"></a>Biztonsági kezdődik a fenyegetések modellezése

A Microsoft már hosszú használt modelljei termékei és által végrehajtott folyamat nyilvánosan elérhető modellezési a vállalat veszélyforrások elleni. A vállalat felhasználói élményt ismerteti, hogy rendelkezik-e a modellezési túli fenyegetések Mik a legtöbb azonnali ismeretekkel nem várt előnyök vonatkozóan. Például is létrehoz egy sérülésre egy megnyitott beszélgetéshez másokkal kívül a fejlesztői csapat, ami új ötleteket és fejlesztések a termékben.

Fenyegetés modellezési célja, hogy hogyan egy támadó képes veszélyeztetheti a rendszer, és ezután ellenőrizze, hogy megfelelő megoldások vannak érvényben. Veszélyforrások elleni modellezési kényszeríti a rendszert úgy tervezték, hanem után a rendszer a megoldások, érdemes figyelembe venni a tervezési csapattal van üzembe helyezve. A tény, különösen fontos, mivel az eszközök a mezőben a számtalan biztonsági védelmekkel történő lehessen hozni, hibalehetőségeket rejt magában, és hagyja ügyfeleinek kockáztatja.

Számos fejlesztői csapatok munkaköre egy kiváló rögzítése a rendszer a funkcionális követelmények, amelyek igénybe vehető. Azonban, hogy valaki visszaél előfordulhat, hogy a rendszer nem egyértelmű módon azonosítása, nehéz lehet. Fenyegetések modellezése segíthetnek megérteni, hogy egy támadó előfordulhat, hogy mire fejlesztői csapatok és miért. Fenyegetések modellezése az strukturált, amely a biztonsági szóló vita tervezési döntéseket hoz a rendszer, valamint a kialakítási hatás biztonság végzett követhető változik. Bár a fenyegetések modellezése egyszerűen csak egy dokumentumot, ezt a dokumentációt is jelöli annak biztosítására, megőrzési több leckéből áll, az ismeretek folytonossági megtudhatta, és súgó, új gyorsan csapat felvétele ideális megoldást. Végül a fenyegetések modellezése pénznembeli díjváltozása ahhoz, hogy más például milyen pedig adja meg az ügyfelek számára kíván biztonsági kötelezettségeit, biztonsági szempontból. E kötelezettségek threat modellezési együtt értesíti, és a meghajtó tesztelését, az eszközök internetes hálózata (IoT) megoldás.

### <a name="when-to-do-threat-modeling"></a>Ha a fenyegetések modellezése

[Veszélyforrások elleni modellezési](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) kínálja a legnagyobb értékű, beépítheti a tervezési fázis során. Ha a tervez, akkor ki a fenyegetéseket, módosítsa a legnagyobb rugalmasságot. A szükséges teljesítendő veszélyforrások kiküszöbölése szándékosan. Sokkal egyszerűbb, mint kockázatcsökkentő hozzáadása, kipróbálása és naprakészek maradjanak, és emellett az ilyen eltávolítás nem mindig lehetőség. Ki a fenyegetéseket, valamint a termék válik több érett végső soron pedig szükség van a több munkát és sokkal nehezebb kompromisszumot kínál a fenyegetések modellezése már a legelején a fejlesztői, mint nehezebb lesz.

### <a name="what-to-consider-for-threat-modeling"></a>Mit kell figyelembe venni a fenyegetések modellezése

Akkor tekintse meg a megoldás egy egész és azt is hangsúlyt a következő területeken:

* A biztonsági és adatvédelmi funkciók
* A Funkciók, amelynek hibák a megfelelő biztonsági
* A Funkciók, amelyek egy megbízhatósági kapcsolat határán touch

### <a name="who-performs-threat-modeling"></a>Akik hajtja végre a fenyegetések modellezése

Veszélyforrások elleni modellezési egy olyan folyamat, mint minden más. Célszerű a veszélyforrások elleni modell dokumentumot, mint bármely másik összetevője a megoldás kezeli, és érvényesítse azt. Számos fejlesztői csapatok munkaköre egy kiváló rögzítése a rendszer a funkcionális követelmények, amelyek igénybe vehető. Azonban, hogy valaki visszaél előfordulhat, hogy a rendszer nem egyértelmű módon azonosítása, nehéz lehet. Fenyegetések modellezése segíthetnek megérteni, hogy egy támadó előfordulhat, hogy mire fejlesztői csapatok és miért.

### <a name="how-to-perform-threat-modeling"></a>Hogyan hajthat végre a fenyegetések modellezése

A fenyegetés a modellezési folyamat négy lépést; áll a lépések a következők:

* Modellezze az alkalmazást
* Fenyegetések számbavétele
* Elhárítani a fenyegetéseket
* A megoldások ellenőrzése

#### <a name="the-process-steps"></a>A folyamat lépéseivel

Három szabályok költségcsökkenést eredményezzen szem előtt kell tartani a fenyegetések modellezése készítése során:

1. Hozzon létre egy kívül a referencia-architektúra diagramja.

2. Indítsa el a szélesség-és felhőközpontú. Áttekintheti, és megismerheti a rendszer egészének részletes műugrásra előtt. Ez a megközelítés segít biztosítani, hogy Ön részletes leírása a megfelelő helyeken.

3. Meghajtó a folyamatot, ne hagyja, hogy a meghajtó, a folyamat. Ha a modellezési fázisban olyan problémát talált, és felfedezheti, vegyen részt benne!! Nem úgy gondolja, hogy slavishly kövesse az alábbi lépéseket kell.

#### <a name="threats"></a>Fenyegetések

A fenyegetések modellezése négy alapvető elemei a következők:

* Például a webes szolgáltatások, a Win32-szolgáltatások, folyamatok és a * démonok nix. Bizonyos összetett entitások (például a helyszíni átjárók és az érzékelők) is emeli ki, mert az egy folyamatot, ha a következő területeken technikai részletezése nem lehetséges.

* Adattárak (bárhol adatokat tárolja, például egy konfigurációs fájlban vagy adatbázis)

* Az adatfolyam (ahol adatokat helyez át, az alkalmazás más elemek között)

* Külső entitások (semmit, amellyel kommunikál a rendszer, de ez nem az alkalmazás felügyelete alá tartozik, példák felhasználókat tartalmazza, és hírcsatornák műholdas)

Az architekturális diagramja szereplő összes elem fenyegetéseket; vonatkozik. Ez a cikk a STRIDE követelnék. Olvasási [Threat újra modellezés, a STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) többet megtudni a STRIDE elemeket.

Különböző elemeit, az alkalmazás diagram egyes STRIDE fenyegetések vonatkozik:

* Folyamatok STRIDE vonatkozik.
* Adatfolyam-gyűjteményre vonatkoznak rájuk a TID
* Adattárak vonatkoznak rá TID, és néha R, ha az adattárak naplófájlokat.
* Külső entitások SRD vonatkozik.

## <a name="security-in-iot"></a>Az IoT biztonsági

Csatlakoztatott speciális célú eszközök kapcsolati surface területeket, és interakció minták, amelyek mindegyike figyelembe kell venni egy keretrendszert nyújt a digitális, az adott eszközökre való hozzáférés biztonságossá tétele jelentős számú rendelkezik. A használt kifejezés a "digitális hozzáférés" Itt megkülönböztetni a közvetlen interakció keresztül végrehajtott műveleteket a hozzáférés-biztonságot amennyiben a fizikai hozzáférés-vezérlés használatával. Ha például üzembe helyezése az eszköz zár szoba ajtón. Fizikai hozzáférés nem elutasítja a hardver- és használatával, miközben intézkedéseket is vezető rendszer zavaró tényező való fizikai hozzáférés megakadályozása.

A kapcsolati minták megismerésével meg "eszköz" és "eszköz" azonos szintű a figyelmet. Minden olyan információt, amely bármely fél által biztosított az eszközökre,. a cél módosítása, illetve annak viselkedését felé az állapot vagy a környezet állapotát befolyásoló az "Eszköz control" meghatározhassa. "Eszköz adatok" meghatározhassa minden olyan információt, az eszköz bocsát ki a másik félre állapotában és a megfigyelt állapotát a környezetében.

Annak érdekében, hogy ajánlott biztonsági eljárások optimalizálása érdekében javasoljuk, hogy egy tipikus IoT-architektúra van felosztva számos összetevő és a fenyegetések modellezése a gyakorlatban részeként. A zónák teljes ebben a szakaszban leírt, és a következők:

* Eszköz,
* Helyszíni átjáró,
* Átjárók, a felhő és a
* Szolgáltatások.

Zónák olyan meghibásodások széles körű lépésig, szegmentálhatja a megoldást. az egyes zónák gyakran rendelkezik a saját adatok és a hitelesítési és engedélyezési követelményeinek. Zónák is használható elkülönítési károkat, és magasabb megbízhatósági zónák alacsony megbízhatósági zónák hatásának korlátozása.

Minden zóna megbízhatósági tartományba tartozik, az alábbi ábrán a pontozott piros vonal észlelt választja el. Adatok és információk átmenetet, jelöl egy forrásból egy másikba. Ez a váltás során az adatokat és információkat lehet Spoofing, módosítás, Letagadhatóság, Információfelfedés, szolgáltatásmegtagadás és jogosultság jogosultságszint-emelés (LÉPÉSKÖZ) vonatkoznak.

![IoT-biztonsági zónák](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Az egyes határán belül kitaláltak összetevők is kell alávetni STRIDE, egy teljes 360 engedélyezése nézet a megoldás modellezési fenyegetést. Az alábbi szakaszok ismertetik az egyes összetevőjének és adott biztonsági problémákat és megoldásokat, amelyek kell üzembe helyezéséhez.

A következő részekben bemutatjuk a szabványos összetevők általában ebben a zónában található.

### <a name="the-device-zone"></a>Az eszköz időzónája

Az eszköz környezet az eszköz azonnal fizikai körülötte, amennyiben a fizikai hozzáférés és/vagy a "helyi hálózat" társ-társ digitális az eszközre is megvalósítható. A "helyi hálózat" adatforrásmérete egy hálózatot, amely önálló, és a – szigetelt, de potenciálisan Áthidalt való – a nyilvános interneten, és bármely rövid hatótávolságú vezeték nélküli választógomb technológia, amely lehetővé teszi a társ-társ kommunikációs eszközök tartalmazza. Ugyanúgy *nem* közé tartozik minden olyan hálózati virtualizációs technológia a helyi hálózaton finomabb létrehozása, és nem is tartalmazza nyilvános operátor hálózatok, amelyek bármely nyilvános hálózat címterületének kommunikálhassanak, ha a két eszköz megkövetelése Adja meg a társ-társ kommunikációs kapcsolat voltak.

### <a name="the-field-gateway-zone"></a>A mező átjáró zóna

Helyszíni átjáró az eszköz és berendezés vagy néhány általános célú kiszolgáló számítógép szoftver, amely kommunikációs engedélyező, és esetleg eszköz verziókezelő rendszer-és eszköz adatfeldolgozási hub funkcionál. A mező átjáró zónát tartalmaz, a helyszíni átjáró magát, és minden hozzá kapcsolt eszközöket. A neve is mutatja, ahogy helyszíni átjárók létesítményekben kívül dedikált adatfeldolgozási működésre, általában kötött helye, fizikai behatolás potenciálisan vonatkozik, és működési redundancia korlátozott. Tegyük fel, győződjön meg arról, hogy a helyszíni átjárók gyakran egy dolog, minden egyik touch és a feladatait van, hogy közben szabotálására.

Helyszíni átjáró különbözik puszta forgalom útválasztó, hogy rendelkezik mint a hozzáférés-kezelés az aktív szerepet, és információáramlás, ami azt jelenti, egy alkalmazás rendelkező entitást és a hálózati kapcsolat vagy a Terminálszolgáltatások munkamenet. Egy NAT-eszköz vagy tűzfal, ellentétben nem tesz eleget helyszíni átjárók, mivel azok nincsenek explicit kapcsolat vagy a munkamenet terminálok, de a inkább egy útvonal (vagy blokk) kapcsolatok vagy a munkamenetek keresztül őket. A helyszíni átjáró van két különböző surface területre. Egy arcokat, az eszközöket, amelyek kapcsolódnak, és a belső, a zóna jelöli, és a másik az összes külső felek arcokat, és a peremhálózaton, a zóna.

### <a name="the-cloud-gateway-zone"></a>A felhő átjáró zóna

Átjáró egy rendszer, amely lehetővé teszi az eszközök vagy helyszíni átjárók számos különböző helyekről és távoli kommunikációs között nyilvános hálózati helyet, általában egy felhőalapú vezérlőt, és az adatok elemzési rendszer, az ilyen rendszerek összevonási felé. Bizonyos esetekben egy felhőátjáróhoz előfordulhat, hogy azonnal biztosítja a hozzáférést a speciális célú eszközök, például táblagépek vagy telefonok terminálok. Az itt tárgyalt környezetben a "felhő" hivatott tekintse meg a dedikált adatfeldolgozási rendszer, amely nincs kötve a csatlakoztatott eszközökkel vagy helyszíni átjárók ugyanazon a helyen. Is egy Felhőbeli zónában működési mértékek célzott fizikai hozzáférés megakadályozása, és nem feltétlenül közzétéve "nyilvános felhőről" infrastruktúrában.  

A felhőátjáró potenciálisan leképezni egy hálózati virtualizációs területre, megvédheti a felhőátjáró és az összes a csatlakoztatott eszközökkel vagy a helyszíni átjárók más hálózati forgalomtól. A felhőátjáró maga nem egy eszköz verziókezelő rendszer vagy egy feldolgozási vagy a tárolási létesítmény az eszközadatok; Ezekben a létesítményekben kommunikáljanak a felhőátjárónak. A felhő átjáró felhőalapú maga az átjáró együtt minden helyszíni átjárók és közvetve vagy közvetlenül csatlakoztatott eszköz tartozik. Az edge, a zóna egy egy különböző támadási, ahol az összes külső felek keresztül kommunikálnak.

### <a name="the-services-zone"></a>A szolgáltatások zóna

"Szolgáltatás" definiálva van ebben a környezetben, bármely szoftverfrissítési összetevő vagy a modul, amely az eszközök egy mező vagy felhő-átjárón keresztül adatok gyűjtése és elemzése, valamint a parancs és vezérlés vele. Szolgáltatások közvetítők. Azok felé az átjárók és más alrendszerek identitás alatt reagálhat, tárolására és adatok elemzése, önállóan parancsokat kiadni data insights vagy ütemezés alapján eszközök és tegye elérhetővé az adatokat és szabályozhatja a funkciókat a jogosult végfelhasználók számára.

### <a name="information-devices-versus-special-purpose-devices"></a>Eszközök információk, és a speciális célú eszközök

Számítógépek, telefonokon és táblagépeken olyan eszközök elsősorban interaktív információk. Telefonokon és táblagépeken körül akkumulátor-élettartamát maximalizálása explicit módon vannak optimalizálva. Lehetőleg kikapcsolása részlegesen nem azonnal személy való interakció során, vagy ha nem biztosító szolgáltatások, például lejátszás, zene, vagy egy adott helyre irányítsa a tulajdonostól. A rendszerek szempontjából ezek az információk technológiai eszközök főleg vannak átjáróként proxyk személyek felé. Műveletek és a "személyek érzékelők" bemeneti gyűjtése javasolásával "személyek indítószerkezeteket".

Speciális célú eszközök, az egyszerű hőmérséklet érzékelőktől az összetevőit őket, több ezer összetett gyártósorokon eltérőek. Ezek az eszközök sokkal hatóköre a cél, és akkor is, ha a felhasználói felület egyes biztosítanak, nagymértékben hatóköre való együttműködésre vagy integráljuk a való világban az eszközök. Azok mérjük és jelentés környezeti körülmények között, kapcsolja szelepet, servos szabályozhatja, hang-riasztások, lámpa váltson, és számos egyéb feladatokat. Ezek segítenek működnek, amelynek egy információk eszköz túl általános, túl költséges, túl nagy vagy túl törékennyé teszi. A konkrét célja azonnal előírja a műszaki tervezést, gyártási és ütemezett élettartama műveletet is rendelkezésre álló pénzügyi költségvetési. Ez a két legfontosabb tényező kombinációja korlátozza az elérhető működési energia költségvetési, fizikai jelenlét, és így rendelkezésre álló tárhelyet, számítási és biztonsági képességeket.

Ha valami "goes nem megfelelő" automatizált és a távoli vezérelhető eszközök, például fizikai hibák vagy ellenőrzési logika hibák willful illetéktelen behatolás és kezelését. Az éles sok meg kell semmisíteni, az épületek looted vagy írása lehet és személyek lehet, hogy sérült, vagy akár die. Ez a károk valaki maxing egy ellopott hitelkártya korlát, mint egy teljesen eltérő osztályt. Eszközök, amelyek áthelyezése dolgokat és érzékelőktől kapott adatok, amelyek végül parancsok, amelyek miatt az alábbiakkal helyezi át, a biztonsági sávban kell lennie, mint bármely e-kereskedelmi vagy banki adathierarchia.

### <a name="device-control-and-device-data-interactions"></a>Eszköz-ellenőrzési és eszköz-adatok interakciói

Csatlakoztatott speciális célú eszközök kapcsolati surface területeket, és interakció minták, amelyek mindegyike figyelembe kell venni egy keretrendszert nyújt a digitális, az adott eszközökre való hozzáférés biztonságossá tétele jelentős számú rendelkezik. A használt kifejezés a "digitális hozzáférés" Itt megkülönböztetni a közvetlen interakció keresztül végrehajtott műveleteket a hozzáférés-biztonságot amennyiben a fizikai hozzáférés-vezérlés használatával. Ha például üzembe helyezése az eszköz zár szoba ajtón. Fizikai hozzáférés nem elutasítja a hardver- és használatával, miközben intézkedéseket is vezető rendszer zavaró tényező való fizikai hozzáférés megakadályozása.

A kapcsolati minták megismerésével meg "eszköz vezérlési" és "eszköz" azonos szintű a figyelmet threat modellezés közben. Minden olyan információt, amely bármely fél által biztosított az eszközökre,. a cél módosítása, illetve annak viselkedését felé az állapot vagy a környezet állapotát befolyásoló az "Eszköz control" meghatározhassa. "Eszköz adatok" meghatározhassa minden olyan információt, az eszköz bocsát ki a másik félre állapotában és a megfigyelt állapotát a környezetében.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Az Azure IoT-referenciaarchitektúra modellezése threat végrehajtása

Microsoft threat modeling az Azure IoT ehhez korábban leírt keretében használja. A következő szakaszban az Azure IoT-Referenciaarchitektúra konkrét példa használatával bemutatják, hogyan kell foglalkoznia a fenyegetések modellezése IoT és hogyan oldja meg az azonosított fenyegetések. Ebben a példában négy fő területtel fókusz azonosítja:

* Eszközök és az adatforrásokat,
* Szállítási adatok
* Eszköz és esemény feldolgozása, és
* Bemutató

![Az Azure IoT modellezési fenyegetés](media/iot-security-architecture/iot-security-architecture-fig2.png)

A következő ábra a Microsoft Threat Modeling Tool által használt adatfolyam-Diagram modell segítségével a Microsoft IoT-architektúra leegyszerűsített nézete nyújtja:

![Fenyegetések modellezése az Azure IoT MS Threat Modeling Tool használatával](media/iot-security-architecture/iot-security-architecture-fig3.png)

Fontos megjegyezni, hogy az architektúra elkülöníti az eszköz és átjáró funkciókat. Ez a megközelítés lehetővé teszi, hogy a felhasználó átjáró eszközök, amelyek biztonságosabb kihasználhatja: képesek kommunikálni a felhőátjáró biztonságos protokollok használatával, amihez általában nagyobb feldolgozási terhelés, hogy a natív eszköz -, például egy thermostat - sikerült Adja meg saját maga. Az Azure-szolgáltatások zóna feltételeztük, hogy az átjáró megjelenjen-e az Azure IoT Hub szolgáltatás.

### <a name="device-and-data-sourcesdata-transport"></a>Eszköz- és források/adatátvitel

Ebben a szakaszban a fenyegetések modellezése identitásalapú korábban leírt architektúra bemutatja, és hogyan cikksorozatnak rejlő fontos szempont áttekintést. Ebben a példában a fenyegetések modellezése alapvető elemeit összpontosít:

* Folyamatok (mind a vezérlő és a külső elemek alatt)
* (Más néven adatfolyamok) kommunikáció
* Storage (más néven adattárak)

#### <a name="processes"></a>Folyamatok

Az egyes kategóriák leírt az Azure IoT-architektúra, az ebben a példában a különböző szakaszaiban található adatok és információk között számos különböző fenyegetések mérséklésére megpróbálja: folyamatok, a kommunikáció és a storage. Következő el a leggyakrabban a "folyamat" kategória, hogyan ezek a kártevők sikerült legjobb enyhíthető áttekintése után áttekintése:

**(S) hamisítást**: A támadó titkosítási kulcs adatai kinyerése előfordulhat, hogy egy eszközt, vagy a szoftver vagy hardver szintjén, és ezt követően a rendszer egy másik fizikai vagy virtuális eszköz identitása alatt az eszköz a megosztottkulcs-anyag foglalt a hozzáférés. Egy jó ábra, amely kapcsolhatja be minden TV és, hogy azok az eszközök népszerű prankster távvezérlés.

**Szolgáltatásmegtagadás (D)**: Egy eszköz nem alkalmas működik vagy történő kommunikációhoz választógomb gyakoriságot vagy cutting fenyegetéseknek zavarása jeleníthetők meg. Például egy felügyeleti kamera, amely a teljesítmény vagy a hálózati kapcsolat szándékosan kiejtése rendelkezett nem készíthető jelentés az adatokat, minden.

**Illetéktelen módosításának (T)**: A támadó előfordulhat, hogy részben vagy egészben cserélje le a szoftvert az eszközön futó potenciálisan lehetővé teszi a lecserélt szoftver kihasználhatja a valódi identitás, az eszköz a megosztottkulcs-anyag vagy a titkosítási funkciók anyagok legfontosabb rendelkezés voltak elérhetők, ha a a program tiltott. Például egy támadó kinyert megosztottkulcs-anyag elfogására és a le adatokat az eszközről, a kommunikáció elérési úttal, és cserélje le az ellopott megosztottkulcs-anyag adatokkal van hitelesítve hamis adatokat is használhatja.

**(I) információk felfedése**: Ha az eszköz úgy szoftver fut, úgy szoftver vélhetően kockáztató adatok illetéktelen felek számára. Például egy támadó kinyert megosztottkulcs-anyag behelyezése maga az eszköz és a vezérlő nebo Pole átjáró vagy siphon ki információkat az átjáró közötti kommunikációs útvonal, előfordulhat, hogy használhatja.

**(E) jogok kiterjesztése**: Egy eszköz, amely adott függvényt is mindenképpen foglalkozhat egyéb teendőivel. Például, hogy van programozva felénél megnyitásához szelepet is kell címeket ágyaznak, egészen a megnyitásához.

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Eszköz |S |Identitás hozzárendelése az eszköz és az eszköz hitelesítéséhez |Eszköz vagy az eszköz részét cserélje le egy másik eszköz. Honnan tudhatja meg a megfelelő eszközökre kiindulásként? |Az eszköz, a Transport Layer Security (TLS) vagy az IPSec protokollt használó hitelesítéséhez. Infrastruktúra támogatnia kell a előre megosztott kulcs (PSK) használatával, amely nem tudja kezelni a teljes aszimmetrikus titkosítási ezeken az eszközökön. Mindemellett az Azure AD [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Tamperproof mechanizmusok az eszközön, például alkalmazni így nehéz a kulcsok és más kriptográfiai elemeit kicsomagolja az eszközön lévő nem lehetséges. |A kockázat esetén, ha valaki van illetéktelen módosítását az eszköz (fizikai zavaró tényező). Hogy biztosan, az eszköz nem módosították. |A leghatékonyabb megoldás egy platformmegbízhatósági modul (TPM) képesség, amely lehetővé teszi a speciális a lapkával áramkört, amelyből a kulcsok nem lehet olvasni, de csak akkor használható, amelyek a kulcsot használják, de soha nem adjuk ki a kulcsot a titkosítási műveletek kulcsok tárolásához. Az eszköz titkosítása memória. Az eszköz kulcskezelés. A kód aláírása. | |
|| E |Az eszköz hozzáférés-vezérlés kellene. Engedélyezési séma. |Az eszköz lehetővé teszi, hogy az egyes műveleteket kell elvégezni a külső forrásnak, vagy akár feltört érzékelők parancsait alapján, ha lehetővé teszi a támadás műveletek végrehajtása egyéb módon nem érhető el. |Az eszköz engedélyezési séma kellene | |
| Helyszíni átjáró |S |A helyszíni átjáró felhőátjárónak hitelesítése (például a tanúsítvány alapú, PSK, vagy jogcím alapján.) |Ha valaki meghamisítását is a helyszíni átjáró, majd azt is jelenthet magát, bármilyen eszközön. |A TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Ugyanazokat az eszközök általában – tárolási és a tanúkiszolgáló fontos problémákkal legjobb esetben van TPM használatát. Az IPSec támogatja a vezeték nélküli érzékelő hálózatok (WSN) bővítmény 6LowPAN. |
|| TRID |A helyszíni átjáró (TPM?) illetéktelen módosítással szembeni védelme |Információfelfedés és az adatok illetéktelen módosításának hamisításra épülő támadásoknak, amely a felhasználót: a felhőbeli átjáró terhelése, a helyszíni átjáró beszél okozhat |Memória titkosítás, a TPM a, hitelesítés. | |
|| E |Helyszíni átjáró hozzáférés-vezérlési mechanizmus | | | |

Íme néhány példa a fenyegetések ebbe a kategóriába:

**Hamisítást**: A támadó titkosítási kulcs adatai kinyerése előfordulhat, hogy egy eszközt, vagy a szoftver vagy hardver szintjén, és ezt követően a rendszer egy másik fizikai vagy virtuális eszköz identitása alatt az eszköz a megosztottkulcs-anyag foglalt a hozzáférés.

**Szolgáltatásmegtagadás**: Egy eszköz nem alkalmas működik vagy történő kommunikációhoz választógomb gyakoriságot vagy cutting fenyegetéseknek zavarása jeleníthetők meg. Például egy felügyeleti kamera, amely a teljesítmény vagy a hálózati kapcsolat szándékosan kiejtése rendelkezett nem készíthető jelentés az adatokat, minden.

**Illetéktelen**: A támadó előfordulhat, hogy részben vagy egészben cserélje le a szoftvert az eszközön futó potenciálisan lehetővé teszi a lecserélt szoftver kihasználhatja a valódi identitás, az eszköz a megosztottkulcs-anyag vagy a titkosítási funkciók anyagok legfontosabb rendelkezés voltak elérhetők, ha a a program tiltott.

**Illetéktelen**: Egy ilyen előcsarnokbeli fényképe sikerült célja egy felügyeleti kamera, amely egy üres előcsarnokbeli látható spektrum képe látható. Füst vagy fire érzékelő sikerült készítőnek tekintene valaki rendelkezés egy világosabb, annak alapján. Mindkét esetben az eszköz lehet technikailag teljesen megbízható-e a rendszer felé, de azt jelenti, hogy úgy információkat.

**Illetéktelen**: A támadó kinyert megosztottkulcs-anyag elfogására és a le adatokat az eszközről, a kommunikáció elérési úttal, és cserélje le az ellopott megosztottkulcs-anyag adatokkal van hitelesítve hamis adatokat is használhatja.

**Illetéktelen**: A támadó előfordulhat, hogy részben vagy teljesen cserélje le a szoftvert az eszközön futó potenciálisan lehetővé teszi a valódi identitás, az eszköz használhatja, ha a megosztottkulcs-anyag vagy a titkosítási funkciók anyagok legfontosabb rendelkezés voltak elérhetők a lecserélt szoftver a tiltott programba.

**Információk felfedése**: Ha az eszköz úgy szoftver fut, úgy szoftver vélhetően kockáztató adatok illetéktelen felek számára.

**Információk felfedése**: A támadó kinyert megosztottkulcs-anyag behelyezése maga az eszköz és a vezérlő nebo Pole átjáró vagy siphon ki információkat az átjáró közötti kommunikációs útvonal, előfordulhat, hogy használhatja.

**Szolgáltatásmegtagadás**: Az eszköz ki van kapcsolva vagy módba kapcsolni, ahol a tájékoztató nem lehetséges (azaz az ipari gépekről számos szándékos).

**Illetéktelen**: Az eszköz újra tudja konfigurálni, hogy ismeretlen a rendszerbe (kívül ismert hitelesítési paraméterek) állapotban működjön, és így adja meg hibásan lesznek értelmezve adatok

**Jogok kiterjesztése**: Egy eszköz, amely adott függvényt is mindenképpen foglalkozhat egyéb teendőivel. Például, hogy van programozva felénél megnyitásához szelepet is kell címeket ágyaznak, egészen a megnyitásához.

**Szolgáltatásmegtagadás**: Az eszköz kapcsolható egy állapotba kerül, ahol a tájékoztató nem lehetséges.

**Illetéktelen**: Az eszköz újra tudja konfigurálni, hogy ismeretlen a rendszerbe (kívül ismert hitelesítési paraméterek) állapotban működjön, és így adja meg az adatokat, amely hibásan lesznek értelmezve.

**Címhamisítást vagy illetéktelen módosítása/Letagadhatóság**: Ha nem védett (amely a ritkán fogyasztói távvezérlés esetében), a támadó névtelenül egy eszköz állapotáról is módosíthatja. Egy jó ábra, amely kapcsolhatja be minden TV és, hogy azok az eszközök népszerű prankster távvezérlés.

#### <a name="communication"></a>Kommunikáció

Fenyegetések eszközöket és a helyszíni átjárók, valamint eszköz és a felhő-átjáró közötti kommunikációs útvonal körül. Az alábbi táblázat az eszköz/VPN nyílt sockets körül útmutatást rendelkezik:

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Device IoT Hub |TID |(D) TLS (PSK/RSA) a forgalom titkosításához |Lehallgatást, vagy zavaró, az eszköz és az átjáró közötti kommunikáció |Biztonság, a protokoll szintjén. Egyéni protokollok, az kell, hogyan lehet megvédeni őket. A legtöbb esetben a kommunikáció történik az eszközről az IoT hub (eszköz kezdeményezi a kapcsolatot). |
| Eszköz eszköz |TID |(D) TLS (PSK/RSA) a forgalom titkosításához. |Az eszközök között átvitt adatok olvasása. Az adatok illetéktelen módosítása. Az eszközt az új kapcsolatok terhelve. |Biztonsági a protokoll szintjén (mqtt-ről vagy AMQP vagy HTTP/CoAP. Egyéni protokollok, az kell, hogyan lehet megvédeni őket. A megoldás a szolgáltatásmegtagadási fenyegetések, hogy a felhő nebo Pole átjárón keresztül eszközök társviszonyt, azok csak act-ügyfélként a hálózat felé. A társviszony-létesítést követően kellene már felügyelt, az átjáró által a partnerek közötti közvetlen kapcsolat eredményezhet |
| Külső entitás eszköz |TID |A külső entitás az eszköz erős párosítás |Az eszköz kapcsolat lehallgatást. Zavarja a folyamatot a kommunikációt az eszközzel |Biztonságosan párosítást a külső entitás az eszköz NFC/Bluetooth LE. A műveleti panelen az eszköz (fizikai) szabályozása |
| Helyszíni átjáró felhőalapú átjáró |TID |TLS (PSK/RSA) a forgalom titkosításához. |Lehallgatást, vagy zavaró, az eszköz és az átjáró közötti kommunikáció |Biztonsági szint (mqtt-ről vagy AMQP vagy HTTP/CoAP) protokollt. Egyéni protokollok, az kell, hogyan lehet megvédeni őket. |
| Eszköz-felhő átjáró |TID |TLS (PSK/RSA) a forgalom titkosításához. |Lehallgatást, vagy zavaró, az eszköz és az átjáró közötti kommunikáció |Biztonsági szint (mqtt-ről vagy AMQP vagy HTTP/CoAP) protokollt. Egyéni protokollok, az kell, hogyan lehet megvédeni őket. |

Íme néhány példa a fenyegetések ebbe a kategóriába:

**Szolgáltatásmegtagadás**: Korlátozott eszközök esetén általában a szolgáltatásmegtagadási fenyegetések azokat aktívan figyelni a bejövő kapcsolatok vagy kéretlen datagramok hálózaton, mert egy támadó is nyissa meg a sok kapcsolattal párhuzamosan, és nem őket szolgáltatás vagy szolgáltatás azok lassan, vagy lehet az eszköz túlterhelve, amelyeknek kéretlen forgalommal. Mindkét esetben az eszköz ténylegesen renderelhető működésképtelenné válik a hálózaton.

**IP-hamisítás, illetéktelen**: Korlátozott eszközök és a speciális célú eszközök gyakran rendelkeznek egy összes biztonsági létesítményekben például jelszó vagy PIN-kód védelmet, vagy ezek teljes mértékben támaszkodik a hálózaton, ami azt jelenti, azok információkhoz való hozzáférés biztosítása, ha egy eszköz ugyanazon a hálózaton, és a hálózati megbízó gyakran csak védett megosztott kulcs. Amely azt jelenti, hogy nyilvánosságra hozza a közös titkos kulcsot, eszköz és a hálózathoz, amikor az eszköz vezérléséhez, vagy tekintse át az adatokat az eszközről rendelkezésre.  

**Hamisítást**: egy támadó előfordulhat, hogy intercept vagy részlegesen bírálja felül a közvetítés és a kezdeményező (közbeékelődéses támadás) agresszívebb

**Illetéktelen**: egy támadó előfordulhat, hogy intercept vagy részlegesen bírálja felül a közvetítést, és elküldi false (hamis) 

**Információk felfedése:** egy támadó előfordulhat, hogy kihallgassa a közvetítés és engedély nélkül megszerzése **szolgáltatásmegtagadásos:** egy támadó előfordulhat, hogy a szórási jel dzsem és információk terjesztési megtagadása

#### <a name="storage"></a>Storage

Minden eszköz- és mezőnevek átjáró valamilyen storage (az adatok, az operációs rendszer (OS) képtárolás Queuing ideiglenes) rendelkezik.

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Megvalósítása** |
| --- | --- | --- | --- | --- |
| Eszköz tárhelye |TRID |Tártitkosítás, a naplók aláírása |A storage (PII adatokat), telemetriai adatok illetéktelen módosítása adatok olvasása. Illetéktelenül várakoztathatók vagy parancs control-adatok a gyorsítótárba. Konfiguráció vagy a belső vezérlőprogram frissítési csomagokat illetéktelenül során a gyorsítótárba, vagy helyileg várólistára vezethet feltörését az operációs rendszer és/vagy a rendszer összetevői |Titkosítás, üzenethitelesítő kódot (MAC), vagy digitális aláírással. Ha lehetséges, erős hozzáférés-vezérlő erőforrásokhoz való hozzáférés révén vezérlő listák (ACL) vagy az engedélyekkel. |
| Eszköz operációs rendszere kép |TRID | |Az operációs rendszer illetéktelenül / cseréje az operációs rendszer összetevői |Csak olvasható operációsrendszer-partíció aláírt operációsrendszer-lemezkép, titkosítás |
| Helyszíni átjáró storage (az adatok kezelés) |TRID |Tártitkosítás, a naplók aláírása |A storage (PII adatokat), telemetriai adatokat, illetéktelenül adatok olvasása illetéktelenül várakoztathatók vagy parancs control-adatok a gyorsítótárba. Konfiguráció vagy a belső vezérlőprogram frissítési csomagokat (eszközökön vagy a helyszíni átjáró felé irányuló) illetéktelenül során a gyorsítótárba, vagy helyileg várólistára vezethet feltörését az operációs rendszer és/vagy a rendszer összetevői |BitLocker |
| A mező átjáró operációsrendszer-lemezkép |TRID | |Az operációs rendszer illetéktelenül / cseréje az operációs rendszer összetevői |Csak olvasható operációsrendszer-partíció aláírt operációsrendszer-lemezkép, titkosítás |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Eszköz és esemény feldolgozása/felhőbeli átjáró zóna

A felhőátjáró rendszer, amely lehetővé teszi, hogy az eszközök vagy helyszíni átjárók számos különböző helyekről és távoli kommunikációs nyilvános hálózati helyet, általában egy felhőalapú vezérlőt, és az adatok elemzési rendszer, az ilyen rendszerek összevonási felé között. Bizonyos esetekben egy felhőátjáróhoz előfordulhat, hogy azonnal biztosítja a hozzáférést a speciális célú eszközök, például táblagépek vagy telefonok terminálok. A tárgyalt környezetben itt "felhő" célja, hogy egy dedikált adatfeldolgozási rendszer, amely nincs kötve a csatlakoztatott eszközökkel vagy helyszíni átjárók ugyanazon a helyen, és ahol működési mértékek célzott fizikai hozzáférés megakadályozása érdekében tekintse meg, de nem feltétlenül a egy " nyilvános felhő"infrastruktúrát. A felhőátjáró potenciálisan leképezni egy hálózati virtualizációs területre, megvédheti a felhőátjáró és az összes a csatlakoztatott eszközökkel vagy a helyszíni átjárók más hálózati forgalomtól. A felhőátjáró maga nem egy eszköz verziókezelő rendszer vagy egy feldolgozási vagy a tárolási létesítmény az eszközadatok; Ezekben a létesítményekben kommunikáljanak a felhőátjárónak. A felhő átjáró felhőalapú maga az átjáró együtt minden helyszíni átjárók és közvetve vagy közvetlenül csatlakoztatott eszköz tartozik.

Átjáró csak a szoftvert, szolgáltatásként való elérhetővé tett végpontokon, amelyhez a helyszíni átjáró és az eszközök csatlakozni egyéni beépített részét. Ezért úgy kell megtervezni a biztonság szem. Hajtsa végre a [SDL](https://www.microsoft.com/sdl) feldolgozása tervezéséről és kiépítéséről ezt a szolgáltatást.

#### <a name="services-zone"></a>Szolgáltatások zóna

A verziókezelő rendszer (vagy a tartományvezérlő) egy szoftveres megoldás, amely a felületek, egy eszközt, vagy egy helyszíni átjáró vagy a felhőátjáró ellenőrizhető egy vagy több eszközt, és/vagy az összegyűjtése és/vagy tárolása és/vagy eszköz bemutató, elemzéséhez, vagy További ellenőrzési célból. Vezérlő rendszereket, amelyek azonnal elősegítik a felhasználókkal interakciót vitafórumban hatókörében csak entitásokat. A kivételek közé tartoznak az eszközökön, például egy kapcsoló, amely lehetővé teszi, hogy egy személy kapcsolja ki az eszközt, vagy egyéb tulajdonságok módosításához, és amelynek nincs digitálisan elérhető funkcionális megfelelője a köztes fizikai vezérlő felületek.

Köztes fizikai vezérlő felületek megegyeznek, ha a fizikai lehessen vezérelni a surface, a függvény szabályozó logikát korlátozza, hogy egy azzal egyenértékű függvényt is kezdeményezhető, távolról, vagy a bemeneti nem felel meg a távoli bemeneti felderítésfeldolgozó – például adatbázisoktól vezérlő felületek elméleti szinten is csatlakozik egy helyi verziókezelő rendszer, amely megegyezik a bármely más távvezérlés rendszer, amely az eszköz párhuzamos csatlakoztatható alapul szolgáló funkcióval. A felhő-számítástechnika, lehet olvassa el a legnépszerűbb fenyegetések [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) lapot.

## <a name="additional-resources"></a>További források

További információkért tekintse át a következő cikkeket:

* [SDL Fenyegetésmodellezési eszköz](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [A Microsoft Azure IoT-referenciaarchitektúra](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
