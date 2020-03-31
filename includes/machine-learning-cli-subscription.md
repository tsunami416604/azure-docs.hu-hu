---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296888"
---
A bejelentkezés után megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési `isDefault: true` adatok az Azure CLI-parancsok jelenleg aktivált előfizetése. Ennek az előfizetésnek meg kell egyeznie az Azure Machine Learning-munkaterületet tartalmazó előfizetéssel. Az előfizetés-azonosítót az [Azure Portalon](https://portal.azure.com) találhatja meg a munkaterület áttekintő lapján. Az SDK-t is használhatja az előfizetés-azonosító lekért a munkaterületi objektumból. Például: `Workspace.from_config().subscription_id`.
    
Másik előfizetés kiválasztásához használja az [az-fiókkészlet parancsot](https://docs.microsoft.com/cli/azure/account#az-account-set) az előfizetés-azonosítóval, amelyre váltani szeretne. Az előfizetések kiválasztásáról további információt a [Több Azure-előfizetés használata című témakörben talál.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)