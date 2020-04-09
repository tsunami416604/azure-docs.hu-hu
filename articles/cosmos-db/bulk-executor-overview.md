---
title: Az Azure Cosmos DB tömeges végrehajtói kódtárának áttekintése
description: Tömeges műveleteket hajtson végre az Azure Cosmos DB-ben a tömeges importálás és a tömeges frissítési API-k tömeges importálása és a tömeges végrehajtó könyvtár által kínált tömeges frissítési API-k on keresztül.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: af17f9c2ef7eea5eb531327d4df13d5885a49b7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985592"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Az Azure Cosmos DB tömeges végrehajtói kódtárának áttekintése
 
Az Azure Cosmos DB egy gyors, rugalmas, globálisan elosztott adatbázis-szolgáltatás, amelyet rugalmas horizontális felskálázásra terveztek a következők támogatása érdekében: 

* Nagy olvasási és írási átviteli sebesség (több millió művelet másodpercenként).  
* Nagy mennyiségű (több száz terabájt, vagy még annál is több) tranzakciós és műveleti adat tárolása kiszámítható, ezredmásodperces késéssel.  

A tömeges végrehajtói kódtár segít kihasználni ezt a hatalmas átviteli sebességet és tárterületet. A tömeges végrehajtói kódtár tömeges importálási és tömeges frissítési API-k segítségével teszi lehetővé a tömeges műveletek végrehajtást az Azure Cosmos DB-ben. A tömeges végrehajtási kódtár funkcióiról a következő szakaszokban talál további információt. 

> [!NOTE] 
> Jelenleg a tömeges végrehajtó könyvtár támogatja az importálási és frissítési műveleteket, és ezt a függvénytárat csak az Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják.
 
## <a name="key-features-of-the-bulk-executor-library"></a>A tömeges végrehajtó könyvtár főbb jellemzői  
 
* Jelentősen csökkenti az ügyféloldali számítási erőforrások at egy tárolóhoz rendelt átviteli szinttel. Egyetlen szálas alkalmazás, amely adatokat ír a tömeges importálási API használatával 10-szer nagyobb írási átviteli sebességét éri el egy többszálas alkalmazással összehasonlítva, amely párhuzamosan írja az adatokat, miközben az ügyfélgép processzorát telíti.  

* Ez absztrakt el az unalmas feladatokat az alkalmazás logikájának írása a kérelmek sebességkorlátozásának kezelésére, a kérelem időtúllépésre és más átmeneti kivételek hatékony kezelésére a könyvtáron belül.  

* Egyszerűsített mechanizmust biztosít a tömeges műveleteket végző alkalmazások számára a horizontális felskálázáshoz. Egy Azure virtuális gépen futó egyetlen tömeges végrehajtó példány 500 K RU/s-nál nagyobb kapacitást használhat fel, és további példányok hozzáadásával érhető el az egyes ügyfélvirtuális gépeken.  
 
* Egy terabájtnál több adatot importálhat egy órán belül egy kibővített architektúra használatával.  

* Tömegesen frissítheti a meglévő adatokat az Azure Cosmos-tárolókban javításként. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hogyan működik a tömeges végrehajtó? 

Amikor egy tömeges művelet importálása vagy frissítése dokumentumok egy entitások kötegével aktiválódik, kezdetben az Azure Cosmos DB partíciókulcs-tartománynak megfelelő gyűjtőkbe kerülnek. Minden egyes gyűjtő, amely megfelel a partíciókulcs-tartomány, azok vannak lebontva mini-kötegek és minden mini-köteg működik, mint egy hasznos, amely véglegesíti a kiszolgálói oldalon. A tömeges végrehajtó könyvtár beépített optimalizálások egyidejű végrehajtása a mini-kötegek belül és partíciókulcs-tartományok között. A következő kép bemutatja, hogy a tömeges végrehajtó hogyan kötegeli az adatokat különböző partíciókulcsokba:  

![Tömeges végrehajtó architektúra](./media/bulk-executor-overview/bulk-executor-architecture.png)

A tömeges végrehajtó könyvtár gondoskodik arról, hogy maximálisan kihasználja a gyűjtemény hez rendelt átviteli. Az Egyes Azure Cosmos DB partíciókulcs-tartományokhoz egy [AIMD-stílusú torlódás-vezérlési mechanizmust](https://tools.ietf.org/html/rfc5681) használ a sebességkorlátozás és az időtúltartományok hatékony kezeléséhez. 

## <a name="next-steps"></a>Következő lépések 
  
* További információ: a [.NET](bulk-executor-dot-net.md) és Java tömeges végrehajtó könyvtárát használó mintaalkalmazások [kipróbálásával.](bulk-executor-java.md)  
* Nézze meg a tömeges végrehajtó SDK információkat és kiadási megjegyzéseket [.NET](sql-api-sdk-bulk-executor-dot-net.md) és [Java](sql-api-sdk-bulk-executor-java.md).
* A tömeges végrehajtó könyvtár integrálva van a Cosmos DB Spark-összekötő, további információkért tekintse meg [az Azure Cosmos DB Spark-összekötő](spark-connector.md) cikket.  
* A tömeges végrehajtó könyvtár is integrálva van egy új verziója az [Azure Cosmos DB-összekötő](../data-factory/connector-azure-cosmos-db.md) az Azure Data Factory adatok másolásához.
