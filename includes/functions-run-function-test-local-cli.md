---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422863"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. A függvény futtatásához indítsa el a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionProj* mappából:

    ```
    func start
    ```

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

1. Másolja a függvény URL-címét `HttpExample` ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` , így a teljes URL-címet, például: `http://localhost:7071/api/HttpExample?name=Functions` . A böngészőben a következőhöz hasonló üzenetnek kell megjelennie `Hello Functions` :

    ![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

1. Ha elkészült, használja a **CTRL C billentyűt** , + **C** és válassza `y` a functions gazdagép leállítását.
