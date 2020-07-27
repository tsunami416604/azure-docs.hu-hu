---
title: Az első függvény létrehozása az Azure portálon
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: cd4ddc5b6b685813740aed63cc6f446bc66e8169
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170094"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Az első függvény létrehozása az Azure portálon

Azure Functions lehetővé teszi a kód kiszolgáló nélküli környezetben való futtatását anélkül, hogy először létre kellene hoznia egy virtuális gépet (VM), vagy közzé kellene tennie egy webalkalmazást. Ebből a cikkből megtudhatja, hogyan használhatja a Azure Functionst a "Hello World" HTTP-trigger függvény létrehozásához a Azure Portalban.

Javasoljuk, hogy [helyileg fejlessze a függvényeket](functions-develop-local.md) , és tegye közzé az Azure-ban egy Function alkalmazásban.  
A következő hivatkozások egyikével kezdheti meg a választott helyi fejlesztési környezet és nyelv használatát:

| Visual Studio Code | Terminál/parancssor | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Ismerkedés a C-vel #](./functions-create-first-function-vs-code.md?pivots=programming-language-csharp)<br/>&bull;&nbsp;[Ismerkedés a Javával](./functions-create-first-function-vs-code.md?pivots=programming-language-java)<br/>&bull;&nbsp;[Ismerkedés a JavaScripttel](./functions-create-first-function-vs-code.md?pivots=programming-language-javascript)<br/>&bull;&nbsp;[Ismerkedés a PowerShell-lel](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell)<br/>&bull;&nbsp;[Ismerkedés a Pythontal](./functions-create-first-function-vs-code.md?pivots=programming-language-python) |&bull;&nbsp;[Ismerkedés a C-vel #](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-csharp)<br/>&bull;&nbsp;[Ismerkedés a Javával](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)<br/>&bull;&nbsp;[Ismerkedés a JavaScripttel](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-javascript)<br/>&bull;&nbsp;[Ismerkedés a PowerShell-lel](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-powershell)<br/>&bull;&nbsp;[Ismerkedés a Pythontal](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) | [Ismerkedés a C-vel #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután hozzon létre egy függvényt az új függvény alkalmazásban.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP-trigger függvény létrehozása

1. A **függvények** ablak bal oldali menüjében válassza a **függvények**lehetőséget, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 
 
1. Az **új függvény** ablakban válassza a **http-trigger**lehetőséget.

    ![HTTP-trigger függvény kiválasztása](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Az **új függvény** ablakban fogadja el az **új függvény**alapértelmezett nevét, vagy adjon meg egy új nevet. 

1. Válassza a **Névtelen** lehetőséget az **engedélyezési szint** legördülő listából, majd kattintson a **függvény létrehozása**elemre.

    Az Azure létrehozza a HTTP trigger függvényt. Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új HTTP-trigger függvényben válassza a **kód + tesztelés** elemet a bal oldali menüben, majd a felső menüben válassza a **függvény URL-címének beolvasása** elemet.

    ![Válassza a függvény URL-címének beolvasása](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. A **függvény URL-címének beolvasása** párbeszédpanelen válassza az **alapértelmezett** lehetőséget a legördülő listából, majd válassza a **Másolás a vágólapra** ikont. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a lekérdezési karakterlánc értékét `?name=<your_name>` az URL végéhez, majd nyomja le az ENTER billentyűt a kérelem futtatásához. 

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. A nyomkövetés kimenetének megtekintéséhez térjen vissza a **kód + teszt** lapra a portálon, és bontsa ki a lap alján található **naplók** nyilat.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

