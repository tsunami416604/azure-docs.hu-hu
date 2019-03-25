---
title: Az Azure Stream Analytics használata az SQL Data Warehouse |} A Microsoft Docs
description: Tippek az Azure Stream Analytics használatával Azure SQL Data Warehouse-megoldások fejlesztése.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 03/22/2019
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 3a260102e9964cfc248ed3dbabab487bd1c5dfc2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403765"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Az Azure Stream Analytics használata SQL Data warehouse-bA
Az Azure Stream Analytics egy teljes körűen felügyelt szolgáltatást, amely kis késleltetésű, magas rendelkezésre állású, méretezhető összetett eseményfeldolgozást keresztül streamelési adatok a felhőben. Olvassa el is alapismeretek a Mobilfunkciók [Azure Stream Analytics bemutatása][Introduction to Azure Stream Analytics]. Ezután megismerheti a Stream Analytics egy teljes körű megoldás létrehozása a következő a [első lépései az Azure Stream Analytics] [ Get started using Azure Stream Analytics] oktatóanyag.

Ebből a cikkből megtudhatja, az Azure SQL Data Warehouse-adatbázis használata a Stream Analytics-feladatok kimeneti fogadójaként.

## <a name="prerequisites"></a>Előfeltételek
Először haladjon végig a következő lépéseket a [első lépései az Azure Stream Analytics] [ Get started using Azure Stream Analytics] oktatóanyag.  

1. Hozzon létre egy Event Hub-bemenet
2. Konfigurálja és indítsa el az eseménylétrehozó alkalmazás
3. Stream Analytics-feladat kiépítése
4. Adja meg a feladat bemeneti és a lekérdezés

Ezután hozzon létre egy Azure SQL Data Warehouse-adatbázis

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Adja meg a feladat kimenete: Az Azure SQL Data Warehouse-adatbázis
### <a name="step-1"></a>1. lépés
A Stream Analytics-feladat kattintson **kimeneti** , az oldalon, majd kattintson a felső **hozzáadása**.

### <a name="step-2"></a>2. lépés
Válassza ki az SQL Database.

### <a name="step-3"></a>3. lépés
A következő oldalon adja meg a következő értékeket:

* *Kimeneti Alias*: Adjon meg egy rövid nevet a feladat kimenetének.
* *Előfizetés*:
  * Ha az SQL Data Warehouse-adatbázis nem ugyanahhoz az előfizetéshez tartozik, mint a Stream Analytics-feladat, válassza ki az SQL Database az aktuális előfizetésben.
  * Ha az adatbázis egy másik előfizetésben található, válassza ki az SQL Database egy másik előfizetésből.
* *Adatbázis*: Adja meg a cél-adatbázis nevét.
* *Kiszolgálónév*: Adja meg az imént megadott adatbázis a kiszolgáló nevét. Az Azure portal segítségével megkeresésével.

![][server-name]

* *Felhasználónév*: Adja meg azon felhasználó felhasználónevét,-fiókot, amely rendelkezik írási engedéllyel az adatbázis.
* *Jelszó*: A megadott felhasználói fiók jelszavát adja meg.
* *tábla*: Adja meg a céloldali tábla nevét az adatbázisban.

![][add-database]

### <a name="step-4"></a>4. lépés
Kattintson a pipa gombra a feladat kimenetének hozzáadása, és győződjön meg arról, hogy Stream Analytics képes csatlakozni az adatbázishoz.

Sikeres a kapcsolat az adatbázissal, látni fogja a portálon értesítést. Az adatbázis kapcsolatának teszteléséhez a vizsgálat gombra.

## <a name="next-steps"></a>További lépések
Integráció áttekintését lásd: [SQL Data Warehouse-integráció áttekintése][SQL Data Warehouse integration overview].

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
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
