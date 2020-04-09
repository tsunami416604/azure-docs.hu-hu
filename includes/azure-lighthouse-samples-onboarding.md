---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986654"
---
Különböző sablonokat biztosítunk az adott előkészítési forgatókönyvekhez. Válassza ki a legmegfelelőbb lehetőséget, majd módosítsa úgy a paraméterfájlt, hogy az megfeleljen az Ön által használt környezetnek. További információ a fájlok használatáról az üzemelő példányban: [Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez](../articles/lighthouse/how-to/onboard-customer.md).

| **Sablon** | **Leírás** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure által delegált erőforrás-kezeléshez. Az üzembe helyezést külön-külön kell elvégezni mindegyik előfizetés esetében. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Előkészíti egy ügyfél egy vagy több erőforráscsoportját az Azure által delegált erőforrás-kezeléshez. Amennyiben az adott előfizetésben egyetlen erőforráscsoportot kíván előkészíteni, használja a **rgDelegatedResourceManagement** parancsot, ha pedig többet, akkor használja a **multipleRgDelegatedResourceManagement** parancsot. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Ha [közzétett egy felügyelt szolgáltatási ajánlatot az Azure Marketplace-en](../articles/lighthouse/how-to/publish-managed-services-offers.md), opcionálisan használhatja ezt a sablont az ajánlatot elfogadó ügyfelek erőforrásainak előkészítésére. A paraméterfájl Marketplace-en megadott értékeinek meg kell egyezniük az ajánlat közzétételekor megadott értékekkel. |

Az üzembe helyezést általában külön-külön kell elvégezni mindegyik előkészített előfizetés esetében, azonban egy adott sablont több előfizetés üzembe helyezéséhez is használhat.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Azure Resource Manager-sablonok telepítése több előfizetéshez. |
