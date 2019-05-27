---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159865"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Hiba ConditionHeadersNotSupported böngészőből az Azure Files használata egy webalkalmazásból

Amikor feltételes fejlécek, mint például a webböngésző egy alkalmazáson keresztül, amely lehetővé teszi az Azure Files-ban üzemeltetett tartalom eléréséhez használja hozzáférés meghiúsul, egy ConditionHeadersNotSupported hiba megjelenítése.

![ConditionHeaderNotSupported Error](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Ok

Feltételes fejlécek még nem támogatott. Alkalmazások azok megvalósítását kell kérnie a teljes fájl minden alkalommal, amikor a fájl érhető el.

### <a name="workaround"></a>Áthidaló megoldás

Amikor új fájlt töltenek fel, a cache-control tulajdonság alapértelmezés szerint a "no-cache". A kérelem a fájl alkalmazásának a kényszerítéséhez minden alkalommal, a fájlt a cache-control tulajdonság frissíteni kell a "no-cache" a "no-cache, no-store, kell-revalidate". Ez tehető [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Storage explorer tartalom-gyorsítótárában módosítása](media/storage-files-condition-headers/storage-explorer-cache.png)