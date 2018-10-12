---
title: Az Azure Cosmos DB tömeges végrehajtó erőforrástár áttekintése |} A Microsoft Docs
description: További információ az Azure Cosmos DB tömeges végrehajtó könyvtárban, a könyvtárban, és architektúrájának használatával járó előnyöket.
keywords: Java tömeges végrehajtó
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 823cb536a1cd0b8f5da7442906b14447c15ae044
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091353"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Az Azure Cosmos DB tömeges végrehajtó erőforrástár áttekintése
 
Az Azure Cosmos DB egy gyors, rugalmas és globálisan elosztott adatbázis-szolgáltatás, amely rugalmas horizontális felskálázási támogatására: 

* Nagy írási és olvasási átviteli sebesség (másodpercenként több millió).  
* Nagy mennyiségű (több terabájtnyi vagy akár több száz) tárolása kiszámítható ezredmásodperces késéssel tranzakciós és a működési adatokat.  

A tömeges végrehajtó könyvtár kihasználja a nagy teljesítményt és tárolókapacitást nyújt segítséget. A tömeges végrehajtó kódtár lehetővé teszi tömeges műveletek az Azure Cosmos DB tömeges importálás és tömeges frissítése API-k. Tudjon meg többet tömeges végrehajtó könyvtár a következő szakaszokban az szolgáltatásokról. 

> [!NOTE] 
> Jelenleg tömeges végrehajtó kódtár támogatja az importálási és frissítési műveleteket és a könyvtár csak az Azure Cosmos DB SQL API-fiókok által támogatott. Lásd: [.NET](sql-api-sdk-bulk-executor-dot-net.md) és [Java](sql-api-sdk-bulk-executor-java.md) kibocsátási megjegyzések a frissítéseket a könyvtárhoz.
 
## <a name="key-features-of-the-bulk-executor-library"></a>A tömeges végrehajtó library kulcsfunkciói  
 
* Jelentősen csökkenti a szükséges egy tároló kiosztott átviteli telítéséhez ügyféloldali számítási erőforrások. Egyetlen szálon futó alkalmazás írja az adatokat a tömeges importálási API eléri-e 10 alkalommal nagyobb a lemezírás teljesítménye egy több szálon futó alkalmazás írja az adatok párhuzamos során az ügyfél mágneses erősítő gép CPU képest.  

* Kódbázis, kezelni a sebességkorlátozás kérelem, a kérelem időtúllépése és egyéb átmeneti kivételek hatékonyan kezeli őket a könyvtárból alkalmazás logika fárasztó feladat tevékenységeit.  

* Az alkalmazások horizontális felskálázása tömeges műveletek végrehajtása egy egyszerűsített mechanizmust biztosít. Egy egyetlen tömeges végrehajtó példány-beli virtuális gépen futó nagyobb, mint 500 ezer Kérelemegység/s is használják, és a egy nagyobb átviteli sebességet érhet el, további példányok hozzáadása a különálló ügyfél virtuális gépeket.  
 
* Azt is tömeges importálás több, mint a terabájt adat egy órán belül egy kibővített architektúra használatával.  

* Meglévő adatok frissítése az Azure Cosmos DB-tárolók, a javítások, tömegesen. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hogyan működik a tömeges végrehajtó? 

Importálásához vagy a dokumentumok frissítéséhez csoportos művelet akkor aktiválódik, a teljes entitásköteget, kezdetben összekeverve be az Azure Cosmos DB partíciókulcs-tartományok megfelelő gyűjtőkbe. Belül az egyes gyűjtők, amely megfelel a partíciókulcs-tartományok azok bontásban mini-kötegek és minden egyes mini batch act as egy hasznos, amelyek a kiszolgálói oldalon előjegyzett. A tömeges végrehajtó könyvtár rendelkezik beépített optimalizálását a mini-kötegek belül és között: partíció kulcstartományokkal egyidejű végrehajtása. Következő kép mutatja be, hogyan tömeges végrehajtó kötegeli az adatokat az eltérő partíciókulcsok:  

![Tömeges végrehajtó architektúra](./media/bulk-executor-overview/bulk-executor-architecture.png)

A tömeges végrehajtó kódtár biztosítja, hogy standardként felhasználja a gyűjteményhez kiosztott átviteli sebesség. Használ egy [AIMD stílusú torlódás vezérlési mechanizmus](https://tools.ietf.org/html/rfc5681) minden egyes Azure Cosmos DB hatékonyan kezeli a sebességkorlátozás és időtúllépéseket kulcstartományhoz particionálásához. 

## <a name="next-steps"></a>További lépések 
  
* További tudnivalókért próbálja ki a tömeges végrehajtó kódtárat a felhasználásához mintaalkalmazásból [.NET](bulk-executor-dot-net.md) és [Java](bulk-executor-java.md).  
* Tekintse meg a tömeges végrehajtó SDK információkat és a kibocsátási megjegyzések a [.NET](sql-api-sdk-bulk-executor-dot-net.md) és [Java](sql-api-sdk-bulk-executor-java.md).
* A tömeges végrehajtó könyvtár integrálva van a Cosmos DB Spark-összekötő, további tudnivalókért lásd: [Azure Cosmos DB Spark-összekötő](spark-connector.md) cikk.  
* A tömeges végrehajtó könyvtárban is integrálva van egy új verziója [Azure Cosmos DB-összekötő](https://aka.ms/bulkexecutor-adf-v2) az Azure Data Factoryben az adatok.
