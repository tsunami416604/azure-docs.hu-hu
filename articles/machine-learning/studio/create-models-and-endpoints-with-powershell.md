---
title: Több végpont létrehozása egy modellhez
titleSuffix: ML Studio (classic) - Azure
description: A PowerShell használatával több Machine Learning modellt és webszolgáltatás-végpontot hozhat létre ugyanazzal az algoritmussal, de különböző betanítási adatkészletekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: d7999488302098980ff9306836ec9d54aceac3be
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837853"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Studio (klasszikus) modellek és webszolgáltatás-végpontok létrehozása egy kísérletből a PowerShell használatával

Íme egy gyakori gépi tanulási probléma: több olyan modellt szeretne létrehozni, amely azonos képzési munkafolyamattal rendelkezik, és ugyanazt az algoritmust használja. Azonban azt szeretné, hogy a különböző betanítási adatkészletek bemenetként legyenek kiválasztva. Ebből a cikkből megtudhatja, hogyan végezheti el ezt a méretezést Azure Machine Learning Studio (klasszikus) használatával csupán egyetlen kísérlettel.

Tegyük fel például, hogy egy globális kerékpár-kölcsönzési üzletág tulajdonosa. Egy regressziós modellt szeretne felépíteni, amellyel előre jelezheti a bérleti igényeket a múltbeli adathalmazok alapján. A világ minden részén van 1 000-es bérleti hely, és összegyűjtöttünk egy adatkészletet az egyes helyekhez. Olyan fontos funkciókat tartalmaznak, mint például a dátum, az idő, az időjárás és az egyes helyekre jellemző forgalom.

A modellt betaníthatja, ha az összes adathalmaz összevont verzióját használja az összes helyen. Az egyes helyszínek azonban egyedi környezettel rendelkeznek. Így jobb megközelítés lenne a regressziós modell külön betanítása az egyes helyekhez tartozó adatkészlet használatával. Így az egyes betanított modellek figyelembe vehetik a különböző tárolási méreteket, a kötetet, a földrajzot, a populációt, a kerékpár-barát forgalmi környezetet és egyebeket.

Ez lehet a legjobb megközelítés, de nem kívánja létrehozni a 1 000-es Azure Machine Learning Studio klasszikus változatát, és minden egyes egyedi helyet jelképez. Amellett, hogy elsöprő feladat, nem hatékonynak tűnik, mivel minden kísérlethez ugyanaz az összetevők tartoznak, kivéve a betanítási adatkészletet.

Szerencsére ezt a [Azure Machine learning Studio (klasszikus) átképzési API](/azure/machine-learning/studio/retrain-machine-learning-model) használatával hajthatja végre, és automatizálhatja a feladatot a [Azure Machine learning Studio (klasszikus) PowerShell](powershell-module.md)-lel.

> [!NOTE]
> Ahhoz, hogy a minta gyorsabban fusson, csökkentse a 1 000 és 10 közötti tárolóhelyek számát. Ugyanezek az alapelvek és eljárások azonban az 1 000-es helyszínekre érvényesek. Ha azonban az 1 000-adatkészletből szeretne betanítani, akkor előfordulhat, hogy párhuzamosan szeretné futtatni a következő PowerShell-parancsfájlokat. Ennek a cikknek a hatóköre meghaladja a jelen cikkben ismertetett műveleteket, de a PowerShell több szálon is használható.  
> 
> 

