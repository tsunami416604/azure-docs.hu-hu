---
title: Több végpont létrehozása egy modellhez
titleSuffix: ML Studio (classic) - Azure
description: A PowerShell használatával több Machine Learning-modellt és webszolgáltatás-végpontot hozhat létre ugyanazzal az algoritmussal, de különböző betanítási adatkészletekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218188"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Studio (klasszikus) modellek és webszolgáltatás-végpontok létrehozása egy kísérletből a PowerShell használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Íme egy gyakori gépi tanulási probléma: Szeretne létrehozni sok modell, amelyek azonos betanítási munkafolyamatot, és ugyanazt az algoritmust használja. De azt szeretné, hogy különböző betanítási adatkészletek bemenetként. Ez a cikk bemutatja, hogyan lehet ezt nagy méretekben az Azure Machine Learning Studio (klasszikus) egyetlen kísérlet használatával.

Tegyük fel például, hogy globális kerékpárkölcsönző franchise-vállalkozása van. Egy regressziós modellt szeretne készíteni a korábbi adatok alapján a bérleti igény előrejelzéséhez. Világszerte 1000 bérelhető hellyel rendelkezik, és minden helyhez összegyűjtött egy adatkészletet. Ezek közé tartoznak a fontos funkciók, mint a dátum, idő, időjárás, és a forgalom, amelyek az egyes helyeken.

A modell betanítása egyszer az összes adatkészletek összes helyen egyesített verziójának használatával. De minden helyen van egy egyedi környezetben. Így egy jobb megközelítés az lenne, hogy a regressziós modell külön-külön az adatkészlet használatával az egyes helyeken. Így minden betanított modell figyelembe veheti a különböző üzletméreteket, mennyiséget, földrajzot, népességet, kerékpárbarát közlekedési környezetet és így tovább.

Lehet, hogy ez a legjobb megközelítés, de nem szeretne 1000 képzési kísérletet létrehozni az Azure Machine Learning Studio (klasszikus) alkalmazásban, amelyek mindegyike egyedi helyet képvisel. Amellett, hogy egy elsöprő feladat, úgy tűnik, nem hatékony, mivel minden kísérlet volna minden ugyanazokat az összetevőket, kivéve a betanítási adatkészlet.

Szerencsére ezt az Azure [Machine Learning Studio (klasszikus) átképzési API](/azure/machine-learning/studio/retrain-machine-learning-model) használatával és a feladat automatizálásával az [Azure Machine Learning Studio (klasszikus) PowerShell](powershell-module.md)használatával érheti el.

> [!NOTE]
> A minta gyorsabb futásához csökkentse a helyek számát 1000-ről 10-re. De ugyanazok az elvek és eljárások vonatkoznak 1000 helyszínre. Ha azonban 1000 adatkészletből szeretne betanítást, érdemes lehet párhuzamosan futtatni a következő PowerShell-parancsfájlokat. Ennek célja a jelen cikk hatókörén kívül esik, de példákat találhat a PowerShell többszálas használatára az interneten.  
> 
> 

