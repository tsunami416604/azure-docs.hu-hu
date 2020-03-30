---
title: Intelligens szerződésintegrációs minták – Azure Blockchain Workbench
description: Az intelligens szerződésintegrációs minták áttekintése az Azure Blockchain Workbench előzetes verziójában.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325971"
---
# <a name="smart-contract-integration-patterns"></a>Intelligens szerződések integrációs mintái

Az intelligens szerződések gyakran olyan üzleti munkafolyamatot jelentenek, amelyet külső rendszerekkel és eszközökkel kell integrálni.

Ezeknek a munkafolyamatoknak a követelményei közé tartozik, hogy olyan elosztott főkönyvi tranzakciókat kell kezdeményezni, amelyek külső rendszerből, szolgáltatásból vagy eszközből származó adatokat tartalmaznak. Emellett külső rendszereknek is reagálniuk kell az intelligens szerződésekből származó eseményekre egy elosztott főkönyvben.

A REST API és az üzenetküldési integráció tranzakciókat küld a külső rendszerek intelligens szerződések egy Azure Blockchain Workbench alkalmazás tartalmazza. Eseményértesítéseket is küld külső rendszereknek az alkalmazáson belül végrehajtott módosítások alapján.

Adatintegrációs forgatókönyvek esetén az Azure Blockchain Workbench adatbázisnézeteket tartalmaz, amelyek egyesítik a blokkláncból származó tranzakciós adatok és az alkalmazások és az intelligens szerződések metaadatait.

Ezenkívül egyes forgatókönyvek, például az ellátási lánccal vagy a médiával kapcsolatosak, szintén szükségessé tehetik a dokumentumok integrálását. Bár az Azure Blockchain Workbench nem biztosít API-hívásokat a dokumentumok közvetlen kezeléséhez, a dokumentumok beépíthetők egy blockchain alkalmazásba. Ez a szakasz is, hogy a minta.

Ez a szakasz az ilyen típusú integrációk alkalmazásához azonosított mintákat tartalmazza a végpontok közötti megoldásokban.

## <a name="rest-api-based-integration"></a>REST API-alapú integráció

Az Azure Blockchain Workbench által létrehozott webalkalmazás on belül a REST API-n keresztül elérhetővé tenni a képességeket. A képességek közé tartozik az Azure Blockchain Workbench feltöltése, konfigurálása és felügyelete alkalmazások, tranzakciók küldése egy elosztott főkönyvbe, valamint az alkalmazás metaadatok és főkönyvi adatok lekérdezése.

A REST API-t elsősorban interaktív ügyfelek, például webes, mobil- és robotalkalmazások használják.

Ez a szakasz a REST API azon aspektusaira összpontosító mintákat vizsgálja, amelyek tranzakciókat küldenek egy elosztott főkönyvbe, és mintákat, amelyek lekérdezik az Azure Blockchain Workbench *láncon kívüli* SQL-adatbázisából származó tranzakciók adatait.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Tranzakciók küldése elosztott főkönyvbe külső rendszerből

Az Azure Blockchain Workbench REST API hitelesített kérelmeket küld a tranzakciók végrehajtása egy elosztott főkönyvben.

![Tranzakciók küldése elosztott főkönyvbe](./media/integration-patterns/send-transactions-ledger.png)

A tranzakciók végrehajtása a korábban ábrázolt folyamat tal történik, ahol:

-   A külső alkalmazás hitelesíti magát az Azure Blockchain Workbench-telepítés részeként kiépített Azure Active Directoryban.
-   A jogosult felhasználók kapnak egy tulajdonosi jogkivonatot, amely az API-ba irányuló kérésekkel elküldhető.
-   A külső alkalmazások hívásokat kezdeményeznek a REST API-ba a tulajdonosi jogkivonat használatával.
-   A REST API üzenetként csomagolja a kérelmet, és elküldi azt a Service Busnak. Innen a rendszer lekéri, aláírja és elküldi a megfelelő elosztott főkönyvbe.
-   A REST API-t az Azure Blockchain Workbench SQL DB a kérelem rögzítésére és az aktuális kiépítési állapot létrehozására kéri.
-   Az SQL DB visszaadja a kiépítési állapotot, és az API-hívás visszaadja az azonosítót a külső alkalmazás, amely hívta.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blockchain Workbench-metaadatok és elosztott főkönyvi tranzakciók lekérdezése

