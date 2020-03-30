---
title: Gyakori kérdések az alkalmazások és szolgáltatások rendelkezésre állásával kapcsolatos kérdésekről
titleSuffix: Azure Cloud Services
description: Ez a cikk a Microsoft Azure Cloud Services alkalmazás- és szolgáltatáselérhetőségével kapcsolatos gyakori kérdéseket sorolja fel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386918"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Alkalmazás- és szolgáltatáselérhetőségi problémák az Azure Cloud Services szolgáltatáshoz: gyakori kérdések (gyakori kérdések)

Ez a cikk a Microsoft Azure Cloud Services alkalmazás- és szolgáltatáselérhetőségével kapcsolatos gyakori kérdéseket [tartalmazza.](https://azure.microsoft.com/services/cloud-services) A [felhőszolgáltatások virtuális gép méretlapján](cloud-services-sizes-specs.md) is megtekintheti a méretadatokat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A szerepem újrahasznosult. Volt-e frissítés a felhőszolgáltatásomhoz?
Nagyjából havonta egyszer a Microsoft kiadja a Windows Azure PaaS virtuális gépek új vendég operációsrendszer-verzióját.A vendég operációs rendszer csak egy ilyen frissítés a folyamatban. A kiadást számos más tényező is befolyásolhatja. Emellett az Azure több százezer gépen fut. Ezért lehetetlen megjósolni a pontos dátumot és időt, amikor a szerepkörök újraindul. Frissítjük a vendég operációs rendszer frissítése RSS-hírcsatorna a legújabb információkat, hogy van, de érdemes figyelembe venni, hogy a jelentett idő, hogy egy hozzávetőleges érték. Tisztában vagyunk azzal, hogy ez problémás az ügyfelek számára, és dolgoznak egy tervet, hogy korlátozzák, vagy pontosan idő újraindítás.

A vendég operációs rendszer legutóbbi frissítéseiről az [Azure Vendég operációs rendszer kiadásai és az SDK-kompatibilitási mátrix](cloud-services-guestos-update-matrix.md)című témakörben talál részletesinformációt.

Az újraindításokkal kapcsolatos hasznos információkért és a vendég- és gazdarendszer-frissítések technikai részleteire mutató információkért tekintse meg az MSDN blogbejegyzés [szerepkörpéldány újraindítását az operációs rendszer frissítése miatt.](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Miért tart a szokásosnál hosszabb ideig a felhőszolgáltatásnak a felhőszolgáltatáshoz intézett első kérés?
Amikor a webkiszolgáló megkapja az első kérést, először újrafordítja a kódot, majd feldolgozza a kérelmet. Ezért tart tovább az első kérés, mint a többi. Alapértelmezés szerint az alkalmazáskészlet leáll felhasználói inaktivitás esetén. Az alkalmazáskészlet alapértelmezés szerint 1740 percenként (29 óránként) is újrahasznosítja a programot.

Az Internet Information Services (IIS) alkalmazáskészletei rendszeres időközönként újrahasznosíthatók, hogy elkerüljék az alkalmazások összeomlásához, lefagyásához vagy memóriavesztéshez vezető instabil állapotokat.

A következő dokumentumok segítenek megérteni és enyhíteni ezt a problémát:
* [Lassú kezdeti terhelés rögzítése az IIS-hez](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webalkalmazás első kérelem után app-pool újrahasznosítás nagyon lassú](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Ha módosítani szeretné az IIS alapértelmezett viselkedését, indítási feladatokat kell használnia, mert ha manuálisan alkalmazza a webes szerepkör példányai módosításait, a módosítások végül elvesznek.

További információt a [Felhőszolgáltatások indítási feladatainak konfigurálása és futtatása](cloud-services-startup-tasks.md)című témakörben talál.
