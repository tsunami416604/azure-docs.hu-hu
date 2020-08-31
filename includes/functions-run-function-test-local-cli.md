---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 55c64048e0604987c5a4c26961e5617106358e76
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84436101"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A függvény futtatásához indítsa el a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionProj* mappából:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

A kimenet vége felé a következő soroknak kell megjelenniük: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Ha a HttpExample nem jelenik meg az alább látható módon, valószínűleg elindította a gazdagépet a projekt gyökérkönyvtárán kívülről. Ebben az esetben a **CTRL** + **C** billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappa mappájához, és futtassa újra az előző parancsot.

Másolja a függvény URL-címét `HttpExample` ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<your-name>` , így a teljes URL-címet, például: `http://localhost:7071/api/HttpExample?name=Functions` . A böngészőben a következőhöz hasonló üzenetnek kell megjelennie `Hello Functions` :

![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

Ha elkészült, használja a **CTRL C billentyűt**, + **C** és válassza `y` a functions gazdagép leállítását.
