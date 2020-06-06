---
title: A felhőben tárolt alkalmazásadatok tárolása, kezelése és megőrzése Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg olyan szolgáltatásokkal, mint a Visual Studio App Center, amelyek lehetővé teszik a mobil alkalmazások adatainak tárolását, kezelését és megőrzését a felhőben.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 724ad41bdafdba976939cbf9b7f88df2de6c6c18
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450667"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Mobile Application-adatok tárolása, szinkronizálása és lekérdezése a felhőből
Függetlenül attól, hogy milyen típusú alkalmazást készít, valószínűleg létrehoz és feldolgozza az adatmennyiséget. Az alkalmazás felhasználói nagy elvárásokkal rendelkeznek. Azt szeretnék, hogy az alkalmazás minden körülmények között gyorsan és zökkenőmentesen működjön. A legtöbb alkalmazás több eszközön is működik. Az alkalmazást asztali vagy mobil eszközről is elérheti. Egyszerre több felhasználó is használhatja az alkalmazást, és megoszthatja az adatokhoz való azonnali és valós idejű adatelérést.

Az alkalmazás felhasználói nem mindig rendelkeznek internetkapcsolattal. Az alkalmazások úgy lettek kialakítva, hogy internetkapcsolat nélkül vagy anélkül is működjenek. A fejlesztőknek ki kell választaniuk a megfelelő megoldást az adattárolásra és a felhőbe való adatszinkronizálásra, így nagyszerű felhasználói élményt nyújthatnak az alkalmazáshoz.

A Microsoft számos szolgáltatást biztosít, amelyek szükségtelenné teszik a kiszolgálók üzembe helyezését, az adatbázis kiválasztását, illetve a méretezéssel vagy a biztonsággal kapcsolatos gondot, hogy a lehető legszélesebb körű élményt nyújtsanak. Ezek a szolgáltatások nagyszerű fejlesztői élményt nyújtanak, amely lehetővé teszi, hogy az alkalmazások adatai a felhőben az SQL vagy a NoSQL API-k használatával legyenek tárolva. Az összes eszközön szinkronizálhatja az adatszinkronizálást, és lehetővé teheti, hogy az alkalmazás hálózati kapcsolatban vagy anélkül is működjön, hogy méretezhető és robusztus alkalmazásokat építsen ki.

A következő szolgáltatások segítségével kezelheti és tárolhatja a felhőben tárolt mobileszközök-adattárakat.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
A [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Felhasználhatja a világra kiterjedő alkalmazások létrehozásához. A Azure Cosmos DB segítségével rugalmasan és egymástól függetlenül méretezheti az átviteli sebességet és a tárterületet az egész világon elérhető Azure-régiók között. A kedvenc API-felületek használatával kihasználhatja a gyors, egyszámjegyű ezredmásodperces adatelérést. Ilyen felületek például az SQL, a MongoDB, a Cassandra, a Tables vagy a Gremlin. Azure Cosmos DB a teljes körű szolgáltatói szerződést (SLA-kat) biztosít az átviteli sebesség, a késés, a rendelkezésre állás és a konzisztencia érdekében.

**A legfontosabb jellemzők**
- Az API-k széles körét támogatja, beleértve az SQL (Core) API-t, a Cassandra APIt, a MongoDB API-t, a Gremlin API-t és a Table API.
- A kulcsrakész globális terjesztés a felhasználóktól függetlenül replikálja az adatait. A felhasználók a legközelebb lévő adataik replikáját használhatják.
- Nincs séma-vagy indexelési felügyelet, mert az adatbázismotor teljes mértékben a séma agnosztikus.
- A teljes regionális jelenlét, mivel a Azure Cosmos DB az összes világszerte elérhető Azure-régióban érhető el, beleértve a nyilvános felhőben 54 régiót is.
- Pontosan meghatározott, több konzisztencia-választási lehetőség van, mivel Azure Cosmos DB több főkiszolgálós replikációs protokollja gondosan lett kialakítva, hogy öt jól meghatározott konzisztencia-lehetőséget biztosítson. Ez az öt választási lehetőség erős, kötött elavulás, munkamenet, konzisztens előtag és végleges.
- 99,999%-os rendelkezésre állás mindkét olvasási és írási művelethez.
- Programozott módon (vagy a Azure Portal használatával) hívja meg a Azure Cosmos DB-fiók regionális feladatátvételét annak biztosítására, hogy az alkalmazás a regionális katasztrófák ellen legyen kialakítva.
- Garantált alacsony késés a esetek 99% percentilis globális időpontjában.

**Referencia**
- [Azure Portal](https://portal.azure.com) 
- [Azure Cosmos DB dokumentáció](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) egy általános célú, összehasonlítható adatbázis által felügyelt szolgáltatás. A használatával magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozhat létre az Azure-Felhőbeli alkalmazásokhoz és megoldásokhoz.

**A legfontosabb jellemzők**
- **Rugalmas adatbázis-modellek és-eszközök:** A rugalmas adatbázisok segítségével a fejlesztők adatbázisok egy csoportja között csoportosíthatók a skálázáshoz. Ezen erőforrások felügyeletének felügyeletéhez egy parancsfájlt kell elküldenie feladatokként. Az SQL-adatbázis ezután végrehajtja a szkriptet az adatbázisokon.
- **Nagy teljesítmény:** A nagy átviteli sebességű alkalmazások kihasználhatják a legújabb verziót. A szolgáltatás 25%-kal nagyobb prémium szintű adatbázis-teljesítményt nyújt.
- **Biztonsági másolatok, replikálás és magas rendelkezésre állás:** A beépített replikáció és az adatbázis szintjén a Microsoft által támogatott SLA biztosítja az alkalmazások folytonosságát és a katasztrofális események elleni védelmet. Az aktív geo-replikáció lehetővé teszi a feladatátvétel és az önkiszolgáló visszaállítás konfigurálását, amely teljes körű vezérlést biztosít a "Hoppá Recovery"-ben. Az adatok visszaállítása akár 35 napig is elvégezhető az adatbiztonsági mentésből.
- **Közel nulla karbantartás:** Az automatikus szoftver a szolgáltatás része. A beépített rendszerreplikák segítenek a belső adatvédelmet, az adatbázisok üzemidőét és a rendszerstabilitást biztosítani. A rendszer-replikák automatikusan átkerülnek az új számítógépekre. Azokat a régiek nem tudják kiépíteni.
- **Biztonság:** Azure SQL Database a biztonsági funkciók portfólióját kínálja a szervezeti vagy iparági megfelelőségi szabályzatoknak való megfelelés érdekében:
    - A naplózás lehetővé teszi a fejlesztők számára a megfelelőséggel kapcsolatos feladatok elvégzését és a tevékenységek ismeretének megszerzését.
    - A fejlesztők és az informatikai eszközök is végrehajthatják az adatbázis szintjén a bizalmas adatokhoz való hozzáférés korlátozását, a dinamikus adatmaszkolást és a Azure SQL Database transzparens adattitkosítását.
    - Azure SQL Database a Key Cloud Auditors ellenőrzi a legfontosabb Azure-megfelelőségi tanúsítványok és jóváhagyások hatókörét, például a HIPAA BAA, az ISO/IEC 27001:2005, a FedRAMP és az EU Model záradékok részét.

**Referencia**
- [Azure Portal](https://portal.azure.com) 
- [Az Azure SQL Database dokumentációja](/azure/sql-database/) 
