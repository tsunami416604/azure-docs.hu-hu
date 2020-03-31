---
title: Alkalmazásadatok tárolása, kezelése és megőrzése a felhőben a Visual Studio App Center és az Azure-szolgáltatások segítségével
description: Ismerje meg az olyan szolgáltatásokat, mint például a Visual Studio App Center, amely lehetővé teszi a mobilalkalmazás-adatok tárolását, kezelését és megőrzését a felhőben.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240911"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobilalkalmazás-adatok tárolása, szinkronizálása és lekérdezése a felhőből
Nem számít, milyen alkalmazást hoz létre, valószínűleg adatokat fog generálni és feldolgozni. Az alkalmazás felhasználói nagy elvárásokat támasztanak. Azt akarják, hogy az alkalmazás minden körülmények között gyorsan és zökkenőmentesen működjön. A legtöbb alkalmazás több eszközön is működik. Előfordulhat, hogy asztali számítógépről vagy mobileszközről éri el az alkalmazást. Egyszerre több felhasználó is használhatja az alkalmazást, és megoszthatja az adatokat azzal az elvárással, hogy azonnali és valós idejű hozzáférést kapjon az adatokhoz.

Az alkalmazás felhasználói nem mindig rendelkeznek internetkapcsolattal. Az alkalmazásokat úgy tervezték és várták, hogy internetkapcsolattal vagy anélkül is működjenek. A fejlesztőknek ki kell választaniuk a megfelelő megoldást adataik felhőbe való tárolásához és szinkronizálásához, hogy nagyszerű felhasználói élményt nyújtsanak az alkalmazásukhoz.

A Microsoft számos olyan szolgáltatást nyújt, amely szükségtelenné teszi a kiszolgálók felpörgetését, az adatbázis kiválasztását, illetve a méretezés vagy a biztonság miatt idotartamát, hogy a lehető leggazdagabb élményt nyújtsa. Ezek a szolgáltatások nagyszerű fejlesztői élményt nyújtanak, amely lehetővé teszi az alkalmazásadatok tárolását a felhőben az SQL vagy a NoSQL API-k használatával. Az összes eszközön szinkronizálhatja az adatokat, és engedélyezheti, hogy az alkalmazás hálózati kapcsolattal vagy anélkül is működjön, így méretezhető és robusztus alkalmazásokat hozhat létre.

Az alábbi szolgáltatásokkal kezelheti és tárolhatja a mobilalkalmazás-adatokat a felhőben.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center Data](/appcenter/data/) egy adatkezelési szolgáltatás. Lehetővé teszi, hogy az alkalmazások online és offline forgatókönyvekben kezeljék, megmaradjanak és szinkronizálják az alkalmazásadatokat a felhőben a különböző eszközökön és platformokon. Az Azure Cosmos DB-re épülő App Center-adatok a felhasználói bázis és az alkalmazások számának növekedésével méreteződnek. Alacsony késleltetést, magas rendelkezésre állást és magas méretezhetőséget biztosít az összes adat hoz.

