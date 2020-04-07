---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673256"
---
Java projektben a kötések a függvénymetódus kötési jegyzeteiként vannak definiálva. A *function.json* fájl ezután automatikusan generálódik ezen jegyzetek alapján.

Tallózással keresse meg a függvénykód helyét _az src/main/java_alatt, nyissa meg `run` a *Function.java* projektfájlt, és adja hozzá a következő paramétert a metódus definíciójához:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

A `msg` paraméter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) egy típus, amely a függvény befejezésekor a kimeneti kötéshez üzenetekként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`tárolási várólista nevű. A storage-fiók kapcsolati karakterláncát `connection` a metódus állítja be. Ahelyett, hogy maga a kapcsolati karakterlánc, adja át az alkalmazás beállítást, amely tartalmazza a storage-fiók kapcsolati karakterlánc.

A `run` metódus definíciójának most a következő példához hasonlóan kell kinéznie:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```