---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028414"
---
> [!TIP]
> A bejelentkezést követően megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési információ az az `isDefault: true` aktuálisan aktivált Azure CLI-parancsokra vonatkozó előfizetése. Ennek az előfizetésnek azonosnak kell lennie, amely tartalmazza a Azure Machine Learning munkaterületet. Az előfizetés AZONOSÍTÓját a [Azure Portal](https://portal.azure.com) a munkaterület áttekintés lapjára kattintva érheti el. Az SDK-t is használhatja az előfizetés AZONOSÍTÓjának lekéréséhez a munkaterület-objektumból. Például: `Workspace.from_config().subscription_id`.
> 
> Egy másik előfizetés kiválasztásához használja a `az account set -s <subscription name or ID>` parancsot, és adja meg az előfizetés nevét vagy azonosítóját a váltáshoz. További információ az előfizetés kiválasztásáról: [több Azure-előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).