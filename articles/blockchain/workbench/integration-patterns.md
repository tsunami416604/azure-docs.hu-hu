---
title: Az intelligens szerződés integrációs minták az Azure Blockchain Workbench használatával
description: Az intelligens szerződés integrációs minták az Azure Blockchain Workbench alkalmazásban áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 11e0e1436e3f640c30fec5e8d6fd9ca10adbd707
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330467"
---
# <a name="smart-contract-integration-patterns"></a>Az intelligens szerződés integrációs minták

Az intelligens szerződések gyakran képviseli egy üzleti munkafolyamatot, amely a külső rendszerek és eszközök való integrálásához szükséges.

Ezek a munkafolyamatok követelményeinek közé tartozik egy külső rendszer, a szolgáltatás vagy az eszköz adatait tartalmazó tranzakciókat az elosztott Főkönyv kezdeményezéséhez szükséges. Az elosztott Főkönyv intelligens szerződések származó eseményekre reagálni külső rendszerekkel is kell.

A REST API-t és az üzenetkezelési integrációs küld tranzakciók külső rendszerekből. az intelligens szerződések tartalmazza az Azure Blockchain Workbench alkalmazásban. Eseményértesítések alapján módosításokat végzett az alkalmazáson belül, a külső rendszerekhez is küld.

Az adatintegrációs forgatókönyveket Azure Blockchain Workbench használatával, amely a blockchain és az alkalmazások és az intelligens szerződések metaadatai tranzakciós adatait kombinációját egyesítése adatbázisnézeteivel készletét tartalmazza.

Emellett bizonyos helyzetekben, kapcsolódnak, adja meg a tanúsítványlánc vagy adathordozóra, például megkövetelheti a dokumentumok az integráció. Azure Blockchain Workbench használatával nem ad meg API-hívások kezeléséhez közvetlenül a dokumentumokat, míg dokumentumok beépíthető a blockchain-alkalmazások. Ez a szakasz a mintát is tartalmaz.

Ez a szakasz tartalmazza a minták a végpontok közötti megoldások megvalósításának egyes Integrációk azonosított.

## <a name="rest-api-based-integration"></a>REST API-alapú integrációs

Képességek az Azure Blockchain Workbench használatával létrehozott webes alkalmazásban a REST API-n keresztül érhetők el. Többek között az Azure Blockchain Workbench feltöltése, konfigurációs és felügyeleti alkalmazások, a tranzakciók küldő alkalmazás metaadatok és a könyvelési adatok lekérdezését, és elosztott Főkönyv.

Interaktív ügyfélprogramok, mint például a webes, mobil-és bot alkalmazások elsősorban a REST API-t.

Ez a szakasz megvizsgálja a REST API által küldött tranzakció egy elosztott Főkönyv és minták a tranzakciókkal kapcsolatos adatokat lekérdezni az Azure Blockchain Workbench szempontjai minták *lánc kikapcsolva* SQL-adatbázis.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Egy külső rendszer tranzakciók küld egy elosztott Főkönyv

Az Azure Blockchain Workbench REST API tranzakciókat hajthat végre egy elosztott Főkönyv hitelesített kérelmeket küld.

![Tranzakciók küld egy elosztott Főkönyv](./media/integration-patterns/send-transactions-ledger.png)

Korábban, ahol a folyamat használata kitaláltak tranzakciók végrehajtása történik:

-   A külső alkalmazás az Azure Active Directory, az Azure Blockchain Workbench üzembe helyezés részeként üzembe helyezett hitelesíti magát.
-   Jogosult felhasználók kapnak, amely az API-hoz küldhető kérések tulajdonosi jogkivonattal.
-   Külső alkalmazások győződjön meg arról, a REST API-hívások tulajdonosi jogkivonat segítségével.
-   A REST API-csomagot a kérelem egy üzenetnek számít, és elküldi azokat a Service Bus. Itt beolvasott, jelentkezett, és a megfelelő elosztott Főkönyv küldött.
-   A REST API egy kérést az Azure Blockchain Workbench SQL DB jegyezze fel a kérelmet, és létrehozza a jelenlegi üzembe helyezési állapotát.
-   Az SQL DB adja vissza a kiépítési állapotot, és az API-hívás az azonosítója, amelynek a neve, a külső alkalmazásnak adja vissza.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>A Blockchain Workbenchet metaadatokat, és elosztott Főkönyv tranzakciók

