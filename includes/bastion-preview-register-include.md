---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305288"
---
1. Győződjön meg arról, hogy az Azure-fiókjával bejelentkezett, és az előfizetés, amelyeket szeretne üzembe helyezni az előzetes verzió használata. Az alábbi példát követve regisztrálása:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Az előfizetés még egyszer a regisztrálja újra a *Microsoft.Network* szolgáltatói névteret.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. A következő paranccsal ellenőrizheti, hogy a *AllowBastionHost* funkció regisztrálva van az előfizetéséhez:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Regisztráció befejezése néhány percet igénybe vehet.
