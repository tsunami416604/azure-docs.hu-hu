---
title: A tömeges importálás és a Azure Cosmos DB adatai frissítése a tömeges végrehajtó kódtár használatával
description: Tömeges műveleteket hajthat végre Azure Cosmos DB a tömeges importálási és tömeges frissítési API-k használatával, amelyet a tömeges végrehajtó függvénytár kínál.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 1716bd64286f1882b9fc224712d227967d78058a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637778"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>A Azure Cosmos DB tömeges végrehajtó függvénytárának áttekintése
 
Az Azure Cosmos DB egy gyors, rugalmas, globálisan elosztott adatbázis-szolgáltatás, amelyet rugalmas horizontális felskálázásra terveztek a következők támogatása érdekében: 

* Nagy olvasási és írási átviteli sebesség (több millió művelet másodpercenként).  
* Nagy mennyiségű (több száz terabájt, vagy még annál is több) tranzakciós és műveleti adat tárolása kiszámítható, ezredmásodperces késéssel.  

A tömeges végrehajtói kódtár segít kihasználni ezt a hatalmas átviteli sebességet és tárterületet. A tömeges végrehajtói kódtár tömeges importálási és tömeges frissítési API-k segítségével teszi lehetővé a tömeges műveletek végrehajtást az Azure Cosmos DB-ben. A tömeges végrehajtási kódtár funkcióiról a következő szakaszokban talál további információt. 

> [!NOTE] 
> Jelenleg a tömeges végrehajtó függvénytár támogatja az importálási és frissítési műveleteket, és ezt a kódtárat csak Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják.
 
## <a name="key-features-of-the-bulk-executor-library"></a>A tömeges végrehajtó könyvtár legfontosabb funkciói  
 
* Jelentősen csökkenti az ügyféloldali számítási erőforrásokat, amelyek a tárolóhoz lefoglalt átviteli sebesség telítettségét igénylik. Egy többszálú alkalmazás, amely a tömeges importálási API használatával ír be adatot, 10 szor nagyobb írási sebességet tesz elérhetővé egy többszálas alkalmazáshoz képest, amely az adatok párhuzamos írását is lehetővé teszi, miközben az ügyfélszámítógép CPU-ját telítettnek kell lennie.  

* Elvonta az alkalmazás logikájának írásával járó unalmas feladatokat a kérések arányának korlátozása, a kérelmek időtúllépése és egyéb átmeneti kivételek kezelése érdekében, a könyvtárban lévő hatékony kezeléssel.  

* Egyszerűsített mechanizmust biztosít a tömeges műveleteket végző alkalmazások számára a vertikális felskálázáshoz. Egy Azure-beli virtuális gépen futó egyetlen tömeges végrehajtó példány több mint 500 kb/s-nál nagyobb mennyiségű, a magasabb átviteli sebességet pedig további példányok hozzáadásával is elérheti az egyes ügyfél virtuális gépeken.  
 
* A kibővíthető architektúra használatával több mint egy terabájtos adatmennyiséget is importálhat egy órán belül.  

* A Azure Cosmos DB tárolókban lévő meglévő adatmennyiséget javításként is frissítheti. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hogyan működik a tömeges végrehajtó? 

Ha a dokumentumok importálására vagy frissítésére szolgáló tömeges művelet az entitások egy kötegével aktiválódik, a rendszer először a Azure Cosmos DB partíciós tartományának megfelelő gyűjtőbe rendezi azokat. A partíciós kulcs tartományának megfelelő gyűjtőn belül a rendszer lebontja a mini-batchs szolgáltatást, és minden egyes mini Batch a kiszolgálón véglegesített adattartalomként működik. A tömeges végrehajtó függvénytár a következő mini-kötegek egyidejű végrehajtásához a partíciós kulcs-tartományokon belül és azok között is beépített optimalizációkat tartalmaz. Az alábbi ábra azt szemlélteti, hogy a tömeges végrehajtó hogyan hajtja végre az adatok különböző partíciós kulcsokban való feldolgozását:  

![Tömeges végrehajtó architektúrája](./media/bulk-executor-overview/bulk-executor-architecture.png)

A tömeges végrehajtó könyvtára gondoskodik a gyűjteményhez lefoglalt átviteli sebesség maximális kihasználásáról. Egy [AIMD stílusú torlódás-vezérlési mechanizmust](https://tools.ietf.org/html/rfc5681) használ minden Azure Cosmos db partíciós kulcs tartományához, így hatékonyan kezelheti a díjszabást és az időtúllépéseket. 

## <a name="next-steps"></a>További lépések 
  
* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása.  
* Tekintse meg a tömeges végrehajtó SDK-információkat, valamint a [.net](sql-api-sdk-bulk-executor-dot-net.md) és a [Java](sql-api-sdk-bulk-executor-java.md)kiadási megjegyzéseit.
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A tömeges végrehajtó függvénytár a [Azure Cosmos db Connector](https://aka.ms/bulkexecutor-adf-v2) új verziójába is integrálva van, Azure Data Factory az adatmásoláshoz.
