---
title: Az intelligens szerződés integrációs minták az Azure Blockchain Workbench használatával
description: Az intelligens szerződés integrációs minták az Azure Blockchain Workbench alkalmazásban áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e1bc16d32167d62d5f66f64bb383fcceeb79eb5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267147"
---
# <a name="smart-contract-integration-patterns"></a>Az intelligens szerződés integrációs minták

Az intelligens szerződések gyakran képviseli egy üzleti munkafolyamatot, amely a külső rendszerek és eszközök való integrálásához szükséges.

Ezek a munkafolyamatok követelményeinek közé tartozik egy külső rendszer, a szolgáltatás vagy az eszköz adatait tartalmazó tranzakciókat az elosztott Főkönyv kezdeményezéséhez szükséges. Az elosztott Főkönyv intelligens szerződések származó eseményekre reagálni külső rendszerekkel is kell.

A REST API és üzenetküldési integrációja lehetővé teszi, hogy mind a külső rendszerek tranzakciók küldhet az Azure Blockchain Workbench alkalmazás szerepel az intelligens szerződések, valamint eseményértesítések küldése külső rendszerekkel, amelyek végbement változások alapján Helyezze el az alkalmazáson belül.

Az adatintegrációs forgatókönyveket Azure Blockchain Workbench használatával, amely a blockchain és az alkalmazások és az intelligens szerződések metaadatai tranzakciós adatait kombinációját egyesítése adatbázisnézeteivel készletét tartalmazza.

Emellett bizonyos helyzetekben, kapcsolódnak, adja meg a tanúsítványlánc vagy adathordozóra, például megkövetelheti a dokumentumok az integráció. Azure Blockchain Workbench használatával nem ad meg API-hívások kezeléséhez közvetlenül a dokumentumokat, míg dokumentumok beépíthető az Azure Blockchain-alkalmazások. Ez a szakasz a mintát is tartalmaz.

Ez a szakasz tartalmazza a minták a végpontok közötti megoldások megvalósításának egyes Integrációk azonosított.

## <a name="rest-api-based-integration"></a>REST API-alapú integrációs

Képességek az Azure Blockchain Workbench használatával létrehozott webes alkalmazásban a REST API-n keresztül érhetők el. Többek között az Azure Blockchain Workbench feltöltése, konfigurációs és felügyeleti alkalmazások, a tranzakciók küldő alkalmazás metaadatok és a könyvelési adatok lekérdezését, és elosztott Főkönyv.

Interaktív ügyfélprogramok, mint például a webes, mobil-és bot alkalmazások elsősorban a REST API-t.

Ez a szakasz megvizsgálja a mintákat, amelyek tranzakciók küldenek egy elosztott Főkönyv, valamint azokat, amelyeket az Azure Blockchain Workbench tranzakciókkal kapcsolatos adatok lekérdezése a REST API szempontjai *lánc kikapcsolva* SQL-adatbázis.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Egy külső rendszer tranzakciók küld egy elosztott Főkönyv

Az Azure Blockchain Workbench REST API lehetővé teszi az elosztott Főkönyv tranzakciók végrehajtásához hitelesített kérelmek küldésére.

![Tranzakciók küld egy elosztott Főkönyv](./media/integration-patterns/send-transactions-ledger.png)

Ez akkor fordul elő, ahol a folyamat, a fenti kitaláltak segítségével:

