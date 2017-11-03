---
title: PowerShell-modul a Machine Learning |} Microsoft Docs
description: "Az Azure Machine Learning PowerShell-modulja nyilvános előzetes verzióban érhető el. PowerShell segítségével munkaterületek, kísérleteket, a webszolgáltatások és több létrehozása és kezelése."
keywords: "kísérlet,lineáris regresszió,machine learning-algoritmusok,machine learning-oktatóanyag,prediktív modellezési technikák,adatelemzési kísérlet"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: garye;haining
ms.openlocfilehash: 47d005e584b6cb87d27a77f56ff619d342481aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>A Microsoft Azure Machine Learning PowerShell-modulja
Az Azure Machine Learning PowerShell-modul, amely lehetővé teszi a Windows PowerShell segítségével kezelje munkaterületek, kísérletek, adatkészleteket, klasszikus webszolgáltatások és további hatékony eszköz.

A dokumentáció megtekintéséhez és a modul letöltéséhez a teljes forráskóddal együtt lépjen a következő helyre: [https://aka.ms/amlps](https://aka.ms/amlps). 

> [!NOTE]
> Az Azure Machine Learning PowerShell modult jelenleg csak előzetes módban működik. A modul továbbfejlesztett és a próbaidőszak alatt kibontva továbbra is. Nyomon követheti a a [Cortana Intelligence és a Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) hírek és információ.

## <a name="what-is-the-machine-learning-powershell-module"></a>Mi a Machine Learning PowerShell-modulja?
A Machine Learning PowerShell-modulja a. A NET-alapú DLL-modult, amely lehetővé teszi, hogy teljes mértékben felügyelhesse a Windows PowerShell Azure Machine Learning munkaterületek, kísérleteket, adatkészleteket, klasszikus webszolgáltatások és klasszikus webszolgáltatás-végpontok. 

A modul, valamint letöltheti a teljes forráskód tartalmazó szabályszerűen elkülönített [C# API réteg](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). A dll-Fájlnak a saját .NET projekt hivatkozik, és kezelése az Azure Machine Learning keresztül .NET-kódot. Emellett a dll-fájl, amely közvetlenül a kedvenc ügyfélről is használhat, alapul szolgáló REST API-k függ.

## <a name="what-can-i-do-with-the-powershell-module"></a>Mire használhatom a PowerShell-modult?
Íme néhány példa a PowerShell-modullal elvégezhető feladatokra. Az alábbi funkciókkal és számos más funkcióval kapcsolatban tekintse meg a [teljes dokumentációt](https://aka.ms/amlps).

* Új munkaterület kiépítése felügyeleti tanúsítvány használatával ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Kísérleti diagramot jelölő JSON-fájlok exportálása és importálása ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Kísérlet futtatása ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Webszolgáltatás létrehozása prediktív kísérletből ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Hozzon létre egy végpontot a közzétett webes szolgáltatás ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* RRS és/vagy BES webszolgáltatás-végpont meghívása ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) és [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Az alábbi gyors példában meglévő kísérlet futtatását mutatjuk be a PowerShell használatával:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Részletesebb használati eset, lásd: Ez a cikk a általában a kért feladat automatizálása a PowerShell-modul segítségével: [létrehozása számos Machine Learning modellek és webes Szolgáltatásvégpontok PowerShell-lel egy kísérlet](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
A Machine Learning PowerShell használatának megkezdéséhez töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a GitHubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az utasítások azt ismertetik, hogyan feloldása a letöltött/unzipped dll-fájlt, és importálja azt a PowerShell környezetben. A parancsmagok többsége számára meg kell adni a munkaterület azonosítóját, a munkaterület engedélyezési jogkivonatát, valamint azt az Azure-régiót, amelyben a munkaterület megtalálható. A legegyszerűbben úgy adja meg az alapértelmezett config.json fájl keresztül történik. Az utasításokat is bemutatják, hogyan konfigurálhatja ezt a fájlt. 

Ha azt szeretné, átmásolhatja a git fa módosításához a kódot, és hogy helyileg Visual Studio.

## <a name="next-steps"></a>Következő lépések
A PowerShell modul teljes dokumentációjában található [https://aka.ms/amlps](https://aka.ms/amlps). 

Kiterjesztett példát a modul használata valós forgatókönyv esetében, tekintse meg a részletes használati eset [létrehozása számos Machine Learning modellek és webes Szolgáltatásvégpontok PowerShell-lel egy kísérlet](create-models-and-endpoints-with-powershell.md).
