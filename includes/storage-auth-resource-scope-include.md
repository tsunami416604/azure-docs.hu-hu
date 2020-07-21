---
title: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518680"
---
Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel az Azure Blob és a várólista erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Egy adott tároló.** Ezen a hatókörön a szerepkör-hozzárendelés a tároló összes blobján, valamint a tároló tulajdonságain és metaadatain is érvényes.
- **Egy külön üzenetsor.** Ezen a hatókörön a szerepkör-hozzárendelés a várólistán lévő üzenetekre, valamint a várólista tulajdonságaira és metaadataira vonatkozik.
- **A Storage-fiók.** Ezen a hatókörön a szerepkör-hozzárendelés minden tárolóra és a blobokra, illetve az összes várólistára és azok üzeneteire vonatkozik.
- **Az erőforráscsoport.** Ezen a hatókörön a szerepkör-hozzárendelés az erőforráscsoport összes tárolási fiókjának összes tárolóján vagy várólistáján érvényes.
- **Az előfizetés.** Ezen a hatókörön a szerepkör-hozzárendelés az előfizetésben lévő összes erőforráscsoport összes tárolóján vagy várólistáján érvényes.
- **Egy felügyeleti csoport.** Ezen a hatókörön a szerepkör-hozzárendelés az összes olyan tárolón vagy várólistán szerepel, amely a felügyeleti csoport összes előfizetésének összes erőforrás-csoportjában található.

További információ a RBAC szerepkör-hozzárendelésekről és a hatókörről: [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../articles/role-based-access-control/overview.md).