Az Azure Blockchain Workbench REST API hitelesített kérelmeket küld az intelligens szerződés végrehajtásával kapcsolatos részletek lekérdezéséhez egy elosztott főkönyvben.

![Metaadatok lekérdezése](./media/integration-patterns/querying-metadata.png)

A lekérdezés a korábban ábrázolt folyamat tal történik, ahol:

1. A külső alkalmazás hitelesíti magát az Azure Blockchain Workbench-telepítés részeként kiépített Azure Active Directoryban.
2. A jogosult felhasználók kapnak egy tulajdonosi jogkivonatot, amely az API-ba irányuló kérésekkel elküldhető.
3. A külső alkalmazások hívásokat kezdeményeznek a REST API-ba a tulajdonosi jogkivonat használatával.
4. A REST API lekérdezi az SQL DB-től érkező kérelem adatait, és visszaadja az okat az ügyfélnek.

## <a name="messaging-integration"></a>Üzenetküldési integráció

Az üzenetküldési integráció megkönnyíti a rendszerekkel, szolgáltatásokkal és eszközökkel való interakciót, ahol az interaktív bejelentkezés nem lehetséges vagy nem kívánatos. Az üzenetküldési integráció kétféle üzenettípusra összpontosít: a tranzakciókat kérő üzenetekre egy elosztott főkönyvben, és az adott főkönyv által a tranzakciók végrehajtásakor közzétett eseményekre.

Az üzenetküldési integráció a felhasználók létrehozásával, a szerződések létrehozásával és a szerződések tranzakcióinak végrehajtásával kapcsolatos tranzakciók végrehajtására és nyomon követésére összpontosít, és elsősorban *a fej nélküli háttérrendszerek* használják.

Ez a szakasz az üzenetalapú API azon aspektusaira összpontosító mintákat vizsgálja, amelyek tranzakciókat küldenek egy elosztott főkönyvbe, és azokat a mintákat, amelyek az alapul szolgáló elosztott főkönyv által közzétett eseményüzeneteket jelölik.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Egyirányú eseménykézbesítés intelligens szerződésből eseményfogyasztóhoz 

Ebben a forgatókönyvben egy esemény történik egy intelligens szerződés, például egy állapotváltozás vagy egy adott típusú tranzakció végrehajtása. Ezt az eseményt egy eseményhálózaton keresztül sugározzák a későbbi felhasználók számára, és ezek a fogyasztók megteszik a megfelelő lépéseket.

Egy példa erre a forgatókönyvre, hogy amikor egy tranzakció bekövetkezik, a fogyasztó figyelmeztetést kap, és intézkedhet, például az adatok rögzítése egy SQL DB vagy a Common Data Service. Ez a forgatókönyv ugyanaz a minta, amely et a Workbench követi az *SQL DB-láncon kívüli* feltöltéséhez.

A másik az lenne, ha egy intelligens szerződés áttérne egy adott állapotba, például amikor egy szerződés *outofcompliance-ba*kerül. Az állapotváltozás bekövetkezése esetén riasztást küldhet a rendszergazda mobiltelefonjára.

![Egyirányú eseménykézbesítés](./media/integration-patterns/one-way-event-delivery.png)

Ez a forgatókönyv a korábban ábrázolt folyamattal történik, ahol:

