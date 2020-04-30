---
title: 'Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és tehet közzé HTTP-triggeres Azure-függvényeket a Visual Studio használatával.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81308931"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval

Azure Functions lehetővé teszi a kód kiszolgáló nélküli környezetben való futtatását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást.

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Visual Studio 2019-et a "Hello World" HTTP által aktivált C# Function-alkalmazás helyi létrehozásához és teszteléséhez, amelyet aztán közzétehet az Azure-ban. 

![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Ez a rövid útmutató a Visual Studio 2019-hez készült. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez először telepítse a [Visual Studio 2019](https://azure.microsoft.com/downloads/)-es frissítést. Győződjön meg arról, hogy a telepítés során kijelöli az **Azure-fejlesztési** számítási feladatot. Ha a Visual Studio 2017 használatával szeretne létrehozni egy Azure Functions projektet, először telepítenie kell a [legújabb Azure functions eszközöket](functions-develop-vs.md#check-your-tools-version).

![A Visual Studio telepítése az Azure-fejlesztési számítási feladattal](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Ha nem rendelkezik [Azure-előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/dotnet/).

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

A Visual Studio létrehoz egy projektet és egy osztályt, amely tartalmazza a HTTP trigger függvény típusának szabványos kódját. A `FunctionName` Method attribútum a függvény nevét állítja be, amely alapértelmezés szerint a ( `Function1`z). Az `HttpTrigger` attribútum azt adja meg, hogy a függvényt egy HTTP-kérelem indítja el. A sablonkód elküld egy HTTP-választ, amely tartalmaz egy értéket a kérelem szövegtörzséből vagy a lekérdezési sztringből.

A megfelelő attribútumok a metódushoz való alkalmazásával kiterjesztheti a függvény képességeit a bemeneti és kimeneti kötésekkel. További információkért lásd az [Azure Functions C#-fejlesztői referenciaanyagának](functions-dotnet-class-library.md)[Eseményindítók és kötések](functions-dotnet-class-library.md#triggers-and-bindings) szakaszát.

Most, hogy már létrehozott egy függvényprojektet és egy HTTP-eseményindítóval aktivált függvényt, tesztelheti a helyi számítógépen.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio integrálva van Azure Functions Core Tools, így helyileg tesztelheti a függvényeket a teljes Azure Functions Runtime használatával.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzététele előtt rendelkeznie kell egy Function alkalmazással az Azure-előfizetésében. A Visual Studio Publishing létrehoz egy Function alkalmazást a projekt első közzétételekor.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a Function alkalmazás alap URL-címét a profil **közzététele** lapról. Cserélje le `localhost:port` a függvény helyi teszteléséhez használt URL-cím részét az új alap URL-címmel. Fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` ehhez az URL-címhez, és futtassa a kérést.

    A HTTP-triggert használó függvényt meghívó URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbi képen látható, hogy milyen választ kap a böngészőben a távoli GET kérelem a függvény által visszaadott válasza:

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Visual studiót használta egy C#-függvény alkalmazás létrehozásához és közzétételéhez az Azure-ban egy egyszerű HTTP-triggert használó függvény használatával. 

A következő cikkből megtudhatja, hogyan adhat hozzá Azure Storage-üzenetsor-kötést a függvényhez:
> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs.md)

