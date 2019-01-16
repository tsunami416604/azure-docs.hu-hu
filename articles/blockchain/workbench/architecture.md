---
title: Az Azure Blockchain Workbench architektúrája
description: Az Azure Blockchain Workbench architektúrája és összetevői áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330636"
---
# <a name="azure-blockchain-workbench-architecture"></a>Az Azure Blockchain Workbench architektúrája

Az Azure Blockchain Workbench azáltal, hogy a megoldás több Azure-összetevőből használatával egyszerűbbé teszi a blockchain-alkalmazások fejlesztését. Blockchain Workbenchet megoldás sablon használatával az Azure Marketplace-en is telepíthető. A sablon lehetővé teszi, hogy válassza ki a modulok és összetevők üzembe helyezése, beleértve a blockchain stack, az ügyfél az alkalmazás, és támogatja az IoT-integráció. Miután üzembe helyezte a Blockchain Workbenchet egy webalkalmazás, az iOS-alkalmazás és az Android-alkalmazás hozzáférést biztosít.

![Blockchain Workbenchet architektúra](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitás és hitelesítés

A Blockchain Workbenchet használó konzorcium a vállalati identitások Azure Active Directory (Azure AD) használatával is összevonható. Workbench állít elő a láncban lévő identitásokat az Azure AD-ben tárolt vállalati identitásokat az új felhasználói fiókokat. Az Identitásleképezés elősegíti a hitelesített bejelentkezési ügyfél API-kat és alkalmazásokat, és a hitelesítési házirendek a szervezetek használja. Workbench is lehetővé teszi társítása a vállalati identitások belül egy adott intelligens szerződés szerepkörökhöz. Emellett Workbench is mechanizmust biztosít a műveletek azonosíthatja ezeket a szerepköröket is igénybe vehet, és milyen időpontban.

Blockchain Workbench telepítése után a felhasználók használják az Blockchain Workbench használatával vagy az ügyfélalkalmazások, REST-alapú ügyfél-API vagy üzenetküldési API-ja. Minden esetben interakciók kell hitelesíteni, vagy Azure Active Directory (Azure AD) vagy az eszközre vonatkozó hitelesítő adatok használatával.

Felhasználók az Azure ad-ben konzorcium identitásuk küldött e-mailes meghívót a résztvevők e-mail-címükkel, vonhat össze. Ha jelentkezik be, ezek a felhasználók hitelesítése a neve, a jelszó és a házirendek használatával. Ha például kétfaktoros hitelesítést a szervezet.

Az Azure AD minden olyan felhasználó, akik hozzáférhetnek a Blockchain Workbenchet kezelésére szolgál. Minden egyes eszköz csatlakozik egy intelligens szerződést is az Azure AD társítva.

Az Azure AD felhasználók hozzárendelése egy külön felügyeleti csoportot is szolgál. A rendszergazda csoporthoz tartozó felhasználók hozzáférési jogok és a Blockchain Workbench, beleértve a szerződések üzembe helyezése és engedélyeket ad egy felhasználónak műveletek eléréséhez egy szerződés engedéllyel. Ezen a csoporton kívüli felhasználók nem rendelkeznek rendszergazdai műveleteket a hozzáférést.

## <a name="client-applications"></a>Ügyfélalkalmazások

Workbench biztosít az automatikusan létrehozott ügyfélalkalmazások webes és mobil (iOS, Android), amely ellenőrzése, tesztelése és blockchain-alkalmazások megtekintéséhez használható. Az alkalmazás felület intelligens szerződés metaadatok alapján dinamikusan generált, és képes kezelni bármilyen használati eset. Az ügyfélalkalmazások számára a felhasználói kezelőfelület továbbítására az a Blockchain Workbenchet által generált teljes blockchain-alkalmazások. Ügyfélalkalmazások hitelesítheti a felhasználókat az Azure Active Directory (Azure AD) segítségével, és ezután jelentenek a felhasználói élmény személyre szabott intelligens kontraktu üzleti környezetével. A felhasználói élmény lehetővé teszi az arra jogosult személyek új intelligens szerződés példányok létrehozását, és hajtsa végre a bizonyos típusú tranzakciók megfelelő pontokon jelöli az intelligens szerződés üzleti folyamatot lehetővé teszi majd megjelenít.

A webalkalmazás a jogosult felhasználók férhessenek a felügyeleti konzolon. A konzol az Azure ad-ben a rendszergazdák csoportjának a felhasználók számára érhető el, és férhet hozzá a következő funkciókat:

* Telepítse a Microsoft által biztosított az intelligens szerződések népszerű forgatókönyvek esetén. Például egy eszköz átviteli forgatókönyvet.
* Töltse fel, és üzembe helyezése saját smart contracts.
* Felhasználói hozzáférés hozzárendelése a intelligens szerződés keretén belül egy adott szerepkörrel.

További információkért lásd: a [Azure Blockchain Workbench minta a Githubon ügyfélalkalmazások](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>Átjáró szolgáltatás API

Blockchain Workbenchet egy REST-alapú átjáró szolgáltatás API-t tartalmaz. A blockchain írásakor az API-t állít elő, és továbbítja az üzeneteket egy eseményközvetítőből. Az API-t kér adatokat, amikor küldi el a láncon kívüli SQL Database-adatbázishoz. Az SQL-adatbázis a láncban lévő adatok és a metaadatokat, amelyek támogatott smart contracts kontextus és konfigurációs információkat nyújt egy replika tartalmazza. Lekérdezések a szükséges adatok visszaadása a láncon kívüli replika figyelmeztet a metaadatokat a szerződéshez tartozó formátumban.

A fejlesztők hozzáférhetnek az átjáró szolgáltatás API-t összeállítania és integrálni a blockchain-megoldások anélkül, hogy az ügyfélalkalmazások Blockchain Workbenchet.

> [!NOTE]
> Ahhoz, hogy a hitelesített hozzáférést az API-hoz, két ügyfélalkalmazások az Azure Active Directoryban regisztrált. Az Azure Active Directory igényel különálló alkalmazást az alkalmazásregisztrációk minden alkalmazástípus (natív és webes). 

## <a name="message-broker-for-incoming-messages"></a>Bejövő üzenetek közvetítő

A fejlesztők számára küldhet üzeneteket közvetlenül az Blockchain Workbench használatával üzeneteket küldhetnek közvetlenül a Service Bus. Például üzenetek API használható operációs rendszerek integrációja, vagy IoT-eszközök.

## <a name="message-broker-for-downstream-consumers"></a>Az alsóbb rétegbeli fogyasztók közvetítő

Az alkalmazás élettartama során a következő események következnek be. Események aktiválhatja a Gateway API vagy a Főkönyv. Eseményértesítések kezdeményezhet az alsóbb rétegbeli kódot az események alapján.

Blockchain Workbenchet automatikusan telepíti az eseményfelhasználók két típusú. Egy fogyasztói a láncon kívüli SQL-tároló feltöltése blockchain események által aktivált. A többi fogyasztó, hogy a feltöltés és tárolás dokumentumok API által előállított eseményeket metaadatainak rögzítése.

## <a name="message-consumers"></a>Üzenetfogyasztó

 Üzenetfogyasztó üzenetek a Service Bus igénybe vehet. Az alapul szolgáló modell eseménykezelési üzenetfogyasztó bővítményei további szolgáltatásokat és rendszereket tesz lehetővé. Például hozzáadhat támogatási töltse fel a cosmos DB vagy az Azure Stream Analytics használatával üzeneteket kiértékeléséhez. A következő szakaszok ismertetik az üzenetfogyasztó Blockchain Workbenchet szerepel.

### <a name="distributed-ledger-consumer"></a>Az elosztott Főkönyv fogyasztói

Az elosztott Főkönyv technológiával (DLT) üzenetek tranzakciók lehet írni a blockchain a metaadatokat tartalmaznak. Az ügyfél lekéri az üzeneteket, és leküldi az adatokat egy tranzakció builder, az aláíró és útválasztó.

### <a name="database-consumer"></a>Adatbázis fogyasztói

Az adatbázis fogyasztói veszi azokat az üzeneteket a Service Bus, és leküldéses értesítések az adatokat egy csatolt például az SQL database-adatbázissal.

### <a name="storage-consumer"></a>Tárolási fogyasztói

A storage fogyasztói veszi azokat az üzeneteket a Service Bus, és adatokat leküldi egy csatolt Storage-tárolóba. Például az Azure Storage-ban tárolja a kivonatolt dokumentumok.

## <a name="transaction-builder-and-signer"></a>Tranzakció builder és az aláíró

Ha a bejövő közvetítő egy üzenetet kell írni a blockchain, feldolgozása DLT fogyasztója. A DLT fogyasztói egy szolgáltatást, amely lekéri a végrehajtani kívánt tranzakció metaadatokat tartalmazó üzenet, és ezután elküldi az információkat a *tranzakció builder és az aláíró*. A *tranzakció builder és az aláíró* alapján az adatok és a kívánt blockchain cél blockchain tranzakció tartalomkiszolgálójáról. Ha kész, a tranzakció van aláírva. Titkos kulcsok Azure Key Vault vannak tárolva.

 Blockchain Workbench használatával kérdezi le a megfelelő titkos kulcsot a Key Vaultból, és aláírja a Key Vault-en kívül a tranzakciót. Miután regisztrált, a tranzakció tranzakció útválasztók és főkönyvek érkezik.

## <a name="transaction-routers-and-ledgers"></a>Tranzakció útválasztók és főkönyvek

Tranzakció útválasztók és főkönyvek aláírt tranzakciók igénybe vehet, és továbbítják őket a megfelelő blockchain. Jelenleg Blockchain Workbenchet Ethereum támogatja a céloldali blockchain.

## <a name="dlt-watcher"></a>DLT figyelője

Egy elosztott Főkönyv technológiával (DLT) figyelője a Blockchain Workbenchet csatolt blokk láncok bekövetkező események figyeli.
Események tükrözik az egyéni felhasználók és rendszerek vonatkozó információt. Például létrehozása új szerződés példányok, a tranzakciók végrehajtása, állapotváltozások. Az események feladatműveletet rögzít és elküldhetők a kimenő közvetítő, így azok alsóbb rétegbeli fogyasztók felhasználhassák.

Például az SQL fogyasztói eseményeket figyeli, használ fel őket, és tölti fel az SQL-adatbázis foglalt értékek közé. A példány lehetővé teszi, hogy újra létrehozhatja a láncban lévő data láncon kívüli tárolóban replikáját.

## <a name="azure-sql-database"></a>Azure SQL-adatbázis

Az Azure SQL database Blockchain Workbenchet csatolt szerződés definíciókat, konfigurációs metaadatok és a blockchain-ban tárolt adatok elérhető SQL replikáját tárolja. Ezeket az adatokat is könnyen lekérdezve, formájában jelenik meg, vagy elemezheti az adatbázis közvetlen elérésével. A fejlesztők és más felhasználók használhatja az adatbázis reporting, analytics, vagy más adatközpontú integrációkkal. Ha például felhasználók tranzakciós adatokat a Power BI használatával jelenítheti meg.

A láncon kívüli tárolók szolgáltatás lehetővé teszi a vállalati szervezetek számára, hogy az SQL-ben, nem pedig a blockchain Főkönyv adatokat lekérdezni. Is a standard szintű sémát, amely nem tartalmaz olyan blokklánc-technológiai veremhez szabványosításával a láncon kívüli tárolók lehetővé teszi a jelentések és más összetevők ismételt projektek, a forgatókönyvek és a szervezetek számára.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage szerződések és -szerződések tartozó metaadatok tárolására szolgál.

A megrendelések és szerződések, a híreket és orvosi képanyag szerepel egy egészében rendőrségi törzs kamerák és fő képek mozgásban lévő adatoknak egyaránt, beleértve a videó származó lemezképek a dokumentumok szerepet játszik a blockchain-központú helyzetekben. Dokumentumok nem megfelelőek közvetlenül a blockchain helyezi.

Blockchain Workbench használatával támogatja a dokumentumok és egyéb médiatartalmak blockchain üzleti logikával rendelkező hozzáadása. A dokumentum vagy adathordozó a tartalom kivonatát a blockchain tárolja, és a tényleges dokumentum vagy a médiatartalmak az Azure Storage tárolja. A társított tranzakció információ a bejövő közvetítő kézbesíti, csomagolt, aláírt, és a blockchain irányítja. Ez a folyamat aktiválása, események, amelyek a kimenő közvetítő keresztül megosztott. Az SQL DB használ fel ezeket az információkat, és elküldi a DB később lekérdezéséhez. Sikerült generáló rendszerek üzenetkézbesítési igényeit is használhatják fel ezeket az eseményeket, hogy a megfelelő módon működjön.

## <a name="monitoring"></a>Figyelés

Workbench biztosít az Application Insights és az Azure Monitor használatával alkalmazásnaplózás. Az Application Insights Blockchain Workbenchet naplózott adatainak tárolására szolgál, és tartalmazza a hibákat, figyelmeztetéseket és sikeres műveletek. Az Application Insights segítségével a fejlesztők az Blockchain Workbench használatával kapcsolatos problémák megoldásában. 

Az Azure Monitor a blockchain-hálózat állapotának nyújt tájékoztatást. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench üzembe helyezése](../../blockchain-workbench/blockchain-workbench-deploy.md)