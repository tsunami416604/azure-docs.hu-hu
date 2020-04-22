---
title: Az Azure Automation grafikus runbook SDK használata
description: Ez a cikk ismerteti, hogyan használhatja az Azure Automation grafikus runbook SDK.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682906"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Az Azure Automation grafikus runbook SDK használata

[A grafikus runbookok](automation-graphical-authoring-intro.md) segítenek az alapul szolgáló Windows PowerShell- vagy PowerShell-munkafolyamat-kód összetettségének kezelésében. A Microsoft Azure Automation grafikus sdk-készítő lehetővé teszi a fejlesztők számára, hogy grafikus runbookokat hozzanak létre és szerkesztsenek az Azure Automation használatával való használatra. Ez a cikk a grafikus runbook kódból történő létrehozásának alapvető lépéseit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Importálja `Microsoft.Azure.Automation.GraphicalRunbook.Model` a csomagot a projektbe.

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

A tevékenységeket a névtérben `Orchestrator.GraphRunbook.Model` a következő osztályok hajtják végre.

|Osztály  |Tevékenység  |
|---------|---------|
|CommandActivity     | PowerShell-parancs meghívása (parancsmag, függvény stb.).        |
|InvokeRunbookActivity     | Meghívja egy másik runbook inline.        |
|Csomópontaktivitás     | Megvárja, amíg az összes bejövő ág befejeződik.        |
|WorkflowScriptActivity tevékenység     | PowerShell- vagy PowerShell-munkafolyamat-kód egy blokkját hajtja végre (a runbook típusától függően) a runbook környezetében. Ez egy hatékony eszköz, de ne használja túl: a felhasználói felület szövegként jeleníti meg ezt a szkriptblokkot; a végrehajtási motor a megadott blokkot fekete dobozként kezeli, és nem kísérli meg a tartalmának elemzését, kivéve egy alapvető szintaxis-ellenőrzést. Ha csak egyetlen PowerShell-parancsot kell meghívnia, inkább commandactivity.        |

> [!NOTE]
> Ne adhatja ki a saját tevékenységeit a megadott osztályokból. Az Azure Automation nem használhatja az egyéni tevékenységtípusokkal rendelkező runbookokat.

A paramétereket `InvokeRunbookActivity` értékleíróként kell megadnia, `CommandActivity` nem pedig közvetlen értékekként. Az értékleírók határozzák meg a tényleges paraméterértékek előállításának módját. A következő értékleírók jelenleg rendelkezésre állnak:


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
> Ne adhatja ki a saját értékleírókat a megadott osztályokból. Az Azure Automation nem használhatja az egyéni értékleíró-típusokkal rendelkező runbookokat.

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

Ezt a karakterláncot a **.graphrunbook** kiterjesztésű fájlba mentheti. A megfelelő runbook importálható az Azure Automationbe.
A szerializált formátum a `Orchestrator.GraphRunbook.Model.dll`program későbbi verzióiban változhat. Ígérjük visszamenőleges kompatibilitás: minden runbook szerializált egy régebbi verzióját `Orchestrator.GraphRunbook.Model.dll` lehet deszerializálható bármely újabb verzió. Az előrekompatibilitás nem garantált: előfordulhat, hogy az újabb verzióval szerializált runbookok nem deszerializálhatók a régebbi verziók ban.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az Azure Automation grafikus runbookjairól, olvassa el [a Grafikus szerzői bevezetés című témakört.](automation-graphical-authoring-intro.md)