Az Azure Blockchain Workbench REST API hitelesített kérelmeket küld az elosztott Főkönyv intelligens szerződés végrehajtásával kapcsolatos lekérdezés részletei.

![Querying metadata](./media/integration-patterns/querying-metadata.png)

Korábban, ahol a folyamat használata kitaláltak lekérdezése történik:

1. A külső alkalmazás az Azure Active Directory, az Azure Blockchain Workbench üzembe helyezés részeként üzembe helyezett hitelesíti magát.
2. Jogosult felhasználók kapnak, amely az API-hoz küldhető kérések tulajdonosi jogkivonattal.
3. Külső alkalmazások győződjön meg arról, a REST API-hívások tulajdonosi jogkivonat segítségével.
4. A REST API lekérdezi az SQL DB-ből a kérelem adatai, és visszaadja az ügyfélnek.

## <a name="messaging-integration"></a>Üzenetkezelési integráció

Integrációs üzenetkezelés lehetővé teszi a rendszerek, szolgáltatások és eszközök, ahol egy interaktív bejelentkezési nem lehetséges vagy nem kívánatos-szal. Üzenetkezelési integrációs kétféle típusú üzenetek összpontosít: kérő üzenetek tranzakciók hajtható végre az elosztott Főkönyv és eseményeket a Főkönyv által elérhetővé tett, ha a tranzakció került sor.

Üzenetkezelési integrációs összpontosít a végrehajtási és felhasználó létrehozása, a szerződés-létrehozási és szerződések a tranzakciók végrehajtási kapcsolatos tranzakciók figyelésére, és elsősorban a *távfelügyelt* háttérrendszerekhez.

Ez a szakasz az üzenet-alapú API, amely a tranzakciók küldeni egy elosztott Főkönyv és a mintákat, amelyek az alapul szolgáló elosztott Főkönyv által elérhetővé tett eseményüzenetek szempontjai minták megvizsgál.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Egyirányú eseménykézbesítés intelligens szerződések egy eseményközpontból, eseményfelhasználó 

Ebben a forgatókönyvben egy esemény fordul elő egy intelligens szerződést, például az állapotváltozás vagy egy adott típusú tranzakciók végrehajtása. Ez az esemény van egy Event Grid alsóbb rétegbeli fogyasztók számára, és ezek a felhasználók szórási, majd a megfelelő műveleteket.

Ebben a forgatókönyvben egy példát az, hogy egy tranzakció esetén a fogyasztó szeretne kapni, és a művelet, például az információk rögzítésére egy SQL-adatbázis vagy a Common Data Service is igénybe vehet. Ebben a forgatókönyvben a Workbench a következő adatokkal való feltöltéséhez ugyanazt a mintát a *lánc kikapcsolva* SQL-Adatbázissal.

Egy másik lenne, ha egy intelligens szerződés egy adott állapotban, például amikor egy szerződés hiányzóra átkerül egy *OutOfCompliance*. Ezt akkor fordul elő, amikor azt válthatja ki egy riasztást küldeni a mobiltelefonjára rendszergazdai szerepkörrel.

![Egyirányú eseménykézbesítés](./media/integration-patterns/one-way-event-delivery.png)

Ebben a forgatókönyvben a korábban, ahol a folyamat használata kitaláltak történik:

