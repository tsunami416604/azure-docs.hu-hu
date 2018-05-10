---
title: Intelligens szerződés integrációs minták Azure Blockchain munkaterület
description: Intelligens szerződés integrációs minták Azure Blockchain munkaterület áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: a6a44e30fe58617b43c5491a72fc882015bc9591
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="smart-contract-integration-patterns"></a>Intelligens szerződés integrációs minták

Intelligens szerződések gyakran jelöli, amelyet a külső rendszerek és egyéb eszközök integrálása üzleti munkafolyamat.

Ezek a munkafolyamatok követelményeinek kell kezdeményezni egy külső rendszer, a szolgáltatás vagy az eszköz adatait tartalmazó elosztott főkönyvi tranzakciók közé tartoznak. Ezek emellett rendelkeznie kell egy elosztott főkönyvi az intelligens szerződések származó események reagálnak külső rendszerekkel.

A REST API-t és az üzenetkezelési integrációs a mind külső rendszerekből tranzakciók Azure Blockchain munkaterület kérelemben intelligens szerződések küldeni, valamint eseményértesítések küldése külső rendszerekből módosításokat lehetőséget nyújtanak. Helyezze az alkalmazáson belül.

A adatok integrációs feladatokhoz Azure Blockchain munkaterület tartalmaz egy adatbázis nézetek, amely egyesíti a tranzakciós adatokat a blockchain és az alkalmazások és az intelligens szerződések metaadatai kombinációja.

Emellett bizonyos esetekben az ellátási lánc vagy adathordozóra, például megkövetelheti az integráció a dokumentumok. Míg Azure Blockchain munkaterületet nyújt API meghívja a dokumentumokat közvetlenül kezelésére, dokumentumok beépíthető Azure Blockchain kérelmet. Ez a szakasz is, hogy a mintának.

Ez a szakasz azonosítani a valósít meg minden ilyen típusú integrációja a végpontok közötti megoldások mintázatokat tartalmazza.

## <a name="rest-api-based-integration"></a>REST API-alapú integrációs

Az Azure Blockchain munkaterület létrehozott webalkalmazás képességeire a REST API-n keresztül érhetők el. Ez magában foglalja az Azure Blockchain munkaterület feltöltését, a konfiguráció és a felügyeleti alkalmazások, a tranzakciók küld egy elosztott főkönyvi és a metaadatok és a főkönyvi adatainak lekérdezésekor.

A REST API elsősorban a interaktív ügyfelekről, például webes, mobil-és botot alkalmazások.

Ez a szakasz ellenőrzi, hogy az azokat a vonatkozásait, a REST API-t, amelyek tranzakciók küldenek egy elosztott főkönyvi, valamint azokat, amelyeket az Azure Blockchain munkaterület tranzakciókkal kapcsolatos adatok lekérdezése összpontosított minták *lánc kikapcsolva* SQL-adatbázis.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Elosztott főkönyvi tranzakciók történő küldéséhez a külső rendszerek

Az Azure Blockchain munkaterület REST API lehetővé teszi egy elosztott főkönyvi tranzakciók végrehajtásához hitelesített kérelmek küldésére.

![Tranzakciók küld egy elosztott főkönyve](media/blockchain-workbench-integration-patterns/send-transactions-ledger.png)

Ez akkor fordul elő, ahol a fenti kitaláltak folyamatot követve:

-   A külső alkalmazás hitelesíti az Azure Active Directory üzembe helyezve az Azure Blockchain munkaterület központi telepítésének részeként.
-   Egy tulajdonosi jogkivonatot, amely az API által érintett elküldhető fogadása engedéllyel rendelkező felhasználók
-   Külső alkalmazások hívások REST API használatával a tulajdonosi jogkivonatot.
-   A REST API-t a csomagok a kérésre egy üzenetet, és elküldi a Service Bus. Itt azt fogja beolvasni, aláírt, és a megfelelő elosztott főkönyvi küldött.
-   A REST API-t egy kérést küld az Azure Blockchain munkaterület SQL DB jegyezze fel a kérelmet, és létrehozza az aktuális üzembe helyezési állapotát.
-   Az SQL-adatbázis a kiépítési állapot és az API-hívás a azonosító beolvasása nevezett azt a külső alkalmazás.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blockchain munkaterület metaadatok és a főkönyvi elosztott tranzakciók lekérdezése

