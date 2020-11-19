---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885213"
---
<!-- List and set subscriptions -->

1. Kérje le az előfizetéseinek a listáját az [az account list](/cli/azure/account#az-account-list) paranccsal:

    ```
    az account list --output table
    ```

2. Használja az `az account set` parancsot azzal az előfizetés-azonosítóval vagy névvel, amelyre váltani szeretne.

    ```
    az account set --subscription "My Demos"
    ```
