---
title: Azure Lighthouse-minták és -sablonok
description: Ezek a minták és Azure Resource Manager-sablonok megmutatják, hogyan történik az ügyfelek előkészítése az Azure által delegált erőforrás-kezeléshez, valamint az Azure Lighthouse-forgatókönyvek támogatása.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286130"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse-minták

A következő táblázat Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure Lighthouse-hoz. Többek között ezek a fájlok is megtalálhatók az [Azure Lighthouse mintaadattárában](https://github.com/Azure/Azure-Lighthouse-samples/).

| **Sablon** | **Leírás** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | Egyetlen Azure Resource Manager-sablonnal több erőforráscsoportot hoz létre. |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Tárfiókokat telepít két különböző erőforráscsoportba. |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Azure Resource Manager-sablonok telepítése több előfizetéshez. |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure által delegált erőforrás-kezeléshez. |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure-felügyeleti szolgáltatásokat hoz létre, összekapcsolja őket, és további megoldásokat telepít. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Engedélyezi és beállítja az Azure Security Centert a célzott Azure-előfizetésen belül. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure által delegált erőforrás-kezeléshez, a Marketplace-en közzétett felügyelt szolgáltatási ajánlat alapján. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Előkészíti egy ügyfél egy vagy több erőforráscsoportját az Azure által delegált erőforrás-kezeléshez. |

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md) részleteit.
- Megismerheti, hogyan történik [az ügyfelek előkészítése az Azure által delegált erőforrás-kezeléshez](../how-to/onboard-customer.md) az Azure Resource Manager-sablonok használatával.
