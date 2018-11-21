---
title: Machine Learning PowerShell-modulja |} A Microsoft Docs
description: Az Azure Machine Learning PowerShell-modulja nyilvános előzetes verzióban érhető el. PowerShell használatával létrehozni és kezelni a munkaterületek, kísérletek, webszolgáltatások és több.
keywords: kísérlet,lineáris regresszió,machine learning-algoritmusok,machine learning-oktatóanyag,prediktív modellezési technikák,adatelemzési kísérlet
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=haining, author=hning86)
ms.author: amlstudiodocs
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: 795ce4e86c5fd8dac34f18cbbe80dd1395d94c5c
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263128"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>A Microsoft Azure Machine Learning PowerShell-modulja
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
* RRS és/vagy BES webszolgáltatás-végpont meghívása ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) és [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Az alábbi gyors példában meglévő kísérlet futtatását mutatjuk be a PowerShell használatával:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Részletesebb használati esetek, lásd: Ez a cikk egy gyakran lekért feladat automatizálása a PowerShell-modul használatával: [számos Machine Learning-modellek és webszolgáltatás service végpontok létrehozása egy kísérletből PowerShell-lel](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
A Machine Learning PowerShell használatának megkezdéséhez töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a GitHubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az útmutató azt ismerteti, hogy feloldása a letöltött/kicsomagolt DLL zárolását, és importálja azt a PowerShell-környezet. A parancsmagok többsége számára meg kell adni a munkaterület azonosítóját, a munkaterület engedélyezési jogkivonatát, valamint azt az Azure-régiót, amelyben a munkaterület megtalálható. Adja meg a legegyszerűbb módja egy alapértelmezett config.json fájl keresztül történik. Az utasításokat is bemutatják, hogyan konfigurálhatja ezt a fájlt. 

Ha azt szeretné, a git fát klónozhat módosítsa a kódot, és fordítsa le helyben a Visual Studióval.

## <a name="next-steps"></a>További lépések
A következő PowerShell-modul szóló teljes dokumentációt annak [ https://aka.ms/amlps ](https://aka.ms/amlps). 

A modul használata a való életből vett forgatókönyv kiterjesztett példát, tekintse meg a részletes használati eset [számos Machine Learning-modellek és webszolgáltatás service végpontok létrehozása egy kísérletből PowerShell-lel](create-models-and-endpoints-with-powershell.md).