-   Az intelligens szerződés új állapotba vált, és eseményt küld a főkönyvnek.
-   A főkönyv fogadja, és kézbesíti az eseményt az Azure Blockchain Workbench.
-   Az Azure Blockchain Workbench előfizetett a főkönyvből származó eseményekre, és megkapja az eseményt.
-   Az Azure Blockchain Workbench közzéteszi az eseményt az eseményrácson lévő előfizetőknek.
-   A külső rendszerek előfizetnek az Eseményrácsra, felhasználják az üzenetet, és megteszik a megfelelő műveleteket.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Egyirányú eseménykézbesítés egy külső rendszerből intelligens szerződésbe

Van is egy forgatókönyv, amely az ellenkező irányból folyik. Ebben az esetben egy eseményt egy érzékelő vagy egy külső rendszer hoz létre, és az adott eseményből származó adatokat intelligens szerződésnek kell elküldeni.

Gyakori példa erre a pénzügyi piacokról származó adatok átadása, például az áruk, részvények vagy kötvények árai egy intelligens szerződéshez.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain workbench közvetlen kézbesítése a várt formátumban

Egyes alkalmazások úgy vannak kialakítva, hogy integrálhatók az Azure Blockchain Workbench-be, és közvetlenül generáljanak és küldjenek üzeneteket a várt formátumokban.

![Közvetlen szállítás](./media/integration-patterns/direct-delivery.png)

Ez a kézbesítés a korábban ábrázolt folyamat tal történik, ahol:

-   Egy esemény történik egy külső rendszerben, amely elindítja egy üzenet létrehozását az Azure Blockchain Workbench számára.
-   A külső rendszer kódot írt, hogy ezt az üzenetet egy ismert formátumban, és elküldi közvetlenül a Service Bus.
-   Az Azure Blockchain Workbench előfizeti az eseményeket a Service Bus, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvbe, és adatokat küld a külső rendszerből egy adott szerződésnek.
-   Az üzenet kézhezvételekor a szerződés új állapotba kerül.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench számára ismeretlen formátumú üzenet kézbesítése

Egyes rendszerek nem módosíthatók az Azure Blockchain Workbench által használt szabványos formátumokban történő üzenetek kézbesítéséhez. Ezekben az esetekben gyakran használhatók az ezekből a rendszerekből származó meglévő mechanizmusok és üzenetformátumok. Pontosabban ezek a rendszerek natív üzenettípusai átalakíthatók a Logic Apps, az Azure Functions vagy más egyéni kód használatával, hogy leképezzék a várt szabványos üzenetküldési formátumok egyikére.

![Ismeretlen üzenetformátum](./media/integration-patterns/unknown-message-format.png)

Ez a korábban ábrázolt folyamat használatával történik, ahol:

-   Olyan esemény történik egy külső rendszerben, amely elindítja egy üzenet létrehozását.
-   A logikai alkalmazás vagy az egyéni kód fogadására szolgál, hogy az üzenetet, és alakítsa át egy szabványos Azure Blockchain Workbench formázott üzenetet.
-   A logikai alkalmazás az átalakított üzenetet közvetlenül a Service Bus.
-   Az Azure Blockchain Workbench előfizeti az eseményeket a Service Bus, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvbe, és adatokat küld a külső rendszerből a szerződés egy adott függvényének.
-   A függvény végrehajtja, és általában módosítja az állapotot. Az állapotváltozás előreviszi az intelligens szerződésben tükröződő üzleti munkafolyamatot, lehetővé téve más funkciók szükség szerinti végrehajtását.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Az ellenőrzés külső folyamatra való áttérése és a befejezésre várva

Vannak olyan esetek, amikor egy intelligens szerződésnek le kell állítania a belső végrehajtást, és át kell adnia egy külső folyamatnak. Ez a külső folyamat ezután befejeződik, üzenetet küld az intelligens szerződésnek, és a végrehajtás az intelligens szerződésen belül folytatódik.

#### <a name="transition-to-the-external-process"></a>Áttérés a külső folyamatra

Ez a minta általában a következő megközelítés sel valósítják meg:

-   Az intelligens szerződés átkerül egy adott állapotba. Ebben az állapotban nem vagy korlátozott számú függvény hajtható végre, amíg egy külső rendszer nem hajt végre a kívánt műveletet.
-   Az állapotváltozás eseményként kerül felszínre egy későbbi fogyasztó számára.
-   A továbbfelhasználó megkapja az eseményt, és elindítja a külső kódfuttatást.

