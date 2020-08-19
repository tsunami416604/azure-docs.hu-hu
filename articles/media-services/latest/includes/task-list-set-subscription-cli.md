---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556291"
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
