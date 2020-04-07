---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673352"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Kimenetkötésdefiníció hozzáadása a függvényhez

Bár egy függvény csak egy eseményindítóval rendelkezhet, több bemeneti és kimeneti kötést is használhat, amelyek lehetővé teszik, hogy más Azure-szolgáltatásokhoz és erőforrásokhoz egyéni integrációs kód írása nélkül csatlakozzon. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ezeket a kötéseket a függvénymappában lévő *function.json* fájlban deklarálja. Az előző rövid útmutatóból a *HttpExample* mappában lévő `bindings` *function.json* fájl két kötést tartalmaz a gyűjteményben:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Minden kötésnek van legalább egy típusa, egy iránya és egy neve. A fenti példában az első `httpTrigger` kötés típusa `in`az irányával . Az `in` irányhoz `name` adja meg egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító meghívásakor a függvénynek küld.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A gyűjtemény második kötése `res`a . Ez `http` a kötés egy`out`kimeneti kötés ( ), amely a HTTP-válasz írására szolgál. 

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A gyűjtemény második kötése `http` az `out`irányával ellátott típusú, `name` `$return` amely esetben a speciális azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja a bemeneti paraméter megadása helyett.

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A gyűjtemény második kötése `res`a . Ez `http` a kötés egy`out`kimeneti kötés ( ), amely a HTTP-válasz írására szolgál. 

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ebben az `msg` esetben a függvény kimeneti argumentumként jelenik meg. Egy `queue` típus esetében meg kell adnia a `queueName` várólistának a nevét is, és meg kell adnia `connection`az Azure Storage-kapcsolat *nevét* (a *local.settings.json-ból)* a alkalmazásban. 
::: zone-end  
