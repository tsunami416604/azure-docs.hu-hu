---
title: Rövid útmutató – az ügyféloldali függvénytárat hívó Azure kommunikációs szolgáltatások használata
titleSuffix: An Azure Communication Services quickstart
description: Ismerje meg a kommunikációs szolgáltatásokat az ügyféloldali kódtár képességeinek meghívásával.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 1e5b0e88c4a84e27d662b2f2c4b39b14538af92a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126798"
---
# <a name="quickstart-use-the-communication-services-calling-client-library"></a>Gyors útmutató: az ügyféloldali függvénytárat hívó kommunikációs szolgáltatások használata

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások meghívásával az ügyféloldali kódtár használatával hang-és videohívások hozzáadásával az alkalmazáshoz.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/calling-sdk-js.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/calling-sdk-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/calling-sdk-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- Tekintse meg a [Calling Hero-mintát](../../samples/calling-hero-sample.md)
- További információ a [hívás működéséről](../../concepts/voice-video-calling/about-call-types.md)