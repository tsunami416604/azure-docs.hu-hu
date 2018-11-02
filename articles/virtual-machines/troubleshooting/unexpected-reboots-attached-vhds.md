---
title: Az Azure virtuális gépekhez csatlakoztatott virtuális merevlemezzel rendelkező virtuális gépek váratlan újraindulása hibaelhárítása |} A Microsoft Docs
description: Hogyan háríthatók el a virtuális gépek váratlan újraindulása.
keywords: ssh kapcsolat elutasítva, ssh hiba, az azure ssh, SSH-kapcsolatot nem sikerült
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 6273087e28be8b784168d5808918d04d0e4cf303
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748153"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása hibaelhárítása

Ha egy Azure virtuális gép (VM) egy csatlakoztatott virtuális merevlemezek, ugyanabban a tárfiókban lévő nagy számú, előfordulhat, hogy túllépi a skálázási célértékei egy önálló tárfiókot a virtuális gép váratlanul újraindítását okozza. Ellenőrizze a tárfiók a perc típusú metrikák (**TotalRequests**/**TotalIngress**/**TotalEgress**) az adatforgalmi csúcsokhoz, amelyek túllépik a a tárfiók skálázási célértékei. Lásd: [mérőszámok emelkedő percentthrottlingerror értéket mutatnak](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) meghatározásához, hogy szabályozási történt a tárfiók segítségért.

Általában minden egyes bemeneti vagy kimeneti művelet egy virtuális Merevlemezt a virtuális gépről a rendszer lefordítja arra **első oldal** vagy **Put lap** a mögöttes lapblob műveleteket. Ezért használhatja a környezetnek a becsült IOPS finomhangolása is rendelkezik egy egyetlen tárfiókban alapján, a viselkedést az alkalmazás hány virtuális merevlemezeket. A Microsoft azt javasolja, 40, vagy kevesebb lemezek egyetlen tárfiókban. Lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../../storage/common/storage-scalability-targets.md) tárfiókok skálázási célértékei kapcsolatban különösen a kérelmek száma összesen és a teljes sávszélesség a tárfiók típusát használja.

Ha a tárfiók túllépte a skálázhatósági célokat, helyezze a VHD-k csökkentése érdekében a tevékenység minden egyes fiók több tárfiókra.
