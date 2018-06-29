---
title: Fejlesztés és helyileg történő futtatása az Azure functions |} Microsoft Docs
description: Megtudhatja, hogyan kód és a helyi számítógépen az Azure functions tesztelése az Azure Functions futtatása előtt.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/27/2018
ms.author: glenga
ms.openlocfilehash: 89f94be4cf624914183480362ae23c62c363622f
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088621"
---
# <a name="code-and-test-azure-functions-locally"></a>Kód és az Azure Functions helyi tesztelése

Képes fejlesztéséhez és teszteléséhez az Azure Functions közben a [Azure Portal], sok fejlesztők inkább egy helyi fejlesztési felület. Funkciók megkönnyíti a kedvenc kód szerkesztő és a fejlesztői eszközök segítségével létrehozása és tesztelése a funkciók a helyi számítógépen. A helyi funkciók csatlakozni tud-e az élő Azure-szolgáltatások, és a helyi számítógépen, a teljes Functions futtatókörnyezete használatával megoldhassuk őket.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetekben

Ahol fejleszt funkciók a helyi számítógépen módja attól függ, a [nyelvi](supported-languages.md) és tooling eszköz beállításait. Az alábbi táblázatban a környezetekben helyi fejlesztési támogatja:

|Környezet                              |Nyelvek         |Leírás|
|-----------------------------------------|------------|---|
| [Parancssor vagy a Terminálszolgáltatások](functions-run-local.md) | [C# parancsfájl (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Az Azure Functions Core eszközök] core runtime és sablonok létrehozása funkciók, amely lehetővé teszi a helyi fejlesztési biztosít. Verzió 2.x támogatási fejlesztői a Windows, Linux és MacOS. A helyi funkciók futási időben Core eszközök támaszkodjon minden környezetben.|
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [C# parancsfájl (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | A [Azure Functions bővítményt a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) funkciókat támogatja a Visual STUDIO Code hozzáadja. A Core eszközök igényel. Fejlesztési támogatják a Linux, a MacOS és a Windows, verzióját használja az alapvető eszközök 2.x. További tudnivalókért lásd: [központi telepítése az Azure Functions használatával](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (osztálytár)](functions-dotnet-class-library.md) | Az Azure Functions eszközök szerepelnek a **Azure fejlesztési** munkaterhelését [Visual Studio 2017 verzió 15.5](https://www.visualstudio.com/vs/) vagy újabb verzió használható. Lehetővé teszi a fordítási funkciók osztály könyvtárba, és a .dll közzététele az Azure-bA. A helyi tesztelési Core-eszközöket tartalmazza. További tudnivalókért lásd: [fejlesztése az Azure Functions Visual Studio használatával](functions-develop-vs.md). |
| [Maven 3](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Integrálható a fő eszközöket Java funkciók fejlesztése. Verzió 2.x támogatja fejlesztői a Windows, Linux és MacOS. További tudnivalókért lásd: [hozzon létre az első függvényét Java és Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Minden helyi fejlesztési környezetben lehetővé teszi a funkció app projektek létrehozása és előre meghatározott funkciók a sablonok segítségével hozzon létre új funkciók. A Core eszközök mindegyike használ, így tesztelése és hibakeresése a funkciók a valódi Functions futtatókörnyezete szemben a saját számítógépén ugyanúgy, mint bármely más alkalmazás. Is közzéteheti az Ön függvény alkalmazásprojektet bármelyik ezekben a környezetekben az Azure-bA.  

## <a name="next-steps"></a>További lépések

+ A Visual Studio 2017 használatával lefordított C# funkciók helyi fejlesztési kapcsolatos további információkért lásd: [fejlesztése az Azure Functions Visual Studio használatával](functions-develop-vs.md).
+ A Mac, Linux vagy a Windows számítógépen használja a Visual STUDIO Code funkciók helyi fejlesztési kapcsolatos további tudnivalókért tekintse meg a [Visual STUDIO Code dokumentáció az Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ A parancssor vagy a Terminálszolgáltatások funkciók fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Functions Core eszközökről](functions-run-local.md).

<!-- LINKS -->

[Az Azure Functions Core eszközök]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
