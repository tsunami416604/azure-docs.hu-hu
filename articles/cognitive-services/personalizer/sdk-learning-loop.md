---
title: 'Rövid útmutató: learning loop létrehozása és használata SDK-val – személyre szabás'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet tudásbázist az ügyfél-SDK használatával.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: e09476bc084465cf08087a3200d8b7d663b0685e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122907"
---
# <a name="quickstart-personalizer-client-library"></a>Gyors útmutató: személyre szabott ügyféloldali kódtár

Személyre szabott tartalom megjelenítése ebben a Python rövid útmutatóban a személyre szabási szolgáltatással.

Ismerkedés a személyre szabott ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

 * Range API – kiválasztja a legjobb elemet a tartalmi elemektől a tartalommal és kontextussal kapcsolatban megadott valós idejű információk alapján.
 * Jutalom API – az üzleti igények alapján határozza meg a jutalom pontszámát, majd küldje el a személyre az API-val. Ez a pontszám lehet egyetlen érték, például 1, jó, 0 vagy rossz, vagy egy, az üzleti igények alapján létrehozott algoritmus.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

::: zone pivot="programming-language-nodejs"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[A megszemélyesítő működése](how-personalizer-works.md)

* [Mi az a személyre szabott?](what-is-personalizer.md)
* [Hol használható a személyre szabás?](where-can-you-use-personalizer.md)
* [hibaelhárítással](troubleshooting.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)található.
