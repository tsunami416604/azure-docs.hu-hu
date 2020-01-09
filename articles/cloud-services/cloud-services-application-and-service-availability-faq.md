---
title: Az alkalmazások és szolgáltatások rendelkezésre állásával kapcsolatos problémák – GYIK
titleSuffix: Azure Cloud Services
description: Ez a cikk az alkalmazással és a szolgáltatás rendelkezésre állásával kapcsolatos gyakori kérdéseket ismerteti Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386918"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az Azure Cloud Services alkalmazások és szolgáltatások rendelkezésre állásával kapcsolatos problémák: gyakori kérdések (GYIK)

Ez a cikk a [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)alkalmazásokkal és szolgáltatásokkal kapcsolatos rendelkezésre állási problémákról tartalmaz gyakori kérdéseket. A mérettel kapcsolatos információkért tekintse meg a [Cloud Services virtuális gép mérete lapot](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A szerepkör újra lett hasznosítva. Történt-e frissítés a felhőalapú szolgáltatáshoz?
A Microsoft körülbelül havonta egyszer kibocsátja a vendég operációs rendszer új verzióját a Windows Azure Pásti virtuális gépekhez. A vendég operációs rendszer csak egy ilyen frissítés a folyamatban. A kiadást számos más tényező is befolyásolhatja. Az Azure emellett több százezer gépen fut. Ezért nem lehet előre jelezni a szerepkörök újraindításának pontos dátumát és időpontját. A vendég operációs rendszer frissítése RSS-hírcsatornát a legújabb információkkal frissítjük, de érdemes megfontolni, hogy a jelentett idő egy hozzávetőleges érték legyen. Tisztában vagyunk azzal, hogy ez problematikus az ügyfelek számára, és egy olyan terv alapján dolgozik, amely korlátozza vagy pontosan újraindul.

A legújabb vendég operációs rendszer frissítéseivel kapcsolatos részletes információkért lásd: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md).

A vendég és a gazdagép operációsrendszer-frissítéseinek technikai részleteit ismertető, az újraindítással és a mutatókkal kapcsolatos hasznos információkért tekintse meg az MSDN blog post [szerepkör-példányának újraindítását az operációs rendszer frissítései miatt](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Miért nem a szokásosnál hosszabb ideig tart a Felhőbeli szolgáltatásra irányuló első kérés a szolgáltatás tétlensége után?
Amikor a webkiszolgáló megkapja az első kérést, először újrafordítja a kódot, majd feldolgozza a kérést. Ezért az első kérelem hosszabb időt vesz igénybe, mint a többi. Alapértelmezés szerint az alkalmazáskészlet felhasználói tétlenség esetén leáll. Az alkalmazáskészlet alapértelmezés szerint 1 740 percenként is újraindul (29 óra).

Az Internet Information Services (IIS) alkalmazáskészletek rendszeres időközönként újrahasznosítható, így elkerülhetők az alkalmazások összeomlását, lefagyását vagy szivárgását okozó instabil állapotok.

A következő dokumentumok segítenek megérteni és enyhíteni a problémát:
* [Az IIS lassú kezdeti terhelésének javítása](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7,5 webalkalmazás első kérése az App-Pool újrahasznosítása után nagyon lassú](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Ha módosítani szeretné az IIS alapértelmezett viselkedését, indítási feladatokat kell használnia, mert ha manuálisan alkalmazza a módosításokat a webes szerepkör példányain, akkor a módosítások végül elvesznek.

További információ: a [felhőalapú szolgáltatás indítási feladatainak konfigurálása és futtatása](cloud-services-startup-tasks.md).
