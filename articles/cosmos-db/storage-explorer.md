---
title: "Azure Cosmos DB kezelése az Azure Storage Explorerben"
description: "Megismerheti, hogyan kezelhető az Azure Cosmos DB az Azure Storage Explorerben."
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
ms.openlocfilehash: baa7eee614159f9c6af493aff74b27773471f7d7
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Azure Cosmos DB kezelése az Azure Storage Explorerben (előzetes verzió)

Az Azure Cosmos DB Azure Storage Explorerben történő használata lehetővé teszi a felhasználók számára az Azure Cosmos DB-entitások kezelését, az adatok módosítását, valamint a tárolt eljárások és eseményindítók frissítését olyan Azure-entitások mellett, mint a tárolóblobok és üzenetsorok. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat. Az Azure Storage Explorer jelenleg SQL <!--and MongoDB-->-fiókokat támogat. Az Azure Storage Explorer nem működik az Azure Cosmos DB helyi emulátorával. 

Ebben a cikkben megismerheti, hogyan használható a Storage Explorer az Azure Cosmos DB kezelésére.


## <a name="prerequisites"></a>Előfeltételek

Azure Cosmos DB-fiók az SQL API-hez <!--or MongoDB API-->. Ha nem rendelkezik fiókkal, az Azure Portalon létrehozhat egyet az [Azure Cosmos DB: SQL API-webalkalmazás létrehozása .NET-tel és az Azure Portallal](create-sql-api-dotnet.md) című cikkben leírtaknak megfelelően.

## <a name="installation"></a>Telepítés

Az Azure Storage Explorer legújabb elemei innen telepíthetők: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Már a Windows-, Linux- és MAC-alapú verzió is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. Az **Azure Storage Explorer** telepítését követően kattintson a bal oldali **beépülő modul** ikonra, ahogy az az alábbi képen is látható.
       
   ![Beépülő modul ikon](./media/storage-explorer/plug-in-icon.png)
 
2. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   ![Csatlakozás Azure-előfizetéshez](./media/storage-explorer/connect-to-azure-subscription.png)

2. Az **Azure bejelentkezési** párbeszédpanelen válassza a **Bejelentkezés** elemet, majd adja meg Azure hitelesítő adatait.

    ![Bejelentkezés](./media/storage-explorer/sign-in.png)

3. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Alkalmaz](./media/storage-explorer/apply-subscription.png)

    Az Explorer panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    ![Fióklista](./media/storage-explorer/account-list.png)

    Sikeresen csatlakoztatta az **Azure Cosmos DB-fiókot** az Azure-előfizetéshez.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Csatlakozás az Azure Cosmos DB-hez kapcsolati karakterlánc használatával

