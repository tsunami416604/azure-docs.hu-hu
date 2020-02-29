---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190855"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

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
> Ha a HttpExample nem jelenik meg az alább látható módon, valószínűleg elindította a gazdagépet a *HttpExample* mappából. Ebben az esetben a **Ctrl**+**C** billentyűkombinációval állítsa le a gazdagépet, navigáljon a szülő *LocalFunctionProj* mappára, és futtassa újra az előző parancsot.

Másolja a `HttpExample` függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<your-name>`, így a teljes URL-címet, például `http://localhost:7071/api/HttpExample?name=Functions`. A böngészőben a következőhöz hasonló üzenetnek kell megjelennie `Hello Functions`:

![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

A `func start` futtatott terminál a kérések során a naplózási kimenetet is megjeleníti.

Ha elkészült, használja a CTRL+**C** **billentyűkombinációt** , és válassza a `y` lehetőséget a functions gazdagép leállításához.