<properties
    pageTitle="A Machine Learning PowerShell-modulja | Microsoft Azure"
    description="Az Azure Machine Learning PowerShell-modulja nyilvános előzetes verzióban érhető el. A PowerShell többek között munkaterületek, kísérletek és webszolgáltatások létrehozására és kezelésére használható."
    keywords="kísérlet,lineáris regresszió,machine learning-algoritmusok,machine learning-oktatóanyag,prediktív modellezési technikák,adatelemzési kísérlet"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>


# A Microsoft Azure Machine Learning PowerShell-modulja

Az Azure Machine Learning PowerShell-modulja egy olyan hatékony eszköz, amely lehetővé teszi a Windows PowerShell használatát többek között munkaterületek, kísérletek, adatkészletek és webszolgáltatások kezelésére.

A dokumentáció megtekintéséhez és a modul letöltéséhez a teljes forráskóddal együtt lépjen a következő helyre: [https://aka.ms/amlps](https://aka.ms/amlps). 

## Mi a Machine Learning PowerShell-modulja?

A Machine Learning PowerShell-modulja egy olyan .NET-alapú DLL-modul, amely lehetővé teszi az Azure Machine Learning-munkaterületek, -kísérletek, -adatkészletek és -webszolgáltatásvégpontok teljes körű kezelését a Windows PowerShellből. A modullal együtt a teljes forráskód is letölthető, amely egy teljes mértekben elválasztott [C# API-réteget](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) is tartalmaz. Ez azt jelenti, hogy erre a DLL-re hivatkozhat a saját .NET-projektjéből, és a .NET-kódon keresztül kezelheti az Azure Machine Learninget. Emellett a DLL azoktól az alapul szolgáló REST API-któl függ, amelyeket közvetlenül a kedvenc ügyfeléből kezelhet.

## Mire használhatom a PowerShell-modult?

Íme néhány példa a PowerShell-modullal elvégezhető feladatokra. Az alábbi funkciókkal és számos más funkcióval kapcsolatban tekintse meg a [teljes dokumentációt](https://aka.ms/amlps).

- Új munkaterület kiépítése felügyeleti tanúsítvány használatával ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Kísérleti diagramot jelölő JSON-fájlok exportálása és importálása ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Kísérlet futtatása ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Webszolgáltatás létrehozása prediktív kísérletből ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Új végpont létrehozása egy közzétett webszolgáltatáson ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- RRS és/vagy BES webszolgáltatás-végpont meghívása ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) és [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Az alábbi gyors példában meglévő kísérlet futtatását mutatjuk be a PowerShell használatával:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

A következő cikkben egy még részletesebb alkalmazási példát láthat egy gyakran lekért feladat a PowerShell-modullal való automatizálására: [Create many Machine Learning models and web service endpoints from one experiment using PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md) (Több Machine Learning-modell és -webszolgáltatásvégpont létrehozása egyetlen kísérletből a PowerShell használatával).

## Hogyan kezdhetek hozzá?

A Machine Learning PowerShell használatának megkezdéséhez töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a GitHubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Fel kell oldania a letöltött/kicsomagolt DLL zárolását, majd importálnia kell azt a saját PowerShell-környezetébe. A parancsmagok többsége számára meg kell adni a munkaterület azonosítóját, a munkaterület engedélyezési jogkivonatát, valamint azt az Azure-régiót, amelyben a munkaterület megtalálható. Ennek legegyszerűbb módja egy alapértelmezett config.json fájl használata. Ennek a módszernek a részletei megtalálhatóak a telepítési utasítások között. Természetesen klónozhatja is a git fát, majd a Visual Studióval helyileg módosíthatja/lefordíthatja a kódot.

## Következő lépések

Az előzetes verzió időszaka alatt a PowerShell-modult folyamatosan fejlesztjük és bővítjük. További hírekért és információkért kövesse a [Cortana Intelligence and Machine Learning blogot](https://blogs.technet.microsoft.com/machinelearning/).



<!--HONumber=Sep16_HO4-->


