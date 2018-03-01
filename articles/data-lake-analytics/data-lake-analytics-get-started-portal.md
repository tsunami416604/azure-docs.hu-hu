---
title: "Az Azure Data Lake Analytics használatának első lépései az Azure Portallal | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja az Azure Portalt egy Data Lake Analytics-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.openlocfilehash: ad5316add4b7806a03e777aee1757841ed866070
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Az Azure Data Lake Analytics használatának első lépései az Azure Portallal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Portalt Azure Data Lake Analytics-fiókok létrehozásához, feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-szolgáltatásokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindításához **Azure-előfizetéssel** kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

A következő lépésben egyidejűleg hozhat létre fiókot a Data Lake Analytics és a Data Lake Store szolgáltatáshoz.  Ez az egyszerű lépés csupán 60 másodpercet vesz igénybe.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson az **Erőforrás létrehozása** >  **Adatok + analitika** > **Data Lake Analytics** elemre.
3. Adja meg az alábbi elemek értékeit:
   * **Név**: Nevezze el a Data Lake Analytics-fiókot (kizárólag kisbetűk és számok használhatók).
   * **Előfizetés:** Válassza ki az Analytics-fiókhoz használt Azure-előfizetést.
   * **Erőforráscsoport**. Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat.
   * **Hely**. Válasszon egy Azure-adatközpontot az Azure Data Lake Analytics-fiókhoz.
   * **Data Lake Store**: Az útmutatót követve hozzon létre egy új Data Lake Store-fiókot, vagy válasszon ki egy már meglévőt. 
4. Igény szerint tarifacsomagot is választhat a Data Lake Analytics-fiókhoz.
5. Kattintson a **Create** (Létrehozás) gombra. 


## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

A következő szöveg egy igen egyszerű U-SQL-szkript. Az egyetlen funkciója, hogy meghatároz egy kisebb adatkészletet a szkriptben, amelyet aztán egy `/data.csv` nevű fájlként kiír a Data Lake Store-ba.

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

1. A Data Lake Analytics-fiókban kattintson az **Új feladat** lehetőségre.
2. Illessze be a fent bemutatott U-SQL-szkript szövegét. 
3. Kattintson a **Feladat elküldése** elemre.   
4. Várjon, amíg a feladat állapota **Sikeres** nem lesz.
5. Sikertelen művelet esetén lásd: [Data Lake Analytics-feladatok figyelése és hibaelhárítása](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
6. Kattintson a **Kimenet** fülre, majd a `data.csv` elemre. 

## <a name="see-also"></a>Lásd még

* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
