---
title: '& Lekérdezés létrehozása Azure Data Lake Analytics-Azure Portal'
description: Azure Data Lake Analytics-fiók létrehozása és U-SQL feladat beküldése az Azure Portalon.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315758"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Data Lake Analytics használatának első lépései az Azure Portalon
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ez az útmutató azt ismerteti, hogyan használhatja az Azure Portalt arra, hogy Azure Data Lake Analytics-fiókokat hozzon létre, feladatokat definiáljon az [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatokat küldjön el Data Lake Analytics-szolgáltatásokba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindításához **Azure-előfizetéssel** kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Most létre kell hoznia egy Data Lake Analytics és egy Azure Data Lake Storage Gen1-fiókot egy időben.  Ez az egyszerű lépés csupán 60 másodpercet vesz igénybe.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson az **Erőforrás létrehozása** >  **Adatok + analitika** > **Data Lake Analytics** elemre.
3. Adja meg az alábbi elemek értékeit:
   * **Név**: Nevezze el Data Lake Analytics-fiókját (csak kisbetűk és számok engedélyezettek).
   * **Előfizetés**: Válassza ki az Analytics-fiókhoz használt Azure-előfizetést.
   * **Erőforráscsoport**. Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat.
   * **Hely**. Válasszon egy Azure-adatközpontot az Azure Data Lake Analytics-fiókhoz.
   * **Data Lake Storage Gen1**: Az utasításokat követve hozzon létre egy új Data Lake Storage Gen1 fiókot, vagy válasszon ki egy meglévőt. 
4. Igény szerint tarifacsomagot is választhat a Data Lake Analytics-fiókhoz.
5. Kattintson a **Create** (Létrehozás) gombra. 


## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

A következő szöveg egy igen egyszerű U-SQL-szkript. Az IT-részleg egy kis adatkészletet határoz meg a parancsfájlban, majd az adatkészletet az alapértelmezett Data Lake Storage Gen1 fiókba írja `/data.csv`a nevű fájlként.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>U-SQL-feladat elküldése

1. A Data Lake Analytics-fiókban válassza az **Új feladat** lehetőséget.
2. Illessze be a fenti U-SQL-szkript szövegét. Nevezze el a feladatot. 
3. A feladat indításához kattintson az **Elküldés** gombra.   
4. Figyelje a feladat **Állapot** értékét, és várjon, amíg a feladat állapota **Sikeres** nem lesz.
5. Válassza az **Adatok**, majd a **Kimenetek** lapot. Válassza ki a `data.csv` nevű kimeneti fájlt, és tekintse meg a kimeneti adatokat.

## <a name="see-also"></a>Lásd még

* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
