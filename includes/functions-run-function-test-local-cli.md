---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673207"
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
> Ha a HttpExample nem jelenik meg az alább látható módon, valószínűleg elindította a gazdagépet a projekt gyökérkönyvtárán kívülről. Ebben az esetben a **CTRL**+**C** billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappa mappájához, és futtassa újra az előző parancsot.

Másolja a `HttpExample` függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzze hozzá `?name=<your-name>`a lekérdezési karakterláncot, `http://localhost:7071/api/HttpExample?name=Functions`így a teljes URL-címet, például:. A böngészőben a következőhöz hasonló `Hello Functions`üzenetnek kell megjelennie:

![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

Ha elkészült, használja a **CTRL C billentyűt**+**C** , `y` és válassza a functions gazdagép leállítását.