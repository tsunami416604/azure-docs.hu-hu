---
title: Studio-webszolgáltatás üzembe helyezése több régióban – az Azure Machine Learning Studióban |} A Microsoft Docs
description: (Másolás) egy új webszolgáltatás más régiókban telepítésének lépéseit. Könnyedén webszolgáltatás üzembe helyezése több régióban több előfizetéseket vagy munkaterületeket nélkül.
services: machine-learning
documentationcenter: ''
author: ericlicoding
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.custom: seodec18
ms.author: amlstudiodocs
ms.openlocfilehash: 742f462ebc3bd191a045be2a0213b1d8bc52adc5
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252677"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service-to-multiple-regions"></a>Az Azure Machine Learning Studio webszolgáltatás üzembe helyezése több régióban

Az Azure új webszolgáltatások egyszerű üzembe helyezése az Azure Machine Learning Studio webszolgáltatás több régióban több előfizetéseket vagy munkaterületeket nélkül teszi lehetővé. 

Díjszabás szolgáltatásé, ezért meg kell adnia egy számlázási csomagot minden olyan régióhoz, amelyben a webszolgáltatást telepíti.

## <a name="to-create-a-plan-in-another-region"></a>Terv létrehozásához egy másik régióban
1. Jelentkezzen be a [Microsoft Azure Machine Learning-webszolgáltatások](https://services.azureml.net/).
2. Kattintson a **csomagok** menüpont.
3. A tervek keresztül nézet lap, kattintson **új**.
4. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amelyben létre kívánja hozni az új csomag.
5. Az a **régió** legördülő menüben válassza ki a régiót az új csomag. A kiválasztott régióban megtervezése beállítások jelennek meg a **beállítások megtervezése** lap részében.
6. Az a **erőforráscsoport** legördülő menüben válassza egy erőforráscsoport, a csomag számára. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
7. A **csomagnév** írja be a csomag nevét.
8. A **csomagokkal**, kattintson az új csomag a számlázási szintet.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="deploying-the-web-service-to-another-region"></a>A web Service szolgáltatásának telepítése egy másik régióba
1. Kattintson a **webszolgáltatások** menüpont.
2. Válassza ki a webszolgáltatás telepítésekor egy új régióban.
3. Kattintson a **másolási**.
4. A **webszolgáltatás neve**, írja be a web service új nevét.
5. A **webalkalmazás-szolgáltatás leírása**, írja be a webszolgáltatás leírását.
6. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amelyben létre kívánja hozni az új webszolgáltatásként.
7. Az a **erőforráscsoport** legördülő menüben válassza egy erőforráscsoport, a webszolgáltatás. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
8. Az a **régió** legördülő menüben válassza ki a régiót, amelyben a webszolgáltatás üzembe helyezéséhez.
9. Az a **tárfiók** legördülő menüben válassza ki a storage-fiók, amely a web service tárolja.
10. Az a **árképzési csomag** legördülő menüben válassza ki azt a csomagot a 8. lépésben kiválasztott régióban.
11. Kattintson a **másolási**.

