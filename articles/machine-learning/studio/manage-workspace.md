---
title: Munkaterületek kezelése
titleSuffix: ML Studio (classic) - Azure
description: Hozzáférés kezelése az Azure Machine Learning Studio (klasszikus) munkaterületekhez, valamint a Machine Learning API webes szolgáltatásainak üzembe helyezése és kezelése
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217950"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület kezelése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> A Webszolgáltatások Machine Learning Web Services portálon történő kezeléséről az [Azure Machine Learning Web Services portálon webszolgáltatások kezelése című témakörben](manage-new-webservice.md)talál további információt.
> 
> 

A Machine Learning Studio (klasszikus) munkaterületek et az Azure Portalon kezelheti.



## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Studio (klasszikus) munkaterület kezelése az Azure Portalon:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy Azure-előfizetés-rendszergazdai fiók használatával.
2. A lap tetején lévő keresőmezőbe írja be a "Machine learning Studio (klasszikus) munkaterületek" kifejezést, majd válassza a **Machine Learning Studio (klasszikus) munkaterületek (Machine Learning Studio ) (klasszikus) munkaterületeket.**
3. Kattintson a kezelni kívánt munkaterületre.

A rendelkezésre álló szabványos erőforrás-kezelési információk és lehetőségek mellett a következőkre is lehetősége van:

- **Tulajdonságok megtekintése** – Ez a lap megjeleníti a munkaterület és az erőforrás adatait, és módosíthatja azt az előfizetési és erőforráscsoportot, amelyhez ez a munkaterület kapcsolódik.
- **Tárolókulcsok újraszinkronizálása** – A munkaterület a tárfiók kulcsait tartja karban. Ha a tárfiók kulcsait módosítja, a **kulcsok újraszinkronizálása gombra** kattintva szinkronizálhatja a kulcsokat a munkaterülettel.

A Studio (klasszikus) munkaterülethez társított webszolgáltatások kezeléséhez használja a Machine Learning Web Services portált. A teljes körű információkért tekintse [meg a Webszolgáltatás kezelése az Azure Machine Learning Web Services portálhasználatával](manage-new-webservice.md) című témakört.

> [!NOTE]
> Új webszolgáltatások üzembe helyezéséhez vagy kezeléséhez hozzá kell rendelnie egy közreműködői vagy rendszergazdai szerepkört azon az előfizetésen, amelyre a webszolgáltatás telepítve van. Ha meghívja egy másik felhasználót egy gépi tanulási Studio (klasszikus) munkaterületre, hozzá kell rendelnie őket egy közreműködői vagy rendszergazdai szerepkörhöz az előfizetésen, mielőtt üzembe helyezhetik vagy kezelhetik a webszolgáltatásokat. 
> 
>A hozzáférési engedélyek beállításáról a [Hozzáférés kezelése az RBAC és az Azure Portal használatával című témakörben](../../role-based-access-control/role-assignments-portal.md)talál további információt.

## <a name="next-steps"></a>További lépések
* További információ a [Machine Learning azure Resource Manager-sablonokkal történő központi telepítéséről.](deploy-with-resource-manager-template.md) 
