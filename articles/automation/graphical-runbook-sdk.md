---
title: Az Azure Automation Grpahical runbook SDK áttekintése
description: Ez a cikk az Azure Automation grafikus runbook SDK használatát ismerteti
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: d4dcf6681ade977847c204dd1237f7cd7a67775e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418252"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Az Azure Automation grafikus runbook SDK használata

[A grafikus runbookok](automation-graphical-authoring-intro.md) olyan runbookok, amelyek segítenek az alapul szolgáló Windows PowerShell- vagy PowerShell-munkafolyamat-kód összetettségének kezelésében. A Microsoft Azure Automation grafikus szerzői SDK lehetővé teszi a fejlesztők számára, hogy grafikus runbookokat hozzanak létre és szerkessenek az Azure Automation szolgáltatással való használatra. A következő kódrészletek a grafikus runbook létrehozásának alapvető folyamatát mutatják be a kódból.

## <a name="pre-requisites"></a>Előfeltételek

Először importálja `Microsoft.Azure.Automation.GraphicalRunbook.Model` a csomagot a projektbe.

## <a name="create-a-runbook-object-instance"></a>Runbook-objektumpéldány létrehozása

Hivatkozzon `Orchestrator.GraphRunbook.Model` a szerelvényre, `Orchestrator.GraphRunbook.Model.GraphRunbook` és hozzon létre egy példányt az osztályból:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook-paraméterek hozzáadása

Objektumok at `Orchestrator.GraphRunbook.Model.Parameter` hozlétre, és adja hozzá őket a runbookhoz:

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

## <a name="add-activities-and-links"></a>Tevékenységek és hivatkozások hozzáadása

A megfelelő típusú tevékenységek et példányosítani kell, és hozzá kell adni a runbookhoz:

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

A tevékenységeket a névtérben `Orchestrator.GraphRunbook.Model` a következő osztályok hajtják végre:

|Osztály  |Tevékenység  |
|---------|---------|
|CommandActivity     | PowerShell-parancs meghívása (parancsmag, függvény stb.).        |
|InvokeRunbookActivity     | Meghívja egy másik runbook inline.        |
|Csomópontaktivitás     | Megvárja, amíg az összes bejövő ág befejeződik.        |
|WorkflowScriptActivity tevékenység     | PowerShell- vagy PowerShell-munkafolyamat-kód egy blokkját hajtja végre (a runbook típusától függően) a runbook környezetében. Ez egy hatékony eszköz, de ne használja túl: a felhasználói felület szövegként jeleníti meg ezt a szkriptblokkot; a végrehajtási motor a megadott blokkot fekete dobozként kezeli, és nem kísérli meg a tartalmának elemzését, kivéve egy alapvető szintaxis-ellenőrzést. Ha csak egyetlen PowerShell-parancsot kell meghívnia, inkább commandactivity.        |

> [!NOTE]
> Ne adhatja le a saját tevékenységeit a megadott osztályokból: az Azure Automation nem fogja tudni használni az egyéni tevékenységtípusokkal rendelkező runbookokat.

A CommandActivity és az InvokeRunbookActivity paramétereket értékleíróként kell megadni, nem pedig közvetlen értékként. Az értékleírók határozzák meg a tényleges paraméterértékek előállításának módját. A következő értékleírók jelenleg rendelkezésre állnak:


|Leíró  |Meghatározás  |
|---------|---------|
|ConstantValueDescriptor     | Kódolt állandó értékre utal.        |
|RunbookParameterValueDescriptor     | Egy runbook-paraméterre hivatkozik név szerint.        |
|ActivityOutputValueDescriptor     | Egy upstream tevékenységkimenetre utal, amely lehetővé teszi az egyik tevékenység számára, hogy "feliratkozzon" egy másik tevékenység által előállított adatokra.        |
|AutomationVariableValueDescriptor     | Egy Automation Variable eszközre hivatkozik név szerint.         |
|AutomationCredentialValueDescriptor     | Egy automation-tanúsítvány eszköznév szerint hivatkozik.        |
|AutomationConnectionValueDescriptor     | Egy Automation Connection eszközre hivatkozik név szerint.        |
|PowerShellExpressionValueDescriptor     | Megadja a szabad formátumú PowerShell-kifejezést, amely et közvetlenül a tevékenység meghívása előtt kiértékeli a rendszer.  <br/>Ez egy hatékony eszköz, de ne használja túl: a felhasználói felület ezt a kifejezést szövegként jeleníti meg; a végrehajtási motor a megadott blokkot fekete dobozként kezeli, és nem kísérli meg a tartalmának elemzését, kivéve egy alapvető szintaxis-ellenőrzést. Ha lehetséges, inkább konkrétabb értékleírók.      |

> [!NOTE]
> Ne hozza létre a saját értékleírókat a megadott osztályokból: Az Azure Automation nem fogja tudni használni az egyéni értékleírótípusokkal rendelkező runbookokat.

A hivatkozások at a kapcsolatot összekapcsoló tevékenységeket, és adja hozzá őket a runbookhoz:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>A runbook mentése fájlba

Runbook `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` szerializálására használható karakterlánc:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ez a karakterlánc menthető egy fájlba a **.graphrunbook** kiterjesztéssel, és ez a fájl importálható az Azure Automationbe.
A szerializált formátum a `Orchestrator.GraphRunbook.Model.dll`program későbbi verzióiban változhat. Ígérjük visszamenőleges kompatibilitás: minden runbook szerializált egy régebbi verzióját `Orchestrator.GraphRunbook.Model.dll` lehet deszerializálható bármely újabb verzió. Az előrekompatibilitás nem garantált: előfordulhat, hogy az újabb verzióval szerializált runbookok nem deszerializálhatók a régebbi verziók ban.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Automation grafikus runbookjairól, olvassa el [a Grafikus szerzői bevezetés című témakört.](automation-graphical-authoring-intro.md)

