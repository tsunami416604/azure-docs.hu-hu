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
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: hu-hu
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure PowerShellt Azure Data Lake Analytics-fiókok létrehozására, majd U-SQL-feladatok elküldéséhez és futtatásához. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="preparing-for-the-tutorial"></a>Felkészülés az oktatóanyag elvégzésére
Egy Data Lake Analytics-fiók létrehozásához először a következőket kell megadnia:

* **Azure-erőforráscsoport**: a Data Lake Analytics-fiókot egy Azure-erőforráscsoporton belül kell létrehoznia.
* **Data Lake Analytics-fióknév**: a Data Lake-fiók neve csak kisbetűket és számokat tartalmazhat.
* **Hely:** az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpont.
* **Alapértelmezett Data Lake Store-fiók**: minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake Store-fiók. A fiókoknak azonos helyen kell lenniük.

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják adattárolásra

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Ha még nem rendelkezik használható erőforráscsoporttal, akkor hozzon létre egyet. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

A naplók tárolásához minden Data Lake Analytics-fiók esetében szükség van egy alapértelmezett Data Lake Store-fiókra. Használhat újból egy meglévő fiókot, vagy létrehozhat egy újat. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Ha az erőforráscsoport és a Data Lake Store-fiók már elérhető, hozzon létre egy Data Lake Analytics-fiókot.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics-fiókkal kapcsolatos információk beszerzése

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL-feladat elküldése

Hozzon létre egy szövegfájlt az alábbi U-SQL-szkript alapján.

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

Küldje el a szkriptet.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>U-SQL-feladatok figyelése

Sorolja fel a fiókban lévő összes feladatot. A kimenet tartalmazza az aktuálisan futó és a nemrégiben befejezett feladatokat is.

```
Get-AdlJob -Account $adla
```

Kérje le egy adott feladat állapotát.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Ahelyett, hogy újra és újra meghívná a Get-AdlAnalyticsJob parancsmagot a feladat befejeződéséig, használja a Wait-AdlJob parancsmagot.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

A feladat befejezése után a mappában található fájlok listázásával győződjön meg arról, hogy a kimeneti fájl létezik.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Ellenőrizze, hogy létezik-e a fájl.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Fájlok fel- és letöltése

Töltse le az U-SQL-szkript kimenetét.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Töltse fel az U-SQL-szkript bemeneteként használni kívánt fájlt.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Lásd még:
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).

