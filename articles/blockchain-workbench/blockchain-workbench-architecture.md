---
title: Azure Blockchain munkaterület-architektúra
description: Azure Blockchain munkaterület architektúrája és összetevői áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: d37913caa94dc4cf79aef9c2c12a7aacce7c03ce
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076732"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain munkaterület-architektúra

Azure Blockchain munkaterület blockchain alkalmazásfejlesztés leegyszerűsíti a több Azure-összetevők megoldás biztosításával. Blockchain munkaterület használatával a megoldássablon az Azure piactéren is telepíthető. A sablon lehetővé teszi, hogy a felhasználók számára a modulok és összetevők blockchain verem, ügyfél-alkalmazás, például központi telepítés Blockchain munkaterületet, és az IoT-integráció támogatja. Amennyiben telepített, akkor Blockchain munkaterület egy webalkalmazás, az iOS-alkalmazás és az Android-alkalmazás hozzáférést biztosít.

![Blockchain munkaterület architektúrája](media/blockchain-workbench-architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitás- és hitelesítési

Blockchain munkaterületet használja, konzorciuma összevonni is a vállalati identitásokat az Azure Active Directoryval (Azure AD). Munkaterület-lánc identitásokat az Azure AD-ben tárolt vállalati identitásokat az új felhasználói fiókokat hoz létre. Az Identitásleképezés lehetővé teszi a hitelesített bejelentkezési ügyfél API-k és alkalmazásokhoz, és a hitelesítési házirendek szervezetek használja. Munkaterület is lehetővé teszi rendelje hozzá a vállalati identitásokat belül egy adott intelligens szerződés adott szerepkörökre. Ezenkívül munkaterület is mechanizmust biztosít a műveletek azonosításához ezeket a szerepköröket is igénybe vehet, és milyen időpontban.

Blockchain munkaterület telepítése után a felhasználók vagy az ügyfélalkalmazások, a REST-alapú ügyfél API vagy az üzenetkezelő API Blockchain munkaterület használják. Minden esetben kapcsolati kell hitelesíteni, vagy Azure Active Directory (Azure AD) vagy az eszközre vonatkozó hitelesítő adatokat.

Felhasználók az Azure AD konzorciumának letöltésük összevonva résztvevők meghívó e-mail küldése az e-mail címen. Bejelentkezéskor, ezek a felhasználók hitelesítése a neve, a jelszó és a házirendek használatával. Például kéttényezős hitelesítést, a szervezet.

Az Azure AD Blockchain munkaterület hozzáféréssel rendelkező felhasználók kezelésére szolgál. Minden eszköz csatlakozik egy intelligens szerződést is az Azure AD hozzá rendelve.

Az Azure AD felhasználók hozzárendelése egy különleges rendszergazdai csoport is szolgál. A felügyeleti csoporthoz tartozó felhasználók kapnak rendszergazdai jogok/művelet Blockchain-munkaterületen, például központi szerződések, és hogy egy felhasználó hozzáférhessen a szerződés engedélyeket ad. Ez a csoport kívüli felhasználók nem rendelkeznek rendszergazdai műveleteket a hozzáférést.

## <a name="client-applications"></a>Ügyfél-alkalmazások

Munkaterület lehetővé teszi az automatikusan létrehozott Ügyfél webes és a mobileszközök (iOS, Android), amely ellenőrzése, tesztelése és blockchain alkalmazások megtekintése. A felület dinamikusan intelligens szerződés metaadatok alapján jön létre, és lehetővé teszi bármely használati eset. Az ügyfélalkalmazások számára biztosítanak a felhasználók számára is elérhető előtér Blockchain munkaterület által generált teljes blockchain alkalmazások. Ügyfél-alkalmazások Azure Active Directory (Azure AD) segítségével a felhasználók hitelesítéséhez és megcélozhassa, majd az üzleti környezet az intelligens egyezmény igazított felhasználói élmény. A felhasználói élmény létrehozását lehetővé tevő felhatalmazott személyek új intelligens szerződés példányokat, és ezután megadja a bizonyos típusú tranzakciók megfelelő pontokon végrehajtani az intelligens szerződés jelöli az üzleti folyamatban lehetőséget.

A webalkalmazás engedéllyel rendelkező felhasználók férhetnek hozzá a felügyeleti konzolon. A konzol az Azure AD a felügyeleti csoportba tartozó felhasználók számára elérhető, és a következő funkciókhoz férhet hozzá:

* Központi telepítése a Microsoft által biztosított az intelligens szerződések népszerű forgatókönyvek esetén. Például egy eszköz átviteli forgatókönyvet.
* Feltöltése és telepítése a saját intelligens szerződések.
* A felhasználói hozzáférés hozzárendelése a intelligens szerződés egy adott szerepkör környezetében.

## <a name="gateway-service-api"></a>Átjáró szolgáltatás API

Blockchain munkaterület egy REST-alapú átjáró szolgáltatás API tartalmazza. Egy blockchain írásakor az API-t állít elő, és kézbesíti az üzeneteket egy esemény broker. Amikor adatokat kér az API-t, lekérdezéseket a rendszer a lánc SQL-adatbázis küldi. Az SQL-adatbázis-lánc adatok és a metaadatokat, amelyek támogatott intelligens szerződések környezet és a konfigurációs információkat nyújt egy replika tartalmazza. Lekérdezések vissza a szükséges adatokat. a lánc replikából figyelmeztet a szerződés metaadatainak formátumban.

A fejlesztők az átjáró szolgáltatás API összeállítania vagy integrálnia blockchain megoldások anélkül, hogy az ügyfélalkalmazások Blockchain munkaterület használatával érheti el.

> [!NOTE]
> Ahhoz, hogy a hitelesített hozzáférést biztosít az API-t, a két ügyfélalkalmazások regisztrálva van az Azure Active Directoryban. Az Azure Active Directory szükséges különböző alkalmazás regisztrációk mindegyik alkalmazástípus (natív és webes). 

## <a name="message-broker-for-incoming-messages"></a>Üzenet broker bejövő üzenetek

A fejlesztők számára történő üzenetküldéshez közvetlenül Blockchain munkaterület üzeneteket küldhetnek közvetlenül a Service Bus. Például az üzenetek API rendszerre történő integráció és az IoT-eszközök is használható.

## <a name="message-broker-for-downstream-consumers"></a>Üzenet broker alárendelt fogyasztók számára

Az az alkalmazás-életciklus során a következő események következnek be. Események is elindítható, az átjáró API-t vagy a főkönyvi. Eseményértesítések kezdeményezhet az alsóbb rétegbeli kódot az események alapján.

Blockchain munkaterület automatikusan telepíti a kétféle típusú eseményfelhasználók. Egy váltja ki blockchain események a lánc SQL-tároló feltöltéséhez. A másik pedig a metaadatok feltöltése és dokumentumok tárolása az API által előállított eseményeket rögzíteni.

## <a name="message-consumers"></a>Üzenet fogyasztók

 Üzenet fogyasztók üzeneteket a Service Bus igénybe vehet. Az alapul szolgáló esemény modell üzenet fogyasztók számára lehetővé teszi a bővítmények további szolgáltatások és rendszerek. Például hozzáadhatja CosmosDB feltöltése vagy üzenetek Azure Streaming Analytics segítségével kiértékelheti a támogatási szolgálathoz. A következő szakaszok ismertetik az üzenet fogyasztók Blockchain munkaterület szerepel.

### <a name="distributed-ledger-consumer"></a>Elosztott főkönyvi fogyasztói

Elosztott főkönyvi (DLT) technológia üzenetek az egyes tranzakciókra vonatkozóan a blockchain íródik a metaadatok tartalmazzák. A fogyasztó beolvassa a, és leküldéses értesítések az adatokat a tranzakció jelentéskészítő, aláíró és útválasztó.

### <a name="database-consumer"></a>Adatbázis-felhasználó

Az adatbázis fogyasztói üzenetek telik el a Service Bus, és leküldéses értesítések az adatok egy csatolt adatbázisba, például az SQL-adatbázis.

### <a name="storage-consumer"></a>Tárolási fogyasztói

A tárolási fogyasztó üzenetek telik el a Service Bus, és leküldéses értesítések adatok csatolt tárolóra. Például Azure Storage tárolóban végzett tárolása kivonatolt dokumentumokat.

## <a name="transaction-builder-and-signer"></a>Tranzakció jelentéskészítő és aláíró

Ha a bejövő üzenet broker üzenet lehet írni a blockchain, feldolgozása a DLT fogyasztó. A DLT fogyasztó az egy szolgáltatás, amely lekérdezi a végrehajtani kívánt tranzakció metaadatokat tartalmazó üzenet, és ezután elküldi az információkat a *tranzakció jelentéskészítő és aláíró*. A *tranzakció jelentéskészítő és aláíró* állítja össze az adatokat, majd a kívánt blockchain cél alapján blockchain tranzakció. Ha kész, a tranzakció alá van írva. A titkos kulcsok Azure Key Vault vannak tárolva.

 Blockchain munkaterület lekéri a megfelelő titkos kulcsot a Key Vault, és aláírja a Key Vault kívül tranzakció. Miután regisztrált, a tranzakció elküldi tranzakció útválasztók és a főkönyvi.

## <a name="transaction-routers-and-ledgers"></a>Tranzakció útválasztók és a főkönyvi

Tranzakció útválasztók és a főkönyvi aláírt tranzakciók igénybe vehet, és a megfelelő blockchain továbbítani. Jelenleg Blockchain munkaterület Ethereum támogatja a célként megadott blockchain között.

## <a name="dlt-watcher"></a>DLT figyelője

Egy elosztott főkönyvi (DLT) technológia megfigyelő Blockchain munkaterület csatolva blokk láncok bekövetkező események figyeli.
Események információ a felhasználók és rendszerek tükrözik. Például az létrehozni az új szerződés példányt, a tranzakciók végrehajtása, és módosításokat állam. Az események rögzítése és a kimenő üzenet broker elküldhetők, így képes használni az alárendelt fogyasztó.

Például az SQL végfelhasználói eseményeket figyeli, akkor azokat és tölti fel az SQL-adatbázis található értékekkel. A másolási lehetővé teszi, hogy a replikák lánc tárolóban a lánc adatok újbóli létrehozása.

## <a name="azure-sql-database"></a>Azure SQL Database

Blockchain munkaterület csatolva az Azure SQL-adatbázis tárolja a szerződés definíciókat, konfigurációs metaadatok és a blockchain tárolt adatok egy SQL-elérhető replika. Ezek az adatok egyszerűen kell lekérdezni, ábrázolt, vagy az adatbázis közvetlen elérésével elemzése. A fejlesztők és más felhasználók használhatja az adatbázis reporting, analytics vagy egyéb adatok-központú integrációja. Például felhasználók tranzakciós adatokat a Power BI használatával jelenítheti meg.

Ez a lánc tároló lehetővé teszi a vállalati szervezetek lekérdezni adatokat a blockchain főkönyvi helyett inkább SQL. Is egy szabványos séma, amely független a blockchain technológia verem szabványosításával a lánc tároló lehetővé teszi, hogy a jelentések és egyéb összetevők újbóli projektek, a forgatókönyvek és a szervezetek között.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage fogja tárolni a szerződések és szerződések társított metaadatokat.

A beszerzési és szerződések, képeket, híreket és orvosi szimbólumokat szerepel egy alakíthatnak ki olyan rendőrségi törzs kamerák és fő mozgásérzékelési képek, beleértve a videó származó a dokumentumok blockchain-központú helyzetekben szerepet játszanak. Dokumentumok megfelelnek nem közvetlenül a blockchain helyezze el.

Blockchain munkaterület támogatja a dokumentumok vagy más blockchain üzleti logikát a médiatartalmak hozzáadni. A dokumentum vagy media tartalom kivonatát a blockchain tárolja, és a tényleges dokumentum vagy médiatartalom Azure Storage szolgáltatásban tárolja. A kapcsolódó információk a bejövő üzenet broker kézbesített, csomagolt, aláírva, és irányítja át a blockchain. Ez a folyamat események, amelyek segítségével a kimenő üzenet broker megosztott váltja ki. Az SQL DB használ fel ezeket az információkat, és elküldi a DB a később lekérdezésre. Alsóbb rétegbeli rendszerek is sikerült felhasználhatják ezeket az eseményeket való működésre szükség szerint.

## <a name="monitoring"></a>Figyelés

Munkaterület használata az Application insights szolgáltatással és az Azure-figyelő alkalmazásnaplózás biztosít. Az Application Insights Blockchain munkaterület naplózott adatainak tárolására szolgál, és a hibák, figyelmeztetések és sikeres műveleteket tartalmaz. Az Application Insights segítségével a fejlesztők Blockchain munkaterület problémák hibakeresését. 

Azure figyelő tájékoztatást nyújt a blockchain hálózati állapotát. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench üzembe helyezése](blockchain-workbench-deploy.md)