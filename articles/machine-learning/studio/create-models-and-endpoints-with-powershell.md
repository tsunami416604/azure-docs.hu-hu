---
title: Több végpont létrehozása egy modellhez
titleSuffix: ML Studio (classic) - Azure
description: A powershellel hozhat létre több Machine Learning-modellek és webszolgáltatás Szolgáltatásvégpontok ugyanazt az algoritmust, de a különböző képzési adathalmazok alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218188"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Studio (klasszikus) modellek és webszolgáltatás-végpontok létrehozása egy kísérletből a PowerShell használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Íme a machine learning gyakran okoz problémát: szeretne létrehozni, amelyek ugyanabban a munkafolyamatban képzés és az azonos algoritmust használja számos modellt. De azt szeretné, hogy különböző képzési adathalmazok alapján bemenetként. Ebből a cikkből megtudhatja, hogyan végezheti el ezt a méretezést Azure Machine Learning Studio (klasszikus) használatával csupán egyetlen kísérlettel.

Például tegyük fel, a saját egy globális uci kerékpárkölcsönzési névhasználati üzleti. Szeretné az előzményadatok alapján bérleti kereslet előrejelzésére regressziós modell létrehozása. 1000 bérleti helyek rendelkezik világszerte, és egy adatkészletet, mindegyik helyen már összegyűjtött. Fontos szolgáltatások például a dátum, idő, időjárás és a forgalom és az egyes helyekre vonatkozó tartalmazzák.

A modell egyszer az összes adatkészlet egyesített verziót használ, az összes hely között lehetett betanításához. De a helyek mindegyike rendelkezik egy egyedi környezetben. Így jobb módszer lehet a külön-külön mindegyik helyen a az adatkészlet használatával regressziós modell betanításához. Ezzel a módszerrel minden betanított modell sikerült vegye figyelembe a különböző tároló méretek, kötet, földrajzi hely, population, kerékpár-barát forgalom környezet és egyéb.

Ez lehet a legjobb megközelítés, de nem kívánja létrehozni a 1 000-es tanítási kísérleteket Azure Machine Learning Studio (klasszikus), és mindegyik egy egyedi helyet jelképez. Amellett, hogy egy eleve bonyolult feladatnak, is úgy tűnik, nem hatékony, mivel minden kísérlet ugyanazokat a betanítási adatkészletet kivételével összetevők lenne.

Szerencsére ezt a [Azure Machine learning Studio (klasszikus) átképzési API](/azure/machine-learning/studio/retrain-machine-learning-model) használatával hajthatja végre, és automatizálhatja a feladatot a [Azure Machine learning Studio (klasszikus) PowerShell](powershell-module.md)-lel.

