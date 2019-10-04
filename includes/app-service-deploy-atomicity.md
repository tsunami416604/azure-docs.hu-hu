---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836832"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazás üzembe helyezése során?

Az összes hivatalosan támogatott üzembe helyezési módokat módosítania kell a fájlt a a `/home/site/wwwroot` az alkalmazás mappájában. Ezek a fájlok megegyeznek azokon, éles környezetben futnak. Ezért a központi telepítés zárolt fájlok miatt meghiúsulhat. Az alkalmazás éles környezetben előfordulhat, hogy is kiszámíthatatlan központi telepítése során, mivel nem minden fájlja frissítve egy időben. Ezek a problémák elkerülése érdekében néhány különböző módja van:

- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás kapcsolat nélküli módban üzembe helyezés során. További információkért lásd: [zárolt fájlokat foglalkozik az üzembe helyezés során](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Helyezze üzembe a [átmeneti tárhely](../articles/app-service/deploy-staging-slots.md) a [automatikus felcserélés](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) engedélyezve van. 
- Használat [futtassa a csomag](https://github.com/Azure/app-service-announcements/issues/84) folyamatos üzembe helyezés helyett.
