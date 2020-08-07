---
title: 'Gyors útmutató: leképezés a REST API-kkal – LUIS'
description: Ebben a REST API rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg, hogy a felhasználó milyen szándéka van a társalgási szövegben.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 2fd52011ed0d139e98740c8de077987edfae2c32
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873160"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Gyors útmutató: leképezés a REST API-kkal

Ebben a rövid útmutatóban egy LUIS-alkalmazást fog használni, amellyel meghatározhatja, hogy a felhasználó milyen szándéka legyen a társalgási szövegben. Küldje el a felhasználó szándékát szövegként a pizza-alkalmazás HTTP-előrejelzési végpontjának. A-végponton LUIS a pizza-alkalmazás modelljét alkalmazza, hogy elemezze a szöveg természetes nyelvét, és meghatározza az alkalmazás tárgyi tartományára vonatkozó általános szándékot és kinyeri az adatokat.

Ehhez a cikkhez egy ingyenes [LUIS](https://www.luis.ai)-fiókra van szüksége.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