> [!NOTE]
> Ahhoz, hogy a minta gyorsabban futnak, a helyek való 1000 10 számának csökkentése. Azonban ugyanazon alapelveket és eljárásokat a alkalmazni 1000 helyekre. Azonban ha szeretné, hogy 1000 adatkészletekből származó betanításához célszerű párhuzamos az alábbi PowerShell-parancsfájlok futtatásához. Hogyan valósítható meg ez a cikk nem foglalkozik, de annak PowerShell példái többszálas az interneten.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Állítsa be a tanítási kísérlet
Használja a [Cortana Intelligence Galleryban](https://gallery.azure.ai)található példaként szolgáló [betanítási kísérletet](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) . Nyissa meg a kísérletet a [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Annak érdekében, hogy kövesse az ebben a példában együtt, érdemes inkább a standard munkaterületet, mint egy ingyenes munkaterületet. Egy végpontot hoz létre minden egyes ügyfél - összesen 10 végpont - és a standard munkaterületre igénylő, mivel az ingyenes munkaterületre legfeljebb 3 végpontok.
> 
> 

A kísérlet egy **adatimportálási** modul használatával importálja a *customer001. csv* betanítási adatkészletet egy Azure Storage-fiókból. Tegyük fel, hogy begyűjtötte a betanítási adatkészleteket az összes kerékpár-kölcsönzési helyről, és ugyanazon a blob Storage-helyen tárolja őket, ahol a fájlnevek a *rentalloc001. csv* fájlból a *rentalloc10. csv*fájlba kerülnek.

![Olvasó modul adatok importálása Azure-blobból](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy a **webszolgáltatások kimeneti** modulja hozzá lett adva a **Train Model** modulhoz.
Amikor ez a kísérlet üzembe webszolgáltatásként, a végponthoz társított kimeneti egy .ilearner fájl formátumát a betanított modell adja vissza.

Azt is vegye figyelembe, hogy az **adatimportálási** modul által használt URL-címet meghatározó webszolgáltatás-paramétert állít be. Ez lehetővé teszi, hogy a paraméter adja meg az egyes képzési adathalmazok alapján minden helyen a modell betanításához.
Sikerült ezt egyéb módon is. SQL-lekérdezés egy webes szolgáltatás paraméterrel segítségével adatokat kérhet le egy SQL Azure adatbázis. A **webszolgáltatások bemeneti** moduljának használatával pedig egy adatkészletet adhat át a webszolgáltatásnak.

![Egy betanított modell modul kimenete egy webszolgáltatás kimeneti moduljának](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most futtassuk ezt a betanítási kísérletet a *rental001. csv* alapértelmezett értékkel, amely betanítási adatkészletet használ. Ha megtekinti a **kiértékelési** modul kimenetét (kattintson a kimenetre, **majd válassza a**vizualizáció lehetőséget), láthatja, hogy a *AUC* = 0,91 egy tisztességes teljesítményt nyújt. Ezen a ponton készen áll a betanítási kísérlet webszolgáltatás üzembe helyezéséhez.

## <a name="deploy-the-training-and-scoring-web-services"></a>A tanítási és pontozási webszolgáltatások üzembe helyezése
A betanítási webszolgáltatás üzembe helyezéséhez kattintson a kísérlet vászon alatt a **webszolgáltatás beállítása** gombra, és válassza a **webszolgáltatás telepítése**lehetőséget. Hívja meg a webszolgáltatás "Uci Kerékpárkölcsönzési képzési".

Most meg kell a pontozási webszolgáltatás üzembe helyezéséhez.
Ennek elvégzéséhez kattintson a **webszolgáltatás beállítása** a vásznon elemre, majd válassza a **prediktív webszolgáltatás**lehetőséget. Ez létrehoz egy pontozó kísérletet.
Együttműködésre webszolgáltatásként néhány kisebb módosításokat kell. A címke oszlop "cnt" eltávolítása a bemeneti adatokat, és korlátozhatja a kimenet csak a-példány azonosítóját és a megfelelő előre jelzett érték.

A munka megtakarításához megnyithatja a [prediktív kísérletet](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) a katalógusban, amely már elkészült.

A webszolgáltatás üzembe helyezéséhez futtassa a prediktív kísérletet, majd kattintson a vászon alatt lévő **webszolgáltatás üzembe helyezése** gombra. A pontozási webszolgáltatás "Uci Kerékpárkölcsönzési pontozási" nevet.

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

Most létrehozott 10 végpontot, és mindegyikük ugyanazt a betanított modellt tartalmazza, amely a *customer001. csv*fájlra van betanítva. Az Azure Portalon megtekintheti őket.

![A portálon megtekintheti a betanított modellek listáját](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A végpontok használata PowerShell-lel tanítási adatkészletek frissítése
A következő lépés, hogy frissítse a végpontok egyedileg minden ügyfél egyedi adatokat tanított modelleket. Először is létre kell hoznia ezeket a modelleket a **Bike Rental Training** web szolgáltatásból. Térjünk vissza a **Bike Rental Training** webszolgáltatáshoz. Meg kell hívnia a BES végpont 10 alkalommal 10 különböző képzési adathalmazok annak érdekében, hogy 10 különböző modell létrehozásához. Ehhez használja a **InovkeAmlWebServiceBESEndpoint** PowerShell-parancsmagot.

A blob Storage-fiók hitelesítő adatait is meg kell adnia `$configContent`ba. A `AccountName`, a `AccountKey`és a `RelativeLocation`mezőkben. A `AccountName` lehet az egyik fiók neve, ahogy az a **Azure Portalban** látható (*tároló* lap). Miután rákattintott egy Storage-fiókra, a `AccountKey` megtekintheti a lenti **hozzáférési kulcsok kezelése** gombot, és átmásolhatja az *elsődleges hozzáférési kulcsot*. A `RelativeLocation` az az elérési út, amely az új modell tárolására szolgáló tárhelyhez viszonyítva jelenik meg. Például a következő parancsfájlban `hai/retrain/bike_rental/` elérési út egy `hai`nevű tárolóra mutat, és a `/retrain/bike_rental/` almappák. Jelenleg nem hozhatók létre almappák a portál felhasználói felületén keresztül, de [több Azure Storage-tallózó](../../storage/common/storage-explorers.md) is lehetővé teszi. Azt javasoljuk, hogy hozzon létre egy új tárolót a tárolóban az új betanított modellek (. iLearner fájlok) tárolásához a következő módon: a tárterület lapon kattintson a **Hozzáadás** gombra a lap alján, és nevezze el `retrain`. Összefoglalva, a következő parancsfájl szükséges módosításai `AccountName`, `AccountKey`és `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`) vonatkoznak.

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

Amint helyett hozhat létre, amely 10 különböző BES feladat konfigurációs json-fájlokat, a fent látható, dinamikusan ehelyett hozza létre a konfigurációs karakterlánc. Ezután a **InvokeAmlWebServceBESEndpoint** parancsmag *jobConfigString* paraméteréhez adja a csatornát. Nagyon, nem szükséges, hogy megőrzi a lemezen.

Ha minden jól megy, egy idő után 10. iLearner-fájlt kell látnia a *model001. iLearner* fájlból a *model010. iLearner*-be az Azure Storage-fiókban. Most már készen áll arra, hogy frissítse a 10 pontozásos webszolgáltatás-végpontot ezekkel a modellekkel a **patch-AmlWebServiceEndpoint** PowerShell-parancsmag használatával. Ne felejtse el megismételni, hogy a nem alapértelmezett végpont programozott módon a korábban létrehozott csak javítására is.

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

Ez viszonylag gyorsan fusson. Ha a végrehajtás befejezését követően lesz sikeresen létrehozott 10 prediktív webszolgáltatás-végpontok. Minden egyedi tanított az adott adatkészlet bérleti helyre, mindezt egyetlen betanítási kísérlet a betanított modell tartalmazni fogja. Ennek ellenőrzéséhez próbálja meg meghívja ezeket a végpontokat a **InvokeAmlWebServiceRRSEndpoint** parancsmaggal, és ugyanazokat a bemeneti adatokat adja meg. Tekintse meg a különböző előrejelzési eredményeket, mivel a modellek különböző képzési csoportok képzett kell látnia.

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