Az Azure Blockchain munkaterület REST API lehetővé teszi hitelesített kérést küld egy elosztott főkönyvi intelligens szerződés végrehajtása lekérdezés részleteit.

![Metaadatok lekérdezése](media/blockchain-workbench-integration-patterns/querying-metadata.png)

Ez akkor fordul elő, ahol a fenti kitaláltak folyamatot követve:

1. A külső alkalmazás hitelesíti az Azure Active Directory üzembe helyezve az Azure Blockchain munkaterület központi telepítésének részeként.
2. Egy tulajdonosi jogkivonatot, amely az API által érintett elküldhető fogadása engedéllyel rendelkező felhasználók
3. Külső alkalmazások hívások REST API használatával a tulajdonosi jogkivonatot.
4. A REST API lekérdezi a kérelmet az SQL-adatbázis adatait, és visszaadja az ügyfélnek.

## <a name="messaging-integration"></a>Üzenetküldési integráció

Üzenetküldési integrációs elősegíti a rendszerek, szolgáltatások és eszközök, ahol nincs interaktív bejelentkezést lehetséges vagy kívánatos való együttműködéshez. Üzenetküldési integrációs kétféle típusú üzenetek, azokat, hogy a tranzakciók hajtható végre egy elosztott főkönyvi és eseményeket, amelyek elérhetők, hogy a főkönyvi, amikor a tranzakciók került sor kérő összpontosít.

Üzenetküldési integráció a végrehajtási összpontosít, és a felhasználó létrehozása, a szerződés létrehozása és a szerződések tranzakciók végrehajtása, és elsősorban a tranzakciók figyelése *távfelügyeleti* háttérrendszerek.

Ez a szakasz ellenőrzi, hogy az azokat a vonatkozásait, az üzenet-alapú API-t, amelyek tranzakciók küldenek egy elosztott főkönyvi és azok, amelyek megfelelnek az eseményüzeneteket jelennek meg, ha az alapul szolgáló elosztott főkönyvi összpontosított minták.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Egyirányú esemény kézbesítése intelligens szerződések egy eseményközpontból, eseményfelhasználó 

Ebben a forgatókönyvben az esemény akkor következik be, belül egy intelligens szerződést, például az állapotváltozás vagy egy adott típusú tranzakció végrehajtása. Ez az esemény egy esemény rácsban, hogy az alárendelt fogyasztók és a fogyasztók szórási van, majd a megfelelő műveletet.

Ebben a forgatókönyvben például, hogy de tranzakció esetén ügyféllel volna értesítést műveletben, például egy SQL-adatbázis vagy a közös adatok szolgáltatás adatrögzítés is beletelhet. Ez az azonos minta munkaterület a következő adatokkal való feltöltéséhez a *lánc kikapcsolva* SQL-Adatbázist.

Egy másik lenne, ha egy intelligens szerződés adott állapotba, például amikor egy szerződés hiányzóra átkerül egy *OutOfCompliance*. Ha az állapotváltozás történik, azt is figyelmeztetés egy rendszergazda mobiltelefon küldendő.

![Egyirányú esemény kézbesítés](media/blockchain-workbench-integration-patterns/one-way-event-delivery.png)

Ez akkor fordul elő, ahol a fenti kitaláltak folyamatot követve:

-   Az intelligens szerződés állapotba egy új kerül, és a főkönyvi küld egy eseményt.
-   A főkönyvi fogad, és kézbesíti az esemény Azure Blockchain munkaterületet.
-   Az Azure Blockchain munkaterület elő van fizetve események a könyvelési, és megkapja az esemény.
-   Azure Blockchain munkaterület közzéteszi az esemény a esemény rács előfizetőknek.
-   Az esemény rács előfizetett külső rendszerekkel, az üzenet felhasználása, hajtanak végre a megfelelő elemek.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Egyirányú esemény egy üzenet kézbesítése külső rendszerekből származó intelligens szerződéshez

