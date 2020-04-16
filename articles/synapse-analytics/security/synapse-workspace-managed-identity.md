---
title: Felügyelt identitás az Azure Synapse-munkaterületen
description: Az Azure Synapse-munkaterületen felügyelt identitást ismerteti
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425118"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Azure Synapse-munkaterület felügyelt identitása (előzetes verzió)

Ebben a cikkben az Azure Synapse-munkaterületen található felügyelt identitásról olvashat.

## <a name="managed-identities"></a>Felügyelt identitások

Az Azure-erőforrások felügyelt identitása az Azure Active Directory egyik szolgáltatása. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. A felügyelt identitás funkció használatával hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést.

Az Azure-erőforrások felügyelt identitások az új neve a szolgáltatás korábbi nevén felügyelt szolgáltatás identitása (MSI). További információ: [Felügyelt identitások.](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="azure-synapse-workspace-managed-identity"></a>Azure Synapse-munkaterület által felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás jön létre az Azure Synapse munkaterület, amikor létrehozza a munkaterületet.

>[!NOTE]
>Ezt a munkaterület által felügyelt identitást a dokumentum többi részén felügyelt identitásként nevezzük.

Az Azure Synapse a felügyelt identitást használja a folyamatok vezényléséhez. A felügyelt identitás életciklusa közvetlenül kapcsolódik az Azure Synapse munkaterülethez. Ha törli az Azure Synapse munkaterületet, majd a felügyelt identitás is törlődik.

A munkaterület felügyelt identitásának engedélyekre van szüksége a folyamatokban végzett műveletek végrehajtásához. Az objektumazonosító vagy az Azure Synapse munkaterület neve segítségével megkeresheti a felügyelt identitást az engedélyek megadásakor.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Felügyelt identitás lekérése az Azure Portalon

A felügyelt identitás az Azure Portalon. Nyissa meg az Azure Synapse-munkaterületet az Azure Portalon, és válassza a bal oldali navigációs **áttekintés** lehetőséget. A felügyelt identitás objektumazonosítója megjelenik a főképernyőn.

![Felügyelt identitásobjektum azonosítója](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

A felügyelt identitásadatok akkor is megjelennek, amikor olyan összekapcsolt szolgáltatást hoz létre, amely támogatja az Azure Synapse Studio felügyelt identitáshitelesítését.

Indítsa el **az Azure Synapse Studio alkalmazást,** és válassza a **Kezelés** lapot a bal oldali navigációs sávon. Ezután válassza a **Csatolt szolgáltatások** lehetőséget, és válassza a **+ Új** lehetőséget egy új csatolt szolgáltatás létrehozásához.

![Csatolt szolgáltatás létrehozása 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Az **Új csatolt szolgáltatás** ablakban írja be az *Azure Data Lake Storage Gen2*parancsot. Válassza ki az **Azure Data Lake Storage Gen2** erőforrástípust az alábbi listából, és válassza a Folytatás **gombot.**

![Csatolt szolgáltatás létrehozása 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

A következő ablakban válassza a **Felügyelt identitás** a **hitelesítéshez módszert.** Megjelenik a felügyelt identitás **neve** és **objektumazonosítója.**

![Csatolt szolgáltatás létrehozása 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>További lépések

[Engedélyek megadása az Azure Synapse-munkaterület felügyelt identitásához](./how-to-grant-workspace-managed-identity-permissions.md)
