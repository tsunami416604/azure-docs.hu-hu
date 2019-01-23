---
Cím: PowerShell module for Machine Learning Studio titleSuffix: Azure Machine Learning Studio description: Az Azure Machine Learning PowerShell-modulja nyilvános előzetes verzióban érhető el. PowerShell használatával létrehozni és kezelni a munkaterületek, kísérletek, webszolgáltatások és több.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.component: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=haining, previous-author=hning86 ms.date: 03/15/2017
---
# <a name="powershell-module-for-azure-machine-learning-studio"></a>Az Azure Machine Learning Studio PowerShell-modulja
Az Azure Machine Learning PowerShell-modulja egy olyan hatékony eszköz, amely lehetővé teszi, hogy a munkaterületek, kísérletek, adatkészletek, a klasszikus webszolgáltatások és további kezelése Windows PowerShell használatával.

A dokumentációban tekintheti meg és töltse le a modult, a teljes forráskóddal együtt [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Az Azure Machine Learning PowerShell-modul jelenleg előzetes módban van. A modul fejlesztjük és bővítjük az előzetes verzió ideje alatt továbbra is. Tartsa szemmel az [Cortana Intelligence and Machine Learning blogon](https://blogs.technet.microsoft.com/machinelearning/) hírek és információ.

## <a name="what-is-the-machine-learning-powershell-module"></a>Mi a Machine Learning PowerShell-modulja?
A Machine Learning PowerShell-modulja egy. NET-alapú DLL-modul, amely lehetővé teszi, hogy teljes mértékben az Azure Machine Learning-munkaterületek, kísérletek, adatkészletek, a klasszikus webszolgáltatások és klasszikus webszolgáltatás-végpontok kezelése a Windows PowerShellben. 

A modullal együtt letöltheti a teljes forráskódot tartalmazó szabályszerűen elválasztott [ C# API-réteget](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). A DLL-re hivatkozhat a saját .NET-projektjéből, és kezelheti az Azure Machine Learning .NET-kódon keresztül. Emellett a dll-fájlt, amelyet közvetlenül a kedvenc ügyfeléből is használhat, alapul szolgáló REST API-k függ.

## <a name="what-can-i-do-with-the-powershell-module"></a>Mire használhatom a PowerShell-modult?
Íme néhány példa a PowerShell-modullal elvégezhető feladatokra. Az alábbi funkciókkal és számos más funkcióval kapcsolatban tekintse meg a [teljes dokumentációt](https://aka.ms/amlps).

* Új munkaterület kiépítése felügyeleti tanúsítvány használatával ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Kísérleti diagramot jelölő JSON-fájlok exportálása és importálása ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Kísérlet futtatása ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Webszolgáltatás létrehozása prediktív kísérletből ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* A végpont létrehozása egy közzétett webszolgáltatáson ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Az RRS és/vagy BES webszolgáltatás végpontja meghívása ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) és [Invoke-AmlWebServiceBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Az alábbi gyors példában meglévő kísérlet futtatását mutatjuk be a PowerShell használatával:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Részletesebb használati esetek tekintse meg a cikk egy gyakran lekért feladat automatizálása a PowerShell-modul segítségével: [Számos Machine Learning-modellek és webszolgáltatás service végpontok létrehozása egy kísérletből PowerShell-lel](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
A Machine Learning PowerShell használatának megkezdéséhez töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a GitHubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az útmutató azt ismerteti, hogy feloldása a letöltött/kicsomagolt DLL zárolását, és importálja azt a PowerShell-környezet. A parancsmagok többsége számára meg kell adni a munkaterület azonosítóját, a munkaterület engedélyezési jogkivonatát, valamint azt az Azure-régiót, amelyben a munkaterület megtalálható. Adja meg a legegyszerűbb módja egy alapértelmezett config.json fájl keresztül történik. Az utasításokat is bemutatják, hogyan konfigurálhatja ezt a fájlt. 

Ha azt szeretné, a git fát klónozhat módosítsa a kódot, és fordítsa le helyben a Visual Studióval.

## <a name="next-steps"></a>További lépések
A következő PowerShell-modul szóló teljes dokumentációt annak [ https://aka.ms/amlps ](https://aka.ms/amlps). 

A modul használata a való életből vett forgatókönyv kiterjesztett példát, tekintse meg a részletes használati eset [számos Machine Learning-modellek és webszolgáltatás service végpontok létrehozása egy kísérletből PowerShell-lel](create-models-and-endpoints-with-powershell.md).