-   Az intelligens szerződés átkerül az új állapot, és eseményt küld az a Főkönyv.
-   A Főkönyv fogadja, és az esemény továbbítja az Azure Blockchain Workbench használatával.
-   Az Azure Blockchain Workbench a könyvelési események által előfizetett, és az eseményt kap.
-   Az Azure Blockchain Workbench az Event Grid-előfizetők tesz közzé az eseményt.
-   Az Event Grid előfizetett külső rendszerekkel, az üzenet használják, és végezze el a megfelelő műveleteket.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Egyirányú eseménykézbesítés az üzenet egy külső rendszer egy intelligens szerződés

Ez a forgatókönyv jut el az ellenkező irányba is van. Ebben az esetben egy érzékelő által létrejön egy esemény, vagy egy külső rendszer és az adatok eredő küldjön el egy intelligens szerződés.

Ilyenek például a szállítást az adatok a pénzügyi piacok számára, például az áruk, készlet vagy kötvényekről, az árak egy intelligens szerződés.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain Workbench használatával a várt formátumban közvetlen szállítása

Egyes alkalmazások integrálása az Azure Blockchain Workbench használatával készültek, és közvetlenül állít elő, és üzeneteket küldhet a várt formátumú.

![Közvetlen kézbesítés](./media/integration-patterns/direct-delivery.png)

A kézbesítési következik be, a folyamat segítségével kitaláltak korábban a where:

-   Egy esemény egy külső rendszer, amely egy üzenetet létrehozása az Azure Blockchain Workbench használatával történik.
-   A külső rendszer ezt az üzenetet létrehozni egy ismert formátumban írt kódot tartalmaz, és közvetlenül a Service Bus küldi.
-   Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
-   Az Azure Blockchain Workbench indítja el a Főkönyv hívást a külső rendszer adatokat küldeni egy adott szerződést.
-   Az üzenet megérkezésekor a szerződés egy új állapot értékre vált.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítési az Azure Blockchain Workbench használatával

Egyes rendszerek nem lehet módosítani a kézbesíti az üzeneteket az Azure Blockchain Workbench által használt szabványos formátumban. Ezen esetekben meglévő mechanizmusok és üzenet ezen rendszerekből formátumban is használható. Pontosabban ezek a rendszerek natív üzenet típusú átalakíthatók leképezése a standard szintű üzenetkezelés várt formátumok egyikét a Logic Apps, az Azure Functions vagy más egyéni kód használatával.

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ez akkor fordul elő, a folyamat segítségével kitaláltak korábban a where:

-   Egy külső rendszer üzenet létrehozását kiváltó esemény történik.
-   Egy logikai alkalmazásban vagy egyéni kódot üzenetet fogadó és a egy standard Azure Blockchain Workbench használatával formázott üzenet alakítása szolgál.
-   A logikai alkalmazás közvetlenül a Service Bus, az átalakított üzenetet küld.
-   Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
-   Az Azure Blockchain Workbench a külső rendszer adatokat küldeni egy adott funkciót, a szerződés a Főkönyv hívást kezdeményez.
-   A függvény hajt végre, és az állapot általában módosítja. Az állapotváltozás helyezi előre az üzleti munkafolyamatot a intelligens szerződést, más függvények, most már megfelelő futtatandó engedélyezése megjelennek.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Külső vezérlőelem transitioning dolgozza fel, és await befejezése

Ha egy intelligens szerződés le kell állítania belső végrehajtását és a egy külső folyamatba való átadása forgatókönyv közül választhat. Majd hajtsa végre külső folyamat, az intelligens szerződést, és a végrehajtási üzenet küldése majd folytatni az intelligens szerződés belül.

#### <a name="transition-to-the-external-process"></a>Áttérés a külső folyamat

Ez a minta jellemzően a következő módon használatával lett megvalósítva:

-   Az intelligens szerződés átmeneteket egy adott állapotba. Ebben az állapotban vagy nem vagy a functions korlátozott számú hajtható végre, amíg egy külső rendszer végrehajt egy kívánt műveletet.
-   Az állapotváltozás eseményként van illesztett egy alsóbb rétegbeli fogyasztó számára.
-   Az alsóbb rétegbeli fogyasztói az eseményt kap, és a külső kód indítja.

