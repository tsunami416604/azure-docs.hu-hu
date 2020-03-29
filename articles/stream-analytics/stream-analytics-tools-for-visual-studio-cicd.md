---
title: Az Azure Stream Analytics CI/CD NuGet csomag használata
description: Ez a cikk bemutatja, hogyan használhatja az Azure Stream Analytics CI/CD NuGet csomagot a folyamatos integrációs és üzembe helyezési folyamat beállításához.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354445"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Az Azure Stream Analytics CI/CD NuGet csomag használata integrációhoz és fejlesztéshez 
Ez a cikk bemutatja, hogyan használhatja az Azure Stream Analytics CI/CD NuGet csomagot egy folyamatos integrációs és üzembe helyezési folyamat beállításához.

Használja a 2.3.0000.0-s vagy újabb verziójú [Stream Analytics-eszközöket a Visual Studio számára](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) az MSBuild támogatásához.

Elérhető egy NuGet-csomag: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Biztosítja az MSBuild, a helyi futtatás és a telepítési eszközöket, amelyek támogatják a [Stream Analytics Visual Studio-projektek](stream-analytics-vs-tools.md)folyamatos integrációs és telepítési folyamatát. 
> [!NOTE]
> A NuGet csomag csak a Stream Analytics Tools for Visual Studio 2.3.0000.0 vagy újabb verziójával használható. Ha a Visual Studio-eszközök korábbi verzióiban létrehozott projekteket, nyissa meg őket a 2.3.0000.0 vagy újabb verzióval, és mentse őket. Ezután az új képességek engedélyezve vannak. 

További információt a [Stream Analytics-eszközök a Visual Studio számára című témakörben talál.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>Msbuild
A standard Visual Studio MSBuild élményhez hasonlóan a projekt létrehozásához két lehetőség közül választhat. Kattintson a jobb gombbal a projektre, és válassza a **Build parancsot.** Az **MSBuild** a NuGet csomagban is használható a parancssorból.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Amikor egy Stream Analytics Visual Studio-projekt sikeresen épül fel, a következő két Azure Resource Manager-sablonfájlt hozza létre a **bin/[Debug/Retail]/Deploy** mappában: 

*  Erőforrás-kezelő sablonfájlja

       [ProjectName].JobTemplate.json 

*  Erőforrás-kezelő paraméterfájlja

       [ProjectName].JobTemplate.parameters.json   

A parameters.json fájl alapértelmezett paraméterei a Visual Studio-projekt beállításaiból származnak. Ha egy másik környezetben szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

> [!NOTE]
> Az összes hitelesítő adat esetében az alapértelmezett értékek null értékűek. Az értékeket a felhőbe való üzembe helyezés előtt **be kell** állítania.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
További információ a [Resource Manager-sablonfájlokkal és az Azure PowerShellhasználatával történő üzembe helyezésről.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) További információ arról, hogyan [használhatók egy objektum paraméterként az Erőforrás-kezelő sablonban.](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

Az Azure Data Lake Store Gen1 felügyelt identitásának kimeneti fogadóként való használatához az Azure-ba való üzembe helyezés előtt hozzáférést kell biztosítania az egyszerű szolgáltatás hoz a PowerShell használatával. További információ az [ADLS Gen1 felügyelt identitással történő központi telepítéséről az Erőforrás-kezelő sablonnal.](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)


## <a name="command-line-tool"></a>Parancssori eszköz

### <a name="build-the-project"></a>A projekt felépítése
A NuGet csomag rendelkezik egy **SA.exe**nevű parancssori eszközzel. Ez támogat tervez épít és helyi vizsgálat -ra egy önkényes gép, melyik tudod használ -ban -a folyamatos egységbe rendezés és folyamatos felszabadítás folyamat. 

A központi telepítési fájlok alapértelmezés szerint az aktuális könyvtár ba kerülnek. A kimeneti elérési utat a következő -OutputPath paraméter rel adhatja meg:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>A parancsfájl helyi tesztelése

Ha a projekt helyi bemeneti fájlokat adott meg a Visual Studióban, a *localrun* paranccsal automatikus parancsfájltesztet futtathat. A kimeneti eredmény az aktuális könyvtárba kerül.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>A Stream Analytics PowerShell API-val használható feladatdefiníciós fájl létrehozása

Az *arm* parancs a builden keresztül létrehozott feladatsablont és feladatsablon paraméterfájlokat veszi át bemenetként. Ezután egyesíti őket egy feladatdefiníciós JSON-fájlba, amely a Stream Analytics PowerShell API-val használható.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Példa:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzon létre egy Azure Stream Analytics felhőfeladatot a Visual Studióban](stream-analytics-quick-create-vs.md)
* [A Test Stream Analytics lekérdezései helyileg a Visual Studióval](stream-analytics-vs-tools-local-run.md)
* [Fedezze fel az Azure Stream Analytics-feladatokat a Visual Studio segítségével](stream-analytics-vs-tools.md)
