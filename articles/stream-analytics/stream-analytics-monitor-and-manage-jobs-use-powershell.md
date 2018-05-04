---
title: Megfigyelés és kezelés Azure Stream Analytics-feladatok PowerShell használatával
description: Ez a cikk ismerteti az Azure PowerShell és a parancsmagok használata figyelésére és Azure Stream Analytics-feladatok kezelésére.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 61c26c55725c19f526680d70f3621d41e9590965
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Figyelheti és kezelheti a Stream Analytics-feladatok Azure PowerShell-parancsmagokkal
Megtudhatja, hogyan figyelheti és kezelheti a Stream Analytics erőforrásokat az Azure PowerShell-parancsmagok és a powershell-parancsprogramokról alapvető Stream Analytics-feladatok végrehajtása.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-parancsmagok futtatását a Stream Analytics előfeltételei
* Azure-erőforráscsoport létrehozása az előfizetésben. Egy Azure PowerShell-parancsfájlpélda a következő: Azure PowerShell információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview);  

Az Azure PowerShell 0.9.8-as:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Az Azure PowerShell 1.0:  

         # Log in to your Azure account
        Connect-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Hozta létre a Stream Analytics-feladatok nincs figyelés alapértelmezés szerint engedélyezett.  Manuálisan engedélyezheti a feladat figyelő lap megnyitásával, majd kattintson az Engedélyezés gombra az Azure portálon figyelése, vagy ehhez programozott módon helyen található lépéseket követve [Azure Stream Analytics - figyelő Stream Analytics feladatok programozottan](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>A Stream Analytics az Azure PowerShell-parancsmagjai
A következő Azure PowerShell-parancsmagok segítségével figyelheti és kezelheti az Azure Stream Analytics-feladatok. Vegye figyelembe, hogy az Azure PowerShell különböző verziói. 
**Az első parancs van a példákban szereplő Azure PowerShell 0.9.8-as, a második parancs egy Azure PowerShell 1.0.** Az Azure PowerShell 1.0 parancsok mindig lesz "AzureRM" a parancsot.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Az Azure-előfizetés vagy a megadott erőforráscsoport definiált összes Stream Analytics-feladatok listája, vagy egy adott feladat erőforráscsoporton belül feladat információ lekérése.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsJob

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

A PowerShell-parancs a Stream Analytics-feladatok kapcsolatos információkat az Azure-előfizetés adja vissza.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

A PowerShell-parancs a Stream Analytics-feladatok kapcsolatos információkat az erőforráscsoport StreamAnalytics-alapértelmezett-közép-amerikai adja vissza.

**3. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

A PowerShell-parancs a Stream Analytics-feladat StreamingJob kapcsolatos információkat az erőforráscsoport StreamAnalytics-alapértelmezett-közép-amerikai adja vissza.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
A bemenetek megadott Stream Analytics-feladatban definiált listája, vagy egy adott bevitel információ lekérése.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

A PowerShell-parancs a feladat StreamingJob definiált összes bemenet információt ad vissza.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

A PowerShell-parancs a feladat StreamingJob definiált EntryStream nevű bemeneti információt ad vissza.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
A megadott Stream Analytics-feladatban definiált kimenetek listája, vagy egy adott kimeneti információ lekérése.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

A PowerShell-parancs a feladat StreamingJob meghatározott a kimenetek információt ad vissza.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

A PowerShell-parancs a feladat StreamingJob meghatározott kimeneti nevű a kimeneti információt ad vissza.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Az adatfolyam-egységek meghatározott kvótát információ lekérése.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

A PowerShell-parancs a kvóta-és a streamelési egységek használati adatokat a központi US régió adja vissza.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Egy Stream Analytics-feladatban definiált átalakítási információ lekérése.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Az Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

A PowerShell-parancs a feladat StreamingJob StreamingJob meghívta az átalakítás információt ad vissza.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Létrehoz egy új bemeneti belül a Stream Analytics-feladat, vagy egy meglévő megadott bemeneti adatok frissítése.

A bemeneti neve adható meg a .JSON kiterjesztésű fájlt vagy a parancssorból. Ha mindkettő meg van adva, a parancssorban neve ugyanaz, mint a fájlban kell lennie.

Ha már létező bemeneti adja meg, és ne adjon meg a-Force paramétert, a parancsmag ekkor megkérdezi, hogy lecseréli a meglévő bemeneti-e.

Ha megadja a – Force paramétert, és adja meg egy létező adjon meg nevet, a bemeneti váltja megerősítés nélküli megadására.

A JSON-fájlstruktúra és a tartalmakat a részletes információkért tekintse meg a [bemeneti létrehozása (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] szakasza a [Stream Analytics felügyeleti REST API Reference Library][stream.analytics.rest.api.reference].

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

A PowerShell-parancs létrehoz egy új bemeneti Input.json fájlból. Ha egy meglévő bemeneti a bemeneti csomagdefiníciós fájl a megadott néven már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

A PowerShell-parancs létrehoz egy új bemeneti EntryStream nevű feladat. Ha egy meglévő bemeneti ezen a néven már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e.

**3. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

A PowerShell-parancs lecseréli a meglévő bemeneti forrás a definíciós fájlból EntryStream hívása definíciója.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Létrehoz egy új Stream Analytics-feladat a Microsoft Azure-ban, vagy egy meglévő megadott feladat definíciójának frissítése.

A feladat nevét a .JSON kiterjesztésű fájlt vagy a parancssorban adható meg. Ha mindkettő meg van adva, a parancssorban neve ugyanaz, mint a fájlban kell lennie.

Ha adja meg a feladat nevét, amely már létezik, és ne adja meg a-Force paramétert, a parancsmag ekkor megkérdezi, hogy lecseréli a meglévő feladat-e.

Ha megadja a – Force paramétert, és adjon meg egy létező feladat, a feladat definíciójához váltja megerősítés nélküli megadására.

A JSON-fájlstruktúra és a tartalmakat a részletes információkért tekintse meg a [Stream Analytics-feladat létrehozása] [ msdn-rest-api-create-stream-analytics-job] szakasza a [Stream Analytics felügyeleti REST API Reference Library][stream.analytics.rest.api.reference].

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

A PowerShell-parancs létrehoz egy új feladatot a JobDefinition.json definícióból. Ha egy meglévő feladat a feladat-definíciós fájl a megadott néven már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

A PowerShell-parancs a felváltja a StreamingJob a feladat definíciójához.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Létrehoz egy új kimeneti belül a Stream Analytics-feladat, vagy frissíti a meglévő kimenettel.  

A kimenet neve adható meg a .JSON kiterjesztésű fájlt vagy a parancssorból. Ha mindkettő meg van adva, a parancssorban neve ugyanaz, mint a fájlban kell lennie.

Ha egy már létező kimeneti adja meg, és ne adja meg a-Force paramétert, a parancsmag ekkor megkérdezi, hogy lecseréli a meglévő kimeneti-e.

Ha megadja a – Force paramétert, és adja meg egy létező kimeneti név, valamint a kimeneti váltja megerősítés nélküli megadására.

A JSON-fájlstruktúra és a tartalmakat a részletes információkért tekintse meg a [kimeneti létrehozása (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] szakasza a [Stream Analytics felügyeleti REST API Reference Library][stream.analytics.rest.api.reference].

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

A PowerShell-parancs egy új kimenetet a feldolgozás StreamingJob "kimeneti" nevű hoz létre. Ha egy meglévő kimeneti ezen a néven már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

A PowerShell-parancs a feladat StreamingJob váltja fel a "kimeneti" definícióját.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Létrehoz egy új átalakítása belül a Stream Analytics-feladat, vagy frissíti a meglévő transzformáció.

Az átalakítás neve adható meg a .JSON kiterjesztésű fájlt vagy a parancssorból. Ha mindkettő meg van adva, a parancssorban neve ugyanaz, mint a fájlban kell lennie.

Ha már létező átalakítás adja meg, és ne adja meg a-Force paramétert, a parancsmag ekkor megkérdezi, hogy lecseréli a meglévő átalakításában szerepel-e.

Ha megadja a – Force paramétert, és adjon meg egy meglévő átalakításában, az átalakítás váltja megerősítés nélküli megadására.

A JSON-fájlstruktúra és a tartalmakat a részletes információkért tekintse meg a [átalakítása létrehozása (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] szakasza a [Stream Analytics felügyeleti REST API Reference Library][stream.analytics.rest.api.reference].

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

A PowerShell-parancs a feladat StreamingJob StreamingJobTransform nevű új átalakítás hoz létre. Ha egy meglévő átalakítást ezen a néven már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e.

**2. példa**

Az Azure PowerShell 0.9.8-as:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Az Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 A PowerShell-parancs a felváltja a feldolgozás StreamingJob StreamingJobTransform definíciója.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Aszinkron módon törli egy adott bevitel a Stream Analytics-feladat, a Microsoft Azure-ban.  
Ha megadja a – Force paramétert a bemeneti törli megerősítés nélküli megadására.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Az Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

A PowerShell-parancs a feladat StreamingJob eltávolítja a bemeneti EventStream.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Aszinkron módon törli egy adott, a Microsoft Azure Stream Analytics-feladat.  
Ha megadja a – Force paramétert, a feladat törli megerősítés nélküli megadására.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Az Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

A PowerShell-parancs a feladat StreamingJob eltávolítja.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
A megadott kimeneti aszinkron módon töröl egy Microsoft Azure Stream Analytics-feladat.  
Ha megadja a – Force paramétert a kimeneti törli megerősítés nélküli megadására.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Az Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

A PowerShell parancs eltávolítja a kimenet a kimenetet a feldolgozás StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Aszinkron módon telepíti, és a Microsoft Azure Stream Analytics-feladat elindul.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Az Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

A PowerShell-paranccsal elindítja a feladatot, 2012. December 12., 12:12:12 értékre StreamingJob egyéni kimeneti kezdési időpontja UTC szerint.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Aszinkron módon történik a Stream Analytics-feladat, a Microsoft Azure-beli leáll, és felszabadítása az erőforrásokhoz, melyeket volt használatban. A feladat definíciójához, és a metaadatok elérhető marad belül az Azure portál és a felügyeleti API-k, az előfizetést, hogy a feladat szerkeszthető és újraindul. Nem kell fizetnie a feladat a leállított állapotban.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Az Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

A PowerShell-parancs a feladat StreamingJob leállítása.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
A Stream Analytics lehetőségét kapcsolódni a megadott bemeneti teszteli.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Az Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

A PowerShell-parancs a StreamingJob a bemeneti EntryStream kapcsolat állapotát teszteli.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
A Stream Analytics lehetőségét kapcsolódni a megadott kimeneti teszteli.

**1. példa**

Az Azure PowerShell 0.9.8-as:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Az Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

A PowerShell parancsot tesztek StreamingJob a kimenetet a kimeneti létesített kapcsolat állapotát.  

## <a name="get-support"></a>Támogatás kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

