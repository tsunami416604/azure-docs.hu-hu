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
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721200"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure Stream Analytics használata az Azure szinapszis Analytics használatával
A Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késleltetésű, magasan elérhető, skálázható, összetett eseményt biztosít a felhőben tárolt adatfolyam-adatfeldolgozáshoz. Az alapvető tudnivalókat a [Azure stream Analytics bevezetésének](../stream-analytics/stream-analytics-introduction.md)olvasásával ismerheti meg. Ezt követően megtudhatja, hogyan hozhat létre teljes körű megoldást a Stream Analytics az [Azure stream Analytics oktatóanyag használatának első lépéseivel](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

Ebből a cikkből megtudhatja, hogyan használhatja az adattárház-adatbázist kimeneti fogadóként a Stream Analytics feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek
Először hajtsa végre az alábbi lépéseket az első [lépések Azure stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) oktatóanyagban.  

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

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Felhasználónév*: adja meg egy olyan fiók felhasználónevét, amely rendelkezik írási engedéllyel az adatbázishoz.
* *Password (jelszó*): adja meg a megadott felhasználói fiók jelszavát.
* *Tábla*: adja meg a cél tábla nevét az adatbázisban.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>4\. lépés
Kattintson a pipa gombra a feladatok kimenetének hozzáadásához és annak ellenőrzéséhez, hogy a Stream Analytics sikeresen tud-e csatlakozni az adatbázishoz.

Amikor az adatbázishoz való csatlakozás sikeres, megjelenik egy értesítés a portálon. A teszt gombra kattintva tesztelheti a kapcsolódást az adatbázishoz.

## <a name="next-steps"></a>Következő lépések
Az integráció áttekintését lásd: [egyéb szolgáltatások integrálása](sql-data-warehouse-overview-integrate.md).
További fejlesztési tippek: [tervezési döntések és kódolási technikák az adattárházak számára](sql-data-warehouse-overview-develop.md).

