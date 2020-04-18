---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616827"
---
> [!TIP]
> A bejelentkezés után megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési `isDefault: true` adatok az Azure CLI-parancsok jelenleg aktivált előfizetése. Ennek az előfizetésnek meg kell egyeznie az Azure Machine Learning-munkaterületet tartalmazó előfizetéssel. Az előfizetés-azonosítót az [Azure Portalon](https://portal.azure.com) találhatja meg a munkaterület áttekintő lapján. Az SDK-t is használhatja az előfizetés-azonosító lekért a munkaterületi objektumból. Például: `Workspace.from_config().subscription_id`.
> 
> Másik előfizetés kiválasztásához `az account set -s <subscription name or ID>` használja a parancsot, és adja meg azt az előfizetés nevet vagy azonosítót, amelyre váltani szeretne. Az előfizetések kiválasztásáról további információt a [Több Azure-előfizetés használata című témakörben talál.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)