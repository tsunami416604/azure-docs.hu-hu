---
title: 'Rövid útmutató: Tanulási ciklus létrehozása és használata az SDK-val – Personalizer'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet tudásbázist az ügyfél SDK használatával.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524869"
---
# <a name="quickstart-personalizer-client-library"></a>Rövid útmutató: Személyre szabó ügyféltár

Személyre szabott tartalmat jeleníthet meg ebben a rövid útmutatóban a Personalizer szolgáltatással.

Ismerkedés a Personalizer ügyfélkönyvtárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

 * Rang API – A tartalomelemek közül a legjobb elemet választja ki a tartalommal és a környezetről megadott valós idejű információk alapján.
 * Jutalom API – A jutalompontszámot az üzleti igényei alapján határozza meg, majd ezzel az API-val elküldi a Personalizer-nek. Ez a pontszám lehet egyetlen érték, például 1 a jó, és 0 a rossz, vagy egy algoritmust hoz létre az üzleti igények alapján.

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

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[A Personalizer működése](how-personalizer-works.md)

* [Mi a Personalizer?](what-is-personalizer.md)
* [Hol használhatja a Personalizert?](where-can-you-use-personalizer.md)
* [hibaelhárítással](troubleshooting.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)
