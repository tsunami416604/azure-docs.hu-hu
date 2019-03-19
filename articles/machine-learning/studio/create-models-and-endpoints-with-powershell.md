---
title: A modell több végpont létrehozása
titleSuffix: Azure Machine Learning Studio
description: A powershellel hozhat létre több Machine Learning-modellek és webszolgáltatás Szolgáltatásvégpontok ugyanazt az algoritmust, de a különböző képzési adathalmazok alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: a191a7adc2c43337b663fc44a8ef40df9d8ffef4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848918"
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Studio-modellek és webszolgáltatás-végpontok létrehozása egy kísérletből a PowerShell használatával

Machine learning gyakran okoz problémát a következő: Szeretne létrehozni, amelyek ugyanabban a munkafolyamatban képzés és az azonos algoritmust használja számos modellt. De azt szeretné, hogy különböző képzési adathalmazok alapján bemenetként. Ez a cikk bemutatja, hogyan ipari méretekben az Azure Machine Learning Studióban ehhez csupán egyetlen kísérletben használatával.

Például tegyük fel, a saját egy globális uci kerékpárkölcsönzési névhasználati üzleti. Szeretné az előzményadatok alapján bérleti kereslet előrejelzésére regressziós modell létrehozása. 1000 bérleti helyek rendelkezik világszerte, és egy adatkészletet, mindegyik helyen már összegyűjtött. Fontos szolgáltatások például a dátum, idő, időjárás és a forgalom és az egyes helyekre vonatkozó tartalmazzák.

A modell egyszer az összes adatkészlet egyesített verziót használ, az összes hely között lehetett betanításához. De a helyek mindegyike rendelkezik egy egyedi környezetben. Így jobb módszer lehet a külön-külön mindegyik helyen a az adatkészlet használatával regressziós modell betanításához. Ezzel a módszerrel minden betanított modell sikerült vegye figyelembe a különböző tároló méretek, kötet, földrajzi hely, population, kerékpár-barát forgalom környezet és egyéb.

Amely lehet, hogy a legjobb módszer, de nem kívánja 1000 képzési kísérletek létrehozása az Azure Machine Learning Studio egyes egy egyedi helyet jelölő. Amellett, hogy egy eleve bonyolult feladatnak, is úgy tűnik, nem hatékony, mivel minden kísérlet ugyanazokat a betanítási adatkészletet kivételével összetevők lenne.

Szerencsére, ennek segítségével végezheti a [Azure Machine Learning Studio átképezési API](/azure/machine-learning/studio/retrain-machine-learning-model) és automatizálja a feladat [Azure Machine Learning Studio PowerShell](powershell-module.md).

