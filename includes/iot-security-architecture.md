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
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034546"
---
A rendszer tervezésekor fontos megérteni a rendszert fenyegető lehetséges veszélyeket, és ennek megfelelően hozzáadni a megfelelő védelmet, mivel a rendszert tervezték és tervezték. Fontos, hogy a terméket a kezdetektől fogva a biztonságot szem előtt tartva tervezze meg, mivel annak megértése, hogy a támadó hogyan képes feltörésre egy rendszerrel, segít abban, hogy a kezdetektől fogva megfelelő megoldások legyenek érvényben.

## <a name="security-starts-with-a-threat-model"></a>A biztonság fenyegetésmodellel kezdődik

A Microsoft régóta használ fenyegetésmodelleket a termékeihez, és nyilvánosan elérhetővé tette a vállalat fenyegetésmodellezési folyamatát. A vállalati tapasztalat azt mutatja, hogy a modellezés nem várt előnyöket túl azonnali megértése, hogy milyen fenyegetések a leginkább érintő. Például, ez is létrehoz egy utat a nyílt vita másokkal kívül a fejlesztőcsapat, ami ahhoz vezethet, hogy az új ötletek és fejlesztések a termék.

A fenyegetésmodellezés célja annak megértése, hogy a támadó hogyan képes feltörésre egy rendszer, és győződjön meg arról, hogy megfelelő megoldások vannak érvényben. A fenyegetésmodellezés arra kényszeríti a tervezőcsapatot, hogy fontolja meg a megoldásokat, mivel a rendszer a rendszer telepítése helyett tervezésű. Ez a tény rendkívül fontos, mert utólagos biztonsági védelmet, hogy számtalan eszköz a területen kivitelezhetetlen, hibahajlamos, és elhagyja az ügyfelek veszélyben.

Számos fejlesztőcsapat kiváló munkát végez az ügyfelek számára előnyös rendszer funkcionális követelményeinek rögzítésében. Azonban a nem nyilvánvaló módszerek azonosítása, hogy valaki esetleg visszaélhet a rendszerrel, nagyobb kihívást jelent. A fenyegetésmodellezés segíthet a fejlesztőcsapatoknak megérteni, hogy mit és miért tehet a támadó. A fenyegetésmodellezés egy strukturált folyamat, amely vitát hoz létre a rendszer biztonsági tervezési döntéseiről, valamint a biztonságra hatást gyakorló terv módosításáról. Míg a fenyegetés modell egyszerűen egy dokumentum, ez a dokumentáció is ideális módja annak, hogy biztosítsa a tudás folytonosságát, megtartása a tanulságokat, és segít az új csapat a fedélzeten gyorsan. Végül a fenyegetésmodellezés eredménye, hogy lehetővé tegye a biztonság egyéb szempontjainak, például azt, hogy milyen biztonsági kötelezettségvállalásokat kíván nyújtani ügyfeleinek. Ezek a kötelezettségvállalások a fenyegetésmodellezéssel együtt tájékoztatják és meghajtón tesztelik az IEt-megoldásokat.

### <a name="when-to-do-threat-modeling"></a>Mikor kell csinálni fenyegetés modellezés

A fenyegetésmodellezés a legnagyobb értéket [nyújtja,](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) ha beépíti a tervezési fázisba. Tervezésekor a legnagyobb rugalmassággal teheti meg a fenyegetések kiküszöbölését. A fenyegetések szándékos kiküszöbölése a kívánt eredmény. Sokkal könnyebb, mint a mérséklések hozzáadása, tesztelése, valamint annak biztosítása, hogy naprakészek maradjanak, sőt, az ilyen kiküszöbölés nem mindig lehetséges. Egyre nehezebb kiküszöbölni a fenyegetéseket, mint egy termék egyre érettebb, és viszont végül több munkát igényel, és sokkal nehezebb kompromisszumokat, mint a fenyegetés modellezés korai szakaszában a fejlesztés.

### <a name="what-to-consider-for-threat-modeling"></a>Mit kell figyelembe venni a fenyegetés modellezése

Meg kell nézni a megoldás egészét, és a következő területekre kell összpontosítania:

* A biztonsági és adatvédelmi funkciók
* Azok a funkciók, amelyek meghibásodása biztonsági
* A megbízhatósági határt érintő funkciók

### <a name="who-performs-threat-modeling"></a>Ki hajt végre fenyegetésmodellezést?

Fenyegetés modellezés egy olyan folyamat, mint bármely más. Célszerű a fenyegetésmodell-dokumentumot a megoldás bármely más összetevőjéhez hasonlóan kezelni és érvényesíteni. Számos fejlesztőcsapat kiváló munkát végez az ügyfelek számára előnyös rendszer funkcionális követelményeinek rögzítésében. Azonban a nem nyilvánvaló módszerek azonosítása, hogy valaki esetleg visszaélhet a rendszerrel, nagyobb kihívást jelent. A fenyegetésmodellezés segíthet a fejlesztőcsapatoknak megérteni, hogy mit és miért tehet a támadó.

### <a name="how-to-perform-threat-modeling"></a>Fenyegetésmodellezés végrehajtása

A fenyegetésmodellezési folyamat négy lépésből áll; a lépések a következők:

* Modellezi az alkalmazást
* Fenyegetések számbavétele
* Fenyegetések enyhítése
* A kockázatcsökkentés ellenőrzése

