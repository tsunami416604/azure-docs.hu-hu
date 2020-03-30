---
title: '& lekérdezés létrehozása Azure Data Lake Analytics – Azure portal'
description: Azure Data Lake Analytics-fiók létrehozása és U-SQL feladat beküldése az Azure Portalon.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315758"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Data Lake Analytics használatának első lépései az Azure Portalon
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ez az útmutató azt ismerteti, hogyan használhatja az Azure Portalt arra, hogy Azure Data Lake Analytics-fiókokat hozzon létre, feladatokat definiáljon az [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatokat küldjön el Data Lake Analytics-szolgáltatásokba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindításához **Azure-előfizetéssel** kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Most hozzon létre egy Data Lake Analytics és egy Azure Data Lake Storage Gen1 fiókot egy időben.  Ez az egyszerű lépés csupán 60 másodpercet vesz igénybe.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson **az Erőforrásadatok** >  **létrehozása + Analytics** > **Data Lake Analytics**elemre.
3. Adja meg az alábbi elemek értékeit:
   * **Név**: Nevezze el a Data Lake Analytics-fiókot (kizárólag kisbetűk és számok használhatók).
   * **Előfizetés:** Válassza ki az Analytics-fiókhoz használt Azure-előfizetést.
   * **Erőforráscsoport**. Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat.
   * **Hely**. Válasszon egy Azure-adatközpontot az Azure Data Lake Analytics-fiókhoz.
   * **Data Lake Storage Gen1:** Kövesse az utasítást egy új Data Lake Storage Gen1 fiók létrehozásához, vagy válasszon ki egy meglévőt. 
4. Igény szerint tarifacsomagot is választhat a Data Lake Analytics-fiókhoz.
5. Kattintson **a Létrehozás gombra.** 


## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

A következő szöveg egy igen egyszerű U-SQL-szkript. Mindössze annyit tesz, hogy definiál egy kis adatkészletet a parancsfájlon belül, majd az `/data.csv`adatkészletet az alapértelmezett Data Lake Storage Gen1 fiókba írja a nevű fájlként.

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
5. Válassza az **Adatok** lapot, majd a **Kimenetek** lapot. `data.csv`

## <a name="see-also"></a>Lásd még

* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md).