## <a name="set-up-the-training-experiment"></a>A betanítási kísérlet beállítása
Használja a [Cortana Intelligenciatárban](https://gallery.azure.ai)található [példabetanítási kísérletet.](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) Nyissa meg ezt a kísérletet az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net) munkaterületen.

> [!NOTE]
> Annak érdekében, hogy kövesse ezt a példát, érdemes lehet használni a szabványos munkaterületet, nem pedig egy szabad munkaterületet. Minden ügyfélszámára egy végpontot hoz létre – összesen 10 végponthoz –, és ehhez szabványos munkaterületszükséges, mivel a szabad munkaterület 3 végpontra korlátozódik.
> 
> 

A kísérlet egy **Import Data** modul t használ a betanítási adatkészlet *importálásához customer001.csv* egy Azure-tárfiókból. Tegyük fel, hogy összegyűjtötte a képzési adatkészleteket az összes kerékpárkölcsönző helyről, és ugyanazon a blobtároló helyen tárolta őket a *rentalloc001.csv-től* a *rentalloc10.csv*fájlnevekig.

![A Reader modul adatokat importál egy Azure-blobból](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Vegye figyelembe, hogy egy **webszolgáltatás kimeneti** modulja hozzá lett adva a **Betanítási modell** modulhoz.
Ha ez a kísérlet webszolgáltatásként van telepítve, a kimenethez társított végpont egy .ilearner fájl formátumában adja vissza a betanított modellt.

Azt is vegye figyelembe, hogy olyan webszolgáltatás-paramétert állított be, amely meghatározza az **Adatok importálása** modul által használt URL-címet. Ez lehetővé teszi, hogy a paraméter használatával adja meg az egyes betanítási adatkészletek betanításához a modell minden helyen.
Van más módja is, hogy ezt megtehesd. Az SQL-lekérdezést egy webszolgáltatás-paraméterrel együtt használhatja az SQL Azure-adatbázisból származó adatok behívásához. Vagy használhatja a **webszolgáltatás bemeneti** modul adathalmazát a webszolgáltatás.

![Betanított modellmodul kimenete egy webszolgáltatás kimeneti moduljához](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Most futtassuk ezt a betanítási kísérletet az alapértelmezett *értékrental0001.csv* betanítási adatkészletként. Ha a **Kiértékelés** modul kimenetét tekinti meg (kattintson a kimenetre, és válassza a Visualize lehetőséget), **láthatja,** hogy az *AUC* = 0.91 tisztességes teljesítményét kapja. Ezen a ponton készen áll egy webszolgáltatás üzembe helyezésére a betanítási kísérletből.

## <a name="deploy-the-training-and-scoring-web-services"></a>A képzési és pontozási webes szolgáltatások üzembe helyezése
A betanítási webszolgáltatás telepítéséhez kattintson a **webszolgáltatás beállítása** gombra a kísérletvászon alatt, és válassza a **Webszolgáltatás telepítése**lehetőséget. Hívja ezt a webszolgáltatás "Bike Rental Training".

Most telepítenie kell a pontozási webszolgáltatás.
Ehhez kattintson a vászon alatti **Webszolgáltatás beállítása** elemre, és válassza a **Prediktív webszolgáltatás lehetőséget.** Ez pontozási kísérletet hoz létre.
Néhány kisebb módosítást kell végrehajtania ahhoz, hogy webszolgáltatásként működjön. Távolítsa el a "cnt" címkeoszlopot a bemeneti adatokból, és korlátozza a kimenetet csak a példányazonosítóra és a megfelelő előre jelzett értékre.

Menteni magát, hogy a munka, megnyithatja a [prediktív kísérlet](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) a Galériában, hogy már elkészített.

A webszolgáltatás üzembe helyezéséhez futtassa a prediktív kísérletet, majd kattintson a **webszolgáltatás telepítése** gombra a vászon alatt. Nevezze el a pontozási webszolgáltatás "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 azonos webszolgáltatás-végpont létrehozása a PowerShell használatával
Ez a webszolgáltatás jön-val egy alapértelmezett végpont. De nem érdekli annyira az alapértelmezett végpont, mivel nem frissíthető. Mit kell tennie, hogy hozzon létre 10 további végpontok, minden helyen egy. Ezt a PowerShell segítségével teheti meg.

Először állítsa be a PowerShell-környezetet:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Ezután futtassa a következő PowerShell parancsot:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Most létrehozott 10 végpontot, és mindegyik tartalmazza ugyanazt a betanított modellt, amelyet az *ügyfél001.csv-n*képeztek ki. Megtekintheti őket az Azure Portalon.

![A betanított modellek listájának megtekintése a portálon](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>A végpontok frissítése külön betanítási adatkészletek használatára a PowerShell használatával
A következő lépés a végpontok frissítése az egyes ügyfelek egyedi adataira egyedileg betanított modellekkel. De először meg kell, hogy ezeket a modelleket a **Bike Rental Training** web szolgáltatás. Térjünk vissza a **Bike Rental Training** web szolgáltatás. A BES-végpontot 10-szer kell meghívnia 10 különböző betanítási adatkészletekkel, hogy 10 különböző modellt hozhasson létre. Ehhez használja az **InovkeAmlWebServiceBESEndpoint** PowerShell-parancsmac- ot.

Emellett meg kell adnia a blob storage-fiók hitelesítő `$configContent`adatait. Nevezetesen a `AccountName`mezőkön `AccountKey`, `RelativeLocation`és . A `AccountName` lehet az egyik fióknevét, ahogy az **Azure Portalon** (*Storage* lapon látható). Ha rákattint egy tárfiókot, a megtalálható nyomja meg `AccountKey` a **Hozzáférés-kulcsok kezelése** gombot az alján, és másolja az elsődleges hozzáférési *kulcs*. Az `RelativeLocation` az elérési út a tárolóhoz képest, ahol egy új modell tárolódik. A következő parancsfájlban lévő elérési út `hai/retrain/bike_rental/` például egy tárolóra `hai`mutat, amely `/retrain/bike_rental/` almappák. Jelenleg nem hozhat létre almappákat a portál felhasználói felületén keresztül, de [számos Azure Storage Explorer s](../../storage/common/storage-explorers.md) idoben lehetővé teszi. Javasoljuk, hogy hozzon létre egy új tárolót a tárolóban az új betanított modellek (.iLearner fájlok) tárolásához az `retrain`alábbiak szerint: a tárolólapon kattintson a **Hozzáadás** gombra az alján, és nevezze el . Összefoglalva, a `AccountName`következő parancsfájl szükséges módosításai a`"retrain/model' + $seq + '.ilearner"` `AccountKey`, , és `RelativeLocation` (: )

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
> A BES-végpont az egyetlen támogatott mód ehhez a művelethez. Az RRS nem használható betanított modellek készítéséhez.
> 
> 

Mint látható fent, ahelyett, hogy 10 különböző BES-feladat konfigurációjson fájlokat, dinamikusan hozza létre a konfigurációs karakterlánc helyett. Ezután az **InvokeAmlWebServceBESEndpoint** parancsmag *feladatConfigString* paraméterének. Tényleg nem kell másolatot tartani a lemezen.

Ha minden jól megy, egy idő után 10 .iLearner fájlt kell látnia, *a model001.ilearner-től* a *model010.ilearner-ig*az Azure storage-fiókjában. Most már készen áll a 10 pontozási webszolgáltatás-végpontok frissítésére ezekkel a modellekkel a **Patch-AmlWebServiceEndpoint** PowerShell-parancsmag használatával. Ne feledje újra, hogy csak a korábban programozottmódon létrehozott nem alapértelmezett végpontokat javíthatja.

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

Ennek elég gyorsan kell futnia. A végrehajtás befejezése után sikeresen létrehozott 10 prediktív webszolgáltatás-végpontot. Mindegyik tartalmazni fog egy betanított modellt egyedileg betanított az adatkészlet en egy bérleti hely, minden egyetlen betanítási kísérlet. Ennek ellenőrzéséhez megpróbálhatja meghívni ezeket a végpontokat az **InvokeAmlWebServiceRRSEndpoint** parancsmag használatával, és ugyanazokat a bemeneti adatokat biztosítva számukra. Meg kell várni, hogy a különböző előrejelzési eredményeket, mivel a modellek betanítása különböző betanítási készletek.

## <a name="full-powershell-script"></a>Teljes PowerShell-parancsfájl
Itt a lista a teljes forráskód:

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
