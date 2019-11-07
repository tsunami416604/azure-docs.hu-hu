---
title: Azure Stream Analytics használata
description: Tippek a Azure Stream Analytics és a Azure SQL Data Warehouse használatával a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685721"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Azure Stream Analytics használata a SQL Data Warehouse
A Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késleltetésű, magasan elérhető, skálázható, összetett eseményt biztosít a felhőben tárolt adatfolyam-adatfeldolgozáshoz. Az alapvető tudnivalókat a [Azure stream Analytics bevezetésének][Introduction to Azure Stream Analytics]olvasásával ismerheti meg. Ezt követően megtudhatja, hogyan hozhat létre teljes körű megoldást a Stream Analytics az [Azure stream Analytics oktatóanyag használatának első lépéseivel][Get started using Azure Stream Analytics] .

Ebből a cikkből megtudhatja, hogyan használhatja a Azure SQL Data Warehouse-adatbázist kimeneti fogadóként a Stream Analytics feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek
Először hajtsa végre az alábbi lépéseket az első [lépések Azure stream Analytics][Get started using Azure Stream Analytics] oktatóanyagban.  

1. Event hub-bemenet létrehozása
2. Event Generator-alkalmazás konfigurálása és elindítása
3. Stream Analytics-feladatok kiépítése
4. A feladatok bemenetének és lekérdezésének megadása

Ezután hozzon létre egy Azure SQL Data Warehouse adatbázist

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Adja meg a feladatok kimenetét: Azure SQL Data Warehouse adatbázis
### <a name="step-1"></a>1\. lépés
A Stream Analytics-feladatokban kattintson a **kimenet** elemre az oldal tetején, majd kattintson a **Hozzáadás**gombra.

### <a name="step-2"></a>2\. lépés
Válassza a SQL Database lehetőséget.

### <a name="step-3"></a>3\. lépés
Adja meg a következő értékeket a következő oldalon:

* *Kimeneti alias*: adjon meg egy rövid nevet a feladatok kimenetének.
* *Előfizetés*:
  * Ha a SQL Data Warehouse-adatbázis a Stream Analytics feladatokhoz tartozó előfizetésben szerepel, válassza a SQL Database használata a jelenlegi előfizetésből lehetőséget.
  * Ha az adatbázis egy másik előfizetésben található, válassza a SQL Database használata másik előfizetésből lehetőséget.
* *Adatbázis*: adja meg a céladatbázis nevét.
* *Kiszolgáló neve*: adja meg az imént megadott adatbázis kiszolgálójának nevét. Ennek megkereséséhez használhatja a Azure Portal.

![][server-name]

* *Felhasználónév*: adja meg egy olyan fiók felhasználónevét, amely rendelkezik írási engedéllyel az adatbázishoz.
* *Password (jelszó*): adja meg a megadott felhasználói fiók jelszavát.
* *Tábla*: adja meg a cél tábla nevét az adatbázisban.

![][add-database]

### <a name="step-4"></a>4\. lépés
Kattintson a pipa gombra a feladatok kimenetének hozzáadásához és annak ellenőrzéséhez, hogy a Stream Analytics sikeresen tud-e csatlakozni az adatbázishoz.

Amikor az adatbázishoz való csatlakozás sikeres, megjelenik egy értesítés a portálon. A teszt gombra kattintva tesztelheti a kapcsolódást az adatbázishoz.

## <a name="next-steps"></a>További lépések
Az integráció áttekintése: [SQL Data Warehouse integráció áttekintése][SQL Data Warehouse integration overview].

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
