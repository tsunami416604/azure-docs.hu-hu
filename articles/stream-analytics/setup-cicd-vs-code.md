---
title: A Azure Stream Analytics CI/CD NPM csomag használata
description: Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD NPM-csomag a folyamatos integrációs és üzembe helyezési folyamat beállításához.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173347"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>A Stream Analytics CI/CD NPM csomag használata
Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD NPM csomag a folyamatos integrációs és üzembe helyezési folyamat beállításához.

## <a name="build-the-vs-code-project"></a>A VS Code projekt összeállítása

Az **ASA-streamanalytics-vel NPM-** csomag használatával engedélyezheti a folyamatos integrációt és üzembe helyezést Azure stream Analytics feladatokhoz. A NPM csomag lehetővé teszi az eszközök számára, hogy [stream Analytics Visual Studio Code](quick-create-vs-code.md)-projektekhez Azure Resource Manager sablonokat lehessen előállítani. A Visual Studio Code telepítése nélkül is használható Windows, macOS és Linux rendszereken.

A [csomag letöltése](https://www.npmjs.com/package/azure-streamanalytics-cicd)után a következő paranccsal exportálhatja a Azure Resource Manager sablonokat. A **scriptPath** argumentum a **asaql** fájl abszolút elérési útja a projektben. Győződjön meg arról, hogy a asaproj. JSON és a JobConfig. JSON fájlok ugyanabban a mappában találhatók, mint a parancsfájl. Ha a **outputPath** nincs megadva, a sablonok a projekt **bin** mappájában, a **központi telepítés** mappában lesznek elhelyezve.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Példa (macOS rendszeren)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code-projekt sikeres létrehozásakor a a következő két Azure Resource Manager sablonfájlt hozza létre a **bin/[debug/Retail]/Deploy** mappában: 

*  Resource Manager-sablonfájl

       [ProjectName].JobTemplate.json 

*  Resource Manager-paraméterek fájlja

       [ProjectName].JobTemplate.parameters.json   

A Parameters. JSON fájlban lévő alapértelmezett paraméterek a Visual Studio Code projekt beállításaiból származnak. Ha egy másik környezetbe szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

> [!NOTE]
> Az összes hitelesítő adat esetében az alapértelmezett értékek NULL értékre vannak állítva. A felhőbe való üzembe helyezés előtt be kell állítania az értékeket.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
További információ a telepítéséről [Resource Manager-sablonfájl és Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). További információ arról, hogyan [használható egy objektum paraméterként egy Resource Manager-sablonban](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Ha Azure Data Lake Store Gen1 felügyelt identitást szeretne használni kimeneti fogadóként, az Azure-ba való üzembe helyezés előtt meg kell adnia a szolgáltatásnevet a PowerShell használatával. További információ a [ADLS Gen1 felügyelt identitással való üzembe helyezéséhez Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)](quick-create-vs-code.md)
* [Stream Analytics lekérdezések tesztelése helyileg a Visual Studio Code-ban (előzetes verzió)](vscode-local-run.md)
* [Ismerkedés a Azure Stream Analytics a Visual Studio Code (előzetes verzió) szolgáltatással](vscode-explore-jobs.md)