#### <a name="the-process-steps"></a>A folyamat lépései

Három ökölszabály, amelyet szem előtt kell tartani a fenyegetésmodell létrehozásakor:

1. Hozzon létre egy diagramot a referenciaarchitektúrából.

2. Kezdjük a szélesség-első. Áttekintést kaphat, és megismerheti a rendszer egészét, mielőtt mélybúvárkodás. Ez a megközelítés segít biztosítani, hogy a megfelelő helyeken merüljön mélyre.

3. Hajt a folyamat, ne hagyja, hogy a folyamat hajt meg. Ha találsz egy problémát a modellezési fázisban, és szeretné felfedezni, megy ez! Nem érzem, meg kell, hogy kövesse ezeket a lépéseket szolgaian.

#### <a name="threats"></a>Fenyegetések

A fenyegetésmodell négy fő eleme a következő:

* Folyamatok, például webszolgáltatások, Win32-szolgáltatások és *nix démonok. Egyes összetett entitások (például helyszíni átjárók és érzékelők) lehet absztrakt folyamatként, ha egy technikai részletezés ezeken a területeken nem lehetséges.

* Adattárak (bárhol tárolt adatok, például konfigurációs fájl vagy adatbázis)

* Adatfolyam (ahol az adatok az alkalmazás többi eleme között mozognak)

* Külső entitások (bármi, ami kölcsönhatásba lép a rendszerrel, de nem áll az alkalmazás ellenőrzése alatt, például a felhasználók és a műholdas hírcsatornák)

Az építészeti diagram minden eleme különböző fenyegetéseknek van kitéve; ez cikk a LÉP mnemonic. Olvassa el [a Fenyegetés modellezése újra, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) többet megtudni a STRIDE elemeket.

Az alkalmazási diagram különböző elemei bizonyos STRIDE fenyegetéseknek vannak kitéve:

* A folyamatok a STRIDE függvény hatálya alá tartoznak
* Az adatáramlások a TID hatálya alá tartoznak
* Az adattárak a TID, és néha az R hatálya alá tartoznak, amikor az adattárak naplófájlok.
* A külső szervezetekre az SRD

## <a name="security-in-iot"></a>Biztonság az IoT-ben

A csatlakoztatott különleges célú eszközök jelentős számú lehetséges interakciós felülettel és interakciós mintával rendelkeznek, amelyek mindegyikét úgy kell tekinteni, hogy keretet biztosítanak az ilyen eszközökhöz való digitális hozzáférés biztosításához. A "digitális hozzáférés" kifejezést itt arra használják, hogy megkülönböztessék azokat a műveleteket, amelyeket közvetlen eszközkölcsönhatás útján hajtanak végre, amennyiben a hozzáférés biztonságát fizikai hozzáférés-vezérlés sel biztosítják. Például, üzembe a készüléket egy szobába egy zár az ajtón. Bár a fizikai hozzáférés nem tagadható meg a szoftverek és hardverek használatával, intézkedéseket lehet tenni annak megakadályozására, hogy a fizikai hozzáférés a rendszer interferenciájához vezessen.

Az interakciós minták feltárása során tekintse meg az "eszközvezérlést" és az "eszközadatokat" ugyanolyan szintű figyelmet. Az "eszközvezérlés" bármely olyan információnak minősíthető, amelyet bármely fél az eszköznek átad azzal a céllal, hogy megváltoztassa vagy befolyásolja annak viselkedését az állapota vagy a környezet állapota felé. Az "eszközadatok" bármely olyan információnak minősíthetők, amelyet az eszköz bármely más fél számára bocsát ki annak állapotáról és környezetének megfigyelt állapotáról.

A biztonsági ajánlott eljárások optimalizálása érdekében ajánlott, hogy egy tipikus IoT-architektúra van osztva több összetevő/zónák részeként a fenyegetés modellezési gyakorlat. Ezek a zónák teljes mértékben le vannak írva ebben a szakaszban, és a következőket tartalmazzák:

* Eszköz
* Helyszíni átjáró,
* Felhőátjárók, és
* Szolgáltatások.

A zónák széles körben levannak menterészél a megoldás menterészéhez; minden zónának gyakran saját adat- és hitelesítési és engedélyezési követelményei vannak. A zónák a károk elkülönítésére és az alacsony megbízhatósági zónák magasabb megbízhatósági zónákra gyakorolt hatásának korlátozására is használhatók.

Minden zónát egy megbízhatósági határ választ el egymástól, amely et a következő ábrán pontozott piros vonalként kell feljegyezni. Az adatok/információk egyik forrásból a másikba való átmenetét jelenti. Az átmenet során az adatok/információk hamisítás, illetéktelen beavatkozás, megtagadás, információhoz való hozzáférés, szolgáltatásmegtagadás és jogosultságilletéktelenítés (STRIDE) tárgyát képezhetik.

