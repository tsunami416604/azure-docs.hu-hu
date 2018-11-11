---
title: Alkalmazás és szolgáltatás rendelkezésre állási problémák a Microsoft Azure Cloud Services – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Cloud Services alkalmazás és szolgáltatás rendelkezésre állásával kapcsolatos gyakori kérdések listája.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 68f3fe0dbd16d3d71dc66af05de6f90156d67dfa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246361"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Alkalmazás és szolgáltatás rendelkezésre állási problémák az Azure Cloud Services: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza – gyakori kérdések az alkalmazás és szolgáltatás rendelkezésre állási problémák [a Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [Cloud Services Virtuálisgép-méretet lapján](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A szerepkör lett újraindul. Történt bármilyen frissítést jelennek meg a felhőszolgáltatás számára?
Nagyjából egyszer egy hónapban, Microsoft által kiadott egy új Vendég operációsrendszer-verzió Windows Azure PaaS-beli virtuális gépek. A vendég operációs rendszer csak egy ilyen frissítés folyamatban. Sok más tényező hatással lehet egy kiadást. Az Azure emellett több százezer gépeket futtat. Ezért nem lehet megjósolni a pontos dátumát és időpontját, amikor a szerepkör újraindul. A legújabb információkkal, amelyek frissítjük, a vendég operációs rendszer frissítése RSS-hírcsatorna, de vegye figyelembe, hogy jelentett idő egy hozzávetőleges értéknek kell lennie. Azt is vegye figyelembe, hogy ez problémát jelent az ügyfelek, és dolgozunk azon korlátozása vagy a pontos idő újraindítások megtervezésével.

Legutóbbi vendég operációs rendszer frissítéseivel kapcsolatos részleteket lásd: [Azure vendég operációs rendszer kiadásai és SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).

Az újraindítások és a Vendég és gazdagép operációs rendszerének frissítések technikai részleteket mutató hivatkozások hasznos információ a blogbejegyzésből MSDN [szerepkör példány újraindítása miatt operációs rendszer Verziófrissítései](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Miért nem a saját felhőszolgáltatás után a szolgáltatás egy ideig inaktív volt az első kérelem szokásosnál?
Amikor a webalkalmazás-kiszolgáló az első kérést kap, először újrafordítja a kódot, és ezután feldolgozza a kérést. Ezért az első kérelem hosszabb időt vesz igénybe, mint a többi. Alapértelmezés szerint az alkalmazáskészlet felhasználói inaktivitás esetekben lekérdezi leállítása. Az alkalmazáskészlet 1,740 percenként (29 óra) is alapértelmezés szerint fog újrahasznosítása.

Lehet, hogy a készletek rendszeres időközönként újrahasznosít instabil állapotok szolgáló alkalmazás elkerülése érdekében az Internet Information Services (IIS) alkalmazás összeomlik, lefagy, vagy a memória elveszít.

A következő dokumentumok segít megismerni, és a probléma megoldásához:
* [Az IIS lassú kezdeti betöltés kijavítása](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webes alkalmazás első kérés a partíciófeldolgozást alkalmazáskészlet újrahasznosítás után](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Ha azt szeretné, módosíthatja az IIS alapértelmezett viselkedését, meg kell használnia az indítási feladatok, mert ha manuálisan, a webes szerepkör példányai alkalmazza a módosításokat, a módosítások végül elvesznek.

További információkért lásd: [konfigurálása és a egy felhőalapú szolgáltatás indítási feladatok futtatásának](cloud-services-startup-tasks.md).
