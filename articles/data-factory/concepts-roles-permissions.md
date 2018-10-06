---
title: Szerepkörök és engedélyek az Azure Data Factory |} A Microsoft Docs
description: Ismerteti a szerepkörök és engedélyek szükségesek a Data Factoryk létrehozásához és a gyermek-erőforrásokat.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 10-04/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: cd6e28298b773f466ea317b7217af7709802aa12
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48817517"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Szerepkörök és engedélyek az Azure Data Factoryhoz

Ez a cikk ismerteti az Azure Data Factory-erőforrások létrehozásához és kezeléséhez szükséges szerepköröket és által ezekhez a szerepkörökhöz rendelt engedélyeket.

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetés az Azure Portalon, az engedélyek megtekintéséhez kattintson a jobb felső sarokban a felhasználónevére, és válassza **engedélyek**. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. 

Létrehozásához és kezeléséhez a Data Factory - adatkészletek, beleértve a társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – az alábbi követelmények vonatkoznak:
- Létrehozásához és kezeléséhez az Azure Portalon, meg kell tartoznia a **Data Factory Közreműködője** az erőforráscsoport szintjén vagy újabb.
- Hozhat létre, és a gyermek-erőforrások kezelése a PowerShell vagy az SDK-t, a **közreműködői** az erőforrásszintek simítása vagy meghaladja a szerepkör is elegendő.

Minta egy felhasználói szerepkörhöz adásával kapcsolatos utasításokért lásd: a [szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) cikk.

## <a name="set-up-permissions"></a>Engedélyek beállítása

Miután egy adat-előállítót hoz létre, szüksége lehet, hogy a data Factory más felhasználók dolgozhassanak. Ezt a hozzáférést biztosíthat más felhasználók számára, fel kell vennie azokat a beépített **Data Factory Közreműködője** szerepkört arra az erőforráscsoportra, amely tartalmazza az adat-előállítóban.

### <a name="scope-of-the-data-factory-contributor-role"></a>A Data Factory Közreműködője szerepkör hatóköre

Tagság a **Data Factory Közreműködője** szerepkör lehetővé teszi a felhasználóknak, tegye a következőket:
- Létrehozása, szerkesztése és törlése az adat-előállítók és gyermekszintű erőforrása, beleértve az adatkészletek, a társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok.
- Resource Manager-sablonok üzembe helyezése. Resource Manager üzembe helyezése a az Azure Portal Data Factory által használt központi telepítési módszer.
- A data Factory az App Insights-riasztások kezelése.
- Hozzon létre támogatási jegyeket.

További információ erről a munkakörről: [Data Factory Közreműködője szerepkör](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Resource Manager-sablon üzembe helyezése

A **Data Factory Közreműködője** szerepkör, az erőforráscsoport szintjén vagy annál magasabb, lehetővé teszi, hogy a felhasználók üzembe helyezése Resource Manager-sablonok. Ennek eredményeképpen a szerepkör tagjai, Resource Manager-sablonok adat-előállítók és a gyermek erőforrásaikat, beleértve az adatkészletek, a társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok üzembe helyezéséhez használható. Tagság a szerepkör nem teszi lehetővé a felhasználó más erőforrások, azonban létrehozása.

### <a name="custom-scenarios-and-custom-roles"></a>Egyéni forgatókönyvek és egyéni szerepkörök

Néha szükség lehet különböző hozzáférési szintek különböző data factory felhasználók megadása. Példa:
- Szükség lehet egy csoportot, amelyben felhasználók csak engedélye az adott adat-előállító.
- Vagy előfordulhat, hogy egy csoportot, amelyben a felhasználók csak figyelheti az adat-előállító (vagy előállítók), de nem módosítható.

Egyéni szerepkörök létrehozásával, és ezeket a szerepköröket a felhasználók hozzárendelése egyéni forgatókönyvekben érheti el. Egyéni szerepkörökkel kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure-ban](..//role-based-access-control/custom-roles.md).

Íme néhány példa, amelyek bemutatják, mit érhet el egyéni szerepkörökkel:

- Lehetővé teszik a felhasználó létrehozásához, szerkesztéséhez vagy bármilyen adat-előállító egy erőforráscsoport törlése az Azure Portalról.

  Rendelje hozzá a beépített **Data Factory közreműködője** az erőforráscsoport szintjén a felhasználó szerepkör. Ha szeretné engedélyezni a hozzáférést minden olyan adat-előállító egy előfizetésben, rendelje hozzá a szerepkört az előfizetés szintjén.

- Lehetővé teszik a felhasználó megtekintése (olvasás), és figyelhető adat-előállító, de nem szerkeszthetik, vagy módosítsa azt.

  Rendelje hozzá a beépített **olvasó** a data factory erőforrás-a felhasználói szerepkör.

- A felhasználó szerkesztheti az Azure Portalon egyetlen data factory segítségével.

  Ebben a forgatókönyvben két szerepkör hozzárendelése szükséges.

  1. Rendelje hozzá a beépített **közreműködői** szerepkör a data factory szintjén.
  2. Egyéni szerepkör létrehozása engedéllyel rendelkező * Microsoft.Resources/deployments/**. Az egyéni szerepkör hozzárendelése a felhasználóhoz erőforráscsoport szintjén.

- A felhasználó frissítése a PowerShell vagy az SDK-t, de nem az Azure portal data factory segítségével.

  Rendelje hozzá a beépített **közreműködői** a data factory erőforrás-a felhasználói szerepkör. Ez a szerepkör lehetővé teszi, hogy a felhasználó, tekintse meg az erőforrásokat az Azure Portalon, de a felhasználó nem férhet hozzá a **közzététel** és **összes közzététele** gombokat.

## <a name="next-steps"></a>További lépések

- További információ az Azure - szerepkörök [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

- Tudjon meg többet a **Data Factory közreműködője** szerepkör - [Data Factory Közreműködője szerepkör](../role-based-access-control/built-in-roles.md#data-factory-contributor).