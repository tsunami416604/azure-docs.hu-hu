---
title: A webszolgáltatás üzembe helyezése több régióba |} Microsoft Docs
description: (Másolás) egy új webszolgáltatás-bővítmény más régiókban telepítésének lépéseit.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 78b37f0e7ac554c1823a0607e43718e5a0ac0067
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835134"
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Webszolgáltatás üzembe helyezése több régióban
Az új Azure Web Services engedélyezi, hogy könnyen telepíthető egy webszolgáltatás-bővítmény több régióba több előfizetések vagy munkaterületek anélkül. 

Tarifacsomag az régió adott, ezért meg kell adnia egy számlázási csomagot minden egyes régió, amelyben a webszolgáltatást telepíti.

## <a name="to-create-a-plan-in-another-region"></a>Egy másik régióban terv létrehozásához
1. Jelentkezzen be a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/).
2. Kattintson a **tervek** menüjét.
3. A csomagok keresztül nézet lap, kattintson a **új**.
4. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amelyben létre kívánja hozni az új tervet.
5. Az a **régió** legördülő menüben válasszon ki egy régiót az új csomag. A terv beállításait a kiválasztott régióban jelenítse meg a **beállítások megtervezése** lap részében.
6. Az a **erőforráscsoport** legördülő menüben válassza egy erőforráscsoport, a terv. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
7. A **neve** írja be a csomag nevét.
8. A **terv beállítások**, kattintson az új csomag számlázási szintjét.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="deploying-the-web-service-to-another-region"></a>A webszolgáltatás egy másik régióban üzembe helyezni
1. Kattintson a **webszolgáltatások** menüjét.
2. Válassza ki a webszolgáltatás új régióban üzembe helyezni.
3. Kattintson a **másolási**.
4. A **webszolgáltatás neve**, adjon meg egy új nevet a webszolgáltatáshoz.
5. A **szolgáltatásleírás webes**, írja be a webszolgáltatás leírását.
6. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amely az új webszolgáltatás legyen elhelyezve.
7. Az a **erőforráscsoport** legördülő menüben válassza a erőforráscsoport a webszolgáltatáshoz. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
8. Az a **régió** legördülő menüben válassza ki azt a régiót, amelyben a webszolgáltatás telepítése.
9. Az a **tárfiók** legördülő menüben válassza ki a megfelelő tárolási fiók, amely a webszolgáltatás tárolja.
10. Az a **ár terv** legördülő menüben válasszon ki egy tervet a 8. lépésben kiválasztott régióban.
11. Kattintson a **másolási**.

