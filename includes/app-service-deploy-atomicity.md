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
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236469"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazás központi telepítése során?

A hivatalos támogatott központi telepítési módszerek rendelkezik egyetlen művelet: ezek a fájlok módosíthatók a a `/site/home/wwwroot` az alkalmazás mappájában. Ezek a ugyanazokat a fájlokat, éles környezetben futnak. Ezért a központi telepítés zárolt fájlok miatt meghiúsulhat, vagy az alkalmazást éles környezetben előfordulhat, hogy az előre nem látható viselkedéshez üzembe helyezése során lehetnek, mert nem összes fájl egyszerre frissítik. Ezen problémák elkerülése érdekében néhány különböző módja van:

- Állítsa le az alkalmazást, vagy engedélyezze a kapcsolat nélküli módban, az alkalmazás központi telepítése során. További információkért lásd: [központi telepítése közben zárolt fájlokat foglalkozó](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Telepítse a [tárolóhely átmeneti](../articles/app-service/web-sites-staged-publishing.md) a [automatikus felcserélés](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) engedélyezve van. 
- Használjon [futtassa a Zip](https://github.com/Azure/app-service-announcements/issues/84) helyette.
