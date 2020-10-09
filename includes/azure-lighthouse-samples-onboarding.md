---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111657"
---
Különböző sablonokat biztosítunk az adott előkészítési forgatókönyvekhez. Válassza ki a legmegfelelőbb lehetőséget, majd módosítsa úgy a paraméterfájlt, hogy az megfeleljen az Ön által használt környezetnek. További információ a fájlok üzemelő példányban való használatáról: [Ügyfél előkészítése az Azure Lighthouse-hoz](../articles/lighthouse/how-to/onboard-customer.md).

| **Sablon** | **Leírás** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure Lighthouse-hoz. Az üzembe helyezést külön-külön kell elvégezni mindegyik előfizetés esetében. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Előkészíti egy ügyfél egy vagy több erőforráscsoportját az Azure Lighthouse-hoz. Amennyiben az adott előfizetésben egyetlen erőforráscsoportot kíván előkészíteni, használja a **rgDelegatedResourceManagement** parancsot, ha pedig többet, akkor használja a **multipleRgDelegatedResourceManagement** parancsot. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Ha [közzétett egy felügyelt szolgáltatási ajánlatot az Azure Marketplace-en](../articles/lighthouse/how-to/publish-managed-services-offers.md), opcionálisan használhatja ezt a sablont az ajánlatot elfogadó ügyfelek erőforrásainak előkészítésére. A paraméterfájl Marketplace-en megadott értékeinek meg kell egyezniük az ajánlat közzétételekor megadott értékekkel. |

Az üzembe helyezést általában külön-külön kell elvégezni mindegyik előkészített előfizetés esetében, azonban egy adott sablont több előfizetés üzembe helyezéséhez is használhat.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Azure Resource Manager-sablonok telepítése több előfizetéshez. |
