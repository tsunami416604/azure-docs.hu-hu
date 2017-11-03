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
ms.openlocfilehash: cc938fdaa6843f7c9e974d9b88a9b682b4678493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Több Machine Learning-modellek és webszolgáltatás-végpont létrehozása egy kísérletből a PowerShell használatával
Ez gyakori probléma machine learning: képzési ugyanabban a munkafolyamatban és ugyanazt az algoritmust használja, de különböző képzési adatkészletek bemenetként rendelkezik sok modellek létrehozásához. Ez a cikk bemutatja, hogyan ehhez az Azure Machine Learning Studióban léptékű csak egyetlen kísérlet használatával.

Például tegyük fel, a globális kerékpárt bérleti franchise vállalat tulajdonosa. A bérleti igény szerint a régebbi adatok alapján becsülhető egy regressziós modell kívánja. A világ különböző 1000 bérleti helyek rendelkezik, és egy adatkészlet egyes helyre, például a dátum, idő, időjárási fontos szolgáltatásokat tartalmaz, és a forgalom és az egyes helyekre vonatkozó korábban összegyűjtött.

Az adatkészletek egyesített verzióját egyszer használja az összes helyszínen modellje betanításához sikerült. De a helyek mindegyikének egyedi környezetben, mert az jobb megközelítés lenne a külön-külön használatával mindegyik helyen a dataset regressziós modell betanításához. Ily módon minden betanított modell volt a különböző tárolási méretek, kötet, geográfiai, feltöltése, figyelembe kerékpárt mobilbarát forgalom környezet *stb*.

Amely lehet, hogy a legjobb módszer, de nem kívánja 1000 képzési kísérletek létrehozása az Azure Machine Learning minden egy egyedi helyet jelölő. Amellett, hogy egy túlságosan feladat, célszerű is tűnik közérthető nem elég hatékony, mivel minden egyes kísérlet ugyanazokat kivételével a képzés dataset összetevők kellene lennie.