Is van olyan forgatókönyvekben, amelyek ellenkező zajlik. Ebben az esetben egy esemény érzékelő által generált, vagy egy külső rendszer és az esemény adatainak küldjön el egy intelligens szerződés.

Ilyenek például a kézbesítését adatok pénzpiaci, például a termékek, a készlet vagy az kötvények, azonban az intelligens szerződés.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Egy Azure-Blockchain munkaterület a várt formátumú közvetlen kézbesítés

Egyes alkalmazások integrálása az Azure Blockchain munkaterület beépített és közvetlenül állít elő, és a várt formátumú üzenetek küldése.

![Közvetlen kézbesítés](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Ez akkor fordul elő, ahol a fenti kitaláltak folyamatot követve:

-   Egy külső rendszerben, amely elindítja a létrehozást Azure Blockchain munkaterület üzenet kerül sor.
-   A külső rendszer kódok Ez az üzenet létrehozása egy ismert formátumban van, és küld ez közvetlenül a Service Bus.
-   Azure Blockchain munkaterület elő van fizetve események a Service bustól, és az üzenet beolvasása.
-   Azure Blockchain munkaterület kezdeményezi a főkönyvi hívása adatokat küldeni a a külső rendszer egy meghatározott szerződést.
-   Az üzenet fogadásakor a szerződés egy új állapotba átmenetek.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenetek kézbesítése Azure Blockchain munkaterület

Egyes rendszerek Azure Blockchain munkaterület által használt szabványos formátumú üzenetek továbbítására nem módosítható. Ezek esetekben meglévő mechanizmusok és üzenet ezen rendszerekből formátumok gyakran használható. Pontosabban ezek a rendszerek natív üzenet típusú átalakíthatók Logic Apps, az Azure Functions vagy egyéb egyéni kód segítségével egy üzenetküldési várt formátumok normál leképezést.

![Ismeretlen üzenet formátuma](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Ez akkor fordul elő, ahol a fenti kitaláltak folyamatot követve:

-   Egy külső rendszerben, amely elindítja a létrehozási üzenet kerül sor.
-   A logikai alkalmazást vagy egyéni kódot adott üzenetet kap, és irányítópulttá, szabványos Azure Blockchain munkaterület formázott üzenetre szolgál.
-   A logikai alkalmazást az átalakítani kívánt üzenetet küld közvetlenül a Service Bus.
-   Azure Blockchain munkaterület elő van fizetve események a Service bustól, és az üzenet beolvasása.
-   Azure Blockchain munkaterület kezdeményezi a főkönyvi hívása a külső rendszer adatokat küldeni a szerződés egy adott funkció.
-   A függvény végrehajtása, és általában módosítja az állapotát. Az állapotváltozás továbblép az üzleti munkafolyamat megjelennek az intelligens szerződést, egyéb funkciók megfelelő most futtatását engedélyezi.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Változik a vezérlőelem külső feldolgozni, és befejezési await

Forgatókönyvek, ahol egy intelligens szerződés le kell állítania belső végrehajtási és kézzel ki, hogy egy külső folyamatban van. Majd hajtsa végre külső folyamat, az intelligens szerződés és végrehajtási üzenet küldése majd folytatni az intelligens szerződés belül.

#### <a name="transition-to-the-external-process"></a>A külső folyamat átlépni

Ebben a mintában általában a következő módon segítségével van megvalósítva:

-   Az intelligens szerződés tér át egy adott állapotban. Ebben az állapotban, vagy nem vagy funkciók korlátozott számú hajtható végre, amíg egy külső rendszer végrehajtja a kívánt műveletet.
-   Az állapotváltozás eseményként van illesztett egy alsóbb rétegbeli fogyasztó számára.
-   Az alsóbb rétegbeli fogyasztói megkapja az esemény, és elindítja a külső kód végrehajtása.

![Átmenet vezérlő külső folyamat](media/blockchain-workbench-integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Az intelligens szerződésből vezérlő vissza

A külső rendszer testreszabása függően lehetséges, hogy vagy nem lehet elküldeni az üzeneteket, amely Azure Blockchain munkaterület vár formátumok egyikében. A külső rendszerek képességét alapján ezek az üzenetek létrehozásához határozza meg a következő két visszatérési elérési utak közül melyik megnyílik.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Egy Azure-Blockchain munkaterület a várt formátumú közvetlen kézbesítés

![](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Ebben a modellben a kommunikációt a szerződést és a későbbi állapotváltozás akkor fordul elő, a fenti következő feldolgozni a where -

-   Elérésekor, a befejezési vagy egy adott mérföldkő, a külső kód végrehajtása, egy eseményt küldeni a Service Bus Azure Blockchain munkaterület csatlakozik.

-   Azok a rendszerek, amely nem lehet közvetlenül leírtakon írni egy üzenetet, amely megfelel az API-t elvárásainak akkor alakul.

-   Az üzenet tartalma csomagolt, és ezután egy adott funkció intelligens egyezmény. Ez történik, a külső rendszer társított felhasználó nevében.

-   A függvény végrehajtása, és általában módosítja az állapot. Az állapotváltozás továbblép az üzleti munkafolyamat megjelennek az intelligens szerződést, egyéb funkciók megfelelő most futtatását engedélyezi.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenetek kézbesítése Azure Blockchain munkaterület

![Ismeretlen üzenet formátuma](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Ebben a modellben, ha szabványos formátumú üzenet nem küldhető el közvetlenül, a kommunikációt a szerződés és későbbi állapotváltozás akkor fordul elő, a fenti következő where feldolgozásához:

1.  Elérésekor, a befejezési vagy egy adott mérföldkő, a külső kód végrehajtása, egy eseményt küldeni a Service Bus Azure Blockchain munkaterület csatlakozik.
2.  A logikai alkalmazást vagy egyéni kódot adott üzenetet kap, és irányítópulttá, szabványos Azure Blockchain munkaterület formázott üzenetre szolgál.
3.  A logikai alkalmazást az átalakítani kívánt üzenetet küld közvetlenül a Service Bus.
4.  Azure Blockchain munkaterület elő van fizetve események a Service bustól, és az üzenet beolvasása.
5.  Azure Blockchain munkaterület kezdeményezi a főkönyvi hívása adatokat küldeni a a külső rendszer egy meghatározott szerződést.
6. Az üzenet tartalma csomagolt, és ezután egy adott funkció intelligens egyezmény. Ez történik, a külső rendszer társított felhasználó nevében.
7.  A függvény végrehajtása, és általában módosítja az állapot. Az állapotváltozás továbblép az üzleti munkafolyamat megjelennek az intelligens szerződést, egyéb funkciók megfelelő most futtatását engedélyezi.

## <a name="iot-integration"></a>Az IoT-integráció

Integráció forgatókönyve a telemetriai adatok, intelligens szerződésben érzékelők felvétel. Az érzékelők kézbesítette adatok alapján, intelligens szerződések sikerült tájékozott műveletek és módosítsa a szerződés állapotát.

Például egy olyan orvosi teherautó volna a hőmérséklet és 110 fok megugrik, ha azt is hatással lehet a orvosi hatékonyságát és nyilvános biztonsági problémát okozhat, ha nem észlelt, és a az ellátási lánc. Ha egy illesztőprogramot a car óránként 100 miles gyorsítani, az eredményül kapott érzékelő adatokat egy gyermektevékenysége a biztosítási szolgáltató biztosítási elindíthatja. Ha a car bérleti autót volt, a GPS-adatok esetleg jelzik, amikor az illesztőprogram merült fel egy geográfiai a bérleti szerződés hatálya kívül, és a szövegminősítési díjat számítanak.

A kérdés, hogy ilyen érzékelő is lehet olyan adatok állandó szinten, ezért nem küldhet, hogy az adatok egy intelligens szerződés. Egy tipikus megoldás, egy másodlagos tároló összes üzenetek kézbesítése közben a blockchain küldött üzenetek számát. Például a óránként egyszer, és ha az egyeztetett kívül esik a benne lévő érték csak rögzített időközönként, például a Beérkezett üzenetek kézbesítése egy intelligens szerződés tartomány esetén. Eltérések kívül eső értékeket ellenőrzése és ellenőrzi, hogy a szerződések üzleti logikát, vonatkozó adatokat kapott és hajtotta végre. Az érték a időközönként ellenőrzése megerősíti, hogy az érzékelő továbbra is van-e jelentés. Minden adat érkezik egy másodlagos jelentéskészítési tárolóba szélesebb körű jelentéskészítési, elemzés és a gépi tanulás engedélyezése. Amikor első érzékelő körébe tartozó GPS nem lehet, hogy szükséges egy intelligens szerződés percenként, például sikerült érdekes adatokkal, jelentéseket vagy leképezési útvonalak használhatók biztosítanak.

Az IoT hubbal általában történik, az Azure platformon eszközökkel való integráció. Az IoT-központ lehetővé teszi a tartalom alapján üzenetek, és lehetővé teszi, hogy a fent leírt ábrázolási.

![Az IoT-üzenetek](media/blockchain-workbench-integration-patterns/iot.png)

A fenti folyamatot egy minta ábrázol, ez a:

-   Egy eszköz közvetlenül, sem az IoT-központ mező átjáró segítségével kommunikál.
-   Az IoT-központ az üzeneteket fogadja, és az üzenetek alapján létrehozott útvonalak kiértékeli például ellenőrizze, hogy az üzenet tartalma. *Az érzékelő jelentést készít egy nagyobb, mint 50 fok hőmérséklet?*
-   Az IoT Hub egy meghatározott Service Bus az útvonal a megadott feltételeknek megfelelő üzenetek küldése.
-   Logikai alkalmazás vagy többi kód figyeli a Service Bus, amely az IoT-központ létrehozva az útvonal.
-   A logikai alkalmazást vagy más kód beolvassa és átalakítás az üzenet formátuma nem ismert.
-   Az átalakított üzenet most egy szabványos formátumban továbbítja a Service Bus a Azure Blockchain munkaterület.
-   Azure Blockchain munkaterület elő van fizetve események a Service bustól, és az üzenet beolvasása.
-   Azure Blockchain munkaterület kezdeményezi a főkönyvi hívása adatokat küldeni a a külső rendszer egy meghatározott szerződést.
-   Az üzenet fogadásakor, a szerződés kiértékeli az adatok, és előfordulhat, hogy módosítsa az állapotot, például az értékelés eredményét alapján magas hőmérséklet, módosítsa az állapotot a *kívüli megfelelőségi*.

## <a name="data-integration"></a>Adatintegráció

Mellett REST API üzenet-alapú Azure Blockchain munkaterület is hozzáférést biztosít egy SQL-adatbázis a alkalmazás és a szerződés metaadatok, valamint a tranzakciós adatokat elosztott főkönyvi feltöltve.

![Adatintegráció](media/blockchain-workbench-integration-patterns/data-integration.png)

Az adatok integrációra jól ismert:

-   Az Azure Blockchain munkaterület alkalmazások, a munkafolyamatok, a szerződések és a tranzakciók metaadatokat tárol a rendes működési viselkedését részeként.
-   Külső rendszerekhez vagy eszközök adja meg egy vagy több párbeszédpanelek a adatait gyűjti össze az adatbázisban, például az adatbázis-kiszolgáló nevét, az adatbázis nevét, a típusú hitelesítés, a bejelentkezési hitelesítő adatok, és melyik adatbázis magukat, hogy megtekinti megkönnyítése érdekében.
-   Lekérdezések írja elleni alárendelt használat megkönnyítése érdekében az SQL-adatbázis nézetek külső rendszerekkel, az szolgáltatások, a jelentéskészítés, a fejlesztői eszközök és a vállalati termelékenységi eszközöket.

## <a name="storage-integration"></a>Tároló integrációja

Számos forgatókönyv attestable fájlokat ment keresztül, és szükség lehet szükség. Több okból lesz egy blockchain a fájlok nem megfelelő. Ehelyett egy közös megoldás, hajtsa végre egy titkosítási kivonatát (például SHA-256) fájl elleni és osszanak meg, hogy egy elosztott főkönyvi kivonatát. A kivonatoló végrehajtása egy későbbi időpontban újra ugyanazt az eredményt kell visszaadnia. Ha a fájl módosult, akkor is, ha a kép csak egy képpontos módosul, a kivonatoló vissza egy másik értéket.

![Tároló integrációja](media/blockchain-workbench-integration-patterns/storage-integration.png)

A minta implementálhatók ahol:

-   A külső rendszerek továbbra is fennáll, a tárolási mechanizmus, például az Azure Storage fájlt.
-   A kulcskivonat a fájl vagy a fájl jön létre, és kapcsolódó metaadatok azonosítója a tulajdonosa, az URL-cím, ahol a fájl akkor található, például stb.
-   A kivonatot, és minden metaadatot egy függvénynek küldött intelligens adategyezmény, például a *FileAdded*
-   A későbbiekben a fájl- és metaadatok is kell újra kivonatolt és összehasonlítja a főkönyvi a tárolt értékek.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>A REST és üzenet API-k segítségével integrációs minták végrehajtására vonatkozó Előfeltételek

Lehetővé teszi a egy külső rendszer vagy az eszköz mostantól az intelligens egyezmény a többi vagy az üzenet API használatával kommunikál, a következőknek kell teljesülniük-

1. Az Azure Active Directoryban a konzorciumnak fiók jön létre, amely a külső rendszer vagy az eszköz jelöli.
2. Az Azure Blockchain munkaterület alkalmazáshoz megfelelő intelligens szerződés(ek) funkciói meghatározni, hogy a külső rendszer vagy az eszköz események fogadásához.
3. Az alkalmazás konfigurációs fájljának az intelligens adategyezmény tartalmazza a szerepkör, amely a rendszer vagy az eszköz lesz hozzárendelve.
4. Az alkalmazás konfigurációs fájljának az intelligens adategyezmény határozza meg, amely jelzi, hogy ez a függvény a meghatározott szerepkör meghívható.
5. Az alkalmazás konfigurációs fájljában és az intelligens szerződések Azure Blockchain munkaterület feltöltése.

Az alkalmazás a feltöltést követően a külső rendszer az Azure Active Directory-fiókot a szerződés és a kapcsolódó szerepkör van hozzárendelve.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Külső rendszer integrációs adatfolyamok integrációs kód írása előtt tesztelése 

Ezáltal a külső rendszerekkel való integrálásához képesek számos forgatókönyv kulcs követelményt. Kívánatos tudni intelligens Szerződés tervezési előzetes vagy a kód fejlesztéséhez párhuzamosan a külső rendszerekkel való integrálásához ellenőrzése.

Az Azure Active Directory (Azure AD) használatát jelentősen felgyorsíthatja a fejlesztés és idő érték. Pontosabban a külső rendszerek kód integrálva is igénybe vehet a nem triviális időn. Azure AD használatával és az automatikus létrehozása Azure Blockchain munkaterület által UX, ez is a fejlesztők olyan jelentkezzen be, hogy külső rendszer munkaterületet, és vár, hogy külső rendszer keresztül a UX értékek feltöltése Ez lehetővé teszi, hogy gyorsan és az ötletek koncepció környezet vagy a igazolása vagy integrációs kód írása a külső rendszerek párhuzamosan ellenőrzése.
