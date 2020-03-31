---
title: Az Azure Stream Analytics használata
description: Tippek az Azure Stream Analytics és az adattárház használatával az Azure Synapse-ban a valós idejű megoldások fejlesztéséhez.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350433"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Az Azure Stream Analytics használata az Azure Synapse Analytics segítségével

Az Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely alacsony késleltetésű, magas rendelkezésre állású, méretezhető összetett eseményfeldolgozást biztosít a felhőben lévő streamelési adatokon keresztül. Az alapokat az Azure Stream Analytics bemutatása című [elolvasásával ismerheti](../../stream-analytics/stream-analytics-introduction.md)meg. Ezután megtudhatja, hogyan hozhat létre végpontok között megoldást a Stream Analytics segítségével az [Azure Stream Analytics használatának első lépéseit](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) követően.

Ebben a cikkben megtudhatja, hogyan használhatja az adatraktárt kimeneti fogadóként az Azure Stream Analytics-feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure Stream Analytics-feladat – Az Azure Stream Analytics-feladat létrehozásához kövesse az [Azure Stream Analytics használatának első](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) lépéseit:  

    1. Event Hub-bemenet létrehozása
    2. Eseménygenerátor-alkalmazás konfigurálása és indítása
    3. Stream Analytics-feladat kiépítése
    4. Feladatbemenet és lekérdezés megadása
* Azure Synapse SQL pool adattárház – Új adattárház létrehozásához kövesse a rövid útmutató lépéseit [egy új adattárház létrehozásához.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Az adattárházra mutató streamelési kimenet megadása

### <a name="step-1"></a>1. lépés

Az Azure Portalon nyissa meg a Stream Analytics-feladatot, és kattintson a **Kimenetek** a **Feladat topológia** menüben.

### <a name="step-2"></a>2. lépés

Kattintson a **Hozzáadás** gombra, és válassza az **SQL Database** parancsot a legördülő menüből.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>3. lépés

Írja be a következő értékeket:

* *Kimeneti alias*: Adja meg a feladat kimenetének rövid nevét.
* *Előfizetés*:
  * Ha az adattárház ugyanabban az előfizetésben van, mint a Stream Analytics feladat, kattintson ***az SQL-adatbázis kiválasztása az előfizetésekből***elemre.
  * Ha az adatbázis egy másik előfizetésben van, kattintson az SQL Database beállításainak manuális megadása elemre.
* *Adatbázis*: Válassza ki a céladatbázist a legördülő listából.
* *Felhasználónév:* Adja meg az adatbázishoz írási engedéllyel rendelkező fiók felhasználónevét.
* *Jelszó*: Adja meg a megadott felhasználói fiók jelszavát.
* *:* Adja meg az adatbáziscéltábla nevét.
* kattintson a **Mentés** gombra

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>4. lépés

A teszt futtatása előtt létre kell hoznia a táblát az adatraktárban.  Futtassa a következő táblakészítő parancsfájlt az SQL Server Management Studio (SSMS) vagy a választott lekérdezési eszköz használatával.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>5. lépés

Az Azure Portalon a Stream Analytics-feladathoz kattintson a feladat nevére.  Kattintson a ***Teszt*** gombra a ***Kimenet részletei*** ablaktáblában.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)Amikor az adatbázissal való kapcsolat sikeres, megjelenik egy értesítés a portálon.

### <a name="step-6"></a>6. lépés

Kattintson a ***Feladattopológia csoport Lekérdezés*** menüparancsára, és módosítsa a lekérdezést úgy, hogy adatokat szúrjon be a létrehozott adatfolyam-kimenetbe. ***Job topology***  A lekérdezés teszteléséhez kattintson a ***Kijelölt lekérdezés tesztelése*** gombra.  Ha a ***lekérdezési*** teszt sikeres, kattintson a Lekérdezés mentése gombra.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7. lépés

Indítsa el az Azure Stream Analytics-feladatot.  Kattintson az ***Áttekintés*** menü ***Start*** gombjára.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

kattintson a ***Start*** gombra a kezdő feladat ablaktáblán.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>További lépések

Az integráció áttekintése az [Egyéb szolgáltatások integrálása](sql-data-warehouse-overview-integrate.md)című témakörben olvashat.
További fejlesztési tippeket az [adatraktárak tervezési döntései és kódolási technikái című témakörben talál.](sql-data-warehouse-overview-develop.md)
