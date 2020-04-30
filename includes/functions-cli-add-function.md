---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673154"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol `--name` az argumentum a függvény egyedi neve (HttpExample), az `--template` argumentum pedig a függvény triggerét (http) adja meg. 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`létrehoz egy HttpExample.cs.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`létrehoz egy, a függvény nevét tartalmazó almappát, amely a projekt választott nyelvéhez és egy *function. JSON*nevű konfigurációs fájlhoz tartozó kódot tartalmaz.
::: zone-end