![IoT biztonsági zónák](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Az egyes határokon belül ábrázolt összetevők is alá vannak téve a STRIDE, amely lehetővé teszi a megoldás teljes 360 fenyegetésmodellezési nézetét. A következő szakaszok részletesen ismertetik az egyes összetevőket, valamint a konkrét biztonsági aggályokat és megoldásokat, amelyeket be kell ülnie.

A következő szakaszok az ezekben a zónákban általában megtalálható szabványos összetevőket ismertetik.

### <a name="the-device-zone"></a>A készülék zóna

Az eszközkörnyezet az eszköz körüli közvetlen fizikai tér, ahol a fizikai hozzáférés és/vagy a "helyi hálózat" egyenrangú digitális hozzáférése megvalósítható. A "helyi hálózat" olyan hálózatnak tekinthető, amely elkülönül és szigetelt – de potenciálisan áthidalva – a nyilvános internettől, és magában foglal minden olyan rövid hatótávolságú vezeték nélküli rádiótechnológiát, amely lehetővé teszi az eszközök peer-to-peer kommunikációját. *Nem* tartalmaz olyan hálózati virtualizációs technológiát, amely egy ilyen helyi hálózat illúzióját kelti, és nem foglalja magában azokat a nyilvános szolgáltatói hálózatokat sem, amelyek két eszközt igényelnek ahhoz, hogy a nyilvános hálózati térben kommunikáljanak, ha egyenrangú kommunikációs kapcsolatba lépnének.

### <a name="the-field-gateway-zone"></a>A helyszíni átjárózóna

A helyszíni átjáró olyan eszköz/berendezés vagy valamilyen általános célú kiszolgálói számítógépes szoftver, amely kommunikációs eszközként, és potenciálisan eszközvezérlő rendszerként és eszközadat-feldolgozó központként működik. A helyszíni átjárózóna magában foglalja magát a terepi átjárót és a hozzá csatlakoztatott összes eszközt. Ahogy a neve is mutatja, a helyszíni átjárók a kijelölt adatfeldolgozó létesítményeken kívül működnek, általában helyhez kötöttek, potenciálisan fizikai behatolásnak vannak kitéve, és korlátozott működési redundanciával rendelkeznek. Minden azt mondani, hogy a mező átjáró általában egy dolog lehet megérinteni, és szabotázs, miközben tudja, mi a funkciója.

A helyszíni átjáró abban különbözik a puszta forgalmi útválasztótól, hogy aktív szerepet játszott a hozzáférés és az információáramlás kezelésében, ami azt jelenti, hogy alkalmazás által megcímzett entitás és hálózati kapcsolat vagy munkamenet-terminál. Ezzel szemben a NAT-eszközök vagy tűzfalak nem minősülnek helyszíni átjárónak, mivel nem explicit kapcsolat- vagy munkamenet-terminálok, hanem rajtuk keresztül létesített útvonalkapcsolatok (vagy letiltások). A helyszíni átjáró két különálló felülettel rendelkezik. Az egyik a hozzá csatlakoztatott eszközök felé néz, és a zóna belsejét képviseli, a másik pedig az összes külső felet, és a zóna széle.

### <a name="the-cloud-gateway-zone"></a>A felhőátjáró zóna

A felhőátjáró olyan rendszer, amely lehetővé teszi a távoli kommunikációt az eszközökről és a helyszíni átjárókról több különböző helyről a nyilvános hálózati térben, jellemzően egy felhőalapú vezérlő és adatelemzési rendszer felé, amely ilyen rendszerek összevonása. Bizonyos esetekben a felhőátjáró azonnal megkönnyítheti a hozzáférést a speciális célú eszközökhöz terminálokról, például táblagépekről vagy telefonokról. Az itt tárgyalt környezetben a "felhő" egy dedikált adatfeldolgozó rendszerre vonatkozik, amely nem ugyanahhoz a helyhez van kötve, mint a csatolt eszközök vagy helyszíni átjárók. A felhőzónában is az operatív intézkedések megakadályozzák a célzott fizikai hozzáférést, és nem feltétlenül vannak kitéve a "nyilvános felhő" infrastruktúrának.  

A felhőátjáró t leképezhet egy hálózati virtualizációs átfedésbe, hogy szigetelje a felhőátjárót és annak összes csatlakoztatott eszközét vagy helyszíni átjáróját bármely más hálózati forgalomtól. Maga a felhőátjáró nem eszközvezérlő rendszer, illetve az eszközadatok feldolgozási vagy tárolási létesítménye; a létesítmény-interfészt a felhőátjáróval. A felhőátjáró-zóna magában foglalja a felhőátjárót, valamint az összes helyszíni átjárót és eszközt, amely közvetlenül vagy közvetve kapcsolódik hozzá. A zóna széle egy különálló felület, ahol minden külső fél kommunikál.

### <a name="the-services-zone"></a>A szolgáltatási zóna

A "szolgáltatás" ebben a környezetben, mint bármely szoftver összetevő vagy modul, amely kapcsolódik az eszközök egy mező- vagy felhő átjáró adatgyűjtés és elemzés, valamint a parancs és ellenőrzés. A szolgáltatások közvetítők. Identitásuk szerint lépnek fel az átjárók és más alrendszerek felé, adatokat tárolnak és elemeznek, önállóan adnak ki parancsokat az eszközöknek az adatok elemzése vagy ütemezése alapján, és az információ- és ellenőrzési képességeket elérhetővé teszik a jogosult végfelhasználók számára.

### <a name="information-devices-versus-special-purpose-devices"></a>Tájékoztató eszközök és különleges célú eszközök

A számítógépek, telefonok és táblagépek elsősorban interaktív információs eszközök. A telefonok és táblagépek kifejezetten az akkumulátor élettartamának maximalizálására vannak optimalizálva. Ők lehetőleg kapcsol távoli részleges mikor nem azonnal egymásra hat -val egy személy, vagy mikor nem felszerelt szolgáltatás szeret játék zene vagy vezető -uk tulajdonos -hoz egy meghatározott elhelyezés. A rendszerek szempontjából ezek az informatikai eszközök elsősorban az emberek felé való megjátszásként működnek. Ezek a "népi aktuátorok" arra utalnak, intézkedések és az "emberek érzékelők" gyűjtése bemenet.

A speciális célú eszközök, az egyszerű hőmérséklet-érzékelőktől a több ezer összetevőből álló komplex gyári gyártósorokig, különbözőek. Ezek az eszközök sokkal hatóköre a célra, és akkor is, ha valamilyen felhasználói felület, ezek nagyrészt hatálya alá tartozó összekötő vagy integrálható eszközök a fizikai világban. Mérik és jelentik a környezeti körülményeket, elfordítják a szelepeket, a szervók vezérlését, a hangjelzőket, a világítást kapcsolják ki, és sok más feladatot is elvégeznek. Segítenek olyan munkát végezni, amelyhez az információs eszköz túl általános, túl drága, túl nagy vagy túl törékeny. A konkrét cél azonnal meghatározza a technikai tervezés, valamint a rendelkezésre álló monetáris költségvetés a termelés és a tervezett élettartam működését. E két fő tényező kombinációja korlátozza a rendelkezésre álló működési energia-költségvetést, a fizikai lábnyomot, és így a rendelkezésre álló tárolási, számítási és biztonsági képességeket.

Ha valami "elromlik" az automatizált vagy távvezérelhető eszközökkel, például fizikai hibákkal vagy logikai hibák vezérlésével, szándékos jogosulatlan behatolással és manipulációval. A termőtelkek et el lehet pusztítani, az épületeket kifosztják vagy leégethetik, és emberek megsérülhetnek, vagy akár meghalhatnak is. Ez egy teljesen más fajta kár, mint valaki maxing ki egy lopott hitelkártya limit. A dolgokmozgatását okozó eszközök biztonsági sávjának, valamint az olyan érzékelőadatoknak, amelyek végül olyan parancsokat eredményeznek, amelyek a dolgok áthelyezését okozzák, magasabbnak kell lennie, mint bármely e-kereskedelmi vagy banki forgatókönyvben.

### <a name="device-control-and-device-data-interactions"></a>Eszközvezérlés és eszközadat-interakciók

A csatlakoztatott különleges célú eszközök jelentős számú lehetséges interakciós felülettel és interakciós mintával rendelkeznek, amelyek mindegyikét úgy kell tekinteni, hogy keretet biztosítanak az ilyen eszközökhöz való digitális hozzáférés biztosításához. A "digitális hozzáférés" kifejezést itt arra használják, hogy megkülönböztessék azokat a műveleteket, amelyeket közvetlen eszközkölcsönhatás útján hajtanak végre, amennyiben a hozzáférés biztonságát fizikai hozzáférés-vezérlés sel biztosítják. Például, üzembe a készüléket egy szobába egy zár az ajtón. Bár a fizikai hozzáférés nem tagadható meg a szoftverek és hardverek használatával, intézkedéseket lehet tenni annak megakadályozására, hogy a fizikai hozzáférés a rendszer interferenciájához vezessen.

Az interakciós minták feltárása során tekintse meg az "eszközvezérlés" és az "eszközadatok" című, azonos szintű figyelmet a fenyegetésmodellezés során. Az "eszközvezérlés" bármely olyan információnak minősíthető, amelyet bármely fél az eszköznek átad azzal a céllal, hogy megváltoztassa vagy befolyásolja annak viselkedését az állapota vagy a környezet állapota felé. Az "eszközadatok" bármely olyan információnak minősíthetők, amelyet az eszköz bármely más fél számára bocsát ki annak állapotáról és környezetének megfigyelt állapotáról.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Fenyegetésmodellezés végrehajtása az Azure IoT referenciaarchitektúrához

A Microsoft a korábban vázolt keretrendszert használja az Azure IoT fenyegetésmodellezéséhez. A következő szakasz az Azure IoT referenciaarchitektúra konkrét példáját használja az IoT-hez való fenyegetésmodellezés és az azonosított fenyegetések kezelésének szemléltetéséhez. Ez a példa négy fő fókuszterületet határoz meg:

* eszközök és adatforrások,
* Adatátvitel,
* Eszköz- és eseményfeldolgozás, valamint
* Megjelenítés

![Fenyegetésmodellezés az Azure IoT-hez](media/iot-security-architecture/iot-security-architecture-fig2.png)

Az alábbi ábra egyszerűsített képet nyújt a Microsoft IoT architektúrájáról a Microsoft Fenyegetésmodellező eszköz által használt adatfolyam-diagrammodell használatával:

![Fenyegetésmodellezés az Azure IoT-hoz az MS Threat Modeling Tool használatával](media/iot-security-architecture/iot-security-architecture-fig3.png)

Fontos megjegyezni, hogy az architektúra elválasztja az eszköz és az átjáró képességeit. Ez a megközelítés lehetővé teszi a felhasználó számára, hogy kihasználja a biztonságosabb átjáróeszközöket: biztonságos protokollok használatával képesek kommunikálni a felhőátjáróval, ami általában nagyobb feldolgozási többletterhelést igényel, amelyet egy natív eszköz – például egy termosztát – önmagában kell biztosítania. Az Azure-szolgáltatások zónában tegyük fel, hogy a felhőátjárót az Azure IoT Hub szolgáltatás képviseli.

### <a name="device-and-data-sourcesdata-transport"></a>Eszköz- és adatforrások/adatátvitel

Ez a szakasz a korábban a fenyegetésmodellezés lencséjén keresztül felvázolt architektúrát vizsgálja, és áttekintést ad arról, hogyan lehet kezelni a benne rejlő aggályokat. Ez a példa a fenyegetésmodell alapvető elemeire összpontosít:

* Folyamatok (mind az Ön ellenőrzése alatt, mind a külső elemek alatt)
* Kommunikáció (más néven adatáramlás)
* Tároló (más néven adattárolók)

#### <a name="processes"></a>Folyamatok

Az Azure IoT-architektúrában ismertetett kategóriák mindegyikében ez a példa megpróbálja csökkenteni a különböző fázisokban lévő adatok/információk különböző szakaszaiban szereplő különböző fenyegetéseket: folyamat, kommunikáció és tárolás. Az alábbiakban áttekintjük a "folyamat" kategória leggyakoribbait, majd áttekintést arról, hogy ezek a fenyegetések hogyan lehet a legjobban enyhíteni:

**Hamisítás (S)**: A támadó szoftver- vagy hardverszinten kinyerheti a kriptográfiai kulcsanyagot egy eszközről, majd ezt követően egy másik fizikai vagy virtuális eszközzel érheti el a rendszert annak az eszköznek az identitása alatt, amelyből a kulcsanyag származik. Egy jó illusztráció a távirányítók, amelyek viszont minden TV és amelyek népszerű prankster eszközök.

**Szolgáltatásmegtagadás (D)**: Az eszköz működésképtelenné tehető a rádiófrekvenciák vagy a vágóvezetékek zavarásával. Például egy olyan megfigyelő kamera, amelynek hálózati kapcsolata szándékosan ki van ütve, egyáltalán nem tud adatokat jelenteni.

**Manipulálás (T)**: A támadó részben vagy egészben lecserélheti az eszközön futó szoftvert, ami lehetővé teszi a kicserélt szoftver számára, hogy kihasználja az eszköz valódi személyazonosságát, ha a kulcsanyag vagy a kulcsanyagokat tároló kriptográfiai eszközök elérhetők voltak a tiltott program számára. A támadó például a kinyert kulcsanyagot kihasználva elfogja és letiltja a kommunikációs útvonalon lévő eszköz adatait, és azokat az ellopott kulcsanyaggal hitelesített hamis adatokkal helyettesíti.

**Információhoz való hozzáférés (I)**: Ha az eszköz manipulált szoftvert futtat, az ilyen manipulált szoftverek potenciálisan adatokat szivárogtathatnak ki jogosulatlan feleknek. A támadó például a kinyert kulcsanyagot kihasználva beillesztheti magát az eszköz és a vezérlő vagy a helyszíni átjáró vagy a felhőátjáró közötti kommunikációs útvonalba, hogy elszívja az adatokat.

**Jogosultság illetéktelen megszerzését (E)**: Egy adott funkciót használó eszköz valami másra kényszeríthető. Például, egy szelep, amely programozott, hogy nyissa félúton lehet becsapni, hogy nyissa ki az egész utat.

| **Összetevő** | **Fenyegetés** | **Kezelés** | **Kockázat** | **Végrehajtása** |
| --- | --- | --- | --- | --- |
| Eszköz |S |Identitás hozzárendelése az eszközhöz és az eszköz hitelesítése |Az eszköz vagy annak egy része cseréje más eszközre. Honnan tudod, hogy a megfelelő eszközzel beszélsz? |Az eszköz hitelesítése a Transport Layer Security (TLS) vagy az IPSec használatával. Az infrastruktúrának támogatnia kell az előmegosztott kulcs (PSK) használatát azokon az eszközökön, amelyek nem tudják kezelni a teljes aszimmetrikus titkosítást. Használja ki az Azure [AD,OAuth-t](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID között |Alkalmazzon hamisíthatatlan mechanizmusokat az eszközre, például úgy, hogy megnehezíti a kulcsok és más kriptográfiai anyagok kinyerését az eszközről. |A kockázat az, ha valaki manipulálja az eszközt (fizikai interferencia). Honnan tudja, hogy az eszközt nem babrálták meg? |A leghatékonyabb megoldás egy platformmegbízhatósági modul (TPM) képessége, amely lehetővé teszi a kulcsok tárolását olyan speciális lapkára integrált áramkörökben, amelyekből a kulcsok nem olvashatók, de csak olyan kriptográfiai műveletekhez használhatók, amelyek a kulcsot használják, de soha nem hozzák nyilvánosságra a kulcsot. Az eszköz memóriatitkosítása. Az eszköz kulcskezelése. Aláírom a kódot. |
|| E |Az eszköz hozzáférés-vezérlése. Engedélyezési rendszer. |Ha az eszköz lehetővé teszi, hogy az egyes műveleteket egy külső forrásból származó parancsok vagy akár sérült érzékelők alapján hajtsák végre, lehetővé teszi a támadás számára, hogy egyébként nem elérhető műveleteket hajtson végre. |Az eszköz engedélyezési séma |
| Helyszíni átjáró |S |A Field átjáró hitelesítése a Cloud Gateway-hez (például tanúsítványalapú, PSK vagy Jogcím alapú.) |Ha valaki meg tudja hamisítani a Field Gateway-t, akkor bármilyen eszközként is bemutatkozhat. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Mindegy kulcsfontosságú tárolási és igazolási vonatkozik az eszközök általában - a legjobb esetben a TPM használata. 6LowPAN kiterjesztés az IPSec-hez a vezeték nélküli érzékelőhálózatok (WSN) támogatásához. |
|| TRID között |A Field Gateway védelme illetéktelen módosítással szemben (TPM?) |A felhőátjárót használó svindló támadások azt gondolva, hogy a helyszíni átjáróval beszél, az adatok hozadékát és az adatok illetéktelen beavatkozását eredményezheti. |Memória titkosítás, TPM, hitelesítés. |
|| E |Hozzáférés-vezérlési mechanizmus a Field Gateway-hez | | |

Íme néhány példa az ebben a kategóriában található fenyegetésekre:

**Hamisítás**: A támadó szoftver- vagy hardverszinten kinyerheti a kriptográfiai kulcsanyagot egy eszközről, majd ezt követően egy másik fizikai vagy virtuális eszközzel érheti el a rendszert annak az eszköznek az identitása alatt, amelyből a kulcsanyag származik.

**Szolgáltatásmegtagadás**: Az eszköz működésképtelenné tehető a rádiófrekvenciák zavarásával vagy a vezetékek elvágásával. Például egy olyan megfigyelő kamera, amelynek hálózati kapcsolata szándékosan ki van ütve, egyáltalán nem tud adatokat jelenteni.

**Hamisítás**: A támadó részben vagy egészben lecserélheti az eszközön futó szoftvert, így a kicserélt szoftver kihasználhatja az eszköz valódi személyazonosságát, ha a kulcsanyag vagy a kulcsanyagokat tároló kriptográfiai eszközök elérhetők voltak a tiltott program számára.

**Manipulálás:** Egy olyan térfigyelő kamera, amely egy üres folyosó látható spektrumképét mutatja, egy ilyen folyosó fényképére irányulhat. A füst- vagy tűzérzékelő jelentheti, hogy valaki egy öngyújtót tart alatta. Mindkét esetben előfordulhat, hogy az eszköz technikailag teljes mértékben megbízható a rendszerrel szemben, de manipulált információkat jelent.

**Illetéktelen beavatkozás:** A támadó a kinyert kulcsanyaggal elhallgathatja és letilthatja a kommunikációs útvonalon lévő eszköz adatait, és lecserélheti azokat az ellopott kulcsanyaggal hitelesített hamis adatokra.

**Illetéktelen beavatkozás**: A támadó részben vagy egészben lecserélheti az eszközön futó szoftvert, így a kicserélt szoftver kihasználhatja az eszköz valódi személyazonosságát, ha a kulcsanyag vagy a kulcsanyagokat tároló kriptográfiai eszközök elérhetők voltak a tiltott program számára.

**Információhoz való illetéktelen hozzáférés**: Ha az eszköz manipulált szoftvert futtat, az ilyen manipulált szoftverek potenciálisan adatokat szivárogtathatnak ki jogosulatlan feleknek.

**Információfelfedés:** A támadó a kinyert kulcsanyagok at használhatja, hogy beadja magát az eszköz és a vezérlő vagy a helyszíni átjáró vagy a felhőátjáró közötti kommunikációs útvonalba, hogy elszívja az adatokat.

**Szolgáltatásmegtagadás**: Az eszköz kikapcsolható vagy olyan üzemmódba kapcsolható, ahol a kommunikáció nem lehetséges (ami sok ipari gépben szándékos).

**Illetéktelen beavatkozás**: Az eszköz átkonfigurálható úgy, hogy a vezérlőrendszer számára ismeretlen állapotban működjön (az ismert kalibrációs paramétereken kívül), és így olyan adatokat szolgáltasson, amelyek félreértelmezhetők

**Jogosultság illetéktelen megszerzésé:** Egy adott funkciót használó eszköz valami másra kényszeríthető. Például, egy szelep, amely programozott, hogy nyissa félúton lehet becsapni, hogy nyissa ki az egész utat.

**Szolgáltatásmegtagadás**: Az eszköz olyan állapottá alakítható, amelyben a kommunikáció nem lehetséges.

**Illetéktelen beavatkozás**: Az eszköz átkonfigurálható úgy, hogy a vezérlőrendszer számára ismeretlen állapotban működjön (az ismert kalibrációs paramétereken kívül), és így félreértelmezhető adatokat szolgáltasson.

**Hamisítás/illetéktelen beavatkozás/megtagadás**: Ha nincs biztosítva (ami ritkán fordul elő a fogyasztói távirányítók esetében), a támadó névtelenül módosíthatja az eszköz állapotát. Egy jó illusztráció a távirányítók, amelyek viszont minden TV és amelyek népszerű prankster eszközök.

#### <a name="communication"></a>Kommunikáció

Az eszközök, eszközök és helyszíni átjárók, valamint az eszköz- és felhőátjáró kontinamányos. Az alábbi táblázat útmutatást nyújt az eszköz/VPN nyitott szoftvercsatornáival kapcsolatosan:

| **Összetevő** | **Fenyegetés** | **Kezelés** | **Kockázat** | **Végrehajtása** |
| --- | --- | --- | --- | --- |
| Eszköz IoT hub |Tid |D) A 2004/200 TLS (PSK/RSA) a forgalom titkosításához |A készülék és az átjáró közötti kommunikáció lehallgatása vagy zavarása |Protokoll szintű biztonság. Az egyéni protokollok, meg kell találni, hogyan védheti meg őket. A legtöbb esetben a kommunikáció az eszközről az IoT Hub (eszköz kezdeményezi a kapcsolatot). |
| Eszközről eszközre |Tid |D) A 2004/200 TLS (PSK/RSA) a forgalom titkosításához. |Adatok olvasása az eszközök közötti átvitel során. Az adatok manipulálása. Az eszköz túlterhelése új kapcsolatokkal |A protokoll szintjén (MQTT/AMQP/HTTP/CoAP. Az egyéni protokollok, meg kell találni, hogyan védheti meg őket. A DoS-fenyegetés kockázatának az, hogy egy felhőalapú vagy helyszíni átjárón keresztül társvállalatokat létesít, és csak a hálózat felé ügyfelekként működnek. A társviszony-létesítés közvetlen kapcsolatot eredményezhet a társak között, miután az átjáró közvetítette őket |
| Külső entitáseszköz |Tid |A külső entitás erős párosítása az eszközzel |Lehallgatom a kapcsolatot a készülékhez. A készülékkel való kommunikáció zavarása |A külső entitás biztonságos párosítása az NFC/Bluetooth LE készülékhez. Az eszköz működési paneljének vezérlése (Fizikai) |
| Field Gateway felhőátjáró |Tid |TLS (PSK/RSA) a forgalom titkosításához. |A készülék és az átjáró közötti kommunikáció lehallgatása vagy zavarása |Protokollszintű biztonság (MQTT/AMQP/HTTP/CoAP). Az egyéni protokollok, meg kell találni, hogyan védheti meg őket. |
| Device Cloud átjáró |Tid |TLS (PSK/RSA) a forgalom titkosításához. |A készülék és az átjáró közötti kommunikáció lehallgatása vagy zavarása |Protokollszintű biztonság (MQTT/AMQP/HTTP/CoAP). Az egyéni protokollok, meg kell találni, hogyan védheti meg őket. |

Íme néhány példa az ebben a kategóriában található fenyegetésekre:

**Szolgáltatásmegtagadás:** A korlátozott eszközök általában DoS-fenyegetés alatt állnak, amikor aktívan figyelik a bejövő kapcsolatokat vagy a kéretlen datagramokat a hálózaton, mert a támadó sok kapcsolatot nyithat meg párhuzamosan, és nem tudja őket lassan kiszolgálni vagy kiszolgálni, vagy az eszközt eláraszthatja a kéretlen forgalom. Az eszköz mindkét esetben hatékonyan működésképtelenné tehető a hálózaton.

**Hamisítás, információhoz való illetéktelen hozzáférés:** A korlátozott eszközök és a speciális célú eszközök gyakran egy az egy az egybeetre biztonsági eszközzel rendelkeznek, például jelszó- vagy PIN-védelemmel, vagy teljes mértékben a hálózat megbízhatóságára támaszkodnak, ami azt jelenti, hogy hozzáférést biztosítanak az információkhoz, ha egy eszköz ugyanazon a hálózaton található, és a hálózatot gyakran csak megosztott kulcs védi. Ez azt jelenti, hogy az eszköz vagy hálózat megosztott titkának nyilvánosságra hozatalakor az eszköz vezérlése vagy az eszköz által kibocsátott adatok megfigyelése lehetséges.  

**Hamisítás**: a támadó elfogja vagy részben felülírja az adást, és meghamisíthatja a kezdeményezőt (ember középen)

**Hamisítás**: a támadó elfogja vagy részben felülírja az adást, és hamis adatokat küldhet 

**Információhoz való illetéktelen hozzáférés:** a támadó lehallgatózhat egy adást, és engedély nélkül kaphat információt **Szolgáltatásmegtagadás:** a támadó zavarhatja a szórási jelet, és megtagadhatja az információterjesztést

#### <a name="storage"></a>Storage

Minden eszköz- és terepi átjáró rendelkezik valamilyen tárhellyel (ideiglenes az adatok sorban állásához, operációs rendszer (OS) lemezkép-tárolás).

| **Összetevő** | **Fenyegetés** | **Kezelés** | **Kockázat** | **Végrehajtása** |
| --- | --- | --- | --- | --- |
| Eszköz tárolása |TRID között |Tárolótitkosítás, a naplók aláírása |Adatok olvasása a tárolóból (PII adatok), a telemetriai adatok manipulálása. A várólistán lévő vagy gyorsítótárazott parancsvezérlő adatok illetéktelen módosítása. A konfigurációs vagy belső vezérlőprogram-frissítési csomagok helyi gyorsítótárazás a helyi gyorsítótárazás vagy várólistára helyezett rendszermódosítása az operációs rendszer és/vagy a rendszerösszetevők sérülését eredményezheti |Titkosítás, üzenethitelesítési kód (MAC) vagy digitális aláírás. Ahol lehetséges, erős hozzáférés-vezérlés erőforrás-hozzáférés-vezérlési listákon (ACL)-en vagy engedélyeken keresztül. |
| Eszköz operációs rendszerének képe |TRID között | |Az operációs rendszer manipulálása /az operációs rendszer összetevőinek cseréje |Írásvédett operációsrendszer-partíció, aláírt operációsrendszer-lemezkép, titkosítás |
| Field Gateway tároló (az adatok sorba állása) |TRID között |Tárolótitkosítás, a naplók aláírása |Adatok olvasása a tárolóból (PII adatok), a telemetriai adatok illetéktelen beavatkozása, a várólistás vagy gyorsítótárazott parancsvezérlő adatok illetéktelen módosítása. A konfigurációs vagy firmware-frissítési csomagok (eszközökre vagy helyszíni átjárókra szánt) illetéktelen módosítása, miközben a gyorsítótárazott vagy a helyi várólistára kerül, az operációs rendszer és/vagy a rendszerösszetevők sérülését eredményezheti |BitLocker |
| Field Gateway OS-lemezkép |TRID között | |Az operációs rendszer manipulálása /az operációs rendszer összetevőinek cseréje |Írásvédett operációsrendszer-partíció, aláírt operációsrendszer-lemezkép, titkosítás |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Eszköz- és eseményfeldolgozási/felhőalapú átjárózóna

A felhőátjáró olyan rendszer, amely lehetővé teszi a távoli kommunikációt az eszközökről és a helyszíni átjárókról több különböző helyről a nyilvános hálózati térben, jellemzően egy felhőalapú vezérlő és adatelemzési rendszer felé, amely ilyen rendszerek összevonása. Bizonyos esetekben a felhőátjáró azonnal megkönnyítheti a hozzáférést a speciális célú eszközökhöz terminálokról, például táblagépekről vagy telefonokról. Az itt tárgyalt környezetben a "felhő" egy olyan dedikált adatfeldolgozó rendszerre vonatkozik, amely nem ugyanahhoz a helyhez van kötve, mint a csatolt eszközök vagy helyszíni átjárók, és ahol az operatív intézkedések megakadályozzák a célzott fizikai hozzáférést, de nem feltétlenül a " nyilvános felhő" infrastruktúrát. A felhőátjáró t leképezhet egy hálózati virtualizációs átfedésbe, hogy szigetelje a felhőátjárót és annak összes csatlakoztatott eszközét vagy helyszíni átjáróját bármely más hálózati forgalomtól. Maga a felhőátjáró nem eszközvezérlő rendszer, illetve az eszközadatok feldolgozási vagy tárolási létesítménye; a létesítmény-interfészt a felhőátjáróval. A felhőátjáró-zóna magában foglalja a felhőátjárót, valamint az összes helyszíni átjárót és eszközt, amely közvetlenül vagy közvetve kapcsolódik hozzá.

A felhőátjáró többnyire egyéni szoftver, amely szolgáltatásként fut, és amelyhez a helyszíni átjáró és az eszközök kapcsolódnak. Mint ilyen, úgy kell megtervezni, a biztonság szem előtt tartva. Kövesse [az SDL](https://www.microsoft.com/sdl) folyamatot a szolgáltatás tervezéséhez és létrehozásához.

#### <a name="services-zone"></a>Szolgáltatási zóna

A vezérlőrendszer (vagy vezérlő) olyan szoftvermegoldás, amely egy eszközzel, egy helyszíni átjáróval vagy felhőátjáróval egy vagy több eszköz vezérlése céljából csatlakozik, és/vagy eszközadatok atprezentációvagy későbbi ellenőrzési célokból összegyűjtésére és/vagy tárolására és/vagy elemzésére szolgál. Az ellenőrzési rendszerek az egyetlen olyan entitások a vita során, amelyek azonnal megkönnyíthetik az emberekkel való interakciót. A kivételek az eszközök köztes fizikai vezérlőfelületei, például egy kapcsoló, amely lehetővé teszi egy személy számára az eszköz kikapcsolását vagy más tulajdonságok módosítását, és amelyekesetében nincs digitálisan elérhető funkcionális megfelelője.

A közbenső fizikai vezérlőfelületek azok, ahol a szabályozó logika korlátozza a fizikai vezérlőfelület funkcióját, így az egyenértékű funkció távolról is elindítható, vagy elkerülhető a bemeneti ütközés a távoli bemenettel – ilyen közbenső a vezérlőfelületek fogalmilag egy olyan helyi vezérlőrendszerhez vannak csatlakoztatva, amely ugyanazt az alapul szolgáló funkciót használja, mint bármely más távvezérlő rendszer, amelyhez az eszköz párhuzamosan csatlakoztatható. A felhőalapú számítástechnikát fenyegető legfontosabb fenyegetések a [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) oldalon olvashatók.

## <a name="additional-resources"></a>További források

További információkért tekintse át a következő cikkeket:

* [SDL fenyegetésmodellező eszköz](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT referenciaarchitektúra](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
