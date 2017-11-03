---
title: "Az Azure Stream Analytics használja az SQL Data Warehouse szolgáltatással |} Microsoft Docs"
description: "Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások Azure Stream Analytics használ."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 14783f0464764a11d7f03a5db1c2d63728a4cb50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Az Azure Stream Analytics használja az SQL Data Warehouse szolgáltatással
Az Azure Stream Analytics egy olyan teljes körűen felügyelt szolgáltatás biztosítása kis késleltetésű, magas rendelkezésre állású, méretezhető összetett eseményfeldolgozást keresztül a streamelési adatok a felhőben. Az alapok tanul olvasása [Azure Stream Analytics bemutatása][Introduction to Azure Stream Analytics]. Majd megismerheti egy végpont megoldás létrehozása a Stream Analytics a következő a [Azure Stream Analytics használatának első] [ Get started using Azure Stream Analytics] oktatóanyag.

Meg ebből a cikkből megtudhatja, hogyan használható az Azure SQL Data Warehouse-adatbázis szolgáltatást kimeneti fogadóként a gőz Analytics-feladatok.

## <a name="prerequisites"></a>Előfeltételek
Először futtassa a következő lépések a [Azure Stream Analytics használatának első] [ Get started using Azure Stream Analytics] oktatóanyag.  

1. Az Event Hubs bemeneti létrehozása
2. Konfigurálni és elindítani az esemény-készítő alkalmazás
3. A Stream Analytics-feladat kiépítése
4. Adja meg a feladat bemeneti és a lekérdezés

Ezután hozzon létre egy Azure SQL Data Warehouse-adatbázis

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Adja meg a feladat kimenetére: Azure SQL Data Warehouse-adatbázis
### <a name="step-1"></a>1. lépés
Kattintson a Stream Analytics-feladat **kimeneti** , és kattintson a lista elejéről **hozzáadása kimeneti**.

### <a name="step-2"></a>2. lépés
Válassza ki az SQL-adatbázist, és kattintson a Tovább gombra.

![][add-output]

### <a name="step-3"></a>3. lépés
Adja meg a következő értékeket a következő lapon:

* *A kimeneti Alias*: Adjon meg egy rövid nevet a feladat kimenete.
* *Előfizetés*:
  * Ha az SQL Data Warehouse-adatbázis a Stream Analytics-feladat tárolóként ugyanazt az előfizetést, válassza ki az SQL Database a jelenlegi előfizetés.
  * Ha az adatbázis egy másik előfizetésben, válassza ki az SQL Database egy másik előfizetésből.
* *Adatbázis*: Adja meg a célként megadott adatbázis nevét.
* *Kiszolgálónév*: Adja meg az adatbázis csak a megadott kiszolgáló nevét. A klasszikus Azure portál segítségével ez.

![][server-name]

* *Felhasználónév*: Adja meg az adatbázis írási engedéllyel rendelkező fiók felhasználónevét.
* *Jelszó*: írja be a megadott felhasználói fiók jelszavát.
* *Tábla*: Adja meg a nevét a céloldali tábla az adatbázisban.

![][add-database]

### <a name="step-4"></a>4. lépés
Kattintson a pipa gombra a feladat kimenetére hozzáadása, és győződjön meg arról, hogy a Stream Analytics képes csatlakozni az adatbázishoz.

![][test-connection]

Ha a kapcsolat az adatbázissal sikeres, akkor megjelenik egy értesítés a portál alján. Kapcsolat tesztelése gombra az alsó tesztelje a kapcsolatot az adatbázis.

## <a name="next-steps"></a>Következő lépések
Az integráció áttekintéséért lásd: [SQL Data Warehouse integrációjának áttekintése][SQL Data Warehouse integration overview].

További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