-   A külső alkalmazás az Azure Active Directory, az Azure Blockchain Workbench üzembe helyezés részeként üzembe helyezett hitelesíti magát.
-   Jogosult felhasználók kapnak, amely az API-hoz küldhető kérések tulajdonosi jogkivonattal.
-   Külső alkalmazások győződjön meg arról, a REST API-hívások tulajdonosi jogkivonat segítségével.
-   A REST API-csomagot a kérelem egy üzenetnek számít, és elküldi azokat a Service Bus. Itt azt fogja beolvasni, aláírt, és a megfelelő elosztott Főkönyv küldött.
-   A REST API egy kérést az Azure Blockchain Workbench SQL DB jegyezze fel a kérelmet, és létrehozza a jelenlegi üzembe helyezési állapotát.
-   Az SQL DB adja vissza a kiépítési állapotot, és az API-hívás az azonosítója, amelynek a neve, a külső alkalmazásnak adja vissza.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>A Blockchain Workbenchet metaadatokat, és elosztott Főkönyv tranzakciók

Az Azure Blockchain Workbench REST API lehetővé teszi a hitelesített kéréseket küldjön be egy elosztott Főkönyv intelligens szerződés végrehajtásával kapcsolatos lekérdezés részletei.

![Querying metadata](./media/integration-patterns/querying-metadata.png)

Ez akkor fordul elő, ahol a folyamat, a fenti kitaláltak segítségével:

1. A külső alkalmazás az Azure Active Directory, az Azure Blockchain Workbench üzembe helyezés részeként üzembe helyezett hitelesíti magát.
2. Jogosult felhasználók kapnak, amely az API-hoz küldhető kérések tulajdonosi jogkivonattal.
3. Külső alkalmazások győződjön meg arról, a REST API-hívások tulajdonosi jogkivonat segítségével.
4. A REST API lekérdezi az SQL DB-ből a kérelem adatai, és visszaadja az ügyfélnek.

## <a name="messaging-integration"></a>Üzenetkezelési integráció

Integrációs üzenetkezelés lehetővé teszi a rendszerek, szolgáltatások és eszközök, ahol egy interaktív bejelentkezést nincs lehetséges vagy nem kívánatos-szal. Üzenetkezelési integrációs kétféle típusú üzenetek, azokat, amelyek kérik, hogy tranzakciók hajtható végre az elosztott Főkönyv és eseményeket, amikor a tranzakció került sor a Főkönyv által feltárt összpontosít.

Üzenetkezelési integrációs összpontosít a végrehajtási és felhasználó létrehozása, a szerződés-létrehozási és szerződések a tranzakciók végrehajtási kapcsolatos tranzakciók figyelésére, és elsősorban a *távfelügyelt* háttérrendszerekhez.

Ez a szakasz az üzenet-alapú API, amely a tranzakciók egy elosztott Főkönyv és azok, amelyek az alapul szolgáló elosztott Főkönyv által elérhetővé tett eseményt üzenetek küldése szempontjai minták megvizsgálja.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Egyirányú eseménykézbesítés intelligens szerződések egy eseményközpontból, eseményfelhasználó 

Ebben a forgatókönyvben egy esemény fordul elő egy intelligens szerződést, például az állapotváltozás vagy egy adott típusú tranzakciók végrehajtása. Ez az esemény van egy Event Grid alsóbb rétegbeli fogyasztók számára, és ezek a felhasználók szórási, majd a megfelelő műveleteket.

Ebben a forgatókönyvben egy példát az, hogy egy tranzakció esetén a fogyasztó szeretne kapni, és a művelet, például az információk rögzítésére egy SQL-adatbázis vagy a Common Data Service is igénybe vehet. Ez a Workbench a következő adatokkal való feltöltéséhez ugyanazt a mintát a *lánc kikapcsolva* SQL-Adatbázissal.

Egy másik lenne, ha egy intelligens szerződés egy adott állapotban, például amikor egy szerződés hiányzóra átkerül egy *OutOfCompliance*. Ezt akkor fordul elő, amikor azt válthatja ki egy riasztást küldeni a mobiltelefonjára rendszergazdai szerepkörrel.

![Egyirányú eseménykézbesítés](./media/integration-patterns/one-way-event-delivery.png)

Ez akkor fordul elő, ahol a folyamat, a fenti kitaláltak segítségével:

-   Az intelligens szerződés átkerül az új állapot, és eseményt küld az a Főkönyv.
-   A Főkönyv fogadja, és az esemény továbbítja az Azure Blockchain Workbench használatával.
-   Az Azure Blockchain Workbench a könyvelési események által előfizetett, és az eseményt kap.
-   Az Azure Blockchain Workbench az Event Grid-előfizetők tesz közzé az eseményt.
-   Az Event Grid előfizetett külső rendszerekkel, az üzenet használják, és megfelelő művelet(ek) igénybe.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Egyirányú eseménykézbesítés az üzenet egy külső rendszer egy intelligens szerződés

Ez a forgatókönyv jut el az ellenkező irányba is van. Ebben az esetben egy érzékelő által létrejön egy esemény, vagy egy külső rendszer és az adatok eredő küldjön el egy intelligens szerződés.

Ilyenek például a szállítást az adatok a pénzügyi piacok számára, például az áruk, készlet vagy kötvényekről, az árak egy intelligens szerződés.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain Workbench használatával a várt formátumban közvetlen szállítása

Egyes alkalmazások integrálása az Azure Blockchain Workbench használatával készültek, és közvetlenül állít elő, és üzeneteket küldhet a várt formátumú.

![Közvetlen kézbesítés](./media/integration-patterns/direct-delivery.png)

Ez akkor fordul elő, ahol a folyamat, a fenti kitaláltak segítségével:

-   Egy esemény egy külső rendszer, amely egy üzenetet létrehozása az Azure Blockchain Workbench használatával történik.
-   A külső rendszer ezt az üzenetet létrehozni egy ismert formátumban írt kódot tartalmaz, és küld ez közvetlenül a Service Bus.
-   Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
-   Az Azure Blockchain Workbench indítja el a Főkönyv hívást a külső rendszer adatokat küldeni egy adott szerződést.
-   Az üzenet megérkezésekor a szerződés egy új állapot értékre vált.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítési az Azure Blockchain Workbench használatával

Egyes rendszerek nem lehet módosítani a kézbesíti az üzeneteket az Azure Blockchain Workbench által használt szabványos formátumban. Ezen esetekben meglévő mechanizmusok és üzenet ezen rendszerekből formátumban is használható. Pontosabban ezek a rendszerek natív üzenet típusú átalakíthatók leképezése a standard szintű üzenetkezelés várt formátumok egyikét a Logic Apps, az Azure Functions vagy más egyéni kód használatával.

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ez akkor fordul elő, ahol a folyamat, a fenti kitaláltak segítségével:

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

A külső rendszer testreszabása függően lehet, hogy vagy nem lehet elküldeni az üzeneteket az Azure Blockchain Workbench vár standard-formátumok egyikében. A külső rendszerek lehetőség alapján generál egyet ezek az üzenetek határozza meg a következő két visszatérési elérési utak közül melyik megnyílik.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Az Azure Blockchain Workbench használatával a várt formátumban közvetlen szállítása

![](./media/integration-patterns/direct-delivery.png)

Ebben a modellben a kommunikációt a szerződés és a későbbi állapotváltozás akkor fordul elő, a fenti következő where - feldolgozása

-   Elérésekor, a befejezési vagy külső kód végrehajtását az adott mérföldkő, a Service Bus, az Azure Blockchain Workbench használatával csatlakozik egy eseményt küld.

-   A rendszereken, nem közvetlenül megfelelően adaptálhassa írjon egy üzenetet, amely megfelel az API-t az elvárásainak akkor lesz átalakítva.

-   Az üzenet tartalma csomagolt, és a egy adott funkciót a intelligens szerződés küldött. Ez történik, a külső rendszer társított felhasználó nevében.