![Átmenet vezérlése külső folyamatra](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Az irányítás visszaadása az intelligens szerződésből

Attól függően, hogy a külső rendszer testreszabása, lehet, hogy nem tudja kézbesíteni az üzeneteket az Azure Blockchain Workbench által elvárt szabványos formátumok egyikében. A külső rendszerek ezen üzenetek egyikének létrehozására való képessége alapján határozza meg, hogy az alábbi két visszatérési útvonal közül melyik et veszi.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain workbench közvetlen kézbesítése a várt formátumban

![](./media/integration-patterns/direct-delivery.png)

Ebben a modellben a szerződéssel való kommunikáció és az azt követő állapotváltozás az előző folyamatot követően történik,

-   A külső kódvégrehajtás befejezését vagy egy adott mérföldkövet elérve egy eseményt küld a Service Bus az Azure Blockchain Workbench-hez.

-   Az api elvárásainak megfelelő üzenet írásához közvetlenül nem adaptálható rendszerek esetében átalakul.

-   Az üzenet tartalma fel van csomagolva, és az intelligens szerződés egy adott funkciójának küldi el. Ez a kézbesítés a külső rendszerhez társított felhasználó nevében történik.

-   A függvény végrehajtja, és általában módosítja az állapotot. Az állapotváltozás előreviszi az intelligens szerződésben tükröződő üzleti munkafolyamatot, lehetővé téve más funkciók szükség szerinti végrehajtását.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench számára ismeretlen formátumú üzenet kézbesítése

![Ismeretlen üzenetformátum](./media/integration-patterns/unknown-message-format.png)

Ebben a modellben, ahol egy szabványos formátumú üzenet nem küldhető el közvetlenül, a szerződéssel való kommunikáció és az azt követő állapotváltozás az előző folyamatot követően történik, ahol:

1.  A külső kódvégrehajtás befejezését vagy egy adott mérföldkövet elérve egy eseményt küld a Service Bus az Azure Blockchain Workbench-hez.
2.  A logikai alkalmazás vagy az egyéni kód fogadására szolgál, hogy az üzenetet, és alakítsa át egy szabványos Azure Blockchain Workbench formázott üzenetet.
3.  A logikai alkalmazás az átalakított üzenetet közvetlenül a Service Bus.
4.  Az Azure Blockchain Workbench előfizeti az eseményeket a Service Bus, és lekéri az üzenetet.
5.  Az Azure Blockchain Workbench hívást kezdeményez a főkönyvbe, és adatokat küld a külső rendszerből egy adott szerződésnek.
6. Az üzenet tartalma fel van csomagolva, és az intelligens szerződés egy adott funkciójának küldi el. Ez a kézbesítés a külső rendszerhez társított felhasználó nevében történik.
7.  A függvény végrehajtja, és általában módosítja az állapotot. Az állapotváltozás előreviszi az intelligens szerződésben tükröződő üzleti munkafolyamatot, lehetővé téve más funkciók szükség szerinti végrehajtását.

## <a name="iot-integration"></a>IoT-integráció

A gyakori integrációs forgatókönyv az érzékelőkből egy intelligens szerződésbe beolvasott telemetriai adatok belefoglalása. Az érzékelők által átadott adatok alapján az intelligens szerződések megalapozott intézkedéseket hozhatnak, és megváltoztathatják a szerződés állapotát.

Például, ha egy gyógyszert szállító teherautó hőmérséklete 110 fokra emelkedik, az hatással lehet a gyógyszer hatékonyságára, és közbiztonsági problémát okozhat, ha nem észlelik és távolítják el az ellátási láncból. Ha a vezető felgyorsította az autót 100 mérföld per óra, az ebből eredő érzékelő információk kiváltó törlését biztosítás a biztosító. Ha az autó bérelt autó volt, a GPS-adatok jelezhetik, hogy a járművezető mikor ment ki a bérleti szerződéshatálya alá tartozó földrajzi területről, és büntetést szabott ki.

A kihívás az, hogy ezek az érzékelők folyamatosan szolgáltathatnak adatokat, és nem helyénvaló ezeket az adatokat intelligens szerződésbe küldeni. Egy tipikus megközelítés, hogy korlátozza a blokkláncnak küldött üzenetek számát, miközben az összes üzenetet egy másodlagos tárolóba kézbesíti. Például csak rögzített időközönként, például óránként egyszer, és ha egy zárt érték kívül esik egy intelligens szerződés egyeztetett tartományán. A tűréshatáron kívül eső értékek ellenőrzése biztosítja, hogy a szerződések üzleti logikájához kapcsolódó adatok beérkeznek és végrehajtásra kerülnek. Az érték időközzel való ellenőrzése megerősíti, hogy az érzékelő még mindig jelentést tesz. Minden adatot egy másodlagos jelentési tárolóba küldünk, hogy szélesebb körű jelentéskészítést, elemzést és gépi tanulást engedélyezzünk. Például, míg a gps-re vonatkozó érzékelők leolvasása nem feltétlenül szükséges percenként egy intelligens szerződéshez, érdekes adatokat nyújthatnak a jelentésekben vagy a térképútvonalakon való használatra.

Az Azure platformon az eszközökkel való integráció általában az IoT Hub használatával történik. Az IoT Hub tartalom alapján biztosítja az üzenetek útválasztását, és engedélyezi a korábban ismertetett funkciók típusát.

![IoT-üzenetek](./media/integration-patterns/iot.png)

A folyamat egy mintát ábrázol:

-   Az eszköz közvetlenül vagy egy helyszíni átjárón keresztül kommunikál az IoT Hub.
-   Az IoT Hub fogadja az üzeneteket, és kiértékeli az üzeneteket útvonalak on létre, amelyek ellenőrzik az üzenet tartalmát, például. *Az érzékelő 50 foknál nagyobb hőmérsékletet jelent?*
-   Az IoT Hub a feltételeknek megfelelő üzeneteket küld egy meghatározott service bus az útvonalhoz.
-   Egy logikai alkalmazás vagy más kód figyeli a Service Bus, amely az IoT Hub által létrehozott az útvonalhoz.
-   A logikai alkalmazás vagy más kód lekéri és átalakítja az üzenetet egy ismert formátumba.
-   Az átalakított üzenet, most egy szabványos formátumú, a Service Bus for Azure Blockchain Workbench.
-   Az Azure Blockchain Workbench előfizeti az eseményeket a Service Bus, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvbe, és adatokat küld a külső rendszerből egy adott szerződésnek.
-   Az üzenet kézhezvételekor a szerződés kiértékeli az adatokat, és megváltoztathatja az állapotot az értékelés eredménye alapján, például magas hőmérséklet esetén módosítsa az állapotot *A megfelelőségen kívül*.

## <a name="data-integration"></a>Adatintegráció

A REST és az üzenetalapú API mellett az Azure Blockchain Workbench hozzáférést biztosít egy alkalmazás- és szerződésmetaadatokkal, valamint elosztott főkönyvekből származó tranzakciós adatokkal feltöltött SQL DB-hez is.

![Adatintegráció](./media/integration-patterns/data-integration.png)

Az adatintegráció jól ismert:

-   Az Azure Blockchain Workbench az alkalmazások, munkafolyamatok, szerződések és tranzakciók metaadatait tárolja a szokásos működési viselkedése részeként.
-   A külső rendszerek vagy eszközök egy vagy több párbeszédablakot biztosítanak az adatbázissal kapcsolatos információk gyűjtésének megkönnyítéséhez, például az adatbázis-kiszolgáló nevét, az adatbázis nevét, a hitelesítés típusát, a bejelentkezési hitelesítő adatokat és a használandó adatbázisnézeteket.
-   A lekérdezések sql-adatbázisnézetekhez vannak intézve, hogy megkönnyítsék a külső rendszerek, szolgáltatások, jelentéskészítési, fejlesztői eszközök és vállalati hatékonyságnövelő eszközök későbbi felhasználását.

## <a name="storage-integration"></a>Tárolási integráció

Számos forgatókönyv szükségessé teheti az tesztelhető fájlok beépítését. Több okból kifolyólag nem helyénvaló fájlokat helyezni egy blokkláncra. Ehelyett egy általános megközelítés egy kriptográfiai kivonat (például SHA-256) végrehajtása egy fájlon, és megosztás, amely kivonatol egy elosztott főkönyvben. A kivonat ismételt végrehajtása a jövőben ugyanazt az eredményt kell visszaadnia. Ha a fájl módosul, még akkor is, ha csak egy képpont módosul a képen, a kivonat más értéket ad vissza.

![Tárolási integráció](./media/integration-patterns/storage-integration.png)

A minta akkor valósítható meg, ha:

-   Egy külső rendszer megőrzött egy fájlt egy tárolási mechanizmusban, például az Azure Storage.An external system persists a file in a storage mechanism, such as Azure Storage.
-   A fájllal vagy a fájllal és a kapcsolódó metaadatokkal kivonat keletkezik, például a tulajdonos azonosítója, a fájl helye szerinti URL stb.
-   A kivonatot és a metaadatokat a rendszer egy intelligens szerződés függvényének küldi el, például *a Fájl hozzáadva*
-   A jövőben a fájl és a metaadatok újra kivonatozhatók, és összehasonlíthatók a főkönyvben tárolt értékekkel.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>A REST- és üzenetAPI-k használatával az integrációs minták megvalósításának előfeltételei

Annak érdekében, hogy egy külső rendszer vagy eszköz a REST vagy az üzenet API-val kommunikáljon az intelligens szerződéssel, a következő

1. Az Azure Active Directory a konzorcium hoz létre egy fiókot, amely képviseli a külső rendszer vagy eszköz.
2. Az Azure Blockchain Workbench-alkalmazás egy vagy több megfelelő intelligens szerződése rendelkezik a külső rendszerből vagy eszközről származó események fogadására meghatározott funkciókkal.
3. Az intelligens egyezmény alkalmazáskonfigurációs fájlja tartalmazza a szerepkört, amelyhez a rendszer vagy az eszköz hozzá van rendelve.
4. Az intelligens egyezmény alkalmazáskonfigurációs fájlja azonosítja, hogy a megadott szerepkör mely állapotokban nevezi meg ezt a függvényt.
5. Az alkalmazás konfigurációs fájl és az intelligens szerződések feltöltése az Azure Blockchain Workbench.

Az alkalmazás feltöltése után az Azure Active Directory-fiók a külső rendszer a szerződéshez és a kapcsolódó szerepkörhöz van rendelve.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Külső rendszerintegrációs folyamatok tesztelése az integrációs kód írása előtt 

A külső rendszerekkel való integráció számos forgatókönyv kulcsfontosságú követelménye. Kívánatos, hogy képes legyen érvényesíteni az intelligens szerződés tervezés előtt vagy azzal párhuzamosan a kódot, hogy integrálja a külső rendszerek.

Az Azure Active Directory (Azure AD) használata nagyban felgyorsíthatja a fejlesztők termelékenységét és az értékeléshez rendelkezésre álló időt. Pontosabban, a kód integrációja egy külső rendszerrel nem triviális időt vehet igénybe. Az Azure AD használatával és az Azure Blockchain Workbench által a UX automatikus generálásával engedélyezheti a fejlesztőknek, hogy külső rendszerként jelentkezzenek be a Blockchain Workbench-be, és a felhasználói felületen keresztül feltöltsék a külső rendszer értékeit. Gyorsan fejleszthet és érvényesíthet ötleteket egy koncepcióigazolási környezetben, mielőtt a külső rendszerekhez integrációs kódot írna.
