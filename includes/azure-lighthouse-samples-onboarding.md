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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456727"
---
Különböző sablonokat biztosítunk az adott előkészítési forgatókönyvekhez. Válassza ki a legmegfelelőbb lehetőséget, majd módosítsa úgy a paraméterfájlt, hogy az megfeleljen az Ön által használt környezetnek. További információ a fájlok használatáról az üzemelő példányban: [Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez](../articles/lighthouse/how-to/onboard-customer.md).

| **Sablon** | **Leírás** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure által delegált erőforrás-kezeléshez. Az üzembe helyezést külön-külön kell elvégezni mindegyik előfizetés esetében. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Előkészíti egy ügyfél egy vagy több erőforráscsoportját az Azure által delegált erőforrás-kezeléshez. Amennyiben az adott előfizetésben egyetlen erőforráscsoportot kíván előkészíteni, használja a **rgDelegatedResourceManagement** parancsot, ha pedig többet, akkor használja a **multipleRgDelegatedResourceManagement** parancsot. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Ha [közzétett egy felügyelt szolgáltatási ajánlatot az Azure Marketplace-en](../articles/lighthouse/how-to/publish-managed-services-offers.md), opcionálisan használhatja ezt a sablont az ajánlatot elfogadó ügyfelek erőforrásainak előkészítésére. A paraméterfájl Marketplace-en megadott értékeinek meg kell egyezniük az ajánlat közzétételekor megadott értékekkel. |

Az üzembe helyezést általában külön-külön kell elvégezni mindegyik előkészített előfizetés esetében, azonban egy adott sablont több előfizetés üzembe helyezéséhez is használhat.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Azure Resource Manager-sablonok telepítése több előfizetéshez. |
