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
ms.openlocfilehash: 91a4a9ae1d3d84f1396adad07d1cda73ee3747c9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312449"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazás üzembe helyezése során?

Összes hivatalosan támogatott üzembe helyezési módszer van egy dolog közös:, módosítja a fájlokat a `/site/home/wwwroot` az alkalmazás mappájában. Ezek a ugyanazokat a fájlokat, amelyek az éles környezetben futnak. Ezért a központi telepítés zárolt fájlok miatt meghiúsulhat, vagy az alkalmazás éles környezetben előfordulhat, hogy az előre nem látható viselkedéshez üzembe helyezés során rendelkezik, mivel nem az összes fájl egyszerre frissülnek. Ezek a problémák elkerülése érdekében néhány különböző módja van:

- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás kapcsolat nélküli módban üzembe helyezés során. További információkért lásd: [zárolt fájlok üzembe helyezése során alkalmazott](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Helyezze üzembe a [átmeneti tárhely](../articles/app-service/web-sites-staged-publishing.md) a [automatikus felcserélés](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) engedélyezve van. 
- Használat [futtassa a csomag](https://github.com/Azure/app-service-announcements/issues/84) helyette.
