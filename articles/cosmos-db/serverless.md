---
title: Használaton alapuló kiszolgáló nélküli ajánlat Azure Cosmos DB
description: További információ a Azure Cosmos DB használaton alapuló kiszolgáló nélküli ajánlatáról.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/23/2020
ms.openlocfilehash: c5086eee805ffbcdf0741eae4db405b1bcbe8692
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760359"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Kiszolgáló nélküli Azure Cosmos DB (előzetes verzió)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB kiszolgáló nélküli szolgáltatás jelenleg előzetes verzióban érhető el. Ez az előzetes verzió szolgáltatói szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB kiszolgáló nélküli használata lehetővé teszi az Azure Cosmos-fiók felhasználáson alapuló használatát, ahol csak az adatbázis-műveletek által felhasznált kérelmekért és az adatok által felhasznált tárterületért kell fizetnie. A kiszolgáló nélküli tárolók másodpercenként több ezer kérést tudnak kiszolgálni, és nincs szükség a kapacitás megtervezésére.

> [!IMPORTANT] 
> Van olyan visszajelzése, amely a kiszolgáló nélküli? Szeretnénk hallani! Nyugodtan eldobni egy üzenetet a Azure Cosmos DB kiszolgáló nélküli csapatnak: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Azure Cosmos DB használatakor minden adatbázis-művelethez a [kérelmek egységében](request-units.md)kifejezett díj tartozik. A díjszabás díja a használt Azure Cosmos-fiók típusától függ:

- A [kiépített átviteli](set-throughput.md) mód esetében az adatbázisokon és tárolókban kiépített átviteli sebesség (kérési egység/másodperc) alapján kell véglegesíteni. Az adatbázis-műveletek költségeit a rendszer levonja a másodpercenként elérhető kérelmek számáról. A számlázási időszak végén a kiépített átviteli sebesség alapján számolunk fel díjat.
- Kiszolgáló nélküli módban a tárolók létrehozásakor nem szükséges átviteli sebességet kiépíteni az Azure Cosmos-fiókban. A számlázási időszak végén az adatbázis műveletei által felhasznált kérelmek számának számlázására kerül sor.

## <a name="use-cases"></a>Használati esetek

Azure Cosmos DB a kiszolgáló nélküli legmegfelelőbb forgatókönyvek, ahol a várt:

