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
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191745"
---
1. Győződjön meg arról, hogy az Azure-fiókjával bejelentkezett, és az előfizetés, amelyeket szeretne üzembe helyezni az előzetes verzió használata. Az alábbi példát követve regisztrálása:

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Az előfizetés még egyszer a regisztrálja újra a *Microsoft.Network* szolgáltatói névteret.

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. A következő paranccsal ellenőrizheti, hogy a *AllowBastionHost* funkció regisztrálva van az előfizetéséhez:

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Regisztráció befejezése néhány percet igénybe vehet.