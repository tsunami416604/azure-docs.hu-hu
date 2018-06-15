---
title: Alkalmazás és szolgáltatás elérhetőségével kapcsolatos problémákat a Microsoft Azure Cloud Services – gyakori kérdések |} Microsoft Docs
description: Ez a cikk a Microsoft Azure-szolgáltatásokhoz alkalmazás és szolgáltatás rendelkezésre állása kapcsolatos gyakori kérdésekre. sorolja fel.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068113"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Alkalmazások és szolgáltatások elérhetőségi problémáinak Azure-szolgáltatásokhoz: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza az alkalmazás és szolgáltatás elérhetőségével kapcsolatos problémákat a kapcsolatos gyakran ismételt kérdések [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [felhőalapú szolgáltatások Virtuálisgép-méretet lap](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A szerepkör lett hasznosítva. A felhőszolgáltatás megkezdődött módosításairól történt?
Nagyjából havonta, Microsoft kiadását egy új vendég operációs rendszer verziója a Windows Azure PaaS virtuális gépeken. A vendég operációs rendszer csak egy ilyen frissítés folyamatban. Egy kiadási sok más tényezőktől is érinti. Emellett Azure több ezer gép több száz futtatja. Ezért lehetetlen előre jelezni a pontos dátum és idő, amikor a szerepkörök újraindul. A legújabb információkkal kell frissítjük a vendég operációs rendszer frissítése RSS-hírcsatorna, de vegye figyelembe, hogy jelenteni idő becsült érték. Azt észleli, hogy ez a problematikus, az ügyfelek, és a terv korlátot vagy a pontos idő újraindítások dolgoznak.

Teljes legutóbbi vendég operációs rendszer frissítése, lásd: [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).

Újraindítás és a Vendég és a gazda operációs Rendszerével frissítések technikai részleteket mutató hivatkozások hasznos információkat lásd az MSDN blogbejegyzésben [szerepkör példány újraindítása miatt az operációs rendszer frissítései](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Miért nem az első kérésre a felhőalapú szolgáltatáshoz után a szolgáltatás inaktív egy ideig tarthat a szokásosnál hosszabb ideig?
Amikor a webkiszolgáló az első kérelmet kap, először újrafordítja a kódot, és majd feldolgozza a kérést. Ezért az első kérésre tovább tart, mint a többi. Alapértelmezés szerint az alkalmazáskészlet felhasználói tétlen esetekben lekérdezi leállítása. Az alkalmazáskészlet is indul újra alapértelmezés szerint minden 1,740 perc (29 óra).

Internet Information Services (IIS) lehet, hogy rendszeres időközönként újrahasznosít alkalmazás vezethet instabil állapotok elkerülése érdekében készletek omlik össze, lefagy, vagy memóriavesztések.

A következő dokumentumok segít megérteni, és a probléma elhárítása érdekében:
* [Az IIS lassú kezdeti betöltés kijavítása](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Az IIS 7.5 webes alkalmazás első kérelem után-alkalmazáskészlet újrahasznosítási nagyon lelassul](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Ha az IIS alapértelmezett konfigurációját módosítani kívánja, szüksége lesz indítási feladatok használatával, mert ha manuálisan módosította a webes szerepkör-példányokban kívánja, a módosítások végül elvesznek.

További információkért lásd: [hogyan lehet konfigurálni és futtatni egy felhőalapú szolgáltatás indítási feladatokat](cloud-services-startup-tasks.md).
