---
title: "Több modellek létrehozása egy kísérlet |} Microsoft Docs"
description: "PowerShell használatával létrehozni több gépi tanulási modellek és webes Szolgáltatásvégpontok ugyanazt az algoritmust, de különböző képzési adatkészletek."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: 44551908c31151e7d8945a3c7c03303b17d8f059
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Több Machine Learning-modellek és webszolgáltatás-végpont létrehozása egy kísérletből a PowerShell használatával
Ez gyakori probléma machine learning: szeretne létrehozni, amelyek ugyanabban a munkafolyamatban képzési és azonos algoritmus sok modellek. De azt szeretné, hogy a különböző képzési adatkészletek bemeneti adatként. Ez a cikk bemutatja, hogyan ehhez az Azure Machine Learning Studióban léptékű csak egyetlen kísérlet használatával.

Például tegyük fel, a globális kerékpárt bérleti franchise vállalat tulajdonosa. A bérleti igény szerint a régebbi adatok alapján becsülhető egy regressziós modell kívánja. A világ különböző 1000 bérleti helyek rendelkezik, és mindegyik helyen dataset korábban összegyűjtött. Például a dátum, idő, időjárási és forgalom fontos szolgáltatásokat, és az egyes helyekre vonatkozó tartalmaznak.

Az adatkészletek egyesített verzióját egyszer használja az összes helyszínen modellje betanításához sikerült. De a helyek mindegyikén egy egyedi környezetben. Ezért jobb megközelítés lenne a külön-külön használatával mindegyik helyen a dataset regressziós modell betanításához. Így minden betanított modell sikerült figyelembe a különböző tárolási méretek, kötet, geográfiai, feltöltése, kerékpárt mobilbarát forgalom környezet, és több.

Amely lehet, hogy a legjobb módszer, de nem kívánja 1000 képzési kísérletek létrehozása az Azure Machine Learning minden egy egyedi helyet jelölő. Amellett, hogy egy túlságosan feladat, célszerű is tűnik nem elég hatékony, mivel minden egyes kísérlet ugyanazokat kivételével a képzés dataset összetevők kellene lennie.

