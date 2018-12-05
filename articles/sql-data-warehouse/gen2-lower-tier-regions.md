---
title: Az Azure SQL Data Warehouse Gen2 támogatja az alacsonyabb számítási szintek |} A Microsoft Docs
description: Az Azure SQL Data Warehouse Gen2 mostantól támogatja az alacsonyabb számítási szintek
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d3c42c13e58156013167b8570a8845ce9de08b3b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882579"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Azure SQL Data Warehouse Gen2-támogatás a kisebb számítási szintek

A Microsoft lehetővé teszi, hogy képes legyen kezelni az erőforrás-igényes lekérdezéseket a villámgyors gyors Azure SQL Data Warehouse Gen2 a alacsonyabb számítási csomag hozzáadásával adattárház futtatásának belépő szintű díja végezhetőek. Ügyfelek élmény az Azure SQL Data Warehouse teljesítményt, rugalmasságot és biztonságot funkcióknak 100 cDWU (Adattárházegység) kezdve, és percek alatt 30 000 cDWU méretezhető. Ügyfelek Gen2 teljesítményt és rugalmasságot biztosít a kisebb számítási szint használata mellett is kihasználhatják. 

A belépési pont a következő generációs adatraktározási elvetésével Microsoft kitárja a kapukat a érték adatvezérelt ügyfelek számára anélkül, hogy melyik próbaverziós környezet a legjobb őket a találgatás egy biztonságos, nagy teljesítményű data warehouse minden előnyét ki kell számítani.  Ügyfelek indul el akár 100 cDWU is le a jelenlegi 500 cDWU belépési pontról is.  Az SQL Data Warehouse Gen2 szüneteltetése és folytatása műveletek és goes túl csak a rugalmasságot, a számítási továbbra is.  Gen2 is támogatja az oszloptár korlátlan tárolási kapacitás 2,5-szer több memóriát lekérdezéseként, valamint akár 128 lekérdezést, és adaptív gyorsítótárazást szolgáltatásokat állapotba helyezése funkciók használatának meggátolása a azonos Adattárházegység képest az 5-ször nagyobb teljesítmény átlaga Gen1 ugyanazon az áron.  Georedundáns biztonsági mentés olyan Gen2 az beépített garantált adatvédelmi szabvány. Az Azure SQL Data Warehouse Gen2 készen áll a méretezhető, amikor áll.

A portálon jelenleg nem támogatja a központi vagy a méretezési lehetőségek érhetők el az alsó szintű Gen2 példányainak. Dolgozunk az ezt a funkciót, addig engedélyezéshez [küldjön el egy jegyet](sql-data-warehouse-get-started-create-support-ticket.md) Ha szeretné kihasználni az új szint.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Ismerkedés az Azure SQL Data Warehouse Gen2 

Ügyfelek eldönthetik, telepíthet egy új Gen2-példányt, vagy egy meglévő Gen1 adattárházpéldányt a rugalmasság és a következő generációs adattárházak teljesítményét frissítéséhez. 

Próbálja ki [Azure SQL Data Warehouse Compute-optimalizált Gen2 kapacitás.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Frissítés [Azure SQL Warehouse számítási optimalizált Gen1 Gen2-re](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) tekintse meg az Azure SQL Data Warehouse Gen2 ezen működés [Microsoft Mechanics videóját.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Támogatott régiók számítási alacsonyabb szinten

- USA keleti US1 
- USA 2. keleti régiója
- Nyugat-Európa
- Észak-Európa
- USA 2. nyugati régiója
- Délkelet-Ázsia
- Déli RÉGIÓJA
- USA középső régiója 
- Kelet-Ázsia
- Kelet-Japán
- Közép-India
- Kelet-Ausztrália
- Közép-Kanada
- Nyugat-Japán 
- Közép-Kanada

## <a name="next-steps"></a>További lépések

- [További](upgrade-to-latest-generation.md) teljesítmény optimalizálása az SQL Data Warehouse frissítésével kapcsolatban. 
