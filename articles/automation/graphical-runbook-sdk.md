---
title: A Azure Automation Grpahical runbook SDK áttekintése
description: Ez a cikk a Azure Automation grafikus Runbook SDK használatát ismerteti
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a06c190931fdd0f49132f815b153c08ece68c9f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849547"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>A Azure Automation grafikus runbook SDK használata

A [grafikus runbookok](automation-graphical-authoring-intro.md) olyan runbookok, amelyek segítik a mögöttes Windows PowerShell-vagy PowerShell munkafolyamat-kód bonyolultságának kezelését. A Microsoft Azure Automation grafikus authoring SDK lehetővé teszi, hogy a fejlesztők grafikus Runbookok hozzanak létre és szerkesszenek a Azure Automation szolgáltatással való használatra. A következő kódrészletek azt mutatják be, hogyan hozhat létre grafikus runbook a kódból.

## <a name="pre-requisites"></a>Előfeltételek

A kezdéshez importálja a `Microsoft.Azure.Automation.GraphicalRunbook.Model` csomagot a projektbe.

## <a name="create-a-runbook-object-instance"></a>Runbook objektum példányának létrehozása

Hivatkozzon a `Orchestrator.GraphRunbook.Model` szerelvényre, és hozza létre a `Orchestrator.GraphRunbook.Model.GraphRunbook` osztály egy példányát:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook paraméterek hozzáadása

Hozza létre `Orchestrator.GraphRunbook.Model.Parameter` objektumokat, és adja hozzá őket a runbook:

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

Hozza létre a megfelelő típusú tevékenységeket, és adja hozzá őket a runbook:

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

A tevékenységeket a következő osztályok valósítják meg a `Orchestrator.GraphRunbook.Model` névtérben:

|Osztály  |Tevékenység  |
|---------|---------|
|CommandActivity     | Meghívja a PowerShell-parancsot (parancsmag, függvény stb.).        |
|InvokeRunbookActivity     | Egy másik runbook-sort hív meg.        |
|JunctionActivity     | Megvárja, amíg az összes bejövő ág befejeződik.        |
|WorkflowScriptActivity     | Végrehajtja a PowerShell vagy a PowerShell munkafolyamat-kód (a runbook típusától függően) egy blokkját a runbook környezetében. Ez egy hatékony eszköz, de nem túlzott: a felhasználói felület ezt a szkriptet fogja megjeleníteni szövegként. a végrehajtó motor a megadott blokkot fekete dobozként kezeli, és nem próbálja meg elemezni a tartalmát, kivéve az alapszintű szintaxis-ellenőrzések során. Ha csak egyetlen PowerShell-parancsot kell meghívnia, inkább a CommandActivity-t.        |

> [!NOTE]
> A megadott osztályokból származó saját tevékenységek ne legyenek származtatva: Azure Automation nem fogja tudni használni a runbookok egyéni tevékenység-típusokkal.

A CommandActivity és a InvokeRunbookActivity paramétereket érték-leíróként kell megadni, nem közvetlen értékekként. Az érték leírók határozzák meg a tényleges paraméterek értékének meghatározását. Jelenleg a következő érték-leírókat kell megadnia:


|Leíró  |Meghatározás  |
|---------|---------|
|ConstantValueDescriptor     | Egy rögzített konstans értékre hivatkozik.        |
|RunbookParameterValueDescriptor     | Név alapján egy runbook paraméterre hivatkozik.        |
|ActivityOutputValueDescriptor     | Egy felsőbb rétegbeli tevékenység kimenetére hivatkozik, amely lehetővé teszi, hogy az egyik tevékenység "fizessen elő" egy másik tevékenység által létrehozott adatokat.        |
|AutomationVariableValueDescriptor     | Egy Automation változóra hivatkozik név szerint.         |
|AutomationCredentialValueDescriptor     | Az Automation-tanúsítvány objektumára hivatkozik név szerint.        |
|AutomationConnectionValueDescriptor     | Egy Automation-kapcsolódási eszközre hivatkozik név szerint.        |
|PowerShellExpressionValueDescriptor     | Egy szabad formátumú PowerShell-kifejezést ad meg, amely csak a tevékenység meghívása előtt lesz kiértékelve.  <br/>Ez egy hatékony eszköz, de nem túlzott: a felhasználói felület ezt a kifejezést szövegként fogja megjeleníteni. a végrehajtó motor a megadott blokkot fekete dobozként kezeli, és nem próbálja meg elemezni a tartalmát, kivéve az alapszintű szintaxis-ellenőrzések során. Ha lehetséges, inkább pontosabb értéket adjon meg.      |

> [!NOTE]
> Ne származtatja a saját értékeit a megadott osztályokból: Azure Automation nem fogja tudni használni a runbookok egyéni értékű leíró típusokkal.

A tevékenységek összekapcsolására és a runbook való hozzáadására szolgáló hivatkozások példánya:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>A runbook mentése fájlba

Runbook szerializálása `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` használatával:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ez a karakterlánc a **. graphrunbook** kiterjesztésű fájlba menthető, és ez a fájl importálható a Azure Automationba.
A szerializált formátum a `Orchestrator.GraphRunbook.Model.dll`későbbi verzióiban változhat. Megígérjük a visszamenőleges kompatibilitást: a `Orchestrator.GraphRunbook.Model.dll` egy régebbi verziójával szerializált runbook bármely újabb verzió deszerializálható. A továbbítási kompatibilitás nem garantált: egy újabb verzióval szerializált runbook nem lehet a régebbi verziókban deszerializálható.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Automation grafikus Runbookok, tekintse meg a [grafikus szerzői műveletek bemutatása](automation-graphical-authoring-intro.md) című témakört.

