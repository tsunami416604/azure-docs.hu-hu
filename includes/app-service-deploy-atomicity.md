---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736894"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazás üzembe helyezése során?

Összes hivatalosan támogatott üzembe helyezési módszer van egy dolog közös:, módosítja a fájlokat a `/home/site/wwwroot` az alkalmazás mappájában. Ezek a ugyanazokat a fájlokat, amelyek az éles környezetben futnak. Ezért a központi telepítés zárolt fájlok miatt meghiúsulhat, vagy az alkalmazás éles környezetben előfordulhat, hogy az előre nem látható viselkedéshez üzembe helyezés során rendelkezik, mivel nem az összes fájl egyszerre frissülnek. Ezek a problémák elkerülése érdekében néhány különböző módja van:

- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás kapcsolat nélküli módban üzembe helyezés során. További információkért lásd: [zárolt fájlok üzembe helyezése során alkalmazott](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Helyezze üzembe a [átmeneti tárhely](../articles/app-service/deploy-staging-slots.md) a [automatikus felcserélés](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) engedélyezve van. 
- Használat [futtassa a csomag](https://github.com/Azure/app-service-announcements/issues/84) helyette.
