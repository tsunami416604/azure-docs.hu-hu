---
title: Azure-funkciók helyi fejlesztése és futtatása
description: Ismerje meg, hogyan kódolhatja és tesztelheti az Azure-függvényeket a helyi számítógépen, mielőtt futtatja őket az Azure Functions ben.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230635"
---
# <a name="code-and-test-azure-functions-locally"></a>Az Azure Functions helyi kódolása és tesztelése

Bár az Azure-funkciókat az Azure [Portalon]fejlesztheti és tesztelheti, sok fejlesztő a helyi fejlesztési élményt részesíti előnyben. Funkciók megkönnyítik a kedvenc kódszerkesztő és fejlesztőeszközök létrehozására és tesztelésére funkciók a helyi számítógépen. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a teljes Functions futásidő használatával hibakeresést okozhatnak a helyi számítógépen.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetek

A helyi számítógépen a funkciók fejlesztésének módja a [nyelvtől](supported-languages.md) és az eszközbeállításoktól függ. Az alábbi táblázatban található környezetek támogatják a helyi fejlesztést:

|Környezet                              |Nyelvek         |Leírás|
|-----------------------------------------|------------|---|
|[Visual Studio kód](functions-develop-vs-code.md)| [C# (osztálykönyvtár),](functions-dotnet-class-library.md) [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | Az [Azure Functions bővítmény vs kód](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) funkciók támogatása vs kód. A központi eszközöket igényli. Támogatja a fejlesztést Linux, MacOS és Windows rendszeren, a Core Tools 2.x verziójának használatakor. További információ: [Az első funkció létrehozása a Visual Studio-kód használatával című témakörben olvashat.](functions-create-first-function-vs-code.md) |
| [Parancssor vagy terminál](functions-run-local.md) | [C# (osztálykönyvtár),](functions-dotnet-class-library.md) [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Az Azure Functions Core Tools] biztosítja a fő futásidejű és sablonok at funkciók létrehozásához, amelyek lehetővé teszik a helyi fejlesztést. A 2.x verzió támogatja a fejlesztést Linux, MacOS és Windows rendszeren. Minden környezet a Core Tools-ra támaszkodik a helyi függvények futásidejében. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (osztálytár)](functions-dotnet-class-library.md) | Az Azure Functions-eszközök a Visual [Studio 2019-es](https://www.visualstudio.com/vs/) és újabb verzióinak **Azure-fejlesztési** munkaterhelései részét képezik. Lehetővé teszi a függvények összeállítását egy osztálytárban, és közzéteheti a .dll fájlt az Azure-ban. Tartalmazza a core eszközök helyi tesztelés. További információ: [Azure-funkciók fejlesztése a Visual Studio használatával.](functions-develop-vs.md) |
| [Maven](functions-create-first-java-maven.md) (különböző) | [Java](functions-reference-java.md) | Integrálható a Core Tools, hogy a java funkciók fejlesztése. A 2.x verzió támogatja a fejlesztést Linux, MacOS és Windows rendszeren. További információ: [Az első függvény létrehozása java és mavenne című témakörben](functions-create-first-java-maven.md)olvashat. Szintén támogatja a fejlesztést az [Eclipse](functions-create-maven-eclipse.md) és [az IntelliJ IDEA használatával](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A helyi fejlesztési környezetek mindegyike lehetővé teszi függvényalkalmazás-projektek létrehozását, és előre definiált Függvénysablonok használatával új függvényeket hozhat létre. Mindegyik a Core Tools-t használja, így tesztelheti és debug a funkciókat a valódi Functions runtime-on a saját gépén, ugyanúgy, mint bármely más alkalmazás. A függvényalkalmazás-projektet ezen környezetek bármelyikéről is közzéteheti az Azure-ban.  

## <a name="next-steps"></a>További lépések

+ Ha többet szeretne tudni a lefordított C# függvények helyi fejlesztéséről a Visual Studio 2019 használatával, olvassa el [az Azure-függvények fejlesztése a Visual Studio használatával .Gobandus a Visual Studio használatával.](functions-develop-vs.md)
+ Ha többet szeretne tudni a VS Code használatával Mac, Linux vagy Windows rendszerű számítógépeken futó függvények helyi fejlesztéséről, olvassa [el az Azure-függvények telepítése a VS-kódból](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Ha többet szeretne tudni a függvények fejlesztéséről a parancssorból vagy a terminálból, olvassa el az Azure Functions Core Tools használata című [témakört.](functions-run-local.md)

<!-- LINKS -->

[Az Azure Functions alapvető eszközei]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portál]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
