---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190855"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Futtassa a függvényt a helyi Azure Functions futásidejű gazdagép nek a *LocalFunctionProj* mappából történő elindításával:

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

A kimenet vége felé a következő sorokjelennek meg: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Ha a HttpExample nem az alábbi módon jelenik meg, akkor valószínűleg a *HttpExample* mappából indította el az állomást. Ebben az esetben a **Ctrl C billentyűvel**+**C** állítsa le az állomást, keresse meg a szülő *LocalFunctionProj* mappát, és futtassa újra az előző parancsot.

Másolja a `HttpExample` függvény URL-címét erről a kimenetről `?name=<your-name>`egy böngészőbe, `http://localhost:7071/api/HttpExample?name=Functions`és fűzze hozzá a lekérdezési karakterláncot, így a teljes URL-t például a . A böngészőnek olyan `Hello Functions`üzenetet kell megjelenítenie, mint:

![A függvény helyileg futtatott eredménye a böngészőben](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

A terminál, amelyben `func start` futtatott is mutatja a napló kimeneti kérések esetén.

Ha készen áll, használja a `y` Ctrl**C** **billentyűt,**+és állítsa le a függvényállomást.