> [!NOTE]
> Ahhoz, hogy a minta gyorsabban futnak, a helyek való 1000 10 számának csökkentése. Azonban ugyanazon alapelveket és eljárásokat a alkalmazni 1000 helyekre. Azonban ha szeretné, hogy 1000 adatkészletekből származó betanításához célszerű párhuzamos az alábbi PowerShell-parancsfájlok futtatásához. Hogyan valósítható meg ez a cikk nem foglalkozik, de annak PowerShell példái többszálas az interneten.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Állítsa be a tanítási kísérlet
A példát követve [betanítási kísérlet](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) , amely szerepel a [Cortana Intelligence Gallery](https://gallery.azure.ai). Ez a kísérlet megnyitásához a [Azure Machine Learning Studio](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Annak érdekében, hogy kövesse az ebben a példában együtt, érdemes inkább a standard munkaterületet, mint egy ingyenes munkaterületet. Egy végpontot hoz létre minden egyes ügyfél - összesen 10 végpont - és a standard munkaterületre igénylő, mivel az ingyenes munkaterületre legfeljebb 3 végpontok. Ha csak egy ingyenes munkaterületet, egyszerűen csak megváltoztatja a parancsfájlok csak th helyeket engedélyezéséhez.
> 
> 

A kísérlet használ egy **adatok importálása** modul importálása a betanítási adatkészletet *customer001.csv* egy Azure storage-fiókból. Tegyük fel, képzési adathalmazok gyűjtött összes uci kerékpárkölcsönzési helyekről, és a fájl nevéből és a ugyanazon a helyen a blob storage tárolja őket *rentalloc001.csv* való *rentalloc10.csv*.

![Adatolvasó modulja adatokat importál az Azure-blobba](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy egy **webes szolgáltatás kimeneti** modul bővült a **tanítási modell** modul.
Amikor ez a kísérlet üzembe webszolgáltatásként, a végponthoz társított kimeneti egy .ilearner fájl formátumát a betanított modell adja vissza.

Vegye figyelembe, hogy egy webes szolgáltatás paraméter, amely meghatározza a URL-cím beállítása, amely a **adatok importálása** modul használja. Ez lehetővé teszi, hogy a paraméter adja meg az egyes képzési adathalmazok alapján minden helyen a modell betanításához.
Sikerült ezt egyéb módon is. SQL-lekérdezés egy webes szolgáltatás paraméterrel segítségével adatokat kérhet le egy SQL Azure adatbázis. Vagy használhat egy **webes bemenet** átadása egy adatkészlet a webszolgáltatás modul.

![Egy webes szolgáltatás kimeneti modul kimenete egy Trained Model-modul](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most futtassa a betanítási kísérlet az alapértelmezett érték használatával *rental001.csv* képzési adatkészletként. Ha megtekinti a kimenetét a **Evaluate** modul (kattintson a kimeneti, és válassza ki **Visualize**), megjelenik egy finomat teljesítményét felveheti *AUC* = 0.91. Ezen a ponton készen áll a betanítási kísérlet webszolgáltatás üzembe helyezéséhez.

## <a name="deploy-the-training-and-scoring-web-services"></a>A tanítási és pontozási webszolgáltatások üzembe helyezése
A képzési webszolgáltatás üzembe helyezéséhez kattintson a **webszolgáltatás beállítása** gombot a kísérletvászon alatt **webszolgáltatás üzembe helyezése**. Hívja meg a webszolgáltatás "Uci Kerékpárkölcsönzési képzési".

Most meg kell a pontozási webszolgáltatás üzembe helyezéséhez.
Ehhez kattintson **webszolgáltatás beállítása** a vásznon, majd válassza alább **prediktív webszolgáltatás**. Ez létrehoz egy pontozó kísérletet.
Együttműködésre webszolgáltatásként néhány kisebb módosításokat kell. A címke oszlop "cnt" eltávolítása a bemeneti adatokat, és korlátozhatja a kimenet csak a-példány azonosítóját és a megfelelő előre jelzett érték.

A munka mentéséhez saját magának, megnyithatja a [prediktív kísérletté](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) , amely már elő lett készítve a katalógusban.

A webszolgáltatás üzembe helyezéséhez futtassa a prediktív kísérletet, majd kattintson a **webszolgáltatás üzembe helyezése** gombot a vászon alatti. A pontozási webszolgáltatás "Uci Kerékpárkölcsönzési pontozási" nevet.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 azonos webszolgáltatás-végpontok létrehozása a PowerShell használatával
A webszolgáltatás tartalmaz egy alapértelmezett végpont. De nem az alapértelmezett végpont az iránt, ezért nem frissíthető óta. Mit kell tennie, hogy 10 további végpontokat, egy az egyes helyeken. Ez a PowerShell segítségével teheti meg.

Először a PowerShell-környezet beállítása:

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

Most hozott létre 10 végpont és az összes bennük ugyanaz a betanított modell tanított *customer001.csv*. Az Azure Portalon megtekintheti őket.

![Betanított modellek listájának megtekintése a portálon](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A végpontok használata PowerShell-lel tanítási adatkészletek frissítése
A következő lépés, hogy frissítse a végpontok egyedileg minden ügyfél egyedi adatokat tanított modelleket. De ezek a modellek létrehozásához először a **uci Kerékpárkölcsönzési képzési** webes szolgáltatás. Lépjen vissza a **uci Kerékpárkölcsönzési képzési** webes szolgáltatás. Meg kell hívnia a BES végpont 10 alkalommal 10 különböző képzési adathalmazok annak érdekében, hogy 10 különböző modell létrehozásához. Használja a **InovkeAmlWebServiceBESEndpoint** ehhez a PowerShell-parancsmagot.

Adja meg azokat a blob storage-fiók hitelesítő adatait is kell `$configContent`. A mezőket, nevezetesen, `AccountName`, `AccountKey`, és `RelativeLocation`. A `AccountName` egyike lehet a fiók nevét, ahogyan az a **az Azure portal** (*tárolási* lap). Ha kattint, a storage-fiók, a `AccountKey` lenyomásával található a **hozzáférési kulcsok kezelése** gombra a lap alján, és másolja a *elsődleges elérési kulcs*. A `RelativeLocation` van viszonyított, a storage új modellt a rendszer hol tárolja. Például az elérési út `hai/retrain/bike_rental/` nevű tárolóba a következő parancsfájl pontok `hai`, és `/retrain/bike_rental/` almappák vannak. Jelenleg nem hozható létre almappákat a portál felhasználói felületén keresztül, de nincsenek [számos Azure Storage-tallózók](../../storage/common/storage-explorers.md) , amely lehetővé teszi, hogy ehhez. Javasoljuk, hogy az új betanított modellek (.iLearner fájlok) módon tárolja a storage-ban hozzon létre egy új tárolót: a tárolási oldalon kattintson a **Hozzáadás** gombra a lap alján, és adja neki a `retrain`. Összefoglalva, az alábbi parancsfájlt a szükséges változtatásokat említett `AccountName`, `AccountKey`, és `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> A BES-végpont nem az egyetlen támogatott mód ehhez a művelethez. RRS szórakoztatóiparban betanított modellek nem használható.
> 
> 

Amint helyett hozhat létre, amely 10 különböző BES feladat konfigurációs json-fájlokat, a fent látható, dinamikusan ehelyett hozza létre a konfigurációs karakterlánc. Hírcsatorna majd, hogy a *jobConfigString* paraméterében a **InvokeAmlWebServceBESEndpoint** parancsmagot. Nagyon, nem szükséges, hogy megőrzi a lemezen.

Ha minden megfelelően működik, ha egy ideig megtekintheti az 10 .iLearner fájlok, a *model001.ilearner* való *model010.ilearner*, az Azure storage-fiókban. Most már készen áll a webszolgáltatás-végpontok kiértékelés ezek a modellek használatával a 10-es frissítés a **Patch-AmlWebServiceEndpoint** PowerShell-parancsmagot. Ne felejtse el megismételni, hogy a nem alapértelmezett végpont programozott módon a korábban létrehozott csak javítására is.

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

Ez viszonylag gyorsan fusson. Ha a végrehajtás befejezését követően lesz sikeresen létrehozott 10 prediktív webszolgáltatás-végpontok. Minden egyedi tanított az adott adatkészlet bérleti helyre, mindezt egyetlen betanítási kísérlet a betanított modell tartalmazni fogja. Ennek az ellenőrzéséhez próbálja meg ezeket a végpontokat használatával a **InvokeAmlWebServiceRRSEndpoint** parancsmagot, így azokat az ugyanazon bemeneti adatokkal. Tekintse meg a különböző előrejelzési eredményeket, mivel a modellek különböző képzési csoportok képzett kell látnia.

## <a name="full-powershell-script"></a>Teljes PowerShell-szkript
Itt látható a teljes forráskódot tartalmazó listát:

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