## <a name="set-up-the-training-experiment"></a>A betanítási kísérlet beállítása
Használja a [Cortana Intelligence Galleryban](https://gallery.azure.ai)található példaként szolgáló [betanítási kísérletet](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) . Nyissa meg a kísérletet a [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Ha ezt a példát szeretné követni, érdemes lehet egy standard munkaterületet használni az ingyenes munkaterületek helyett. Mindegyik ügyfélhez létre kell hoznia egy végpontot – összesen 10 végpontra –, és ehhez standard munkaterületre van szükség, mivel az ingyenes munkaterület 3 végpontra van korlátozva.
> 
> 

A kísérlet egy **adatimportálási** modul használatával importálja a *customer001. csv* betanítási adatkészletet egy Azure Storage-fiókból. Tegyük fel, hogy begyűjtötte a betanítási adatkészleteket az összes kerékpár-kölcsönzési helyről, és ugyanazon a blob Storage-helyen tárolja őket, ahol a fájlnevek a *rentalloc001. csv* fájlból a *rentalloc10. csv*fájlba kerülnek.

![Olvasó modul adatok importálása Azure-blobból](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy a **webszolgáltatások kimeneti** modulja hozzá lett adva a **Train Model** modulhoz.
Ha a kísérlet webszolgáltatásként van telepítve, a kimenethez társított végpont egy. ilearner-fájl formátumban adja vissza a betanított modellt.

Azt is vegye figyelembe, hogy az **adatimportálási** modul által használt URL-címet meghatározó webszolgáltatás-paramétert állít be. Ez lehetővé teszi, hogy a paraméterrel egyéni betanítási adatkészleteket határozzon meg az egyes helyekhez tartozó modell betanításához.
Ezt többféleképpen is elvégezheti. Egy webszolgáltatási paraméterrel rendelkező SQL-lekérdezés használatával lekérheti az adatok SQL Azure adatbázisból való beolvasását. A **webszolgáltatások bemeneti** moduljának használatával pedig egy adatkészletet adhat át a webszolgáltatásnak.

![Egy betanított modell modul kimenete egy webszolgáltatás kimeneti moduljának](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most futtassuk ezt a betanítási kísérletet a *rental001. csv* alapértelmezett értékkel, amely betanítási adatkészletet használ. Ha megtekinti a **kiértékelési** modul kimenetét (kattintson a kimenetre, **majd válassza a**vizualizáció lehetőséget), láthatja, hogy a *AUC* = 0,91 egy tisztességes teljesítményt nyújt. Ezen a ponton készen áll arra, hogy üzembe helyezzen egy webszolgáltatást ebből a betanítási kísérletből.

## <a name="deploy-the-training-and-scoring-web-services"></a>A képzési és pontozási webszolgáltatások üzembe helyezése
A betanítási webszolgáltatás üzembe helyezéséhez kattintson a kísérlet vászon alatt a **webszolgáltatás beállítása** gombra, és válassza a **webszolgáltatás telepítése**lehetőséget. Hívja meg ezt a webszolgáltatást "Bike Rental Training".

Most telepítenie kell a pontozási webszolgáltatás szolgáltatást.
Ennek elvégzéséhez kattintson a **webszolgáltatás beállítása** a vásznon elemre, majd válassza a **prediktív webszolgáltatás**lehetőséget. Ez létrehoz egy pontozási kísérletet.
Néhány kisebb módosítást kell végrehajtania, hogy webszolgáltatásként működjön. Távolítsa el a "CNT" címke oszlopot a bemeneti adatokból, és korlátozza a kimenetet csak a példány-azonosítóra és a megfelelő előre jelzett értékre.

A munka megtakarításához megnyithatja a [prediktív kísérletet](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) a katalógusban, amely már elkészült.

A webszolgáltatás üzembe helyezéséhez futtassa a prediktív kísérletet, majd kattintson a vászon alatt lévő **webszolgáltatás üzembe helyezése** gombra. Nevezze el a pontozási webszolgáltatás "Bike Rental pontozás" nevet.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 azonos webszolgáltatás-végpont létrehozása a PowerShell-lel
Ez a webszolgáltatás alapértelmezett végpontot tartalmaz. De nem érdekli az alapértelmezett végpont, mert az nem frissíthető. Mit kell tennie, hogy 10 további végpontot hozzon létre, egyet az egyes helyekhez. Ezt a PowerShell használatával teheti meg.

Először állítsa be a PowerShell-környezetet:

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

Most létrehozott 10 végpontot, és mindegyikük ugyanazt a betanított modellt tartalmazza, amely a *customer001. csv*fájlra van betanítva. Megtekintheti őket a Azure Portalban.

![A portálon megtekintheti a betanított modellek listáját](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A végpontok frissítése külön betanítási adatkészletek használatára a PowerShell használatával
A következő lépés a végpontok frissítése az egyes ügyfelek egyedi adateszközeire egyedileg betanított modellekkel. Először is létre kell hoznia ezeket a modelleket a **Bike Rental Training** web szolgáltatásból. Térjünk vissza a **Bike Rental Training** webszolgáltatáshoz. 10 különböző modell létrehozásához meg kell hívnia a BES-végpontot 10 alkalommal 10 különböző betanítási adatkészlettel. Ehhez használja a **InovkeAmlWebServiceBESEndpoint** PowerShell-parancsmagot.

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
> A BES végpont az egyetlen támogatott mód ehhez a művelethez. A ERŐFORRÁSREKORDOK nem használhatók betanított modellek készítésére.
> 
> 

Ahogy a fentiekben is látható, a 10 különböző BES-beli konfigurációs JSON-fájl létrehozása helyett Ehelyett dinamikusan létrehozza a konfigurációs karakterláncot. Ezután a **InvokeAmlWebServceBESEndpoint** parancsmag *jobConfigString* paraméteréhez adja a csatornát. A lemezen nem kell megőrizni a másolást.

Ha minden jól megy, egy idő után 10. iLearner-fájlt kell látnia a *model001. iLearner* fájlból a *model010. iLearner*-be az Azure Storage-fiókban. Most már készen áll arra, hogy frissítse a 10 pontozásos webszolgáltatás-végpontot ezekkel a modellekkel a **patch-AmlWebServiceEndpoint** PowerShell-parancsmag használatával. Ne feledje újra, hogy csak a korábban létrehozott, nem alapértelmezett végpontok javítását végezheti el.

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

Ennek elég gyorsnak kell lennie. A végrehajtás befejezésekor a rendszer sikeresen létrehozott 10 prediktív webszolgáltatás-végpontot. Mindegyik olyan betanított modellt tartalmaz, amely egyedi módon van betanítva a bérleti helyhez tartozó adatkészletre, mindezt egyetlen betanítási kísérlet során. Ennek ellenőrzéséhez próbálja meg meghívja ezeket a végpontokat a **InvokeAmlWebServiceRRSEndpoint** parancsmaggal, és ugyanazokat a bemeneti adatokat adja meg. Várhatóan eltérő előrejelzési eredményeket kell látnia, mivel a modellek különböző betanítási csoportokkal vannak betanítva.

## <a name="full-powershell-script"></a>Teljes PowerShell-parancsfájl
Az alábbi lista tartalmazza a teljes forráskódot:

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