![Átmenet vezérlőelem külső folyamat](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Az intelligens szerződések vezérlő vissza

A külső rendszer testre szabhatók, attól függően lehet, hogy vagy nem lehet elküldeni az üzeneteket az Azure Blockchain Workbench vár standard-formátumok egyikében. Alapján hozzon létre egyet külső rendszerek lehetővé teszi üzenetek határozza meg, amely a következő két adja vissza az elérési út használatban van.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain Workbench használatával a várt formátumban közvetlen szállítása

![](./media/integration-patterns/direct-delivery.png)

Ebben a modellben a kommunikációt a szerződés és a későbbi állapotváltozás akkor fordul elő, az előző folyamat során a következő ahol -

-   Elérésekor, a befejezési vagy külső kód végrehajtását az adott mérföldkő, a Service Bus, az Azure Blockchain Workbench használatával csatlakozik egy eseményt küld.

-   A rendszereken, nem közvetlenül megfelelően adaptálhassa írjon egy üzenetet, amely megfelel az API-t az elvárásainak átalakítva.

-   Az üzenet tartalma csomagolt, és a egy adott funkciót a intelligens szerződés küldött. A kézbesítési történik, a külső rendszer társított felhasználó nevében.

-   A függvény hajt végre, és az állapot általában módosítja. Az állapotváltozás helyezi előre az üzleti munkafolyamatot a intelligens szerződést, más függvények, most már megfelelő futtatandó engedélyezése megjelennek.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítési az Azure Blockchain Workbench használatával

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ebben a modellben, ahol egy szabványos formátumra nem küldhető közvetlenül, a szerződés a kommunikációs és későbbi állapot módosításai a következő az előző where feldolgozásához:

1.  Elérésekor, a befejezési vagy külső kód végrehajtását az adott mérföldkő, a Service Bus, az Azure Blockchain Workbench használatával csatlakozik egy eseményt küld.
2.  Egy logikai alkalmazásban vagy egyéni kódot üzenetet fogadó és a egy standard Azure Blockchain Workbench használatával formázott üzenet alakítása szolgál.
3.  A logikai alkalmazás közvetlenül a Service Bus, az átalakított üzenetet küld.
4.  Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
5.  Az Azure Blockchain Workbench indítja el a Főkönyv hívást a külső rendszer adatokat küldeni egy adott szerződést.
6. Az üzenet tartalma csomagolt, és a egy adott funkciót a intelligens szerződés küldött. A kézbesítési történik, a külső rendszer társított felhasználó nevében.
7.  A függvény hajt végre, és az állapot általában módosítja. Az állapotváltozás helyezi előre az üzleti munkafolyamatot a intelligens szerződést, más függvények, most már megfelelő futtatandó engedélyezése megjelennek.

## <a name="iot-integration"></a>IoT-integráció

Integrációs forgatókönyve a telemetriai adatok, érzékelők intelligens szerződés keretében történő felvételére. Érzékelők által továbbított adatok alapján, az intelligens szerződések sikerült megalapozott műveleteket és módosítsa a szerződés állapotát.

Például ha egy orvoslás továbbítása teherautó 110 fokban megugrik a hőmérséklet, azt hatással lehet a orvoslás hatékonyságát és nyilvános biztonsági problémát okozhat, ha nem észlelt, és a az ellátási lánc. Egy illesztőprogram óránként 100 mérföldre, hogy gyorsított, ha az eredményül kapott érzékelőitől válthatja ki a biztosítási szolgáltató biztosítási lemondás. Ha az autó bérleti autót volt, a GPS-adatok esetleg azt jelzik, ha az illesztőprogram a hatálya alá bérleti szerződés keretein belül egy térségen kívül történt, és egy napján belül pótdíj díja szerint számítjuk fel.

A kihívás abban áll, hogy érzékelőktől is lehet továbbítása adatok állandó történik, és nem célszerű minden adatot küld egy intelligens szerződést. Egy jellemző példán, hogy korlátozza az összes üzenet adatbáziscsoportok másodlagos áruházban a blockchain küldött üzenetek számát. Például a időszakonként csak állandó, például óránként egyszer, és ha tartalmazott értéket egy egyeztetett kívül esik a Beérkezett üzenetek továbbítására tartomány egy intelligens szerződés alapján. Tolerancia kívül eső értékeket ellenőrzése, biztosítja, hogy a szerződések üzleti logikát, vonatkozó adatokat fogadott, és végre. Az érték ellenőrzése a időközönként megerősíti, hogy az érzékelő még küld jelentést. Minden adatot megkap egy másodlagos kimutatási áruházban szélesebb körű jelentéskészítési, elemzési és gépi tanulási engedélyezéséhez. Például bár a érzékelőinek adatai a GPS-adatok lekérdezése nem szükséges egy intelligens szerződés percenként, nyújtani érdekesebb adatokat megjelölheti használható jelentések vagy hozzárendelési útvonalak.

Az Azure platformon integráció az eszközök általában történik az IoT hubbal. Az IoT Hub biztosít a tartalom alapján üzenetek útválasztását, és lehetővé teszi, hogy a korábban leírt ábrázolási.

![IoT-üzenetekhez](./media/integration-patterns/iot.png)

A folyamat egy minta ábrázolja:

-   Egy eszköz közvetlenül vagy egy helyszíni átjáró az IoT hub-n keresztül kommunikál.
-   Az IoT Hub fogadja az üzeneteket, és kiértékeli az üzenetek elleni létre útvonalakat például ellenőrizze, hogy az üzenet tartalmát. *Az érzékelő jelentést készít egy nagyobb, mint 50 fok hőmérséklet?*
-   Az IoT Hub küldi az üzeneteket, amelyek megfelelnek a feltételeknek, egy meghatározott Service Bus, az útvonal.
-   Egy logikai alkalmazásban vagy más kódot figyeli a Service Bus, amely az IoT Hub hozott létre az útvonal.
-   A logikai alkalmazásban vagy más kód kérdezi le, és a egy ismert formátumba az üzenet átalakítása.
-   Az átalakított üzenetet most egy szabványos formátumra van küldeni a Service Bus az Azure Blockchain Workbench használatával.
-   Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
-   Az Azure Blockchain Workbench indítja el a Főkönyv hívást a külső rendszer adatokat küldeni egy adott szerződést.
-   Az üzenet megérkezésekor a szerződés kiértékeli az adatokat, és előfordulhat, hogy az állapotváltozáshoz. például ezen értékelés eredménye alapján a legmagasabb hőmérséklet, állapotának *kívüli megfelelőségi*.

## <a name="data-integration"></a>Adatintegráció

REST és üzenetalapú API-val az Azure Blockchain Workbench is nyújt hozzáférést egy SQL-adatbázisba beírja az alkalmazás és a szerződés metaadatok, valamint az elosztott főkönyvek tranzakciós adatait.

![Adatintegráció](./media/integration-patterns/data-integration.png)

Az adatok integrációját a jól ismert:

-   Az Azure Blockchain Workbench a rendes működési viselkedés részét képező alkalmazások, munkafolyamatok, szerződések és tranzakciók metaadatokat tárol.
-   Külső rendszerekhez vagy eszközök adjon meg egy vagy több párbeszédpanelek megkönnyítése érdekében a gyűjteményhez tartozó információt tárol az adatbázisban, például az adatbázis-kiszolgáló nevét, az adatbázis neve, a típusú hitelesítés, bejelentkezési hitelesítő adatait, és melyik adatbázist megtekinti vehető igénybe.
-   Lekérdezések külső rendszerekkel, az szolgáltatások, a jelentéskészítés, a fejlesztői eszközök és a vállalati hatékonyságnövelő eszközök által írt szemben az SQL database nézetek alsóbb rétegbeli használat megkönnyítése érdekében.

## <a name="storage-integration"></a>Storage-integráció

Számos forgatókönyv attestable fájlok átfogó szükség lehet szükség. Több okból nem helyénvaló helyezi a blockchain a fájlokat. Ehelyett egy általánosan használt megközelítés, hogy hajtsa végre a fájl (például SHA-256) titkosítási kivonatot, és megoszthatja a kivonatot az elosztott Főkönyv. A kivonatoló végrehajtása egy későbbi időpontban újra ugyanazt az eredményt adja vissza. Ha a fájlt módosítanak, akkor is, ha csak egy képpontos módosul a képet, a kivonatot egy másik értéket adja vissza.

![Storage-integráció](./media/integration-patterns/storage-integration.png)

A minta implementálhatók ahol:

-   Egy külső rendszer továbbra is fennáll, egy olyan tárolási mechanizmus, például az Azure Storage-fájlra.
-   Kivonatot a vagy a fájl hozza létre, és a kapcsolódó metaadat-azonosítója a tulajdonosa, az URL-címet, ahol a fájl megtalálható, például stb.
-   A kivonatot, és az összes metaadat a érkezik egy intelligens szerződés, a függvény például *FileAdded*
-   A jövőben a fájl- és metaadatok lehetnek újra kivonatolt és összehasonlítja az értékeket a Főkönyv tárolja.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Integrációs minták a REST és üzenet API-k végrehajtására vonatkozó Előfeltételek

Megkönnyítése érdekében arra, hogy az egy külső rendszer vagy eszköz a REST vagy az üzenet API használatával intelligens szerződés kommunikál, a következőknek kell teljesülniük-

1. Az Azure Active Directoryban a consortium, a fiók jön létre, amely a külső rendszer vagy az eszköz jelöli.
2. Egy vagy több, az Azure Blockchain Workbench alkalmazás megfelelő intelligens szerződések rendelkezik a külső rendszer vagy eszköz az események fogadására meghatározott függvényeket.
3. Az alkalmazás konfigurációs fájljának az intelligens szerződés tartalmazza a szerepkört, amely a rendszer vagy az eszköz van hozzárendelve.
4. Az alkalmazás konfigurációs fájljának az intelligens szerződéshez azonosítja a amely megállapítja, hogy a meghatározott szerepkör hívja meg ezt a funkciót.
5. Az alkalmazás konfigurációs fájljának és a hozzá tartozó smart contracts töltenek fel az Azure Blockchain Workbench használatával.

Miután feltöltötte az alkalmazást, az Azure Active Directory-fiókot a külső rendszer hozzá van rendelve a szerződés és a kapcsolódó szerepkör.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Integrációs kód írása előtt a külső rendszer az integrációs folyamatok tesztelése 

Külső rendszerek integrálása akkor számos forgatókönyv-kulcs szükséges. Kívánatos képesnek lennie ellenőriznie az intelligens szerződés Tervező korábbi vagy a kód fejlesztéséhez párhuzamosan külső rendszerekkel való integrációt segítik.

Az Azure Active Directory (Azure AD) használatát is jelentősen gyorsabban fejlesztői hatékonyság és az idő értékre. Pontosabban a kódot és egy külső rendszer közötti integráció is igénybe vehet egy nem triviális mennyi ideig. Azure AD-vel és az automatikus létrehozása az Azure Blockchain Workbench UX, engedélyezheti a fejlesztők számára, hogy jelentkezzen be a külső rendszer Blockchain Workbenchet, és töltse fel az értékeket a külső rendszer a UX-n keresztül Gyors fejlesztése és érvényesítése ötleteit egy megvalósíthatósági tesztkörnyezetben, a külső rendszerekhez készült integrációs kód írása előtt.