-   A függvény végrehajtása, és általában módosítják az állapot. Az állapotváltozás helyezi előre az üzleti munkafolyamatot a intelligens szerződést, más függvények, most már megfelelő futtatandó engedélyezése megjelennek.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítési az Azure Blockchain Workbench használatával

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ebben a modellben, ahol egy szabványos formátumra nem küldhető közvetlenül, a szerződés a kommunikációs és későbbi állapot módosításai a következő a fenti where feldolgozásához:

1.  Elérésekor, a befejezési vagy külső kód végrehajtását az adott mérföldkő, a Service Bus, az Azure Blockchain Workbench használatával csatlakozik egy eseményt küld.
2.  Egy logikai alkalmazásban vagy egyéni kódot üzenetet fogadó és a egy standard Azure Blockchain Workbench használatával formázott üzenet alakítása szolgál.
3.  A logikai alkalmazás közvetlenül a Service Bus, az átalakított üzenetet küld.
4.  Az Azure Blockchain Workbench a a Service Bus-eseményekre által előfizetett, és lekéri az üzenet.
5.  Az Azure Blockchain Workbench indítja el a Főkönyv hívást a külső rendszer adatokat küldeni egy adott szerződést.
6. Az üzenet tartalma csomagolt, és a egy adott funkciót a intelligens szerződés küldött. Ez történik, a külső rendszer társított felhasználó nevében.
7.  A függvény végrehajtása, és általában módosítják az állapot. Az állapotváltozás helyezi előre az üzleti munkafolyamatot a intelligens szerződést, más függvények, most már megfelelő futtatandó engedélyezése megjelennek.

## <a name="iot-integration"></a>IoT-integráció

Integrációs forgatókönyve a telemetriai adatok, érzékelők intelligens szerződés keretében történő felvételére. Érzékelők által továbbított adatok alapján, az intelligens szerződések sikerült megalapozott műveleteket és módosítsa a szerződés állapotát.

Például ha egy orvoslás továbbítása teherautó 110 fokban megugrik a hőmérséklet, azt hatással lehet a orvoslás hatékonyságát és nyilvános biztonsági problémát okozhat, ha nem észlelt, és a az ellátási lánc. Egy illesztőprogram gyorsítani a autó óránként 100 mérföld, ha az eredményül kapott érzékelőitől válthatja ki a biztosítási szolgáltató biztosítási lemondás. Ha az autó bérleti autót volt, a GPS-adatok esetleg jelzi, ha az illesztőprogram egy saját bérleti megállapodás hatálya alá tartozó térségen kívül történt, és egy napján belül pótdíj díja szerint számítjuk fel.

A kihívás abban áll, hogy érzékelőktől is lehet továbbítása adatok állandó történik, és nem célszerű minden adatot küld egy intelligens szerződést. Egy jellemző példán, hogy korlátozza az összes üzenet adatbáziscsoportok másodlagos áruházban a blockchain küldött üzenetek számát. Például a időszakonként csak állandó, például óránként egyszer, és ha tartalmazott értéket egy egyeztetett kívül esik a Beérkezett üzenetek továbbítására tartomány egy intelligens szerződés alapján. Tolerancia kívül eső értékeket ellenőrzése, biztosítja, hogy a szerződések üzleti logikát, vonatkozó adatokat fogadott, és végre. Az érték ellenőrzése a időközönként megerősíti, hogy az érzékelő még küld jelentést. Minden adatot megkap egy másodlagos kimutatási áruházban szélesebb körű jelentéskészítési, elemzési és gépi tanulási engedélyezéséhez. Például bár a érzékelőinek adatai a GPS-adatok lekérdezése nem szükséges egy intelligens szerződés percenként, nyújtani érdekesebb adatokat megjelölheti használható jelentések vagy hozzárendelési útvonalak.

Az Azure platformon integráció az eszközök általában történik az IoT hubbal. Az IoT Hub lehetővé teszi üzenetek továbbítását a tartalom alapján, és lehetővé teszi, hogy a fent leírt ábrázolási.

![IoT-üzenetekhez](./media/integration-patterns/iot.png)

