---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673352"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Kimeneti kötési definíció hozzáadása a függvényhez

Bár a függvények csak egy triggerrel rendelkezhetnek, több bemeneti és kimeneti kötéssel is rendelkezhet, amelyek lehetővé teszik más Azure-szolgáltatásokhoz és-erőforrásokhoz való csatlakozást az egyéni integrációs kód írása nélkül. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ezeket a kötéseket a Function *. JSON* fájlban deklarálhatja a függvény mappájában. Az előző rövid útmutatóban a *function. JSON* fájl a *HttpExample* mappában két kötést tartalmaz a `bindings` gyűjteményben:  
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
Minden kötéshez legalább egy típus, egy irány és egy név tartozik. A fenti példában az első kötés típusa `httpTrigger` az irány. `in` Az `in` iránynál `name` adja meg egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító által meghívott függvénynek továbbít.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A gyűjtemény második kötésének neve `res`. Ez `http` a kötés egy kimeneti kötés (`out`), amely a http-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, `out` adjon hozzá egy `queue` típusú `msg`kötést a következő kódban látható módon:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A gyűjtemény `http` második kötése az iránysal `out`van megadva, ebben az esetben a speciális `name` érték `$return` azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja, nem pedig bemeneti paramétert.

Ha a függvényből szeretne írni egy Azure Storage-várólistába, `out` adjon hozzá egy `queue` típusú `msg`kötést a következő kódban látható módon:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A gyűjtemény második kötésének neve `res`. Ez `http` a kötés egy kimeneti kötés (`out`), amely a http-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, `out` adjon hozzá egy `queue` típusú `msg`kötést a következő kódban látható módon:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ebben az esetben a `msg` függvény kimeneti argumentumként van megadva. `queue` A `queueName` típushoz meg kell adnia a várólista nevét is, és meg kell adnia az Azure Storage-kapcsolatok *nevét* (a *Local. Settings. JSON*fájlból) `connection`a alkalmazásban. 
::: zone-end  