- **Alacsony, időszakos és kiszámíthatatlan forgalom**: mivel az ilyen helyzetekben a kiépítési kapacitás nem szükséges, és lehet, hogy költséghatékony
- **Mérsékelt teljesítmény**: mivel a kiszolgáló nélküli tárolók [meghatározott teljesítménnyel](#performance) rendelkeznek

Ezen okok miatt a kiszolgáló nélküli Azure Cosmos DB a következő helyzetekben kell figyelembe venni:

- A Azure Cosmos DB első lépései
- Új alkalmazások fejlesztése, tesztelése és prototípusa
- Kis-és közepes méretű alkalmazások futtatása nehezen megbecsülhető időszakos forgalommal
- Integráció a kiszolgáló nélküli számítási szolgáltatásokkal, például a [Azure functions](../azure-functions/functions-overview.md)

Tekintse meg a [kiépített átviteli sebesség és a kiszolgáló nélküli cikk közötti választást](throughput-serverless.md) , ahol további útmutatást talál a használati esetnek leginkább megfelelő ajánlat kiválasztásához.

## <a name="using-serverless-resources"></a>Kiszolgáló nélküli erőforrások használata

A kiszolgáló nélküli egy új Azure Cosmos-fióktípus, ami azt jelenti, hogy új fiók létrehozásakor a **kiépített átviteli sebesség** és a **kiszolgáló** nélküli lehetőséget kell választania. Létre kell hoznia egy új kiszolgáló nélküli fiókot a kiszolgáló nélküli használat megkezdéséhez. Az előzetes verzióban az új kiszolgáló nélküli fiók létrehozásának egyetlen támogatott módja [a Azure Portal](create-cosmosdb-resources-portal.md). A meglévő fiókok kiszolgáló nélküli üzemmódba való áttelepítése jelenleg nem támogatott.

Bármely kiszolgáló nélküli fiókban létrehozott tároló kiszolgáló nélküli tároló. A kiszolgáló nélküli tárolók ugyanazokat a képességeket teszik elérhetővé, mint a kiépített átviteli sebesség módban létrehozott tárolók, így az adatok pontosan ugyanúgy olvashatók, írhatók és lekérdezve. A kiszolgáló nélküli fiókok és tárolók azonban speciális tulajdonságokkal is rendelkeznek:

> [!IMPORTANT]
> A korlátozások némelyike enyhíthető vagy eltávolítható, ha a kiszolgáló nélkül általánosan elérhetővé válik, és **a visszajelzés** segít nekünk eldönteni! Forduljon a kiszolgáló nélküli felhasználói élményhez: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- A kiszolgáló nélküli fiókok csak egyetlen Azure-régióban futhatnak. A létrehozása után nem lehet további Azure-régiókat felvenni a kiszolgáló nélküli fiókba.
- A [szinapszis hivatkozás előzetes funkciója](synapse-link.md) nem engedélyezhető kiszolgáló nélküli fiókban.
- A kiépítési sebesség nem szükséges a kiszolgáló nélküli tárolók esetében, ezért a következő utasítások alkalmazhatók:
    - Kiszolgáló nélküli tároló létrehozásakor nem adhat meg átviteli sebességet, és ez hibát jelez.
    - Nem lehet olvasni vagy frissíteni az átviteli sebességet egy kiszolgáló nélküli tárolón, és ez hibát jelez.
    - Nem hozható létre megosztott átviteli sebességű adatbázis egy kiszolgáló nélküli fiókban, így egy hibaüzenetet ad vissza.
- A kiszolgáló nélküli tárolók legfeljebb 50 GB adat és index tárolására használhatók.

## <a name="monitoring-your-consumption"></a>A felhasználás figyelése

Ha korábban már használta a Azure Cosmos DBt a kiépített átviteli sebesség módban, akkor a kiszolgáló nélküli szolgáltatás költséghatékony, ha a forgalom nem igazolja a kiosztott kapacitást. A kompromisszum azt jelzi, hogy a költségek kevésbé kiszámíthatók, mert az adatbázis által feldolgozott kérelmek száma alapján számolunk fel díjat. Ezért fontos, hogy figyelje a jelenlegi fogyasztását.

A fiók **metrikák** ablaktábláján megtalálhatja az Áttekintés lapon az **áttekintő** **kérési egységek** nevű diagramot. Ez a diagram azt mutatja, hogy a fiók hány kérési egységet használ fel:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="A felhasznált kérelmek egységeit bemutató diagram" border="false":::

Az [itt](monitor-request-unit-usage.md)leírtak szerint ugyanazt a diagramot használhatja Azure monitor használatakor. Vegye figyelembe, hogy Azure Monitor lehetővé teszi a [riasztások](../azure-monitor/platform/alerts-metric-overview.md)beállítását, amelyek segítségével értesítheti, ha a kérés egységének felhasználása egy bizonyos küszöbértéket adott meg.

## <a name="performance"></a><a id="performance"></a>Teljesítmény

A kiszolgáló nélküli erőforrások bizonyos teljesítménybeli jellemzőket adnak meg, amelyek különböznek a kiosztott átviteli sebességtől:

- **Rendelkezésre állás**: miután a kiszolgáló nélküli ajánlatot általánosan elérhetővé válik, a kiszolgáló nélküli tárolók rendelkezésre állása a 99,9%-os szolgáltatói szerződés (SLA) lesz, ha Availability Zones (zóna redundancia) nem használatos. Az SLA értéke 99,99%, ha Availability Zones van használatban.
- **Késés**: miután a kiszolgáló nélküli ajánlatot általánosan elérhetővé válik, a kiszolgáló nélküli tárolók késését az írásokhoz 10 ezredmásodperc vagy annál kisebb szolgáltatási szintű célkitűzés (SLO) fogja fedezni. A pont – olvasási művelet egy adott elem beolvasását jelenti az azonosító és a partíciós kulcs értéke alapján.
- **Feltörtség**: miután a kiszolgáló nélküli ajánlat általánosan elérhetővé válik, a kiszolgáló nélküli tárolók feltörését a 95%-os szolgáltatási szint célkitűzése (SLO) fogja fedezni. Ez azt jelenti, hogy a maximális kiváltási idő legalább 95%-át elérheti.

> [!NOTE]
> Az Azure előzetes verziójának részeként Azure Cosmos DB kiszolgáló nélküli kizárva a szolgáltatói szerződések (SLA). A fent említett teljesítmény-jellemzők előzetes verzióként jelennek meg, ha általánosan elérhető az ajánlat.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a kiszolgáló nélküli használattal a következő cikkekkel:

- [Az Azure Cosmos DB kérelemegységei](request-units.md)
- [Válasszon a kiosztott átviteli sebesség és a kiszolgáló nélküli megoldás között](throughput-serverless.md)
- [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
