---
title: A felhőben tárolt alkalmazásadatok tárolása, kezelése és megőrzése Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek segítségével a felhőben tárolhatja, kezelheti és megőrizheti a mobil alkalmazások adatait.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795574"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobile Application-adatok tárolása, szinkronizálása és lekérdezése a felhőből
Függetlenül attól, hogy milyen típusú alkalmazást szeretne létrehozni, valószínűleg adatgenerálás és-feldolgozás várható. A felhasználói elvárások magasak, és azt szeretnék, hogy az alkalmazás minden körülmények között gyorsan és zökkenőmentesen működjön. A legtöbb alkalmazás több eszközön is működik, így az alkalmazáshoz való hozzáféréskor előfordulhat, hogy egy asztali vagy mobil eszközről fér hozzá. Egyszerre több felhasználó is használhatja az alkalmazást, és megoszthatja az adatmegosztást azzal, hogy az adatokhoz való azonnali és valós idejű adathozzáférés várható.

Ezen felül az alkalmazás felhasználói nem mindig fognak internetkapcsolatot létesíteni. az alkalmazások úgy vannak kialakítva, hogy internetkapcsolattal vagy anélkül is dolgozhatnak. Mindezek egyre összetettebbek, ez egy hatalmas feladat a fejlesztők számára az adatok felhőbe való tárolásához és szinkronizálásához, így nagyszerű felhasználói élményt biztosítanak az alkalmazásokhoz.

A Microsoft számos szolgáltatást biztosít, amely kiküszöböli a kiszolgálók üzembe helyezésének szükségességét, az adatbázis kiválasztását, illetve a méretezéssel vagy a biztonsággal kapcsolatos gondot, hogy a lehető legszélesebb körű élményt nyújtson. Ezek a szolgáltatások nagyszerű fejlesztői élményt biztosítanak, amely lehetővé teszi az alkalmazásadatok tárolását a felhőben SQL-vagy NoSQL API-k használatával, az összes eszközön lévő adatszinkronizálást, valamint az alkalmazás hálózati kapcsolatok nélküli vagy anélküli működésének lehetővé tételét, hogy méretezhető és robusztus legyen alkalmazások.

A következő szolgáltatások segítségével kezelheti és tárolhatja a felhőben tárolt mobileszközök-adattárakat.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center az adatok](/appcenter/data/) egy adatkezelési szolgáltatás, amely lehetővé teszi, hogy az alkalmazások a felhőben lévő alkalmazásadatok felügyeletét, megőrzését és szinkronizálását a különböző eszközökön és platformokon keresztül online és offline helyzetekben is kezeljék. A Cosmos DB szolgáltatásra **épül** , amely a felhasználói bázis és az alkalmazások számának növekedésével bővül, ez a szolgáltatás biztosítja az alacsony késést, a magas rendelkezésre állást és a nagy skálázhatóságot az összes adathoz.

