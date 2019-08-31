---
title: A Azure Stream Analytics CI/CD NuGet csomag használata az integrációhoz és a fejlesztéshez
description: Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD NuGet-csomag a folyamatos integrációs és üzembe helyezési folyamat beállításához.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 47bcd29ca8a1da0c42f7bc39aeb4ffc1ad8e8571
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172897"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>A Azure Stream Analytics CI/CD NuGet csomag használata az integrációhoz és a fejlesztéshez 
Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD NuGet csomag a folyamatos integrációs és üzembe helyezési folyamat beállításához.

Használja a Visual studióhoz készült [stream Analytics-eszközök](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) 2.3.0000.0 vagy újabb verzióját az MSBuild támogatásának megismeréséhez.

NuGet-csomag érhető el: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Olyan MSBuild, helyi futtatási és üzembe helyezési eszközöket biztosít, amelyek támogatják [stream Analytics Visual Studio-projektek](stream-analytics-vs-tools.md)folyamatos integrációját és üzembe helyezési folyamatát. 
> [!NOTE]
> A NuGet csomag csak a Visual studióhoz készült Stream Analytics Tools for 2.3.0000.0 vagy újabb verziójával használható. Ha a Visual Studio Tools korábbi verzióiban hoztak létre projekteket, egyszerűen nyissa meg őket a 2.3.0000.0 vagy a fenti verzióval, és mentse. Ezután az új funkciók engedélyezve vannak. 

További információ: [stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
A standard Visual Studio MSBuild-élményhez hasonlóan a projekt létrehozásához két lehetőség közül választhat. Kattintson a jobb gombbal a projektre, majd válassza a **Létrehozás**lehetőséget. A NuGet csomagban található **MSBuild** -t is használhatja a parancssorból.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio-projekt sikeres létrehozásakor a a következő két Azure Resource Manager sablonfájlt hozza létre a **bin/[debug/Retail]/Deploy** mappában: 

*  Resource Manager-sablonfájl

       [ProjectName].JobTemplate.json 

*  Resource Manager-paraméterek fájlja

       [ProjectName].JobTemplate.parameters.json   

A Parameters. JSON fájlban lévő alapértelmezett paraméterek a Visual Studio-projekt beállításaiból származnak. Ha egy másik környezetbe szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

> [!NOTE]
> Az összes hitelesítő adat esetében az alapértelmezett értékek NULL értékre vannak állítva. A felhőbe való üzembe helyezés előtt be kell állítania az értékeket.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
További információ a telepítéséről [Resource Manager-sablonfájl és Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). További információ arról, hogyan [használható egy objektum paraméterként egy Resource Manager-sablonban](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Ha Azure Data Lake Store Gen1 felügyelt identitást szeretne használni kimeneti fogadóként, az Azure-ba való üzembe helyezés előtt meg kell adnia a szolgáltatásnevet a PowerShell használatával. További információ a [ADLS Gen1 felügyelt identitással való üzembe helyezéséhez Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Parancssori eszköz

### <a name="build-the-project"></a>A projekt felépítése
Az NuGet-csomag egy **sa. exe**nevű parancssori eszközzel rendelkezik. Támogatja a projektek kialakítását és a helyi tesztelést egy tetszőleges gépen, amelyet a folyamatos integráció és a folyamatos kézbesítés folyamata használhat. 

A központi telepítési fájlok alapértelmezés szerint az aktuális könyvtár alá kerülnek. A kimeneti elérési utat a következő-OutputPath paraméter használatával adhatja meg:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>A parancsfájl helyi tesztelése

Ha a projekt helyi bemeneti fájlokat adott meg a Visual Studióban, a *localrun* parancs használatával futtathat egy automatizált parancsfájl-tesztet. A kimeneti eredmény az aktuális könyvtár alá kerül.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>A Stream Analytics PowerShell API-val használni kívánt feladatdefiníció létrehozása

Az *ARM* parancs a Build as bemenet használatával generált sablont és a feladatütemezés paramétereit veszi alapul. Ezután egy, a Stream Analytics PowerShell API-val használható, feladatdefiníció nevű JSON-fájlba kombinálja őket.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Példa:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studióban](stream-analytics-quick-create-vs.md)
* [Stream Analytics-lekérdezések Visual studióval helyileg tesztelése](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics feladatok megismerése a Visual Studióval](stream-analytics-vs-tools.md)
