---
title: "Az Azure Storage Explorer Azure Cosmos DB kezelése"
description: "Útmutató: Azure Cosmos Adatbázist Azure Tártallózó kezelése."
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: 
author: jejiang
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: Jejiang
ms.openlocfilehash: 6ed5ddab021006da633b30cafc4e1988a064ab92
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Az Azure Tártallózó (előzetes verzió) Azure Cosmos DB kezelése

Azure Tártallózó Azure Cosmos DB használatával lehetővé teszi a felhasználók Azure Cosmos DB entitások kezelése, kezelhetők adatok, frissítse a tárolt eljárások és eseményindítók és egyéb Azure entitások, például a tárolási blobokat és üzenetsorokat. Mostantól ugyanarra az eszközre segítségével kezelheti a különböző Azure entitások egy helyen. Ilyenkor az Azure Tártallózó SQL és a MongoDB fiókokat támogat. Az Azure Cosmos DB helyi Emulator nem működik az Azure Storage-kezelővel. 

Ebből a cikkből megismerheti Azure Cosmos DB kezelése a Tártallózó használatával.


## <a name="prerequisites"></a>Előfeltételek

Egy Azure Cosmos DB fiókot az SQL API-t vagy a MongoDB API. Ha nincs fiókja, létrehozhat egy Azure-portálon a leírtak szerint [Azure Cosmos DB: .NET és az Azure-portálon az SQL API webalkalmazás összeállítása](create-sql-api-dotnet.md).

## <a name="installation"></a>Telepítés