Szerencsére a Microsoft ennek segítségével végezheti a [Azure Machine Learning átképezési API](retrain-models-programmatically.md) és a feladat automatizálását [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Ahhoz, hogy a minta felgyorsulnak, azt fogja Rövidítse le a helyek való 1000 10. De azonos alapelvek és eljárások 1000 helyekre érvényesek. Az egyetlen különbség az, hogy ha azt szeretné, hogy az 1000 adatkészletek képzése érdekében érdemes lehet gondolja át, hogy a következő PowerShell-parancsfájlok futtatásakor párhuzamosan. Módjáról a cikk túlmutat, de található példák PowerShell többszálas az interneten.  
> 
> 

## <a name="set-up-the-training-experiment"></a>A tanítási kísérletet beállítása
Példa használni fogjuk [tanítási kísérletet](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) , amely azt a már létrehozott a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Nyissa meg az ehhez a kísérlethez a [Azure Machine Learning Studio](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Ahhoz, hogy kövesse az ebben a példában együtt, érdemes lehet egy szabad munkaterület helyett egy szabványos munkaterület használja. Jelenleg csak létrehozunk egy végpont minden ügyfél - 10-végpontok – összesen és a, amely szükséges egy szabványos munkaterület óta egy szabad munkaterület 3 végpontok korlátozódik. Ha csak egy ingyenes munkaterület, csak a engedélyezése csak 3 helyeket az alábbi parancsfájlok módosítása.
> 
> 

A kísérlet használ egy **adatok importálása** modul importálása a képzés dataset *customer001.csv* az Azure storage-fiók. Tegyük fel, azt tanítási adathalmazt gyűjtött összes kerékpárt bérleti helyét és közötti fájlnévvel blob storage ugyanazon a helyen tárolja őket *rentalloc001.csv* való *rentalloc10.csv*.

![Kép](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy egy **webes szolgáltatás kimeneti** modul hozzá lett adva a **tanítási modell** modul.
Ha ehhez a kísérlethez webszolgáltatásként lett telepítve, a végpont társított, hogy a kimenet a betanított modell egy .ilearner fájl formátumát visszaadható.

Is vegye figyelembe, hogy az URL-cím beállítjuk a webszolgáltatási paraméter, amely a **és adatokat importálhat** modul használja. Ez lehetővé teszi, hogy a paraméter segítségével adjon meg egyedi képzési adatkészletek mindegyik helyen a modell betanításához.
Egyéb módon azt sikerült ezt, például az SQL-lekérdezést a webes szolgáltatás paraméterrel lehet adatokat lekérni az SQL Azure-adatbázis, vagy egyszerűen használ egy **webszolgáltatás bemenetét** DataSet adatkészletben átadása a webszolgáltatás modul.

![Kép](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most most futtatni a tanítási kísérletet az alapértelmezett érték használatával *rental001.csv* , a képzési adatkészlet. Ha a kimenetét a **Evaluate** modul (kattintson a kimeneti, jelölje be **képi megjelenítés opcióra**), láthatja azt lekérése decent teljesítményétől *AUC* = 0.91. Ezen a ponton még egy webszolgáltatás-bővítmény kívül a tanítási kísérletet üzembe helyezésére.

## <a name="deploy-the-training-and-scoring-web-services"></a>A képzés és webszolgáltatások pontozási központi telepítése
A képzési webszolgáltatás üzembe helyezése, kattintson a **webes szolgáltatások beállítása** a kísérletvászon alatt gombra, majd az **webes szolgáltatás telepítése**. Hívja meg a webszolgáltatás "" kerékpárt bérleti képzési".

Most létre kell a pontozási webszolgáltatás üzembe helyezése.
Ehhez kattintson a Microsoft **webes szolgáltatások beállítása** a vászonra, és válasszon alább **prediktív webszolgáltatás**. Ezzel létrehoz egy pontozási kísérletet.
Szükség lesz néhány kisebb mértékben, hogy a bemeneti adatokból webszolgáltatásként, mint például a címke "számláló" oszlop eltávolítása használható legyen, és korlátozza a kimenetet, és csak a példányazonosítót a megfelelő előre jelzett érték.

Takarítson meg a munka, egyszerűen nyissa meg a [prediktív kísérletté](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) a gyűjteményben, amely már elő van készítve.

A webszolgáltatás üzembe helyezése, futtassa a prediktív kísérletté, majd kattintson a **webes szolgáltatás telepítése** a vászon alatti gombra. A pontozási webszolgáltatás "Kerékpárt bérleti pontozási" name ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 azonos webszolgáltatás-végpontok létrehozása a PowerShell használatával
Ez a webszolgáltatás tartalmaz egy alapértelmezett végpont. De még nincs az alapértelmezett végpont az érdekelt óta nem lehet frissíteni. Mit kell tennie, ha a 10 további végpontokat, mindegyik helyen egy. A PowerShell-lel azt fogja erre.

Először beállítjuk a PowerShell-környezetben:

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

Most nagyságú 10 végpontok és az összes tartalmazzák a azonos betanított modell betanítása a *customer001.csv*. Az Azure felügyeleti portálon megtekintheti őket.

![Kép](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A PowerShell használatával külön tanítási adathalmazt használandó végpontok frissítése
A következő lépéssel frissítése érdekében szükség a végpontok minden ügyfélnél egyedi adatokat a egyedileg betanítása modellekkel. Először kell ezek a modellek létrehozásához, de a **kerékpárt bérleti képzési** webes szolgáltatás. Lépjen vissza a **kerékpárt bérleti képzési** webes szolgáltatás. A BES végpont 10-szer adatkészletekkel 10 különböző képzési hívás 10 különböző modell előállításához szükséges. Fogjuk használni a **InovkeAmlWebServiceBESEndpoint** ehhez PowerShell-parancsmagot.

Adja meg azokat a blob storage-fiók hitelesítő adatait is kell `$configContent`, nevezetesen mezőket `AccountName`, `AccountKey` és `RelativeLocation`. A `AccountName` egyike lehet a fióknevek látható módon a **klasszikus Azure felügyeleti portálon** (*tárolási* lap). A tárfiók kattintva a `AccountKey` billentyűkombináció lenyomásával is található a **elérési kulcsok kezelése** alsó gombra, és másolja a *elsődleges elérési kulcsot*. A `RelativeLocation` van az elérési út viszonyítva tárhelyét új modell tárolásához. Például az elérési út `hai/retrain/bike_rental/` a parancsfájl alábbi pontok nevű tárolót `hai`, és `/retrain/bike_rental/` találhatók. Jelenleg nem hozható létre almappákat a felhasználói felület portálon keresztül, de nincsenek [több Azure Tártallózók](../../storage/common/storage-explorers.md) , amely lehetővé teszi. Javasoljuk, hogy hozzon létre egy új tároló az alábbiak szerint tárolja az új betanított modellek (.ilearner fájlok) a tárolóban: a tároló lapon kattintson a a **Hozzáadás** alsó gombra, és adjon neki nevet `retrain`. Összefoglalva, az alábbi parancsfájlt a szükséges változtatásokat vonatkoznak `AccountName`, `AccountKey` és `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

10 különböző BES feladat konfigurációs json-fájlokat, létrehozása helyett a fent látható azt dinamikusan hozzon létre helyette a konfigurációs karakterlánc, és hírcsatorna úgy, hogy a *jobConfigString* paramétere a **InvokeAmlWebServceBESEndpoint** parancsmag, mivel valójában nem szükséges, hogy megőrzi a lemezen.

Ha minden megfelelően működik, egy kis idő után kell megjelennie 10 .ilearner fájlok, a *model001.ilearner* való *model010.ilearner*, az Azure-tárfiókot. Most már minden készen áll a webszolgáltatás végpontjainak pontozási ezek a modellek használata a 10 frissíteni a **javítás-AmlWebServiceEndpoint** PowerShell-parancsmagot. Ne felejtse el újra, hogy azt is csak javítására, de a nem alapértelmezett végpontok programozott módon korábban létrehozott.

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

Ez meglehetősen gyorsan fusson. A végrehajtás befejezése után fog sikeresen létrehoztunk 10 prediktív webszolgáltatás-végpontok, minden tartalmazó modell betanítását, mindezt egy egyetlen tanítási kísérletet a bérleti helyre az adott adatkészlettel egyedileg képezni. Ezt ellenőrizheti, hogy próbálja meg ezeket a végpontokat használatával hívja a **InvokeAmlWebServiceRRSEndpoint** parancsmagot, hogy biztosít számukra ugyanazzal a bemeneti adatok, és tekintse meg a különböző előrejelzés eredményeket, mivel a modellek képzett különböző képzési készletekkel várható.

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
