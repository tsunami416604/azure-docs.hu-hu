---
title: Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel
description: Az Azure PowerShell-lel létrehozhat egy Azure Data Lake Analytics-fiókot, és beküldhet egy U-SQL feladatot.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: get-started-article
ms.date: 05/04/2017
ms.openlocfilehash: 70534b050577e749310d81b50c300c7d98e416af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623087"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure PowerShellt Azure Data Lake Analytics-fiókok létrehozására, majd U-SQL-feladatok elküldéséhez és futtatásához. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure Data Lake Analytics-fiók**. Lásd: [Ismerkedés a Data Lake Analytics szolgáltatással](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ez az oktatóanyag feltételezi az Azure PowerShell használatának előzetes ismeretét. Az előzetes ismeretek fontos része az Azure-ba történő bejelentkezés. Ha segítségre van szüksége, tekintse meg az [Ismerkedés az Azure PowerShell szolgáltatással](https://docs.microsoft.com/powershell/azure/get-started-azureps) című cikket.

Előfizetés nevével történő bejelentkezéshez:

```
Connect-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Az előfizetés neve helyett előfizetés-azonosítót is használhat a bejelentkezéshez:

```
Connect-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
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

Küldje el a szkript szövegét a `Submit-AdlJob` parancsmaggal és a `-Script` paraméterrel.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" �Script $script
```

Másik lehetőségként elküldhet egy szkriptfájlt a `-ScriptPath` paraméterrel:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" �ScriptPath $filename
```

A feladatok állapotát a `Get-AdlJob` parancsmaggal kérheti le. 

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Ahelyett, hogy újra és újra meghívná a Get-AdlJob parancsmagot a feladat befejezéséig, használja a `Wait-AdlJob` parancsmagot.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Töltse le a kimeneti fájlt az `Export-AdlStoreItem` parancsmaggal.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Lásd még
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
