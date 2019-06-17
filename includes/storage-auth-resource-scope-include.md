---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115564"
---
Az RBAC-szerepkört rendel egy rendszerbiztonsági tag, mielőtt határozza meg, hogy a rendszerbiztonsági tagot kell hozzáférés hatókörét. Ajánlott eljárások szabályozzák, hogy minden esetben célszerű csak a minimális lehetséges hatókör megadása.

Az alábbi lista ismerteti a szintek milyen gazdagépcsoportjaira az Azure blob és üzenetsor erőforrásokhoz való hozzáférés a minimális hatókör kezdve:

- **Tároló.** Ebben a hatókörben szerepkör-hozzárendelés minden a blobokat a tárolóba, valamint a tároló tulajdonságainak és metaadatainak vonatkozik.
- **Az egyes üzenetsorába.** Ebben a hatókörben szerepkör-hozzárendelés üzeneteket az üzenetsor, valamint a várólista-tulajdonságok és metaadatok vonatkozik.
- **A storage-fiók.** Ebben a hatókörben a szerepkör-hozzárendelés a blobok és az összes tárolót, vagy valamennyi üzenetsorok és az üzenetek vonatkozik.
- **Az erőforráscsoport.** Ebben a hatókörben szerepkör-hozzárendelés minden, a tárolók és a tárfiókot az erőforráscsoportban lévő összes üzenetsor vonatkozik.
- **Az előfizetés.** Ebben a hatókörben szerepkör-hozzárendelés minden, a tárolók és az összes a tárfiókok minden előfizetésben az erőforráscsoportok a várólisták vonatkozik.

> [!IMPORTANT]
> Ha az előfizetése tartalmazza az Azure DataBricks-névtér, az előfizetések szintjén hozzárendelt szerepkörök blokkolva lesz a blob és üzenetsor-adatokhoz való hozzáférést.
