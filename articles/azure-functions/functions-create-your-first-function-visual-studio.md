---
title: Az első függvény létrehozása az Azure-ban a Visual Studióval
description: Hozzon létre és tegyen közzé HTTP-triggerrel aktivált Azure Functions-függvényt a Visual Studióval.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 6d09453db0fdf8c09ad6d90c63b27f48508302be
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230713"
---
# <a name="create-your-first-function-using-visual-studio"></a>Az első függvény létrehozása a Visual Studio használatával

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet vagy közzé kellene tennie egy webalkalmazást.

Ebből a cikkből megtudhatja, hogyan használható a Visual Studio 2019 a "Hello World" függvény helyi létrehozásához és teszteléséhez, majd az Azure-ba való közzétételéhez. Ez a rövid útmutató a Visual Studio 2019-hez készült. Ha functions-projektet hoz létre a Visual Studio 2017 használatával, először telepítenie kell a [legújabb Azure functions eszközöket](functions-develop-vs.md#check-your-tools-version).

![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez először telepítenie kell a [Visual Studio 2019](https://azure.microsoft.com/downloads/)alkalmazást. Győződjön meg arról, hogy az **Azure-fejlesztési** munkaterhelés is telepítve van.

![A Visual Studio telepítése az Azure-fejlesztési számítási feladattal](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

A Visual Studio létrehoz egy projektet és egy osztályt, amely tartalmazza a HTTP trigger függvény típusának szabványos kódját. A metódus `FunctionName` attribútuma a függvény nevét állítja be, amely alapértelmezés szerint `HttpTrigger`. A `HttpTrigger` attribútum azt adja meg, hogy a függvényt egy HTTP-kérelem indítja el. A sablonkód elküld egy HTTP-választ, amely tartalmaz egy értéket a kérelem szövegtörzséből vagy a lekérdezési sztringből.

A függvény képességeit a bemeneti és kimeneti kötésekkel bővítheti, ha a megfelelő attribútumokat alkalmazza a metódusra. További információkért lásd az [Azure Functions C#-fejlesztői referenciaanyagának](functions-dotnet-class-library.md#triggers-and-bindings) [Eseményindítók és kötések](functions-dotnet-class-library.md) szakaszát.

Most, hogy már létrehozott egy függvényprojektet és egy HTTP-eseményindítóval aktivált függvényt, tesztelheti a helyi számítógépen.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio integrálva van Azure Functions Core Tools, így a függvények a teljes functions futtatókörnyezet használatával helyileg is tesztelhető.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. A Visual Studio Publishing létrehoz egy Function alkalmazást a projekt első közzétételekor.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel. Ahogyan korábban, most is az URL-címhez fűzze hozzá a `?name=<YOUR_NAME>` lekérdezési sztringet, és hajtsa végre a kérelmet.

    A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbiakban látható a böngészőben a távoli GET kérelemre a függvény által visszaadott válasz:

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Következő lépések

A Visual Studióval egy egyszerű HTTP-triggerrel C# aktivált függvény használatával hozhat létre és tehet közzé egy Function-alkalmazást az Azure-ban. Ha többet szeretne megtudni a függvények .NET-es kódtáraként való fejlesztéséről, tekintse meg a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs.md)
