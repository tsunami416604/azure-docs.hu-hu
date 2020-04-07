---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673154"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adjon hozzá egy függvényt a projekthez `--name` a következő paranccsal, ahol az argumentum a függvény egyedi neve (HttpExample), és az `--template` argumentum a függvény eseményindítóját (HTTP) adja meg. 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`HttpExample.cs kódfájlt hoz létre.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`létrehoz egy, a függvény nevének megfelelő almappát, amely a projekt választott nyelvének megfelelő kódfájlt és *function.json*nevű konfigurációs fájlt tartalmaz.
::: zone-end