---
title: Az Azure Cosmos DB BulkExecutor kódtárának ismertetése |} Microsoft Docs
description: Tudnivalók Azure Cosmos DB BulkExecutor könyvtárban, a könyvtárban, és az architektúra használatának előnyeit.
keywords: Java tömeges végrehajtó
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 5f1987009d2277590e32136336340aa1b3be07fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610341"
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Az Azure Cosmos DB BulkExecutor kódtárának ismertetése
 
Azure Cosmos-adatbázis egy gyors, rugalmas és globálisan elosztott adatbázis-szolgáltatás, amely rugalmasan kibővítési támogatására: 

* Nagy írási és olvasási teljesítmények (műveletek száma másodpercenként több millió).  
* Jelentős mennyiségű (több száz terabájt, vagy még több) tárolja az előre jelezhető ezredmásodperces késési tranzakciós és működési adatokat.  

A BulkExecutor könyvtár segít a kihasználja a nagy átviteli sebesség és tárterület, a BulkExecutor library lehetővé teszi a tömeges műveletek Azure Cosmos DB tömeges importálással és tömeges frissítés API-k. További BulkExecutor könyvtár a következő szakaszokban az szolgáltatásokról. 

> [!NOTE] 
> Jelenleg Bulkxecutor könyvtár támogatja az importálás, és a frissítési műveletek és a szalagtár csak Azure Cosmos DB SQL API-fiókokat támogatja. Lásd: [.NET](sql-api-sdk-bulk-executor-dot-net.md) és [Java](sql-api-sdk-bulk-executor-java.md) kibocsátási megjegyzések a frissítéseket a könyvtárhoz.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>A legfontosabb jellemzők BulkExecutor-függvénytár  
 
* Ez jelentősen csökkenti az ügyféloldali számítási erőforrásokat, az átviteli sebesség lefoglalt tárolót telítsük szükséges. Egyetlen összefűzött alkalmazás írja az adatokat a tömeges importálási API éri el a 10 alkalommal nagyobb teljesítménye írja az adatok párhuzamos során az ügyfél mágneses erősítő gép CPU többszálas alkalmazás képest.  

* Az kivonatolja számítógépnél fárasztó feladatainak írása az alkalmazáslogikát, hogy kezelni tudják a kérelem szabályozása, kérelem időtúllépése, és más átmeneti kivételek hatékonyan csatlakoztatását a szalagtárban kezelnek.  

* Alkalmazások tömeges műveletek alapján kibővíthet egy egyszerűsített mechanizmust biztosít. Egy Azure virtuális Gépen futó BulkExecutor egypéldányos nagyobb, mint 500 KB-os RU/mp vehet igénybe, és további példányokat hozzáadásával egyéni ügyfél virtuális gépek nagyobb átviteli sebesség érhető el.  
 
* Az tömegesen importálási több mint egy terabájt adatok egy órán belül egy kibővíthető architecture használatával.  

* Meglévő adatok frissítése az Azure Cosmos DB-tárolókban lévő javítások, tömegesen azt. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hogyan működik a tömeges végrehajtó? 

Importálandó, vagy frissítse a dokumentumok csoportos művelet akkor váltódik ki, az egy teljes entitásköteget, kezdetben átrendezve be az Azure Cosmos DB kulcs partíciótartomány megfelelő gyűjtők. Belül minden gyűjtő, amely megfelelne a partíciós kulcs széles azok osztható mini-kötegek és minden minimális kötegelt act, a hasznos adatok között, a kiszolgáló oldalán előjegyzett. A BulkExecutor könyvtár létrehozta az optimalizálást a párhuzamos végrehajtás ezek mini-köteg belül és között partíció kulcstartományokkal. Következő kép bemutatja, hogyan BulkExecutory kötegek különböző partíciókulcsúak az adatok:  

![Tömeges végrehajtó architektúrája](./media/bulk-executor-overview/bulk-executor-architecture.png)

A tömeges végrehajtó könyvtár biztosítja, hogy a gyűjtemény számára kiosztott átviteli sebesség standardként használatára. Használja az [AIMD stílusú torlódás vezérlési mechanizmus](https://tools.ietf.org/html/rfc5681) az egyes Azure Cosmos DB partícióazonosító kulcs tartomány hatékonyan tudja kezelni a sávszélesség-szabályozás és időtúllépéseket okoz. 

## <a name="next-steps"></a>További lépések 
  
* További által próbálhatja ki a tömeges végrehajtó szalagtár fel mintaalkalmazások [.NET](bulk-executor-dot-net.md) és [Java](bulk-executor-java.md).  
* Tekintse meg a BulkExecutor SDK információkat és a kibocsátási megjegyzések a [.NET](sql-api-sdk-bulk-executor-dot-net.md) és [Java](sql-api-sdk-bulk-executor-java.md).
* A tömeges végrehajtó könyvtár integrálva van a Cosmos DB Spark összekötő, további információkért lásd: [Azure Cosmos DB Spark összekötő](spark-connector.md) cikk.  
* A BulkExecutor könyvtár is integrálva van egy új verziója [Azure Cosmos DB összekötő](https://aka.ms/bulkexecutor-adf-v2) az Azure Data Factory-adatok másolása.
