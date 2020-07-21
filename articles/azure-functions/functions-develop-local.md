---
title: Az Azure functions fejlesztése és futtatása helyileg
description: Megtudhatja, hogyan teheti meg az Azure functions szolgáltatást a helyi számítógépen, mielőtt futtatja őket a Azure Functionson.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 2362fcd27f1b5f7c0b3411eae5b3b94c7dc6cf4c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540179"
---
# <a name="code-and-test-azure-functions-locally"></a>Az Azure Functions helyi kódolása és tesztelése

A [Azure Portal]Azure functions fejlesztése és tesztelése során számos fejlesztő inkább a helyi fejlesztési élményt részesíti előnyben. A functions lehetővé teszi a kedvenc Kódszerkesztő és fejlesztői eszközeinek használatát a függvények létrehozásához és teszteléséhez a helyi számítógépen. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a teljes functions futtatókörnyezettel a helyi számítógépen is hibakeresést végezhetnek.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetek

A funkciók helyi számítógépen való fejlesztésének módja a [nyelvi](supported-languages.md) és az eszközkészletbeli beállításoktól függ. A következő táblázat környezetei támogatják a helyi fejlesztést:

|Környezet                              |Nyelvek         |Leírás|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (osztály könyvtára)](functions-dotnet-class-library.md), [c# parancsfájl (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell), [Python](functions-reference-python.md) | A [vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) -hoz készült Azure functions-bővítmény támogatja a vs Code funkciót. Az alapvető eszközöket igényli. Támogatja a Linux, a MacOS és a Windows fejlesztését, ha a Core Tools 2. x verzióját használja. További információért lásd: [az első függvény létrehozása a Visual Studio Code használatával](functions-create-first-function-vs-code.md). |
| [Parancssor vagy terminál](functions-run-local.md) | [C# (osztály könyvtára)](functions-dotnet-class-library.md), [c# parancsfájl (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | A [Azure functions Core Tools] az alapszintű futtatókörnyezetet és sablonokat biztosít a függvények létrehozásához, amelyek lehetővé teszik a helyi fejlesztést. A 2. x verzió támogatja a Linux, a MacOS és a Windows fejlesztését. Minden környezet a helyi függvények futtatókörnyezetének alapvető eszközein alapul. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (osztálytár)](functions-dotnet-class-library.md) | A Azure Functions eszközöket a [Visual Studio 2019](https://www.visualstudio.com/vs/) -es és újabb verzióiban elérhető **Azure-fejlesztési** számítási feladatok tartalmazzák. Lehetővé teszi a függvények fordítását egy osztály-függvénytárban, és a. dll közzétételét az Azure-ban. A a helyi teszteléshez szükséges alapvető eszközöket tartalmazza. További információ: [Azure functions fejlesztése a Visual Studióval](functions-develop-vs.md). |
| [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser) (különböző) | [Java](functions-reference-java.md) | A Java-függvények fejlesztését lehetővé tevő alapvető eszközökkel integrálható. A 2. x verzió támogatja a Linux, a MacOS és a Windows fejlesztését. További információ: [az első függvény létrehozása a Java és a Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser)használatával. Az [Eclipse](functions-create-maven-eclipse.md) és a [IntelliJ IDEA](functions-create-maven-intellij.md) használatával is támogatja a fejlesztést |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A helyi fejlesztési környezetek mindegyike lehetővé teszi a Function app-projektek létrehozását és az előre definiált függvények sablonjait új függvények létrehozásához. Mindegyik a legfontosabb eszközöket használja, így a saját gépén lévő valós functions-futtatókörnyezettel ugyanúgy tesztelheti és hibakeresést végezhet, mint bármely más alkalmazás esetében. A Function app-projektet bármelyik környezetből az Azure-ba is közzéteheti.

## <a name="next-steps"></a>Következő lépések

+ További információ a lefordított C# függvények helyi fejlesztéséről a Visual Studio 2019 használatával: [Azure functions fejlesztése a Visual Studióval](functions-develop-vs.md).
+ Ha többet szeretne megtudni a függvények helyi fejlesztéséről Mac, Linux vagy Windows rendszerű számítógépeken a VS Code használatával, olvassa el a [Azure functions telepítése a vs Code-ból](/azure/developer/javascript/tutorial-vscode-serverless-node-01)című témakört.
+ A függvények parancssorból vagy terminálból történő fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure functions Core Tools használata](functions-run-local.md)című témakört.

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
