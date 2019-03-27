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
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449957"
---
Az RBAC-szerepkört rendel egy rendszerbiztonsági tag, mielőtt határozza meg, hogy a rendszerbiztonsági tagot kell hozzáférés hatókörét. Ajánlott eljárások szabályozzák, hogy minden esetben célszerű csak a minimális lehetséges hatókör megadása.

Az alábbi lista ismerteti a szintek milyen gazdagépcsoportjaira az Azure blob és üzenetsor erőforrásokhoz való hozzáférés a minimális hatókör kezdve:

- **Tároló.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolóba, valamint a tároló tulajdonságainak és metaadatainak blobok.
- **Az egyes üzenetsorába.** Ebben a hatókörben egy rendszerbiztonsági tag üzeneteket az üzenetsor, valamint a várólista-tulajdonságok és metaadatok hozzáféréssel rendelkezik.
- **A storage-fiók.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér a blobok és az összes tárolót, vagy valamennyi üzenetsorok és az üzenetek.
- **Az erőforráscsoport.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és a tárfiókot az erőforráscsoportban lévő összes üzenetsor.
- **Az előfizetés.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és az üzenetsorok, az összes, az erőforráscsoport az előfizetésben található összes tárfiókot.
