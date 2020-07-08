---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024891"
---
Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel az Azure Blob és a várólista erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Egy adott tároló.** Ezen a hatókörön a szerepkör-hozzárendelés a tároló összes blobján, valamint a tároló tulajdonságain és metaadatain is érvényes.
- **Egy külön üzenetsor.** Ezen a hatókörön a szerepkör-hozzárendelés a várólistán lévő üzenetekre, valamint a várólista tulajdonságaira és metaadataira vonatkozik.
- **A Storage-fiók.** Ezen a hatókörön a szerepkör-hozzárendelés minden tárolóra és a blobokra, illetve az összes várólistára és azok üzeneteire vonatkozik.
- **Az erőforráscsoport.** Ezen a hatókörön a szerepkör-hozzárendelés az erőforráscsoport összes tárolási fiókjának összes tárolóján vagy várólistáján érvényes.
- **Az előfizetés.** Ezen a hatókörön a szerepkör-hozzárendelés az előfizetésben lévő összes erőforráscsoport összes tárolóján vagy várólistáján érvényes.
