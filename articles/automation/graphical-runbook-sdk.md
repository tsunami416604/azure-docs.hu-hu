---
title: Az Azure Automation Grpahical runbook SDK áttekintése
description: Ez a cikk azt ismerteti, hogyan használható az Azure Automation grafikus Runbook SDK
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 702af8311887afc94e7127704d3377e944503324
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239927"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Az Azure Automation grafikus runbook SDK használata

[Grafikus runbookok](automation-graphical-authoring-intro.md) olyan runbookok, amelyek az alapul szolgáló Windows PowerShell vagy a PowerShell-munkafolyamati kód kiküszöböli kezelésében nyújt segítséget. A Microsoft Azure Automation grafikus szerzői SDK segítségével a fejlesztők hozhat létre, és a grafikus Runbookok szerkesztése az Azure Automation szolgáltatással való használatra. Az alábbi kódrészleteket mutatja a alapszintű grafikus runbook létrehozása a kódból.

## <a name="pre-requisites"></a>Előfeltételek

Először importálja a `Microsoft.Azure.Automation.GraphicalRunbook.Model` csomagot a projektbe a Finderből.

## <a name="create-a-runbook-object-instance"></a>Egy runbook objektum-példány létrehozása

Referencia a `Orchestrator.GraphRunbook.Model` szerelvény, és hozzon létre egy példányt a `Orchestrator.GraphRunbook.Model.GraphRunbook` osztály:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>A runbook paramétereinek hozzáadása

Hozza létre `Orchestrator.GraphRunbook.Model.Parameter` objektumokat, és hozzáadhatja őket a runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Tevékenységek és a hivatkozások hozzáadása

Hozza létre a megfelelő típusú tevékenységek, és hozzáadhatja őket a runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

A következő osztályok által megvalósított tevékenységek a `Orchestrator.GraphRunbook.Model` névteret:

|Osztály  |Tevékenység  |
|---------|---------|
|CommandActivity     | Hívja meg egy PowerShell-parancsot (a parancsmag, függvény, stb.).        |
|InvokeRunbookActivity     | Egy másik beágyazott runbookot hív meg.        |
|JunctionActivity     | Megvárja, amíg befejeződik az összes bejövő ágak.        |
|WorkflowScriptActivity     | Végrehajtja a kódblokkot PowerShell vagy a PowerShell-munkafolyamat (attól függően, a runbook típusa) a runbook kontextusában. Ez egy hatékony eszköz, de nem túlhasználatnak: a felhasználói felület jelennek meg a parancsfájl-blokkon szövegként; a végrehajtó motor fogja kezelni a megadott blokk egy fekete mezőt, és elvégzi a tartalomelemzés, kivéve egy alapszintű szintaxis-ellenőrzés nem tett kísérletek. Ha csupán egyetlen PowerShell-paranccsal meghívni, inkább CommandActivity.        |

> [!NOTE]
> Saját tevékenységek származtatva a megadott osztályok: Azure Automation nem fogja tudni típusú egyéni tevékenységek használata a runbookok.

CommandActivity és InvokeRunbookActivity paraméter értéke leírók, nincs közvetlen értékeket meg kell adni. Leírók értéket adja meg, hogyan kell a tényleges paraméter értékét mutatni. A következő érték leírók jelenleg áll rendelkezésre:


|Leíró  |Meghatározás  |
|---------|---------|
|ConstantValueDescriptor     | Állandó érték változtatható hivatkozik.        |
|RunbookParameterValueDescriptor     | Hivatkozik egy runbook paraméter neve.        |
|ActivityOutputValueDescriptor     | Egy felsőbb szintű tevékenység kimeneti, így egy tevékenység egy másik tevékenység által előállított adatok "előfizetés" hivatkozik.        |
|AutomationVariableValueDescriptor     | Egy Automation-változóeszköz név alapján hivatkozik.         |
|AutomationCredentialValueDescriptor     | Egy Automation-tanúsítványobjektum név alapján hivatkozik.        |
|AutomationConnectionValueDescriptor     | Egy Automation-kapcsolatobjektum név alapján hivatkozik.        |
|PowerShellExpressionValueDescriptor     | Megadja a szabad formátumú PowerShell-kifejezés, amelyek értékelik a tevékenység meghívása előtt.  <br/>Ez egy hatékony eszköz, de nem túlhasználatnak: a felhasználói felület jelennek meg a kifejezés szövegként; a végrehajtó motor fogja kezelni a megadott blokk egy fekete mezőt, és elvégzi a tartalomelemzés, kivéve egy alapszintű szintaxis-ellenőrzés nem tett kísérletek. Ha lehetséges, inkább az érték pontosabb leírók.      |

> [!NOTE]
> A saját érték leírók származtatva a megadott osztályok: Azure Automation nem fogja tudni az egyéni érték leíró típusok runbookokkal.

Hozza létre a tevékenységek összekapcsolása hivatkozásokat, és hozzáadhatja őket a runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>A runbook mentése fájlba

Használat `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` egy karakterláncot egy runbook szerializálásához:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ez a karakterlánc egy fájlt, melyekbe menthetők a **.graphrunbook** bővítményt, és ezt a fájlt az Azure Automationbe importálhatók.
Szerializált formátuma jövőbeli verzióiban változhat `Orchestrator.GraphRunbook.Model.dll`. Garantáljuk az előző verziókkal való kompatibilitás: minden runbook egy régebbi verziójával szerializált `Orchestrator.GraphRunbook.Model.dll` is lehet deszerializálni, minden újabb verziója. Nem garantált, kompatibilitást: egy runbook szerializálni egy újabb verzióval nem lehet régebbi verziói deszerializálható.

## <a name="next-steps"></a>További lépések

Az Azure Automation grafikus Runbookokban kapcsolatos további információkért lásd: [grafikus létrehozásról bemutatása](automation-graphical-authoring-intro.md)
