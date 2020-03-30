---
title: Szerepkörök és engedélyek az Azure Data Factoryhoz
description: Az adatgyárak létrehozásához és a gyermekerőforrásokkal való munkához szükséges szerepkörök és engedélyek ismertetése.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: d143992317c77c6fc3137527bea485a98c046daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969228"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Szerepkörök és engedélyek az Azure Data Factoryhoz

Ez a cikk ismerteti az Azure Data Factory-erőforrások létrehozásához és kezeléséhez szükséges szerepköröket, valamint a szerepkörök által megadott engedélyeket.

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetésben található engedélyek megtekintéséhez a Microsoft Azure Portalon kattintson a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. 

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:
- A gyermekerőforrások Microsoft Azure Portalon való létrehozásához és kezeléséhez a **Data Factory közreműködője** szerepkörhöz kell tartoznia az erőforráscsoport szintjén vagy felette.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../cost-management-billing/manage/add-change-subscription-administrator.md) című cikket.

## <a name="set-up-permissions"></a>Engedélyek beállítása

A Data Factory létrehozása után érdemes lehet más felhasználókszámára az adat-előállítóval dolgozni. Ahhoz, hogy ezt a hozzáférést más felhasználók számára, hozzá kell adnia őket a beépített **Data Factory Közreműködőszerepkör** az erőforrás-csoport, amely tartalmazza az adat-előállító.

### <a name="scope-of-the-data-factory-contributor-role"></a>Az adatfeldolgozó közreműködői szerepkör hatóköre

A **Data Factory Közreműködői** szerepkörben való tagság lehetővé teszi a felhasználók számára, hogy a következő dolgokat tegyék:
- Adatgyárakat és gyermekerőforrásokat hozhat létre, szerkeszthet és törölhet, beleértve az adatkészleteket, a csatolt szolgáltatásokat, a folyamatokat, az eseményindítókat és az integrációs futtatásokat.
- Telepítse az Erőforrás-kezelő sablonokat. A Resource Manager központi telepítése a Data Factory által az Azure Portalon használt telepítési módszer.
- Az App Insights-riasztások kezelése adatelőállítók számára.
- Hozzon létre támogatási jegyeket.

A szerepkörről további információt a [Data Factory Közreműködői szerepkör című témakörben talál.](../role-based-access-control/built-in-roles.md#data-factory-contributor)

### <a name="resource-manager-template-deployment"></a>Erőforrás-kezelő sablonjának telepítése

A **Data Factory Közreműködő** szerepkör az erőforráscsoport szintjén vagy felett lehetővé teszi a felhasználók számára az Erőforrás-kezelő-sablonok telepítését. Ennek eredményeképpen a szerepkör tagjai az Erőforrás-kezelő sablonjai segítségével telepíthetik az adatgyárakat és a gyermekerőforrásaikat, beleértve az adatkészleteket, a kapcsolódó szolgáltatásokat, a folyamatokat, az eseményindítókat és az integrációs futtatásokat. A szerepkörben való tagság azonban nem teszi lehetővé, hogy a felhasználó más erőforrásokat hozzon létre.

Az Azure Repos és a GitHub engedélyei függetlenek a Data Factory engedélyektől. Ennek eredményeképpen az Olvasó szerepkörben csak az Olvasó szerepkörben csak tagsággal rendelkező felhasználó szerkesztheti a Data Factory gyermekerőforrásait, és véglegesítheti a tárműtor módosításait, de nem teheti közzé ezeket a módosításokat.

> [!IMPORTANT]
> Az Erőforrás-kezelő sablon üzembe helyezése a **Data Factory Közreműködői** szerepkörrel nem emeli az engedélyeket. Ha például üzembe helyez egy sablont, amely létrehoz egy Azure virtuális gépet, és nincs engedélye a virtuális gépek létrehozására, a központi telepítés engedélyezési hibával sikertelen lesz.

### <a name="custom-scenarios-and-custom-roles"></a>Egyéni forgatókönyvek és egyéni szerepkörök

Néha előfordulhat, hogy különböző hozzáférési szinteket kell megadnia a különböző adatfeldolgozó felhasználókszámára. Példa:
- Szükség lehet egy olyan csoportra, ahol a felhasználók csak egy adott adatelőüzemhez rendelkeznek engedélyekkel.
- Vagy szükség lehet egy csoport, ahol a felhasználók csak figyelni egy adatgyár (vagy gyárak), de nem módosíthatja azt.

Ezeket az egyéni forgatókönyveket egyéni szerepkörök létrehozásával és a felhasználók szerepkörökhöz való hozzárendelésével érheti el. Az egyéni szerepkörökről további információt az [Egyéni szerepkörök az Azure-ban című témakörben talál.](..//role-based-access-control/custom-roles.md)

Íme néhány példa, amely bemutatja, hogy mit érhet el az egyéni szerepkörökkel:

- Hagyja, hogy a felhasználó hozzon létre, szerkessze vagy törölje az erőforráscsoport bármely adatgyárát az Azure Portalról.

  Rendelje hozzá a beépített **Data Factory közreműködői** szerepkört az erőforráscsoport szintjén a felhasználóhoz. Ha egy előfizetés bármely adat-előállítójához hozzáférést szeretne engedélyezni, rendelje hozzá a szerepkört az előfizetés szintjén.

- Hagyja, hogy a felhasználó megtekintse (olvassa) és figyelje az adat-előállítót, de ne módosítsa vagy módosítsa azt.

  Rendelje hozzá a beépített **olvasószerepkört** az adat-előállító erőforráshoz a felhasználószámára.

- Hagyja, hogy egy felhasználó egyetlen adatgyárat szerkesztsen az Azure Portalon.

  Ebben a forgatókönyvben két szerepkör-hozzárendelést igényel.

  1. Rendelje hozzá a beépített **közreműködői** szerepkört az adat-előállító szinten.
  2. Hozzon létre egyéni szerepkört a **Microsoft.Resources/deployments/** engedéllyel. Rendelje hozzá ezt az egyéni szerepkört a felhasználóhoz erőforráscsoport szinten.

- A kapcsolat tesztelése csak csatolt szolgáltatásban

    Hozzon létre egy egyéni szerepkört a következő műveletekhez szükséges engedélyekkel: **Microsoft.DataFactory/factories/getFeatureValue/read** és **Microsoft.DataFactory/factories/getDataPlaneAccess/read**. Rendelje hozzá ezt az egyéni szerepkört az adat-előállító erőforráshoz a felhasználószámára.

- Hagyja, hogy egy felhasználó frissítse az adat-előállító powershellvagy az SDK, de nem az Azure Portalon.

  Rendelje hozzá a beépített **közreműködői** szerepkört az adat-előállító erőforráshoz a felhasználószámára. Ez a szerepkör lehetővé teszi, hogy a felhasználó az Azure Portalon tekintse meg az erőforrásokat, de a felhasználó nem férhet hozzá az Összes **közzététele** és **közzététele** gombhoz.

## <a name="next-steps"></a>További lépések

- További információ az Azure-beli szerepkörökről – [A szerepkör-definíciók megismerése](../role-based-access-control/role-definitions.md)

- További információ a **Data Factory közreműködői** szerepkör – [Data Factory Közreműködői szerepkörről.](../role-based-access-control/built-in-roles.md#data-factory-contributor)