Szerencsére Ez elvégezhető használatával a [Azure Machine Learning átképezési API](retrain-models-programmatically.md) és a feladat automatizálását [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Ahhoz, hogy a minta felgyorsulnak, a helyek való 1000 10 kevesebb. De azonos alapelvek és eljárások 1000 helyekre érvényesek. Azonban ha szeretné, hogy az 1000 adatkészletek képzése érdekében érdemes a következő PowerShell-parancsfájlok párhuzamos futtatásához. Módjáról a cikk túlmutat, de található példák PowerShell többszálas az interneten.  
> 
> 

## <a name="set-up-the-training-experiment"></a>A tanítási kísérletet beállítása
A példa [tanítási kísérletet](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) , hogy a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Nyissa meg az ehhez a kísérlethez a [Azure Machine Learning Studio](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Ahhoz, hogy kövesse az ebben a példában együtt, érdemes lehet egy szabad munkaterület helyett egy szabványos munkaterület használja. Minden ügyfél - 10-végpontok – összesen hozzon létre egy végpontot, és, amelyhez egy szabványos munkaterület, mivel egy szabad munkaterület korlátozódik 3 végpontok. Ha csak egy ingyenes munkaterület, csak módosítása csak th helyek esetében engedélyezi a parancsfájlok.
> 
> 

A kísérlet használ egy **adatok importálása** modul importálása a képzés dataset *customer001.csv* az Azure storage-fiók. Tegyük fel a tanítási adathalmazt gyűjtött összes kerékpárt bérleti helyét, és közötti fájlnévvel blob storage ugyanazon a helyen tárolja őket *rentalloc001.csv* való *rentalloc10.csv*.

![Kép](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy egy **webes szolgáltatás kimeneti** modul hozzá lett adva a **tanítási modell** modul.
Ha ehhez a kísérlethez webszolgáltatásként lett telepítve, a végpont társított kimeneti egy .ilearner fájl formátumát a betanított modell adja vissza.

Vegye figyelembe azt is, hogy egy webes szolgáltatás paraméter, amely meghatározza a URL-cím beállítása, amely a **és adatokat importálhat** modul használja. Ez lehetővé teszi, hogy a paraméter segítségével adjon meg egyedi képzési adatkészletek mindegyik helyen a modell betanításához.
Más módon sikerült ezt. A webes szolgáltatás paraméterrel egy SQL-lekérdezés segítségével adatokat lekérni az SQL Azure-adatbázis. Vagy használhat egy **webszolgáltatás bemenetét** DataSet adatkészletben átadása a webszolgáltatás modul.

![Kép](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most most futtatni a tanítási kísérletet az alapértelmezett érték használatával *rental001.csv* , a képzési adatkészlet. Ha megtekinti a kimenetét a **Evaluate** modul (kattintson a kimeneti, jelölje be **Visualize**), láthatja, beállíthatja a decent teljesítményének *AUC* = 0.91. Ekkor készen áll a tanítási kísérletet kívül webes szolgáltatás telepítése.

## <a name="deploy-the-training-and-scoring-web-services"></a>A képzés és webszolgáltatások pontozási központi telepítése
A képzési webszolgáltatás üzembe helyezése, kattintson a **webes szolgáltatások beállítása** a kísérletvászon alatt gombra, majd az **webes szolgáltatás telepítése**. Hívja meg a webszolgáltatás "" kerékpárt bérleti képzési".

Most kell telepítenie a pontozási webszolgáltatáshoz.
Ehhez kattintson **webes szolgáltatások beállítása** a vászonra, és válasszon alább **prediktív webszolgáltatás**. Ezzel létrehoz egy pontozási kísérletet.
Kell néhány kisebb mértékben webszolgáltatásként használható legyen. Távolítsa el a címke "számláló" oszlop a bemeneti adatok, és az eredmény csak a azonosítója és a megfelelő előre jelzett értéknek a(z) korlátozza.

Takarítson meg a munka, nyissa meg a [prediktív kísérletté](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) a gyűjteményben, amely már elő van készítve.

A webszolgáltatás üzembe helyezése, futtassa a prediktív kísérletté, majd kattintson a **webes szolgáltatás telepítése** a vászon alatti gombra. A pontozási webszolgáltatás "Kerékpárt bérleti pontozási" name ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 azonos webszolgáltatás-végpontok létrehozása a PowerShell használatával
Ez a webszolgáltatás tartalmaz egy alapértelmezett végpont. De érdekli nem, az alapértelmezett végpont óta nem lehet frissíteni. Mit kell tennie, ha a 10 további végpontokat, mindegyik helyen egy. Ehhez a PowerShell használatával.

Először állítsa be a PowerShell környezetet:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Ezután futtassa a következő PowerShell-parancsot:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Most létrehozott 10 végpontok és az összes tartalmazzák azonos betanítása modell betanítása a *customer001.csv*. Azokat az Azure-portálon tekintheti meg.

![Kép](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A PowerShell használatával külön tanítási adathalmazt használandó végpontok frissítése
A következő lépéssel frissítése érdekében szükség a végpontok minden ügyfélnél egyedi adatokat a egyedileg betanítása modellekkel. Ehhez azonban először kell ezek a modellek létrehozásához a **kerékpárt bérleti képzési** webes szolgáltatás. Lépjen vissza a **kerékpárt bérleti képzési** webes szolgáltatás. meg kell hívnia a BES végpont 10-szer adatkészletekkel 10 különböző képzési 10 különböző modell előállításához. Használja a **InovkeAmlWebServiceBESEndpoint** ehhez PowerShell-parancsmagot.

Adja meg azokat a blob storage-fiók hitelesítő adatait is kell `$configContent`. A mezőket, nevezetesen `AccountName`, `AccountKey`, és `RelativeLocation`. A `AccountName` egyike lehet a fióknevek látható módon a **Azure-portálon** (*tárolási* lap). A tárfiók kattintva a `AccountKey` billentyűkombináció lenyomásával is található a **elérési kulcsok kezelése** alsó gombra, és másolja a *elsődleges elérési kulcsot*. A `RelativeLocation` van az elérési út viszonyítva tárhelyét új modell tárolásához. Például az elérési út `hai/retrain/bike_rental/` nevű tárolót a következő parancsfájl pontok `hai`, és `/retrain/bike_rental/` találhatók. Jelenleg nem hozható létre almappákat a felhasználói felület portálon keresztül, de nincsenek [több Azure Tártallózók](../../storage/common/storage-explorers.md) , amely lehetővé teszi. Javasoljuk, hogy hozzon létre egy új tároló az alábbiak szerint tárolja az új betanított modellek (.iLearner fájlok) a tárolóban: a tároló lapon kattintson a **Hozzáadás** alsó gombra, és adjon neki nevet `retrain`. Összefoglalva, az alábbi parancsfájlt a szükséges változtatásokat vonatkoznak `AccountName`, `AccountKey`, és `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> A BES-végpont esetében az egyetlen támogatott mód ehhez a művelethez. Betanított modellek előállító RR-EKET nem használható.
> 
> 

10 különböző BES feladat konfigurációs json-fájlokat, létrehozása helyett a fent látható, dinamikusan inkább hozzon létre a konfigurációs karakterlánc. Majd hírcsatorna úgy, hogy a *jobConfigString* paramétere a **InvokeAmlWebServceBESEndpoint** parancsmag. Valójában nem szükséges, hogy megőrzi a lemezen.

Ha minden megfelelően működik, egy kis idő után kell megjelennie 10 .iLearner fájlok, a *model001.ilearner* való *model010.ilearner*, az Azure-tárfiókot. Most már készen áll a webszolgáltatás végpontjainak pontozási ezek a modellek használata a 10 frissíteni a **javítás-AmlWebServiceEndpoint** PowerShell-parancsmagot. Ne felejtse el újra, hogy a nem alapértelmezett végpontok programozott módon a korábban létrehozott csak javítás is.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Ez meglehetősen gyorsan fusson. A végrehajtás befejezése után fog sikeresen létrehozott 10 prediktív webszolgáltatás-végpontok. Minden egyes egyedileg az adott adatkészlettel betanítása bérleti helyre, mindezt egy egyetlen tanítási kísérletet a modell betanítását fogja tartalmazni. Ezt ellenőrizheti, hogy próbálja meg ezeket a végpontokat használatával hívja a **InvokeAmlWebServiceRRSEndpoint** parancsmagot, hogy biztosít számukra az ugyanazon bemeneti adatokkal. Tekintse meg a különböző előrejelzés eredményeket, mivel a modellek képzett különböző képzési készletekkel kell látnia.

## <a name="full-powershell-script"></a>Teljes PowerShell-parancsfájl
A teljes forráskód listája itt található:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
