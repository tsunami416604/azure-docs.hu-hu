---
title: Az Azure Stream Analytics-feladatok figyelése és kezelése a PowerShell segítségével
description: Ez a cikk ismerteti, hogyan használhatja az Azure PowerShellt és a parancsmagokat az Azure Stream Analytics-feladatok figyelésére és kezelésére.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 7781f35fe7c17e4a0f307f559945caf648b23f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431709"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Stream Analytics-feladatok figyelése és kezelése az Azure PowerShell-parancsmagokkal
Megtudhatja, hogyan figyelheti és kezelheti a Stream Analytics-erőforrásokat az Azure PowerShell-parancsmagokkal és az alapvető Stream Analytics-feladatokat végrehajtó PowerShell-parancsfájlokkal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Az Azure PowerShell-parancsmagok futtatásának előfeltételei a Stream Analytics szolgáltatáshoz
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. Az alábbiakban egy minta Azure PowerShell-parancsfájl. Az Azure PowerShell-információkról az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál.  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription �SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> A programozott módon létrehozott Stream Analytics-feladatok alapértelmezés szerint nincsenek engedélyezve a figyelés.  Manuálisan engedélyezheti a figyelést az Azure Portalon, ha navigál a feladat figyelője lapra, és az Engedélyezés gombra kattint, vagy ezt programozott módon teheti meg az [Azure Stream Analytics – Stream Analytics-feladatok figyelése programozott módon című lépéseit követve.](stream-analytics-monitor-jobs.md)
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-parancsmagok a Stream Analytics szolgáltatáshoz
A következő Azure PowerShell-parancsmagok használhatók az Azure Stream Analytics-feladatok figyelésére és kezelésére. Vegye figyelembe, hogy az Azure PowerShell különböző verziókat rendelkezik. 
**A példákban az első parancs az Azure PowerShell 0.9.8, a második parancs az Azure PowerShell 1.0.** Az Azure PowerShell 1.0-parancsok mindig az "Az" a parancsban.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzstreamAnalyticsJob
Felsorolja az Azure-előfizetésben vagy a megadott erőforráscsoportban definiált összes Stream Analytics-feladatot, vagy leadja a feladatinformációkat egy adott feladatról egy erőforráscsoporton belül.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Ez a PowerShell-parancs az Azure-előfizetésben szereplő összes Stream Analytics-feladatadatait adja vissza.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Ez a PowerShell-parancs a StreamAnalytics-Default-Central-US erőforráscsoport összes Stream Analytics-feladatával kapcsolatos információt adja vissza.

