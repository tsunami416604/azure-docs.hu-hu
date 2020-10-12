---
title: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534132"
---
Mielőtt Azure-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférési hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel az Azure Blob és a várólista erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Egy adott tároló.** Ezen a hatókörön a szerepkör-hozzárendelés a tároló összes blobján, valamint a tároló tulajdonságain és metaadatain is érvényes.
- **Egy külön üzenetsor.** Ezen a hatókörön a szerepkör-hozzárendelés a várólistán lévő üzenetekre, valamint a várólista tulajdonságaira és metaadataira vonatkozik.
- **A Storage-fiók.** Ezen a hatókörön a szerepkör-hozzárendelés minden tárolóra és a blobokra, illetve az összes várólistára és azok üzeneteire vonatkozik.
- **Az erőforráscsoport.** Ezen a hatókörön a szerepkör-hozzárendelés az erőforráscsoport összes tárolási fiókjának összes tárolóján vagy várólistáján érvényes.
- **Az előfizetés.** Ezen a hatókörön a szerepkör-hozzárendelés az előfizetésben lévő összes erőforráscsoport összes tárolóján vagy várólistáján érvényes.
- **Egy felügyeleti csoport.** Ezen a hatókörön a szerepkör-hozzárendelés az összes olyan tárolón vagy várólistán szerepel, amely a felügyeleti csoport összes előfizetésének összes erőforrás-csoportjában található.

További információ az Azure-beli szerepkör-hozzárendelésekről és a hatókörről: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../articles/role-based-access-control/overview.md).