A fenti folyamatot ábrázolja a mintát, esetében ez van megvalósítva:

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

Számos forgatókönyv attestable fájlok átfogó szükség lehet szükség. Több okból lesz az a blockchain-fájlok nem megfelelő. Ehelyett egy általánosan használt megközelítés, hogy hajtsa végre a fájl (például SHA-256) titkosítási kivonatot, és megoszthatja a kivonatot az elosztott Főkönyv. A kivonatoló végrehajtása egy későbbi időpontban újra ugyanazt az eredményt adja vissza. Ha a fájlt módosítanak, akkor is, ha csak egy képpontos módosul a képet, a kivonatot egy másik értéket adja vissza.

![Storage-integráció](./media/integration-patterns/storage-integration.png)

A minta implementálhatók ahol:

-   Egy külső rendszer továbbra is fennáll, egy olyan tárolási mechanizmus, például az Azure Storage-fájlra.
-   Kivonatot a vagy a fájl hozza létre, és a kapcsolódó metaadat-azonosítója a tulajdonosa, az URL-címet, ahol a fájl megtalálható, például stb.
-   A kivonatot, és az összes metaadat a érkezik egy intelligens szerződés, a függvény például *FileAdded*
-   A jövőben a fájl- és metaadatok lehetnek újra kivonatolt és összehasonlítja az értékeket a Főkönyv tárolja.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Integrációs minták a REST és üzenet API-k végrehajtására vonatkozó Előfeltételek

Megkönnyítése érdekében arra, hogy az egy külső rendszer vagy eszköz a REST vagy az üzenet API használatával intelligens szerződés kommunikál, a következőknek kell teljesülniük-

1. Az Azure Active Directoryban a consortium, a fiók jön létre, amely a külső rendszer vagy az eszköz jelöli.
2. Az Azure Blockchain Workbench alkalmazás a megfelelő intelligens szerződés(ek) rendelkezik a külső rendszer vagy eszköz az események fogadására meghatározott függvényeket.
3. Az alkalmazás konfigurációs fájljának az intelligens szerződés tartalmazza a szerepkör, amely a rendszer vagy az eszköz lesz hozzárendelve.
4. Az alkalmazás konfigurációs fájljának az intelligens szerződéshez azonosítja a amely megállapítja, hogy ez a függvény nem hívható meg a meghatározott szerepkör.
5. Az alkalmazás konfigurációs fájljának és a hozzá tartozó smart contracts töltenek fel az Azure Blockchain Workbench használatával.

Miután feltöltötte az alkalmazást, az Azure Active Directory-fiókot a külső rendszer hozzá van rendelve a szerződés és a kapcsolódó szerepkör.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Integrációs kód írása előtt a külső rendszer az integrációs folyamatok tesztelése 

Biztonságosabbá teszi a külső rendszerekkel való integrációt segítik a akkor számos forgatókönyv-kulcs szükséges. Kívánatos képesnek lennie ellenőriznie az intelligens szerződés Tervező korábbi vagy a kód fejlesztéséhez párhuzamosan külső rendszerekkel való integrációt segítik.

Az Azure Active Directory (Azure AD) használatát is jelentősen gyorsabban fejlesztői hatékonyság és az idő értékre. Pontosabban a kódot és egy külső rendszer közötti integráció is igénybe vehet egy nem triviális mennyi ideig. Azure AD-vel és az automatikus létrehozása az Azure Blockchain Workbench UX, ezzel lehetővé tehetik a fejlesztők számára, hogy jelentkezzen be a Workbench a külső rendszer, és töltse fel a UX-n keresztül, hogy külső rendszerből a várt értékek Ez lehetővé teszi, hogy gyors fejlesztéséhez és az ötletek egy megvalósíthatósági tesztkörnyezetben vagy előtt, vagy integrációs kódot ír a külső rendszerekhez párhuzamos ellenőrzése.
