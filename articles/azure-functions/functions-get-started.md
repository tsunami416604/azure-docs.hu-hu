---
title: Bevezetés az Azure Functions használatába
description: Hajtsa végre az első lépéseket a Azure Functions használatának elvégzéséhez.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975024"
---
# <a name="getting-started-with-azure-functions"></a>Bevezetés az Azure Functions használatába

## <a name="introduction"></a>Introduction (Bevezetés)

[Azure functions](./functions-overview.md) lehetővé teszi, hogy a rendszerek logikáját könnyen elérhető kódrészletbe implementálja. Ezeket a kódrészleteket "functions"-nek nevezzük.

Az első lépésekhez használja az alábbi forrásokat.

::: zone pivot="programming-language-csharp"

| Műveletek | További források |
| --- | --- |
| **Az első függvény létrehozása** | A következő eszközök egyikének használata:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Parancssor](./create-first-function-cli-csharp.md) |
| **Futó függvény megjelenítése** | <li>[Azure-minták böngésző](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Azure közösségi könyvtár](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Ismerkedés egy interaktív oktatóanyaggal**| <li>[Az üzleti felhasználási helyzethez legmegfelelőbb kiszolgáló nélküli Azure-technológia kiválasztása](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Jól összeállítható keretrendszer – teljesítmény-hatékonyság](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure-függvény végrehajtása triggerekkel](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Az [interaktív oktatóanyagok teljes listáját](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)Microsoft Learn tekintheti meg.|
| **Részletes információ** | <li>Megtudhatja, hogyan [növelheti vagy csökkentheti](./functions-scale.md) a függvények az igény szerinti egyezést<li>Ismerje meg az elérhető különböző [üzembe helyezési módszereket](./functions-deployment-technologies.md)<li>A függvények elemzését a beépített [monitorozási eszközök](./functions-monitoring.md) használatával teheti meg<li>A [C# nyelvi referenciájának](./functions-dotnet-class-library.md) beolvasása|

::: zone-end

::: zone pivot="programming-language-java"
| Műveletek | További források |
| --- | --- |
| **Az első függvény létrehozása** | A következő eszközök egyikének használata:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Java/Maven függvény a Terminal/parancssor használatával](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ ötlet](./functions-create-maven-intellij.md) |
| **Futó függvény megjelenítése** | <li>[Azure-minták böngésző](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Azure közösségi könyvtár](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Ismerkedés egy interaktív oktatóanyaggal**| <li>[Az üzleti felhasználási helyzethez legmegfelelőbb kiszolgáló nélküli Azure-technológia kiválasztása](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Jól összeállítható keretrendszer – teljesítmény-hatékonyság](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Alkalmazás fejlesztése az Azure Functions Maven-bővítményével](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Az [interaktív oktatóanyagok teljes listáját](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)Microsoft Learn tekintheti meg.|
| **Részletes információ** | <li>Megtudhatja, hogyan [növelheti vagy csökkentheti](./functions-scale.md) a függvények az igény szerinti egyezést<li>Ismerje meg az elérhető különböző [üzembe helyezési módszereket](./functions-deployment-technologies.md)<li>A függvények elemzését a beépített [monitorozási eszközök](./functions-monitoring.md) használatával teheti meg<li>A [Java nyelvi referenciájának](./functions-reference-java.md) beolvasása|
::: zone-end

::: zone pivot="programming-language-javascript"
| Műveletek | További források |
| --- | --- |
| **Az első függvény létrehozása** | A következő eszközök egyikének használata:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js terminál/parancssor](./create-first-function-cli-java.md) |
| **Futó függvény megjelenítése** | <li>[Azure-minták böngésző](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Azure közösségi könyvtár](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Ismerkedés egy interaktív oktatóanyaggal** | <li>[Az üzleti felhasználási helyzethez legmegfelelőbb kiszolgáló nélküli Azure-technológia kiválasztása](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Jól összeállítható keretrendszer – teljesítmény-hatékonyság](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Kiszolgáló nélküli API-k készítése az Azure Functions használatával](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Kiszolgáló nélküli logika létrehozása az Azure Functions használatával](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Node.js és Express API-k újrabontása kiszolgáló nélküli API-kra az Azure Functions szolgáltatással](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Az [interaktív oktatóanyagok teljes listáját](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)Microsoft Learn tekintheti meg.|
| **Részletes információ** | <li>Megtudhatja, hogyan [növelheti vagy csökkentheti](./functions-scale.md) a függvények az igény szerinti egyezést<li>Ismerje meg az elérhető különböző [üzembe helyezési módszereket](./functions-deployment-technologies.md)<li>A függvények elemzését a beépített [monitorozási eszközök](./functions-monitoring.md) használatával teheti meg<li>Olvassa el a [JavaScript](./functions-reference-node.md) vagy a [írógéppel](./functions-reference-node.md#typescript) nyelvi referenciáját|
::: zone-end

::: zone pivot="programming-language-powershell"
| Műveletek | További források |
| --- | --- |
| **Az első függvény létrehozása** | <li>A [Visual Studio Code](./create-first-function-vs-code-powershell.md) használata |
| **Futó függvény megjelenítése** | <li>[Azure-minták böngésző](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Azure közösségi könyvtár](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Ismerkedés egy interaktív oktatóanyaggal** | <li>[Az üzleti felhasználási helyzethez legmegfelelőbb kiszolgáló nélküli Azure-technológia kiválasztása](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Jól összeállítható keretrendszer – teljesítmény-hatékonyság](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Kiszolgáló nélküli API-k készítése az Azure Functions használatával](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Kiszolgáló nélküli logika létrehozása az Azure Functions használatával](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Azure-függvény végrehajtása triggerekkel](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Az [interaktív oktatóanyagok teljes listáját](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)Microsoft Learn tekintheti meg.|
| **Részletes információ** | <li>Megtudhatja, hogyan [növelheti vagy csökkentheti](./functions-scale.md) a függvények az igény szerinti egyezést<li>Ismerje meg az elérhető különböző [üzembe helyezési módszereket](./functions-deployment-technologies.md)<li>A függvények elemzését a beépített [monitorozási eszközök](./functions-monitoring.md) használatával teheti meg<li>A [PowerShell nyelvi referenciájának](./functions-reference-powershell.md)beolvasása|
::: zone-end

::: zone pivot="programming-language-python"
| Műveletek | További források |
| --- | --- |
| **Az első függvény létrehozása** | A következő eszközök egyikének használata:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminál/parancssor](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Futó függvény megjelenítése** | <li>[Azure-minták böngésző](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Azure közösségi könyvtár](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Ismerkedés egy interaktív oktatóanyaggal** | <li>[Az üzleti felhasználási helyzethez legmegfelelőbb kiszolgáló nélküli Azure-technológia kiválasztása](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Jól összeállítható keretrendszer – teljesítmény-hatékonyság](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Kiszolgáló nélküli API-k készítése az Azure Functions használatával](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Kiszolgáló nélküli logika létrehozása az Azure Functions használatával](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Az [interaktív oktatóanyagok teljes listáját](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)Microsoft Learn tekintheti meg.|
| **Részletes információ** | <li>Megtudhatja, hogyan [növelheti vagy csökkentheti](./functions-scale.md) a függvények az igény szerinti egyezést<li>Ismerje meg az elérhető különböző [üzembe helyezési módszereket](./functions-deployment-technologies.md)<li>A függvények elemzését a beépített [monitorozási eszközök](./functions-monitoring.md) használatával teheti meg<li>A [Python nyelvi referenciájának](./functions-reference-python.md) beolvasása|
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Functions-alkalmazások anatómiájának megismerése](./functions-reference.md)
