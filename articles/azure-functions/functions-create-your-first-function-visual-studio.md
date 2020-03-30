---
title: 'Rövid útmutató: Az első függvény létrehozása az Azure-ban a Visual Studio használatával'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és tehet közzé http-aktivált Azure-függvényt a Visual Studio használatával.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 13a03ca6820229fc90467236e0c23f236664d73f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056578"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rövid útmutató: Az első függvény létrehozása az Azure-ban a Visual Studio használatával

Az Azure Functions lehetővé teszi a kód futtatását kiszolgáló nélküli környezetben anélkül, hogy először létre kell hoznia egy virtuális számítógépet, vagy közzé kell tennie egy webalkalmazást.

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és tesztelhet helyileg egy "hello world" HTTP-triggered C# függvényalkalmazást, amelyet ezután közzétesz az Azure-ban. 

![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Ez a rövid útmutató a Visual Studio 2019-hez készült. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez először telepítse a [Visual Studio 2019-et.](https://azure.microsoft.com/downloads/) Győződjön meg arról, hogy a telepítés során kiválasztja az **Azure fejlesztési** számítási feladatokat. Ha inkább a Visual Studio 2017 használatával szeretne Azure Functions-projektet létrehozni, először telepítenie kell a [legújabb Azure Functions eszközöket.](functions-develop-vs.md#check-your-tools-version)

![A Visual Studio telepítése az Azure fejlesztési munkaterhelésével](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

A Visual Studio létrehoz egy projektet és osztályt, amely a HTTP trigger funkciótípus sablonkódját tartalmazza. A `FunctionName` metódus attribútum beállítja a függvény nevét, amely alapértelmezés szerint `Function1`a . Az `HttpTrigger` attribútum azt adja meg, hogy a függvényt HTTP-kérelem váltja ki. A sablonkód elküld egy HTTP-választ, amely tartalmaz egy értéket a kérelem szövegtörzséből vagy a lekérdezési sztringből.

Bővítse ki a funkció képességeit bemeneti és kimeneti kötésekkel a megfelelő attribútumok alkalmazásával a metódusra. További információkért lásd az [Azure Functions C#-fejlesztői referenciaanyagának](functions-dotnet-class-library.md)[Eseményindítók és kötések](functions-dotnet-class-library.md#triggers-and-bindings) szakaszát.

Most, hogy már létrehozott egy függvényprojektet és egy HTTP-eseményindítóval aktivált függvényt, tesztelheti a helyi számítógépen.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio integrálható az Azure Functions core eszközeivel, így a teljes Azure Functions-futásidő használatával helyileg tesztelheti a funkciókat.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzététele előtt rendelkeznie kell egy függvényalkalmazással az Azure-előfizetésében. A Visual Studio közzétételi webhelye a projekt első közzétételekor létrehoz egy függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a **Profil közzététele** lapról. Cserélje `localhost:port` le az URL-címnek azt a részét, amelyet a függvény helyi teszteléséhez használt az új alap URL-címre. Fűzze hozzá `?name=<YOUR_NAME>` a lekérdezési karakterláncot ehhez az URL-címhez, és futtassa a kérelmet.

    A HTTP által aktivált függvényt meghívja URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott távoli GET-kérésre adott válasz látható:

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Visual Studio segítségével hozhat létre és tehet közzé egy C# függvényalkalmazást az Azure-ban egy egyszerű HTTP-aktivált funkcióval. 

A következő cikkre való ugrással megtudhatja, hogyan adhat hozzá egy Azure Storage-várólista-kötést a függvényhez:
> [!div class="nextstepaction"]
> [Azure Storage-várólista-kötés hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs.md)