**A legfontosabb jellemzők**
- Egyszerű kiépítés egy új Azure Cosmos DB-adatbázisba vagy egy meglévő Azure Cosmos DB-adatbázisba a Visual Studio App Center portálról.
- NoSQL adatbázis-támogatás az alkalmazások adatainak egyszerű tárolásához, szinkronizálásához és lekérdezéséhez.
- Az Azure Cosmos DB-re épülő szolgáltatás örökli az Azure Cosmos DB által kínált legfontosabb funkciók többségét, és globálisan skálázható az üzleti igényeknek megfelelően.
- Online és offline szinkronizálási lehetőségek az adatok eszközök közötti szinkronizálásához.
- Mobil ügyfél SDK-k, amelyekkel könnyedén kezelheti a privát alkalmazásadatokat.
- Platformtámogatás iOS, Android, Xamarin és React Native rendszerhez.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Az App Center-adatok – első lépések](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Segítségével bolygóméretű alkalmazásokat hozhat létre. Az Azure Cosmos DB segítségével rugalmasan és függetlenül skálázhatja az átviteli és tárolási kapacitást tetszőleges számú Azure-régióban világszerte. A gyors, egyszámjegyű adatelérés előnyeit a kedvenc API-felületei segítségével használhatja ki. Ezek a felületek közé tartozik az SQL, A MongoDB, a Cassandra, a Tables vagy a Gremlin. Az Azure Cosmos DB egyedülálló módon átfogó szolgáltatásiszint-szerződéseket (SLA-kat) biztosít az átviteli, késési, rendelkezésre állási és konzisztencia.Azure Cosmos DB uniquely provides comprehensive service level agreements (SlAs) for throughput, latency, availability, and consistency.

**A legfontosabb jellemzők**
- Az API-k széles skáláját támogatja, beleértve az SQL (Core) API-t, a Cassandra API-t, a MongoDB API-t, a Gremlin API-t és a Table API-t.
- A kulcsrakész globális disztribúció replikálja az adatokat, bárhol is legyenek a felhasználók. A felhasználók a hozzájuk legközelebb eső adatok replikáját használhatják.
- Nincs séma- vagy indexkezelés, mert az adatbázismotor teljesen agnosztikus.
- Mindenütt jelen lévő regionális jelenlét, mivel az Azure Cosmos DB világszerte minden Azure-régióban elérhető, amely több mint 54 régiót tartalmaz a nyilvános felhőben.
- Pontosan meghatározott, több konzisztencia választás, mert az Azure Cosmos DB többfős replikációs protokoll gondosan tervezték, hogy öt jól meghatározott konzisztencia választási lehetőségeket kínál. Ez az öt lehetőség erős, határolt frissesség, munkamenet, konzisztens előtag és végleges.
- 99.999% elérhetőség mind olvasási és írási.
- Programozott módon (vagy az Azure Portalon keresztül) az Azure Cosmos DB-fiók regionális feladatátvételét hívja meg annak érdekében, hogy az alkalmazás egy regionális katasztrófának ellenálljon.
- Garantáltan alacsony késleltetés a 99.

**Referencia**
- [Azure-portál](https://portal.azure.com) 
- [Az Azure Cosmos DB dokumentációja](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) egy általános célú relációs adatbázis által felügyelt szolgáltatás. Segítségével magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozhat létre az Azure-felhőben lévő alkalmazásokhoz és megoldásokhoz.

**A legfontosabb jellemzők**
- **Rugalmas adatbázismodellek és -eszközök:** Egy rugalmas adatbázis, a fejlesztők erőforrásokat a skálázás adatbázisok csoportja között. Az erőforrások felügyeleti kezeléséhez egy parancsfájlt küld el feladatként. Az SQL-adatbázis ezután végrehajtja a parancsfájlt az adatbázisokközött.
- **Nagy teljesítmény:** A nagy átviteli sebességű alkalmazások kihasználhatják a legújabb verzió előnyeit. 25%-kal több prémium adatbázis-teljesítményt biztosít.
- **Biztonsági mentések, replikáció és magas rendelkezésre állás:** A beépített replikáció és a Microsoft által támogatott SLA adatbázis szinten biztosítja az alkalmazások folytonosságát és a katasztrofális események elleni védelmet. Az aktív georeplikáció lehetővé teszi a feladatátvétel és az önkiszolgáló visszaállítás konfigurálását, amelyek teljes körű vezérlést biztosítanak az "oops recovery" felett. Az adatok visszaállítása legfeljebb 35 napos adatbiztonsági mentésekből érhető el.
- **Közel nulla karbantartás:** Az automatikus szoftver a szolgáltatás része. A beépített rendszerreplikák segítenek a benne rejlő adatvédelem, az adatbázis-uptime és a rendszer stabilitásának biztosításában. A rendszerreplikák automatikusan új számítógépekre kerülnek. Menet közben vannak elrendelve, ahogy a régiek elbuknak.
- **Biztonság:** Az Azure SQL Database biztonsági funkciók portfólióját kínálja a szervezeti vagy iparági megfelelőségi szabályzatok teljesítéséhez:
    - A naplózás lehetővé teszi a fejlesztők számára, hogy megfelelőségi feladatokat hajtsanak végre, és ismereteket szerezzenek a tevékenységekről.
    - A fejlesztők és az informatikai rendszerek adatbázisszinten valósíthatnak meg szabályzatokat a bizalmas adatokhoz való hozzáférés korlátozása érdekében sorszintű biztonsággal, dinamikus adatmaszkolással és az Azure SQL Database transzparens adattitkosításával.
    - Az Azure SQL Database-t a legfontosabb felhőauditorok ellenőrzik a legfontosabb Azure-megfelelőségi tanúsítványok és -jóváhagyások, például a HIPAA BAA, az ISO/IEC 27001:2005, a FedRAMP és az EU modellzáradékok hatókörének részeként.

**Referencia**
- [Azure-portál](https://portal.azure.com) 
- [Az Azure SQL Database dokumentációja](/azure/sql-database/) 