**3. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Ez a PowerShell-parancs a StreamAnalytics-Default-Central-US erőforráscsoport Stream Analytics-feladatával kapcsolatos információkat adja vissza.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzstreamanalyticsInput
Egy adott Stream Analytics-feladatban definiált összes bemenet et sorol fel, vagy információt kap egy adott bemenetről.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs a StreamingJob feladatban definiált összes bemenetadatait adja vissza.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Ez a PowerShell-parancs a StreamingJob feladatban definiált EntryStream nevű bemenetadatait adja vissza.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzstreamAnalyticskimenet
Egy adott Stream Analytics-feladatban definiált összes kimenet et sorol fel, vagy egy adott kimenetről kap információt.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs a StreamingJob feladatban definiált kimenetekről ad vissza információkat.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Ez a PowerShell-parancs a StreamingJob feladatban definiált Output nevű kimenetre vonatkozó információkat adja vissza.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Információt kap a kvóta streamelési egységek egy adott régióban.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota �Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota �Location "Central US" 
```

Ez a PowerShell-parancs az USA középső régiójában lévő streamelési egységek kvótájáról és használatával kapcsolatos információkat adja vissza.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzstreamAnalyticsTransformation
Információt kap egy Stream Analytics-feladatban definiált adott átalakításról.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Ez a PowerShell-parancs a StreamingJob nevű átalakítással kapcsolatos információkat adja vissza a StreamingJob feladatban.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>Új-AzureStreamAnalyticsInput | Új-AzstreamanalyticsInput
Új bemenetet hoz létre egy Stream Analytics-feladaton belül, vagy frissíti a meglévő megadott bemenetet.

A bemenet neve megadható a .json fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban lévő névnek meg kell egyeznie a fájlban lévővel.

Ha olyan bemenetet ad meg, amely már létezik, és nem adja meg a Kényszerít paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő bemenetet.

Ha megadja a Force paramétert, és megad egy meglévő bemeneti nevet, a bemenetet megerősítés nélkül cseréli le a rendszer.

A JSON-fájl szerkezetéről és tartalmáról a Stream Analytics felügyeleti REST [API referenciatárának][stream.analytics.rest.api.reference] [Bemenet létrehozása (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] című szakaszában talál részletes információt.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Ez a PowerShell-parancs új bemenetet hoz létre az Input.json fájlból. Ha a bemeneti definíciós fájlban megadott névvel rendelkező meglévő bemeneti bemeneti fájl már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Ez a PowerShell-parancs új bemenetet hoz létre az EntryStream nevű feladatban. Ha már definiált egy ilyen nevű meglévő bemenetet, a parancsmag megkérdezi, hogy cserélje ki.

**3. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Ez a PowerShell-parancs lecseréli az EntryStream nevű meglévő bemeneti forrás definícióját a fájlból származó definícióra.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>Új-AzureStreamAnalyticsJob | Új-AzstreamAnalyticsJob
Új Stream Analytics-feladatot hoz létre a Microsoft Azure-ban, vagy frissíti egy meglévő megadott feladat definícióját.

A feladat neve megadható a .json fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban lévő névnek meg kell egyeznie a fájlban lévővel.

Ha olyan feladatnevet ad meg, amely már létezik, és nem adja meg a Kényszerítés paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő feladatot.

Ha megadja a Kényszerítés paramétert, és megad egy meglévő feladatnevet, a feladatdefiníció t megerősítés nélkül cseréli le.

A JSON-fájlszerkezetről és -tartalomról a [Stream Analytics-kezelés felügyeleti REST API referenciakönyvtárának][stream.analytics.rest.api.reference] [Stream Analytics-feladat létrehozása][msdn-rest-api-create-stream-analytics-job] című szakaszában talál részletes információt.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Ez a PowerShell-parancs új feladatot hoz létre a Feladatdefinition.json definíciójából. Ha a feladatdefiníciós fájlban megadott névvel rendelkező meglévő feladat már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Ez a PowerShell-parancs lecseréli a StreamingJob feladatdefinícióját.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>Új-AzureStreamAnalyticsOutput | Új-AzstreamAnalyticskimenet
Új kimenetet hoz létre egy Stream Analytics-feladaton belül, vagy egy meglévő kimenetet frissít.  

A kimenet neve megadható a .json fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban lévő névnek meg kell egyeznie a fájlban lévővel.

Ha olyan kimenetet ad meg, amely már létezik, és nem adja meg a Kényszerítés paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő kimenetet.

Ha megadja a Force paramétert, és megad egy meglévő kimeneti nevet, a kimenet et megerősítés nélkül cseréli le a rendszer.

A JSON-fájl szerkezetéről és tartalmáról a Stream Analytics felügyeleti REST [API referenciatárának][stream.analytics.rest.api.reference] [Kimenet létrehozása (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] című szakaszában talál részletes információt.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Ez a PowerShell-parancs létrehoz egy új kimenetet, amelyet "kimenet" néven hoz létre a StreamingJob feladatban. Ha már definiált egy ilyen nevű meglévő kimenetet, a parancsmag megkérdezi, hogy cserélje ki.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Ez a PowerShell-parancs lecseréli a "kimenet" definícióját a StreamingJob feladatban.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | Új-Azstreamanalyticstransformation
Új átalakítást hoz létre egy Stream Analytics-feladaton belül, vagy frissíti a meglévő átalakítást.

Az átalakítás neve megadható a .json fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban lévő névnek meg kell egyeznie a fájlban lévővel.

Ha olyan átalakítást ad meg, amely már létezik, és nem adja meg a Kényszerít ás paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő átalakítást.

Ha megadja a Force paramétert, és megad egy meglévő átalakítási nevet, az átalakítás megerősítés nélkül lecserélődik.

A JSON-fájl szerkezetéről és tartalmáról a Stream Analytics felügyeleti REST [API referenciakönyvtárának][stream.analytics.rest.api.reference] [Átalakítási létrehozása (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] című szakaszában talál részletes információt.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Ez a PowerShell-parancs egy új átalakítást hoz létre StreamingJobTransform néven a StreamingJob feladatban. Ha egy meglévő átalakítás már definiálva van ezzel a névvel, a parancsmag megkérdezi, hogy cserélje ki.

**2. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

 Ez a PowerShell-parancs felváltja a StreamingJobTransform definícióját a StreamingJob feladatban.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Eltávolítás-AzureStreamAnalyticsInput | Eltávolítás-AzstreamanalyticsInput
Aszinkron módon töröl egy adott bemenetet egy Stream Analytics-feladatból a Microsoft Azure-ban.  
Ha megadja a Force paramétert, a bemenet megerősítés nélkül törlődik.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Ez a PowerShell-parancs eltávolítja a bemeneti EventStream a feladat StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Eltávolítás-AzureStreamAnalyticsJob | Eltávolítás-AzstreamAnalyticsJob
Aszinkron módon töröl egy adott Stream Analytics-feladatot a Microsoft Azure-ban.  
Ha megadja a Kényszerítés paramétert, a feladat megerősítés nélkül törlődik.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Ez a PowerShell parancs eltávolítja a feladat StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Eltávolítás-AzureStreamAnalyticsOutput | Remove-AzstreamAnalyticsOutput
Aszinkron módon töröl egy adott kimenetet egy Stream Analytics-feladatból a Microsoft Azure-ban.  
Ha megadja a Force paramétert, a kimenet megerősítés nélkül törlődik.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Ez a PowerShell-parancs eltávolítja a kimeneti kimenetet a feladat StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzstreamAnalyticsJob
Aszinkron módon telepíti és elindítja a Stream Analytics-feladatot a Microsoft Azure-ban.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Ez a PowerShell-parancs elindítja a streamingjob feladatot egy egyéni kimeneti kezdési idő beállítással december 12, 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzstreamAnalyticsJob
Aszinkron módon leállítja a Stream Analytics-feladat futtatását a Microsoft Azure-ban, és leállítja a használatban lévő erőforrások lefoglalását. A feladatdefiníció és a metaadatok továbbra is elérhetők maradnak az előfizetésen belül az Azure Portalon és a felügyeleti API-kon keresztül, így a feladat szerkeszthető és újraindítható. A leállított állapotban lévő feladatért nem kell fizetnie.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Ez a PowerShell-parancs leállítja a StreamingJob feladatot.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzstreamanalyticsInput
Teszteli, hogy a Stream Analytics képes-e csatlakozni egy adott bemenethez.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Ez a PowerShell-parancs teszteli a bemeneti EntryStream kapcsolati állapotát a StreamingJob ban.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-Azstreamanalyticskimenet
Teszteli, hogy a Stream Analytics képes-e csatlakozni egy adott kimenethez.

**1. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Ez a PowerShell-parancs teszteli a kimeneti kimenet kapcsolati állapotát a StreamingJob ban.  

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

