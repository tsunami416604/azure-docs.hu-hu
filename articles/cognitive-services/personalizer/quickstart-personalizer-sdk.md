---
title: 'Rövid útmutató: learning loop létrehozása és használata SDK-val – személyre szabás'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet tudásbázist a személyre szabott ügyféloldali kódtár használatával.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: személyre szabott, Azure személyre szabott, gépi tanulás
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 3acfd8134a2a65e6ea9f019baff24b8b8149f7f3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777320"
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
> A [megszemélyesítő működése](how-personalizer-works.md) 
>  [Hol használható a személyre szabás?](where-can-you-use-personalizer.md)
