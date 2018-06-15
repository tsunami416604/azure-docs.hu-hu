---
title: Folyamatosan integrálása és a Stream Analytics eszközökkel fejleszthet
description: A cikkből megtudhatja, hogyan használhatja a Visual Studio eszközök Azure Stream Analytics állíthat be egy folyamatos integrációt és a telepítési folyamat.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906273"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Folyamatosan integrálása és a Stream Analytics eszközökkel fejleszthet
A cikkből megtudhatja, hogyan használható az Azure Stream Analytics-eszközök Visual Studio használatával állítsa be a folyamatos integrációt és a telepítési folyamatot.

2.3.0000.0 használata verzió vagy újabb, [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) kérhet támogatást az MSBuild.

A NuGet csomag érhető el: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Az MSBuild, a helyi Futtatás és a központi telepítési eszközöket, amelyek támogatják a folyamatos integrációt és a telepítési folyamatot a Stream Analytics Visual Studio-projektek biztosít. 
> [!NOTE] 
A NuGet-csomag használható a Visual Studio csak a 2.3.0000.0 vagy annál újabb Stream Analytics eszközök. Ha a Visual Studio eszközök korábbi verzióiban létrehozott projektek, nyissa meg őket a 2.3.0000.0 vagy annál újabb, és mentse. Majd az új képességek is engedélyezve vannak. 

További információkért lásd: [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

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


