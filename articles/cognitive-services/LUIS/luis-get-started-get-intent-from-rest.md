---
title: 'Rövid útmutató: Leképezés rest API-kkal - LUIS'
description: Ebben a REST API-gyorsindításban egy elérhető nyilvános LUIS-alkalmazás segítségével határozza meg a felhasználó szándékát a társalgási szövegből.
ms.topic: quickstart
ms.date: 02/03/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 4e2c32b2eaf8cd6935e8e6b45bf79a1f3c756316
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524665"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Rövid útmutató: Leképezés rest API-kkal

Ebben a rövid útmutatóban elérhető nyilvános LUIS-alkalmazással határozza meg egy felhasználó szándékát egy beszélgetés szövegéből. Küldje el szövegként a felhasználó szándékát a nyilvános alkalmazás HTTP-előrejelzési végpontjára. A LUIS a végpontnál a nyilvános alkalmazás modelljét alkalmazza a természetes nyelvű szövegen a jelentés elemzése érdekében, amellyel meghatározza az általános szándékot, valamint kinyeri az alkalmazás témájában releváns adatokat.

Ez a rövid útmutató a végponti REST API-t használja. További információkért tekintse meg a [végponti API-dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

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

