---
title: 'Gyors útmutató: modell módosítása REST API-kkal – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben a REST API rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg, hogy a felhasználó milyen szándéka van a társalgási szövegben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18, devx-track-python, devx-track-javascript, devx-track-csharp
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/03/2020
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: a62b12ccbc6f7d96dc1a2a9b49ab5eec0175f49d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078639"
---
# <a name="quickstart-change-model-with-rest-apis"></a>Gyors útmutató: modell módosítása REST API-kkal

Ebben a rövid útmutatóban egy pizza-alkalmazáshoz és az alkalmazás betanításához fog példát hosszúságú kimondott szöveg hozzáadni. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-model-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-model-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-model-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-model-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-model-rest-python.md)]
::: zone-end
