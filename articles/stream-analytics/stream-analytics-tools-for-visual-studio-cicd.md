---
title: A folyamatos integrációt és telepítést folyamat létrehozásához használja a Stream Analytics Visual Studio eszközök |} Microsoft Docs
description: Az oktatóanyag a szolgáltatás folyamatos integrációt és telepítést folyamat beállításához használja a Stream Analytics Visual Studio eszközök
keywords: a Visual studio NuGet, DevOps, CI/CD
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 14bb15f19b517b55281959f0de970e3f5e0d360b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Állítsa be a folyamatos integrációt és a telepítési folyamatot a Stream Analytics Visual Studio eszközök segítségével
Ebben az oktatóanyagban elsajátíthatja, hogyan használható az Azure Stream Analytics Visual Studio eszközök állíthat be egy folyamatos integrációt és a telepítési folyamat.

A legújabb verzióra (2.3.0000.0 vagy újabb) a [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) MSBuild támogatása.

Szerepel továbbá egy újonnan kiadott NuGet-csomag [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Az MSBuild, a helyi Futtatás és a központi telepítési eszközöket, amelyek támogatják a folyamatos integrációt és a telepítési folyamatot a Stream Analytics Visual Studio-projektek biztosít. 
> [!NOTE] 
A NuGet-csomag használható a Visual Studio csak a 2.3.0000.0 vagy annál újabb Stream Analytics eszközök. Ha a Visual Studio eszközök korábbi verzióiban létrehozott projektek, nyissa meg őket a 2.3.0000.0 vagy annál újabb, és mentse. Majd az új képességek is engedélyezve vannak. 

Ismerje meg, hogyan használható a [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Például a standard Visual Studio MSBuild élmény, a projekt felépítéséhez két lehetőség van. Kattintson jobb gombbal a projektre, és válassza a **Build**. Is használhat, **MSBuild** a NuGet csomag a parancssorból.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Sikeresen létrehozta a Stream Analytics Visual Studio-projekt, amikor a következő két Azure Resource Manager sablon fájl alapján generál a **bin / [hibakeresési/kereskedelmi] / telepítése** mappába: 

*  Erőforrás-kezelő sablonfájl

       [ProjectName].JobTemplate.json 

*  Erőforrás-kezelő paraméterfájl

       [ProjectName].JobTemplate.parameters.json   

Az alapértelmezett a parameters.json fájl paraméterei a Visual Studio-projektet az beállításai közül. Ha szeretné központilag telepíteni egy másik környezetbe, ennek megfelelően cserélje le a paramétereket.

> [!NOTE] 
Minden a hitelesítő adatokat, az alapértelmezett értéke null. Ön *szükséges* értékeinek beállítását a felhőbe történő telepítése előtt.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
További tudnivalók a [a Resource Manager sablon fájl és az Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). További tudnivalók a [objektum használata a Resource Manager-sablon egyik paraméterének](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Parancssori eszköz

### <a name="build-the-project"></a>A projekt felépítése
A NuGet-csomag rendelkezik nevű parancssori eszköz **SA.exe**. Támogatja a project build és a helyi tesztelése tetszőleges géphez, amelynek segítségével a folyamatos integrációt és folyamatos kézbesítési folyamatának. 

A telepítési fájlok alapértelmezés szerint az aktuális könyvtár alá. A kimeneti elérési út a következő - OutputPath paraméter használatával adhat meg:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>A parancsfájl helyi tesztelése

Ha a projekt helyi bemeneti fájlok van megadva, a Visual Studio, egy automatizált parancsfájl teszt segítségével futtathatja a *localrun* parancsot. A kimeneti eredmények helyezkedik el, az aktuális könyvtárban.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Egy feladat csomagdefiníciós fájl használata a Stream Analytics PowerShell API készítése

A *arm* parancs a feladat sablon és a feladat paraméter sablonfájlokat bemenetként összeállítása során generált fogadja. Majd össze egy fájlba feladat definition JSON használható a Stream Analytics PowerShell API-t.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Példa:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