**Főbb funkciók**
- Egyszerűen **kiépítheti az új Cosmos db adatbázist** , vagy **csatlakozhat egy meglévő Cosmos db-adatbázishoz** a app Center portálról.
- **NoSQL adatbázis-támogatás** az alkalmazásadatok egyszerű tárolásához, szinkronizálásához és lekérdezéséhez.
- A **Cosmos DBra épülő**szolgáltatás a Azure Cosmos db által kínált legfontosabb funkciók többségét örökli, és **globálisan méretezhető** az üzleti igények kielégítése érdekében.
- Az **online és az offline szinkronizálási** funkciókkal szinkronizálhatja az adatokat az eszközök között.
- Mobil **ügyféloldali SDK** -k, amelyek segítségével könnyedén kezelheti a magánjellegű alkalmazásadatok szolgáltatásait.
- **Platform-támogatás** – iOS, Android, Xamarin, natív reagálás.

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center-adatkezeléssel](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
A [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása, amely segítséget nyújt a Planet Scale-alkalmazások létrehozásában. Egy gombnyomással a Cosmos DB lehetővé teszi, hogy rugalmasan és egymástól függetlenül méretezheti az átviteli sebességet és a tárterületet a globálisan tetszőleges számú Azure-régióban. Rugalmasan méretezheti az átviteli sebességet és a tárterületet, és kihasználhatja a kedvenc API-felületek, például az SQL, a MongoDB, a Cassandra, a Tables vagy a Gremlin használatával elérhető gyors, egyszámjegyű ezredmásodperces adatelérést. Cosmos DB a teljes körű szolgáltatói szerződést (SLA-kat) biztosít az átviteli sebesség, a késés, a rendelkezésre állás és a konzisztencia érdekében.

**Főbb funkciók**
- A **számos API** -t támogat: SQL (Core) api, Cassandra API, MongoDB API, Gremlin api és Table API.
- A **kulcsrakész globális terjesztés** minden esetben replikálja az adatait, így a felhasználók a számukra legközelebb eső adatreplikával dolgozhatnak.
- **Nincs séma-vagy indexelési felügyelet** , mivel az adatbázismotor teljes mértékben séma-független.
- **A Cosmos db** minden globális Azure-régióban elérhető, beleértve a nyilvános felhőben 54 és a régiókat is.
- **Pontosan meghatározott, több konzisztencia-választási lehetőség** van, mivel Cosmos db több főkiszolgálós replikációs protokollja úgy van kialakítva, hogy öt jól meghatározott konzisztencia-választási lehetőséget biztosítson – erős, kötött elavulás, munkamenet, konzisztens előtag és végleges.
- **99,999%-os rendelkezésre állás** mindkét olvasási és írási művelethez.
- **Programozott módon (vagy a portálon keresztül) hívja meg a Cosmos-fiók regionális feladatátvételét** annak érdekében, hogy az alkalmazás úgy legyen kialakítva, hogy ellenálljanak a regionális katasztrófának.
- **Garantált kis késés a esetek 99% percentilis**esetében világszerte.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com) 
- [Dokumentáció](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) egy általános célú, kapcsolati adatbázis által felügyelt szolgáltatás, amely lehetővé teszi, hogy magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozzon létre az Azure-Felhőbeli alkalmazások és megoldások számára.

**Főbb funkciók**
- **Rugalmas adatbázisok modelljei és eszközei** – a rugalmas adatbázisok lehetővé teszi a fejlesztők számára, hogy a méretezéshez adatbázisok egy csoportja között használják az erőforrásokat, amelyeket aztán felügyelheti egy parancsfájl feladatként való elküldésével, és a SQL Database végrehajtja a parancsfájl az adatbázisokon keresztül.
- A **nagy teljesítményű** , nagy átviteli sebességű alkalmazások kihasználhatják a legújabb verziót, amely 25%-kal nagyobb prémium szintű adatbázis-teljesítményt nyújt.
- A **biztonsági mentések, a replikálás és a magas rendelkezésre állás** – a beépített replikáció és az adatbázis szintjén a Microsoft által támogatott SLA biztosítja az alkalmazások folytonosságát és a katasztrofális események elleni védelmet. Az aktív geo-replikáció lehetővé teszi a feladatátvétel és az önkiszolgáló visszaállítás konfigurálását, amely teljes körű vezérlést biztosít a "Hoppá Recovery" (adatok helyreállítása az elérhető biztonsági másolatokból akár 35 napig).
- **Közel nulla karbantartás** – az automatikus szoftver a szolgáltatás részét képezi, a beépített rendszerreplikák pedig segítik a belső adatvédelmet, az adatbázisok üzemidőét és a rendszer stabilitását. A rendszerreplikák automatikusan átkerülnek a régiek helyett üzembe helyezett új számítógépekre.
- A **Security** -SQL Database biztonsági funkciók portfólióját kínálja a szervezeti vagy iparági megfelelőségi szabályzatoknak való megfelelés érdekében
    - A naplózás lehetővé teszi a fejlesztők számára a megfelelőséggel kapcsolatos feladatok elvégzését és a tevékenységek ismeretének megszerzését.
    - A fejlesztők és az informatikai eszközök is végrehajthatják az adatbázis szintjén a bizalmas adatokhoz való hozzáférés korlátozását, a dinamikus adatmaszkolást és a Azure SQL Database transzparens adattitkosítását.
    - A SQL Database a Key Cloud Auditors ellenőrzi a legfontosabb Azure-megfelelőségi minősítések és-jóváhagyások, például a HIPAA BAA, az ISO/IEC 27001:2005, a FedRAMP és az EU Model záradékok hatókörének részeként.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com) 
- [Dokumentáció](/azure/sql-database/)
   