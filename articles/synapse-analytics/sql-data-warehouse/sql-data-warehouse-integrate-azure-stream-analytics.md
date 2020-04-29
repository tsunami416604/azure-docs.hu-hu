---
title: Azure Stream Analytics használata
description: Tippek az Azure Szinapszisban az adattárházban való Azure Stream Analytics használatára a valós idejű megoldások fejlesztéséhez.
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
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633287"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure Stream Analytics használata az Azure szinapszis Analytics használatával

A Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késleltetésű, magasan elérhető, skálázható, összetett eseményt biztosít a felhőben tárolt adatfolyam-adatfeldolgozáshoz. Az alapvető tudnivalókat a [Azure stream Analytics bevezetésének](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)olvasásával ismerheti meg. Ezt követően megtudhatja, hogyan hozhat létre teljes körű megoldást a Stream Analytics az [Azure stream Analytics oktatóanyag használatának első lépéseivel](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Ebből a cikkből megtudhatja, hogyan használhatja az adattárházat kimeneti fogadóként a Azure Stream Analytics feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure Stream Analytics feladat – Azure Stream Analytics feladat létrehozásához kövesse az [Ismerkedés a Azure stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oktatóanyagban című témakör lépéseit a következőre:  

    1. Event hub-bemenet létrehozása
    2. Event Generator-alkalmazás konfigurálása és elindítása
    3. Stream Analytics-feladatok kiépítése
    4. A feladatok bemenetének és lekérdezésének megadása
* Azure szinapszis SQL Pool adattárház – új adattárház létrehozásához kövesse a rövid útmutató lépéseit [egy új adattárház létrehozásához](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Adja meg az adatraktárra mutató folyamatos átviteli kimenetet

### <a name="step-1"></a>1. lépés

A Azure Portal lépjen a Stream Analytics feladatra, és kattintson a **kimenetek** elemre a **feladatok topológiai** menüjében.

### <a name="step-2"></a>2. lépés

Kattintson a **Hozzáadás** gombra, és válassza a **SQL Database** elemet a legördülő menüből.

![SQL Database kiválasztása](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>3. lépés

Írja be a következő értékeket:

* *Kimeneti alias*: adjon meg egy rövid nevet a feladatok kimenetének.
* *Előfizetés*:
  * Ha az adattárház ugyanabban az előfizetésben található, mint a Stream Analytics-feladatokkal, kattintson a SQL Database kiválasztása lehetőségre az ***előfizetésekben***.
  * Ha az adatbázis egy másik előfizetésben található, kattintson az SQL Database beállításainak manuális megadása lehetőségre.
* *Adatbázis*: válassza ki a célként szolgáló adatbázist a legördülő listából.
* *Felhasználónév*: adja meg egy olyan fiók felhasználónevét, amely rendelkezik írási engedéllyel az adatbázishoz.
* *Password (jelszó*): adja meg a megadott felhasználói fiók jelszavát.
* *Tábla*: adja meg a cél tábla nevét az adatbázisban.
* kattintson a **Save (Mentés** ) gombra

![Befejezett SQL Database űrlap](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>4. lépés

A teszt futtatása előtt létre kell hoznia a táblát az adattárházban.  Futtassa az alábbi táblázat-létrehozási parancsfájlt a SQL Server Management Studio (SSMS) vagy a választott lekérdezési eszköz használatával.

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

A Azure Portal Stream Analytics feladatokhoz kattintson a feladatokra.  Kattintson a ***teszt*** gombra a ***kimenet részletei*** ablaktáblán.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Ha az adatbázishoz való csatlakozás sikeres volt, megjelenik egy értesítés a portálon.

### <a name="step-6"></a>6. lépés

Kattintson a ***lekérdezés*** menü ***feladatok topológiája*** elemére, és módosítsa a lekérdezést, hogy adatokat helyezzen be a létrehozott stream-kimenetbe.  A lekérdezés teszteléséhez kattintson a ***kiválasztott lekérdezés tesztelése*** gombra.  Ha a lekérdezési teszt sikeres, kattintson a ***lekérdezés mentése*** gombra.

![Lekérdezés mentése](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7. lépés

Indítsa el a Azure Stream Analytics feladatot.  Kattintson a ***Start*** gombra az ***Áttekintés*** menüben.

![Stream Analytics-feladat indítása](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kattintson a ***Start gombra a kezdési*** feladatok ablaktáblán.

![Kattintson a Start gombra](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>További lépések

Az integráció áttekintését lásd: [egyéb szolgáltatások integrálása](sql-data-warehouse-overview-integrate.md).
További fejlesztési tippek: [tervezési döntések és kódolási technikák az adattárházak számára](sql-data-warehouse-overview-develop.md).
