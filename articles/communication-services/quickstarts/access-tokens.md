---
title: Rövid útmutató – hozzáférési tokenek létrehozása és kezelése
titleSuffix: An Azure Communication Services quickstart
description: Ismerje meg, hogyan kezelheti az identitásokat és a hozzáférési jogkivonatokat az Azure Communication Services Felügyeleti ügyféloldali kódtár használatával.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947215"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Rövid útmutató: hozzáférési tokenek létrehozása és kezelése

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások használatának első lépései a kommunikációs szolgáltatások felügyeleti ügyféloldali függvénytárának használatával a hozzáférési tokenek kiépítéséhez és kezeléséhez. A hozzáférési tokenek lehetővé teszik a csevegést és az ügyféloldali kódtárak közvetlen hitelesítését az Azure kommunikációs szolgáltatásokkal szemben. Ezek a tokenek a megvalósított kiszolgálóoldali jogkivonat-létesítési szolgáltatás alapján jönnek létre. Ezután a kommunikációs szolgáltatások ügyféloldali kódtárainak inicializálására szolgálnak az ügyféleszközök esetében.

Vegye figyelembe, hogy a jelen oktatóanyagban szereplő képekben szereplő díjak csak példaként szolgálnak.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Az alkalmazás kimenete az összes befejezett műveletet leírja:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Identitások kezelése
> * Hozzáférési tokenek kiadása
> * A kommunikációs szolgáltatások felügyeleti ügyféloldali kódtár használata


> [!div class="nextstepaction"]
> [Hanghívás hozzáadása az alkalmazáshoz](./voice-video-calling/getting-started-with-calling.md)

A következőket is érdemes elvégezheti:

 - [A hitelesítés ismertetése](../concepts/authentication.md)
 - [Csevegés hozzáadása az alkalmazáshoz](./chat/get-started.md)
 - [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
