---
title: Szerepkörök és engedélyek az Azure Data Factory |} A Microsoft Docs
description: Ismerteti a szerepkörök és engedélyek szükségesek a Data Factoryk létrehozásához és a gyermek-erőforrásokat.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 19666eb668dd120c1705c6a62a8ba1abd2321026
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575715"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Szerepkörök és engedélyek az Azure Data Factoryhoz

Ez a cikk ismerteti az Azure Data Factory-erőforrások létrehozásához és kezeléséhez szükséges szerepköröket és által ezekhez a szerepkörökhöz rendelt engedélyeket.

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetésben található engedélyek megtekintéséhez a Microsoft Azure Portalon kattintson a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. 

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:
- A gyermekerőforrások Microsoft Azure Portalon való létrehozásához és kezeléséhez a **Data Factory közreműködője** szerepkörhöz kell tartoznia az erőforráscsoport szintjén vagy felette.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

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

Az Azure-kódtárak és a GitHub engedélyei függetlenek az adat-előállító engedélyek. Ennek eredményeképpen egy tárház engedélyekkel rendelkező felhasználó csak az Olvasó szerepkör egyik tagját szerkesztheti a Data Factory gyermekerőforrásait és véglegesítési vált a tárház, de ezeket a módosításokat nem tehető közzé.

> [!IMPORTANT]
> A Resource Manager-sablon telepítése a **Data Factory Közreműködője** szerepkör nem a jogosultságszint. Például ha telepít egy sablont, amely egy Azure virtuális gépet hoz létre, és nincs engedélye létrehozni virtuális gépeket, a központi telepítés sikertelen, és egy engedélyezési.

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
  2. Egyéni szerepkör létrehozása engedéllyel rendelkező **Microsoft.Resources/deployments/**. Az egyéni szerepkör hozzárendelése a felhasználóhoz erőforráscsoport szintjén.

- A felhasználó frissítése a PowerShell vagy az SDK-t, de nem az Azure portal data factory segítségével.

  Rendelje hozzá a beépített **közreműködői** a data factory erőforrás-a felhasználói szerepkör. Ez a szerepkör lehetővé teszi, hogy a felhasználó, tekintse meg az erőforrásokat az Azure Portalon, de a felhasználó nem férhet hozzá a **közzététel** és **összes közzététele** gombokat.

## <a name="next-steps"></a>További lépések

- További információ az Azure - szerepkörök [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

- Tudjon meg többet a **Data Factory közreműködője** szerepkör - [Data Factory Közreműködője szerepkör](../role-based-access-control/built-in-roles.md#data-factory-contributor).
