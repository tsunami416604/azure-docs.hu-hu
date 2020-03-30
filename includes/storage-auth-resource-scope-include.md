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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460494"
---
Mielőtt rbac szerepkört rendelne egy rendszerbiztonsági taghoz, határozza meg a rendszerbiztonsági tag hozzáférési tartományát. A legjobb gyakorlatok azt diktálják, hogy mindig a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken a legszűkebb hatókörtől kezdve az Azure blob- és várólista-erőforrásokhoz való hozzáférést hatókörbe tartozó személyek használhatják:

- **Egyedi tároló.** Ebben a hatókörben egy szerepkör-hozzárendelés a tárolóban lévő összes blobra, valamint a tároló tulajdonságaira és metaadataira vonatkozik.
- **Egyéni várólista.** Ebben a hatókörben a szerepkör-hozzárendelés a várólistában lévő üzenetekre, valamint a várólista tulajdonságaira és metaadataira vonatkozik.
- **A tárfiók.** Ebben a hatókörben egy szerepkör-hozzárendelés minden tárolóra és azok blobjaira, illetve az összes várólistára és üzenetére vonatkozik.
- **Az erőforráscsoport.** Ebben a hatókörben egy szerepkör-hozzárendelés az erőforráscsoport összes tárfiókjának összes tárolójára vagy várólistájára vonatkozik.
- **Az előfizetés.** Ebben a hatókörben egy szerepkör-hozzárendelés az összes tárolók vagy várólisták az összes erőforrásfiókok az előfizetés összes erőforrás-fiókok.

> [!IMPORTANT]
> Ha az előfizetés tartalmaz egy Azure DataBricks névteret, az előfizetés hatókörébe tartozó szerepkörök nem adnak hozzáférést a blob és a várólista-adatokhoz. A szerepköröket inkább az erőforráscsoportba, a tárfiókba vagy a tárolóba vagy a várólistába kell helyezni.     
