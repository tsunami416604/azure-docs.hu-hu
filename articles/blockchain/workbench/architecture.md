---
title: Az Azure Blockchain Workbench-architektúra
description: Az Azure Blockchain Workbench Előzetes architektúrájának és összetevőinek áttekintése.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324901"
---
# <a name="azure-blockchain-workbench-architecture"></a>Az Azure Blockchain Workbench-architektúra

Az Azure Blockchain Workbench Preview leegyszerűsíti a blockchain alkalmazásfejlesztést azáltal, hogy több Azure-összetevőt használó megoldást biztosít. A Blockchain Workbench az Azure Marketplace-en egy megoldássablon használatával telepíthető. A sablon lehetővé teszi, hogy kiválassza a modulokat és az összetevőket a telepítéshez, beleértve a blokklánc-verem, az ügyfélalkalmazás típusát és az IoT-integráció támogatását. Üzembe helyezés után a Blockchain Workbench hozzáférést biztosít egy webalkalmazáshoz, iOS-alkalmazáshoz és Android-alkalmazáshoz.

![Blockchain Workbench architektúra](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitás és hitelesítés

A Blockchain Workbench használatával a konzorcium az Azure Active Directory (Azure AD) használatával összehasználhatja vállalati identitásait. A Workbench új felhasználói fiókokat hoz létre a láncon belüli identitások hoz létre az Azure AD-ben tárolt vállalati identitásokkal. Az identitásleképezés megkönnyíti a hitelesített bejelentkezést az ügyfél API-kba és alkalmazásokba, és a szervezetek hitelesítési házirendjeit használja. A Workbench lehetővé teszi a vállalati identitások társítására adott intelligens szerződésen belüli adott szerepkörökhöz. Emellett a Workbench is biztosít egy mechanizmust, hogy azonosítsa a szerepkörök által végrehajtható műveleteket, és mikor.

A Blockchain Workbench telepítése után a felhasználók az ügyfélalkalmazásokon, a REST-alapú ügyfélAPI-n vagy a Messaging API-n keresztül kommunikálnak a Blockchain Workbench alkalmazásával. Minden esetben a kapcsolati tevékenységeket hitelesíteni kell, akár az Azure Active Directory (Azure AD) vagy az eszköz-specifikus hitelesítő adatokon keresztül.

A felhasználók összeállítják identitásukat egy konzorciumi Azure AD-ben, ha e-mailben meghívót küldenek a résztvevőknek az e-mail címükre. Bejelentkezéskor ezek a felhasználók a név, a jelszó és a házirendek használatával lesznek hitelesítve. Például a szervezet kétfaktoros hitelesítése.

Az Azure AD a Blockchain Workbench-hez hozzáféréssel rendelkező összes felhasználó kezelésére szolgál. Minden intelligens szerződéshez csatlakozó eszköz az Azure AD-hez is társítva van.

Az Azure AD is használható a felhasználók hozzárendelése egy speciális rendszergazdai csoporthoz. A rendszergazdai csoporthoz társított felhasználók hozzáférést kapnak a Blockchain Workbench-en belüli jogokhoz és műveletekhez, beleértve a szerződések telepítését és a szerződés elérésére vonatkozó engedélyek megadását. A csoporton kívüli felhasználók nem férnek hozzá a rendszergazdai műveletekhez.

## <a name="client-applications"></a>Ügyfélalkalmazások

A Workbench automatikusan generált ügyfélalkalmazásokat biztosít webes és mobileszközökhöz (iOS, Android), amelyek a blokklánc-alkalmazások érvényesítésére, tesztelésére és megtekintésére használhatók. Az alkalmazás felület dinamikusan jön létre alapján intelligens szerződés metaadatok és befogadására bármilyen használati eset. Az ügyfélalkalmazások a blockchain workbench által létrehozott teljes blockchain alkalmazások hoz létre egy felhasználó felé néző előtér-front-front-endet. Az ügyfélalkalmazások hitelesítik a felhasználókat az Azure Active Directoryn (Azure AD) keresztül, és ezután az intelligens szerződés üzleti környezetéhez igazított felhasználói élményt mutatnak be. A felhasználói élmény lehetővé teszi, hogy az engedélyezett személyek új intelligens szerződéspéldányokat hozzanak létre, majd lehetővé teszik bizonyos típusú tranzakciók végrehajtását az intelligens szerződés által képviselt üzleti folyamat megfelelő pontjain.

A webalkalmazásban a jogosult felhasználók hozzáférhetnek a rendszergazdai konzolhoz. A konzol az Azure AD Rendszergazda csoportjának felhasználói számára érhető el, és hozzáférést biztosít a következő funkciókhoz:

* A Microsoft telepítése intelligens szerződéseket biztosított a népszerű forgatókönyvekhez. Például egy eszközátadási forgatókönyv.
* Töltsd fel és telepítsd a saját intelligens szerződéseiket.
* Felhasználói hozzáférés hozzárendelése az intelligens szerződéshez egy adott szerepkör környezetében.

További információ: az [Azure Blockchain Workbench minta ügyfélalkalmazások a GitHubon.](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)

## <a name="gateway-service-api"></a>Átjárószolgáltatás API-ja

A Blockchain Workbench tartalmaz egy REST-alapú átjárószolgáltatás API-t. Blokkláncba írásakor az API létrehoz és kézbesítüzeneteket egy eseményközvetítőnek. Amikor az API adatokat kér, a lekérdezések a láncon kívüli SQL-adatbázisba kerülnek. Az SQL-adatbázis a láncon belüli adatok és metaadatok replikáját tartalmazza, amely a támogatott intelligens szerződések környezet- és konfigurációs adatait tartalmazza. A lekérdezések a szerződés metaadatai által tájékoztatott formátumban adják vissza a szükséges adatokat az off-chain replika.Queries return the required data from the off-chain replica in a formatinformed by the metadata for the contract.

A fejlesztők hozzáférhetnek az átjárószolgáltatás API-jához, hogy blockchain megoldásokat építsenek vagy integráljanak anélkül, hogy a Blockchain Workbench ügyfélalkalmazásokra támaszkodnának.

> [!NOTE]
> Az API-hoz való hitelesített hozzáférés engedélyezéséhez két ügyfélalkalmazás van regisztrálva az Azure Active Directoryban. Az Azure Active Directory minden alkalmazástípus (natív és web) külön alkalmazásregisztrációt igényel. 

## <a name="message-broker-for-incoming-messages"></a>Üzenetközvetítő a bejövő üzenetekhez

Azok a fejlesztők, akik közvetlenül a Blockchain Workbench-nek szeretnének üzeneteket küldeni, közvetlenül a Service Bus-nak küldhetnek üzeneteket. Az üzenetek API-t például rendszerről rendszerre történő integrációra vagy IoT-eszközökre is használható.

## <a name="message-broker-for-downstream-consumers"></a>Üzenetközvetítő downstream fogyasztók számára

Az alkalmazás életciklusa során események következnek be. Az eseményeket az átjáró API vagy a főkönyv is aktiválhatja. Az eseményértesítések az esemény alapján elindíthatják az alsóbb rétegbeli kódot.

A Blockchain Workbench automatikusan telepíti az eseményfogyasztók két típusát. Az egyik fogyasztót blockchain események aktiválják az off-chain SQL áruház feltöltéséhez. A másik fogyasztó a dokumentumok feltöltésével és tárolásával kapcsolatos API által létrehozott események metaadatainak rögzítése.

## <a name="message-consumers"></a>Üzenet fogyasztók

 Az üzenetfogyasztók a Service Bus-tól fogadnak üzeneteket. Az üzenetfogyasztók mögöttes eseményezési modellje lehetővé teszi a további szolgáltatások és rendszerek kiterjesztését. Például hozzáadhat támogatást a CosmosDB feltöltéséhez vagy az üzenetek kiértékeléséhez az Azure Streaming Analytics használatával. A következő szakaszok a Blockchain Workbench-ben szereplő fogyasztók üzenetét ismertetik.

### <a name="distributed-ledger-consumer"></a>Elosztott főkönyvi fogyasztó

A megosztott könyvelési technológia (DLT) üzenetei tartalmazzák a blokkláncba írandó tranzakciók metaadatait. A fogyasztó lekéri az üzeneteket, és leküldi az adatokat egy tranzakciókészítőnek, aláírónak és útválasztónak.

### <a name="database-consumer"></a>Adatbázis-fogyasztó

Az adatbázis-fogyasztó a Service Bus-tól fogadja az üzeneteket, és leküldi az adatokat egy csatolt adatbázisba, például az SQL-adatbázisba.

### <a name="storage-consumer"></a>Tárolási fogyasztó

A tárolófogyasztó üzeneteket fogad a Service Bus-tól, és adatokat küld egy csatolt tárolóba. Például a kivonatolt dokumentumok tárolása az Azure Storage-ban.

## <a name="transaction-builder-and-signer"></a>Tranzakciókészítő és aláíró

Ha a bejövő üzenetközvetítőn lévő üzenetet a blokkláncra kell írni, akkor azt a dlt-fogyasztó dolgozza fel. A DLT-fogyasztó egy szolgáltatás, amely lekéri az üzenetet, amely metaadatokat tartalmaz a kívánt tranzakció végrehajtásához, majd elküldi az információt a *tranzakciókészítőnek és az aláírónak.* A *tranzakciókészítő és aláíró* az adatok és a kívánt blokklánc-cél alapján egy blockchain tranzakciót állít össze. Az összeszerelésután a tranzakció aláírásra kerül. A személyes kulcsok az Azure Key Vaultban tárolódnak.

 A Blockchain Workbench lekéri a megfelelő titkos kulcsot a Key Vaultból, és a Key Vaulton kívül aláírja a tranzakciót. Az aláírást követően a tranzakció telenküldést követően a rendszer elküldi a tranzakciós útválasztóknak és főkönyvi tételeknek.

## <a name="transaction-routers-and-ledgers"></a>Tranzakciós útválasztók és főkönyvek

A tranzakciós útválasztók és főkönyvek aláírt tranzakciókat vesznek fel, és a megfelelő blokkláncba irányítják őket. Jelenleg a Blockchain Workbench támogatja az Ethereum-ot, mint cél blokkláncot.

## <a name="dlt-watcher"></a>DLT figyelő

Az elosztott főkönyvi technológia (DLT) figyelő figyeli a Blockchain Workbench-hez csatlakoztatott blokkláncokon bekövetkező eseményeket.
Az események az egyénekre és rendszerekre vonatkozó információkat tükrözik. Például új szerződéspéldányok létrehozása, tranzakciók végrehajtása és állapotváltozások. Az események rögzítése és küldése a kimenő üzenet-közvetítő, így azokat a későbbi felhasználók is fellehet használni.

Például az SQL-fogyasztó figyeli az eseményeket, felhasználja azokat, és feltölti az SQL-adatbázist a mellékelt értékekkel. A másolat lehetővé teszi a láncon kívüli üzletben lévő on-chain adatok másolatának kikapcsolódását.

## <a name="azure-sql-database"></a>Azure SQL-adatbázis

A Blockchain Workbenchhez csatolt Azure SQL-adatbázis a szerződésdefiníciókat, a konfigurációs metaadatokat és a blokkláncban tárolt adatok SQL-hozzáférhető replikáját tárolja. Ezek az adatok könnyen lekérdezhetők, vizualizálhatók vagy elemezhetők az adatbázis közvetlen elérésével. A fejlesztők és más felhasználók az adatbázist jelentéskészítésre, elemzésre vagy más adatközpontú integrációra használhatják. A felhasználók például a Power BI segítségével jeleníthetik meg a tranzakciós adatokat.

Ez a láncon kívüli tároló lehetővé teszi a vállalati szervezetek számára, hogy az adatokat sql-ben, nem pedig egy blokklánc-főkönyvben kérdezzék le. Emellett a blockchain technológia halmok független szabványosséinak szabványosításával az off-chain tároló lehetővé teszi a jelentések és egyéb összetevők újrafelhasználását projektek, forgatókönyvek és szervezetek között.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage a szerződések és a szerződésekhez társított metaadatok tárolására szolgál.

A megrendeléstől és a hajóraklevéltől kezdve a hírekben és az orvosi képekben használt képekig, a kontinuumból származó videókig, beleértve a rendőrségi testkamerákat és a főbb mozgóképeket, a dokumentumok számos blokklánc-központú forgatókönyvben szerepet játszanak. A dokumentumok nem megfelelőek közvetlenül a blokkláncon való elhelyezésre.

Blockchain Workbench támogatja a képességét, hogy adjunk dokumentumokat vagy más médiatartalom blockchain üzleti logika. A dokumentum vagy médiatartalom kivonata a blokkláncban, a tényleges dokumentum vagy médiatartalom pedig az Azure Storage-ban tárolódik. A kapcsolódó tranzakciós adatok at a bejövő üzenetközvetítő, csomagolt, aláírt, és továbbítja a blockchain. Ez a folyamat eseményeket indít el, amelyek a kimenő üzenetközvetítőn keresztül vannak megosztva. Az SQL DB felhasználja ezt az információt, és elküldi azt a db későbbi lekérdezésre. A downstream rendszerek is felhasználhatják ezeket az eseményeket, hogy megfelelően működjenek.

## <a name="monitoring"></a>Figyelés

A Workbench alkalmazásnaplózást biztosít az Application Insights és az Azure Monitor használatával. Az Application Insights a Blockchain Workbench összes naplózott információjának tárolására szolgál, és hibákat, figyelmeztetéseket és sikeres műveleteket tartalmaz. Az Application Insights segítségével a fejlesztők hibakeresés ével kapcsolatos problémák blockchain workbench. 

Az Azure Monitor a blokklánc-hálózat állapotáról nyújt tájékoztatást. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench üzembe helyezése](../../blockchain-workbench/blockchain-workbench-deploy.md)