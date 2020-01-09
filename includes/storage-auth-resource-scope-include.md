---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460494"
---
Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel az Azure Blob és a várólista erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Egy adott tároló.** Ezen a hatókörön a szerepkör-hozzárendelés a tároló összes blobján, valamint a tároló tulajdonságain és metaadatain is érvényes.
- **Egy külön üzenetsor.** Ezen a hatókörön a szerepkör-hozzárendelés a várólistán lévő üzenetekre, valamint a várólista tulajdonságaira és metaadataira vonatkozik.
- **A Storage-fiók.** Ezen a hatókörön a szerepkör-hozzárendelés minden tárolóra és a blobokra, illetve az összes várólistára és azok üzeneteire vonatkozik.
- **Az erőforráscsoport.** Ezen a hatókörön a szerepkör-hozzárendelés az erőforráscsoport összes tárolási fiókjának összes tárolóján vagy várólistáján érvényes.
- **Az előfizetés.** Ezen a hatókörön a szerepkör-hozzárendelés az előfizetésben lévő összes erőforráscsoport összes tárolóján vagy várólistáján érvényes.

> [!IMPORTANT]
> Ha az előfizetése tartalmaz egy Azure DataBricks-névteret, az előfizetésre hatókörbe tartozó szerepkörök nem biztosítanak hozzáférést a blob és a üzenetsor adataihoz. A hatókör szerepkörei az erőforráscsoport, a Storage-fiók vagy a tároló vagy a várólista számára.     