Telepítse a legújabb Azure Tártallózó bits: [Azure Tártallózó](https://azure.microsoft.com/features/storage-explorer/), most Windows, Linux és MAC-verziója támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. Miután telepítette a **Azure Tártallózó**, kattintson a **beépülő modul** ikonra a bal oldalon az alábbi ábrán látható módon.
       
   ![Beépülő modul ikon](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Válassza ki **vegyen fel egy Azure-fiókot**, és kattintson a **bejelentkezési**.

   ![Csatlakozás Azure-előfizetéshez](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. Az a **Azure bejelentkezési** párbeszédpanelen jelölje ki **bejelentkezés**, és írja be Azure hitelesítő adatait.

    ![Bejelentkezés](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Jelölje ki az előfizetését a listából, és kattintson a **alkalmaz**.

    ![Jelentkezés](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    Az Intéző ablaktáblát frissítéseket, és a fiókok megjeleníti a kijelölt előfizetésben.

    ![Fiók listája](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    Sikeresen csatlakozott a **Azure Cosmos DB fiók** az Azure-előfizetéshez.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Csatlakozás Azure Cosmos DB egy kapcsolati karakterlánc használatával

Egy Azure Cosmos DB kapcsolódni megadásának alternatív módja, hogy használja a kapcsolati karakterláncot. A következő lépésekkel csatlakozzon a kapcsolati karakterlánc használatával.

1. Található **helyi és a kapcsolódó** a bal oldali fában, kattintson a jobb gombbal **Azure Cosmos DB fiókok**, válassza a **Azure Cosmos DB kapcsolódás...**

    ![Csatlakozás Azure Cosmos DB kapcsolati karakterlánc](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Válassza ki a megfelelő **alapértelmezett élmény** vonatkozó a fióktípust vagy **DocumentDB** vagy **MongoDB**, illessze be a **kapcsolati karakterlánc**, és kattintson a **OK** Azure Cosmos DB fiók kapcsolódni. A kapcsolati karakterlánc beolvasása információkért lásd: [a kapcsolati karakterlánc beolvasása](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![kapcsolat-karakterlánc](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Az Azure Cosmos DB erőforrás-kezelés

Kezelheti egy Azure Cosmos DB fiókot ezzel a következő műveleteket:
* Nyissa meg a fiókot az Azure-portálon
* Az erőforrás hozzáadása a gyorselérési listája
* Keresés és a frissítési források
* Adatbázisok létrehozása és törlése;
* Gyűjtemények létrehozása és törlése;
* Létrehozása, szerkesztése, törlése és dokumentumok szűrése
* Tárolt eljárások, eseményindítók és felhasználó által definiált függvények kezelése

### <a name="quick-access-tasks"></a>A gyors hozzáférés feladatok

Kattintson a jobb gombbal az előfizetés az Intéző ablaktáblát, sok gyors művelet feladatokat hajthat végre:

* Kattintson a jobb gombbal egy Azure Cosmos DB fiókot vagy egy adatbázisban, dönthet úgy **nyissa meg a portál** és kezelése az Azure portálon a böngészőben az erőforrás.

     ![Nyissa meg a portálon](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Azt is megteheti Azure Cosmos DB fiók, adatbázis, gyűjtemény **gyorselérési**.
* **Keresés itt** lehetővé teszi, hogy kulcsszavas keresést a kiválasztott elérési úton található.

    ![Itt keresése](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Adatbázis és gyűjtemény kezelése
#### <a name="create-a-database"></a>Adatbázis létrehozása 
Kattintson a jobb gombbal a Azure Cosmos DB fiók, válassza a **Create Database**, bemeneti adatbázis nevét, és nyomja le az **Enter** befejezéséhez.

![Adatbázis létrehozása](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Adatbázis törlése
Kattintson a jobb gombbal az adatbázist, kattintson a **adatbázis törlése**, és kattintson a **Igen** az előugró ablakban. Az adatbázis-csomópont törlődik, és az Azure Cosmos DB fiók automatikusan frissíti.

![Database1 törlése](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Database2 törlése](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Gyűjtemény létrehozása
Kattintson a jobb gombbal az adatbázishoz, válassza a **gyűjtemény létrehozása**, és adja meg a következő információkat, például **Gyűjteményazonosító**, **tárolási kapacitás**stb. A befejezéshez kattintson az **OK** gombra. A partíciós kulcs beállításai információkért lásd: [terv a particionálás](partition-data.md#designing-for-partitioning).

Ha egy partíció van létrehozásához használja a gyűjtemény létrehozásának befejezése után, a partíciós kulcs értékét a gyűjtemény nem módosítható.

![Collection1 létrehozása](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Collection2 létrehozása](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>A gyűjtemény törlése
Kattintson a jobb gombbal a gyűjteményhez, kattintson a **gyűjtemény törlése**, és kattintson a **Igen** az előugró ablakban. 

A gyűjtemény csomópont törlődik, és automatikusan frissíti az adatbázist.  

![Gyűjtemény törlése](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>A dokumentum kezelése

#### <a name="create-and-modify-documents"></a>Létrehozása és módosítása a dokumentumok
Hozzon létre egy új dokumentumot, nyissa meg a **dokumentumok** kattintson a bal oldali **új dokumentum**, szerkesztheti a tartalmakat, a jobb oldali ablaktáblán, majd kattintson a **mentése**. Létező dokumentum frissíteni, és kattintson **mentése**. Módosítások is elvesznek kattintva **elvetése**.

![Dokumentum](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Dokumentum törlése
Kattintson a **törlése** gombbal pedig törölheti a kijelölt dokumentumot.
#### <a name="query-for-documents"></a>A dokumentumok lekérdezés
A dokumentum-szűrő szerkesztése megadásával egy [SQL-lekérdezés](sql-api-sql-query.md) majd **alkalmaz**.

![Szűrés](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Tárolt eljárások, eseményindítók és felhasználó által megadott függvények kezelése
* A bal oldali fában tárolt eljárás létrehozásához kattintson a jobb gombbal **tárolt eljárás**, válassza ki **tárolt eljárás létrehozása**, adjon meg egy nevet a bal oldalon található, és írja be a tárolt eljárás parancsfájlok a megfelelő, majd Kattintson a **létrehozása**. 
* Szerkesztheti is meglévő tárolt eljárások duplán, így a frissítés, majd **frissítése** mentéséhez, vagy kattintson a **elvetése** megszakítja a módosítást.

![Tárolt eljárás](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* A műveletek **eseményindítók** és **UDF** hasonlóak a **tárolt eljárások**.

## <a name="next-steps"></a>Következő lépések

* Az alábbi videóban megtekinthet Azure Cosmos DB használata az Azure Tártallózó: [Azure Cosmos DB használja az Azure Tártallózó](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* További információ a Tártallózó, és csatlakozzon a további szolgáltatások [Ismerkedés a Tártallózó (előzetes verzió)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

