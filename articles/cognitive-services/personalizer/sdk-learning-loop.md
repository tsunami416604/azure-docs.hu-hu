---
title: 'Rövid útmutató: learning loop létrehozása és használata SDK-val – személyre szabás'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet tudásbázist az ügyfél-SDK használatával.
ms.topic: quickstart
ms.date: 04/27/2020
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 6a683a88a335c04eb1f1eb0b5bc74e80e2125c76
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407768"
---
# <a name="quickstart-personalizer-client-library"></a>Gyors útmutató: személyre szabott ügyféloldali kódtár

Személyre szabott tartalom megjelenítése ebben a rövid útmutatóban a személyre szabási szolgáltatással.

Ismerkedés a személyre szabott ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

 * Range API – kiválasztja a legjobb elemet a műveletek közül, a tartalommal és környezettel kapcsolatban megadott valós idejű információk alapján.
 * Jutalom API – az üzleti igények alapján határozza meg a jutalom pontszámát, majd küldje el a személyre az API-val. Ez a pontszám lehet egyetlen érték, például 1, jó, 0 vagy rossz, vagy egy, az üzleti igények alapján létrehozott algoritmus.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[A Personalizer működése](how-personalizer-works.md)

* [Mi a Personalizer?](what-is-personalizer.md)
* [Hol használhatja a Personalizert?](where-can-you-use-personalizer.md)
* [Hibaelhárítás](troubleshooting.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)található.
