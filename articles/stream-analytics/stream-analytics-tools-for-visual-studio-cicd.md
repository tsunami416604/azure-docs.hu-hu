---
title: Folyamatos integráció és a Stream Analytics tools fejlesztése
description: Ez a cikk ismerteti, hogyan állítsa be a folyamatos integráció és üzembe helyezési folyamat az Azure Stream Analytics Visual Studio-eszközök használatával.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: 85cb23d865133f2ee769874aa5150ab13e04cd69
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410571"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Folyamatos integráció és a Stream Analytics tools fejlesztése
Ez a cikk ismerteti, hogyan használható az Azure Stream Analytics tools for Visual Studio használatával állítsa be a folyamatos integrációs és üzembe helyezési folyamat.

2.3.0000.0 verzióját használja, vagy a fenti, [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) kérhet támogatást az MSBuild.

A NuGet-csomag érhető el: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Ez tartalmazza az MSBuild-, helyi futtatási, valamint a telepítési eszközöket, amelyek támogatják a Stream Analytics Visual Studio-projektek folyamatos integrációs és üzembehelyezési folyamatait. 
> [!NOTE] 
A NuGet-csomag használható csak a 2.3.0000.0 vagy újabb verziója a Stream Analytics Tools for Visual Studio. Ha korábbi verzióiban a Visual Studio tools-projektet, nyissa meg őket a 2.3.0000.0 vagy újabb verzió, és mentse. Ezután az új képességek is engedélyezve van. 

További információkért lásd: [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Például a szabványos Visual Studio MSBuild élményt hozhat létre egy projektet két lehetősége van. Kattintson a jobb gombbal a projektre, és válassza **összeállítása**. Használhatja még **MSBuild** a NuGet csomag a parancssorból.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Ha egy Stream Analytics Visual Studio-projekt sikeresen létrejött, a következő két Azure Resource Manager sablon fájlt alatt állít elő a **bin / [hibakeresési/kiskereskedelmi] és üzembe helyezése** mappa: 

*  Resource Manager-sablonfájl

       [ProjectName].JobTemplate.json 

*  Resource Manager-paraméterfájl

       [ProjectName].JobTemplate.parameters.json   

Az alapértelmezett paramétereket a parameters.json fájlban vannak a Visual Studio-projektben a beállításokat. Ha azt szeretné, egy másik környezetben való üzembe helyezéséhez, annak megfelelően cserélje le a paramétereket.

> [!NOTE] 
Az összes a hitelesítő adatokat, az alapértelmezett értékek vannak beállítva a null. Ön *szükséges* értékeinek beállítását a felhő üzembe helyezése előtt.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Ismerje meg, hogyan [üzembe helyezése Resource Manager-sablonfájlban és Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Ismerje meg, hogyan [objektum használata paraméterként a Resource Manager-sablonnal](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Felügyelt identitás használatára az Azure Data Lake Store Gen1 kimeneti fogadóként, meg kell hozzáférést biztosítania a szolgáltatásnévnek előtt üzembe helyezni az Azure PowerShell használatával. Ismerje meg, hogyan [ADLS Gen1 felügyelt identitással üzembe helyezése a Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Parancssori eszköz

### <a name="build-the-project"></a>A projekt felépítése
A NuGet-csomag rendelkezik nevű parancssori eszköz **SA.exe**. Támogatja a project build és a egy tetszőleges számítógépre, amelyen a folyamatos integrációs és teljesítési folyamat használható helyi tesztelést. 

Az üzembe helyezési fájlokat alapértelmezés szerint az aktuális könyvtár alá kerülnek. A kimeneti elérési út a következő – OutputPath paraméter segítségével adhatja meg:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>A parancsfájl helyi tesztelése

Ha a projekt helyi bemeneti fájlok van megadva, a Visual Studióban, egy automatizált parancsfájl tesztelési használatával futtathatja a *localrun* parancsot. A kimeneti eredmények az aktuális könyvtár alá kerül.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Hozzon létre egy feladatot csomagdefiníciós fájl a Stream Analytics PowerShell API használata

A *arm* parancs fogadja a feladat sablon és a feladat sablon paraméterfájljait keresztül build bemenetként létrehozott. Ezután azt egyesíti azokat egy feladat definícióját JSON-fájlt, amely a Stream Analytics PowerShell API-val használható.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Példa:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