Az Azure Cosmos DB-hez történő csatlakozás másik módja a kapcsolati karakterlánc használata. Az alábbi lépéseket követve csatlakozhat kapcsolati karakterlánccal.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal az **Azure Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás az Azure Cosmos DB-hez…** lehetőséget.

    ![Csatlakozás az Azure Cosmos DB-hez kapcsolati karakterlánccal](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Válassza ki a fióktípusnak megfelelő <!--either--> **DocumentDB** <!--or **MongoDB**--> **Alapértelmezett szolgáltatást**, illessze be a **kapcsolati karakterláncot**, majd kattintson az **OK** gombra az Azure Cosmos DB-fiókhoz történő csatlakozáshoz. A kapcsolati karakterlánc lekérésével kapcsolatos információk: [A kapcsolati karakterlánc lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Kapcsolati karakterlánc](./media/storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Az Azure Cosmos DB-erőforrások kezelése

Az Azure Cosmos DB-fiók az alábbi műveletekkel kezelhető:
* A fiók megnyitása az Azure Portalon;
* Az erőforrás hozzáadása a gyorselérési listához;
* Erőforrások keresése és frissítése;
* Adatbázisok létrehozása és törlése;
* Gyűjtemények létrehozása és törlése;
* Dokumentumok létrehozása, szerkesztése, törlése és szűrése;
* Tárolt eljárások, eseményindítók és felhasználói függvények kezelése.

### <a name="quick-access-tasks"></a>Feladatok gyors elérése

Ha a jobb gombbal kattint egy előfizetésre az Explorer panelen, gyorsan végrehajthat számos műveletet:

* Kattintson a jobb gombbal egy Azure Cosmos DB-fiókra vagy -adatbázisra, majd a **Megnyitás a portálon** lehetőség kiválasztásával kezelheti az erőforrást a böngészőben az Azure Portalon.

     ![Megnyitás a portálon](./media/storage-explorer/open-in-portal.png)

* Emellett Azure Cosmos DB-fiókot, -adatbázist és -gyűjteményt is hozzáadhat a **gyorselérési** eszköztárhoz.
* A **Keresés innen** funkció lehetővé teszi a kulcsszavaknak a kijelölt útvonalon történő keresését.

    ![keresés innen:](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Adatbázisok és gyűjtemények kezelése
#### <a name="create-a-database"></a>Adatbázis létrehozása 
Kattintson a jobb gombbal az Azure Cosmos DB-fiókra, válassza az **Adatbázis létrehozása** elemet, adja meg az adatbázis nevét, majd a befejezéshez nyomja le az **Enter** billentyűt.

![Adatbázis létrehozása](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Adatbázis törlése
Kattintson a jobb gombbal az adatbázisra, kattintson az **Adatbázis törlése** elemre, majd kattintson az **Igen** gombra a felugró ablakban. A rendszer törli az adatbázis-csomópontot, és automatikusan frissíti az Azure Cosmos DB-fiókot.

![1. adatbázis törlése](./media/storage-explorer/delete-database1.png)  

![2. adatbázis törlése](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Gyűjtemény létrehozása
Kattintson a jobb gombbal az adatbázisra, válassza a **Gyűjtemény létrehozása** elemet, majd adja meg a következő információkat, amilyen például **Gyűjtemény azonosítója**, a **Tárkapacitás** stb. A befejezéshez kattintson az **OK** gombra. A partíciókulcs beállításaival kapcsolatos további információk: [Particionálásra való tervezés](partition-data.md#designing-for-partitioning).

Ha partíciókulcsot használt a gyűjtemény létrehozásakor, akkor a létrehozást követően a partíciókulcs értéke nem módosítható a gyűjteményben.

![1. gyűjtemény létrehozása](./media/storage-explorer/create-collection.png)

![2. gyűjtemény létrehozása](./media/storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Gyűjtemény törlése
Kattintson a jobb gombbal a gyűjteményre, kattintson a **Gyűjtemény törlése** elemre, majd kattintson az **Igen** gombra a felugró ablakban. 

A rendszer törli a gyűjteménycsomópontot, és automatikusan frissíti az adatbázist.  

![Gyűjtemény törlése](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumentumok kezelése

#### <a name="create-and-modify-documents"></a>Dokumentumok létrehozása és módosítása
Új dokumentum létrehozásához kattintson a bal oldali ablakban található **Dokumentumok** elemre, kattintson az **Új dokumentum létrehozása** lehetőségre, szerkessze a tartalmakat a jobb oldali ablaktáblában, majd kattintson a **Mentés** gombra. Frissíthet meglévő dokumentumot is, amelyet a **Mentés** gombra kattintva menthet. A módosítások elvetéséhez kattintson az **Elvetés** gombra.

![Dokumentum](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Dokumentum törlése
Kattintson a **Törlés** gombra a kijelölt dokumentum törléséhez.
#### <a name="query-for-documents"></a>Dokumentumok lekérdezése
Szerkessze a dokumentumszűrőt egy [SQL-lekérdezés](sql-api-sql-query.md) beírásával, majd kattintson az **Alkalmaz** gombra.

![Szűrés](./media/storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Tárolt eljárások, eseményindítók és felhasználói függvények kezelése
* Tárolt eljárás létrehozásához kattintson a jobb gombbal a bal oldali fában található **Tárolt eljárás** elemre, válassza a **Tárolt eljárás létrehozása** lehetőségre, adjon meg egy nevet a bal oldalon, írja be a tárolt eljárás szkriptjeit a jobb oldali ablakban, majd kattintson a **Létrehozás** gombra. 
* Meglévő tárolt eljárás szerkesztéséhez kattintson duplán az eljárásra, végezze el a frissítést, majd kattintson a **Frissítés** gombra a mentéshez, vagy kattintson az **Elvetés** gombra a módosítások visszavonásához.

![Tárolt eljárás](./media/storage-explorer/stored-procedure.png)

* Az **Eseményindítók** és **Felhasználói függvények** esetében használható műveletek hasonlóak a **Tárolt eljárások** esetében használhatókhoz.

## <a name="next-steps"></a>További lépések

* Tekintse meg a következő videót az Azure Cosmos DB Azure Storage Explorerben való használatáról: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Az Azure Cosmos DB használata Azure Storage Explorerben).
* További információk a Storage Explorerrel és további szolgáltatások csatlakoztatásával kapcsolatban: [Ismerkedés a Storage Explorer alkalmazással (előzetes verzió)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

