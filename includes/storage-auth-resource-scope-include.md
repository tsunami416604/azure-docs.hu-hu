---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622637"
---
Az RBAC-szerepkört rendel egy rendszerbiztonsági tag, mielőtt határozza meg, hogy a rendszerbiztonsági tagot kell hozzáférés hatókörét. Ajánlott eljárások szabályozzák, hogy minden esetben célszerű csak a minimális lehetséges hatókör megadása.

Az alábbi lista ismerteti a szintek milyen gazdagépcsoportjaira az Azure blob és üzenetsor erőforrásokhoz való hozzáférés a minimális hatókör kezdve:

- **Tároló.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolóba, valamint a tároló tulajdonságainak és metaadatainak blobok.
- **Az egyes üzenetsorába.** Ebben a hatókörben egy rendszerbiztonsági tag üzeneteket az üzenetsor, valamint a várólista-tulajdonságok és metaadatok hozzáféréssel rendelkezik.
- **A storage-fiók.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér a blobok és az összes tárolót, vagy valamennyi üzenetsorok és az üzenetek.
- **Az erőforráscsoport.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és a tárfiókot az erőforráscsoportban lévő összes üzenetsor.
- **Az előfizetés.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és az üzenetsorok, az összes, az erőforráscsoport az előfizetésben található összes tárfiókot.

> [!IMPORTANT]
> Ha az előfizetése tartalmazza az Azure DataBricks-névtér, az előfizetések szintjén hozzárendelt szerepkörök blokkolva lesz a blob és üzenetsor-adatokhoz való hozzáférést.