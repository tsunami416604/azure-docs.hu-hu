---
title: "Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel | Microsoft Docs"
description: "Az Azure PowerShell használata egy Data Lake Analytics-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozására U-SQL használatával, valamint a feladat elküldésére. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.openlocfilehash: 4f73e27c733edae658d1ea3bdabe48076328279b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure PowerShellt Azure Data Lake Analytics-fiókok létrehozására, majd U-SQL-feladatok elküldéséhez és futtatásához. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure Data Lake Analytics-fiók**. Lásd: [Ismerkedés a Data Lake Analytics szolgáltatással](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ez az oktatóanyag feltételezi az Azure PowerShell használatának előzetes ismeretét. Az előzetes ismeretek fontos része az Azure-ba történő bejelentkezés. Ha segítségre van szüksége, tekintse meg az [Ismerkedés az Azure PowerShell szolgáltatással](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps) című cikket.

Előfizetés nevével történő bejelentkezéshez:

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Az előfizetés neve helyett előfizetés-azonosítót is használhat a bejelentkezéshez:

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Sikeres művelet esetén a parancs kimenete az alábbi szöveghez hasonlít:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Felkészülés az oktatóanyag elvégzésére

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják az adattárolásra:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics-fiókkal kapcsolatos információk beszerzése

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL-feladat elküldése

Hozzon létre egy PowerShell-változót a U-SQL-szkript tárolásához.

```
$script = @"
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

"@
```

Küldje el a szkriptet.

```
$job = Submit-AdlJob -AccountName $adla –Script $script
```

Azt is megteheti, hogy fájlként menti a szkriptet, és a következő paranccsal küldi el:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -AccountName $adla –ScriptPath $filename
```


Kérje le egy adott feladat állapotát. Használja a parancsmagot addig, amíg a feladat be nem fejeződik.

```
$job = Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Ahelyett, hogy újra és újra meghívná a Get-AdlAnalyticsJob parancsmagot a feladat befejeződéséig, használja a Wait-AdlJob parancsmagot.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Töltse le a kimeneti fájlt.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Lásd még:
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
