---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616827"
---
> [!TIP]
> A bejelentkezést követően megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési információ `isDefault: true` az az aktuálisan aktivált Azure CLI-parancsokra vonatkozó előfizetése. Ennek az előfizetésnek azonosnak kell lennie, amely tartalmazza a Azure Machine Learning munkaterületet. Az előfizetés AZONOSÍTÓját a [Azure Portal](https://portal.azure.com) a munkaterület áttekintés lapjára kattintva érheti el. Az SDK-t is használhatja az előfizetés AZONOSÍTÓjának lekéréséhez a munkaterület-objektumból. Például: `Workspace.from_config().subscription_id`.
> 
> Egy másik előfizetés kiválasztásához használja `az account set -s <subscription name or ID>` a parancsot, és adja meg az előfizetés nevét vagy azonosítóját a váltáshoz. További információ az előfizetés kiválasztásáról: [több Azure-előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).