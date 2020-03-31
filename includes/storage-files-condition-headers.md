---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737491"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Hiba feltételfejlécekNem támogatottak webalkalmazásból az Azure Files from Browser használatával

A ConditionHeadersNotSupported hiba akkor fordul elő, amikor az Azure Files-ban tárolt tartalom elérése feltételes fejléceket, például webböngészőt használó alkalmazáson keresztül sikertelen. A hiba azt jelenti, hogy a feltételfejlécek nem támogatottak.

![Azure Files feltételes fejlécek hiba](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Ok

A feltételes fejlécek még nem támogatottak. Az azokat végrehajtó alkalmazásoknak minden alkalommal meg kell kérniük a teljes fájlt, amikor a fájlhoz hozzáférnek.

### <a name="workaround"></a>Áthidaló megoldás

Új fájl feltöltésekéna alapértelmezés szerint a gyorsítótár-vezérlő tulajdonság "nincs gyorsítótár". Ahhoz, hogy az alkalmazás minden alkalommal kérje a fájlt, a fájl gyorsítótár-vezérlő tulajdonságát frissíteni kell a "nincs gyorsítótár" tulajdonságról a "nincs gyorsítótár, nincs tároló, must-revalidate" tulajdonságra. Ez az Azure [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használatával érhető el.

![A Storage Explorer tartalomgyorsítótárának módosítása az Azure Files feltételes fejléceihez](media/storage-files-condition-headers/storage-explorer-cache.png)