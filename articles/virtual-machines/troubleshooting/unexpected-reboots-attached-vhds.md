---
title: A csatlakoztatott virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindítása az Azure-beli virtuális gépeken | Microsoft Docs
description: A virtuális gépek váratlan újraindításának megoldása.
keywords: SSH-kapcsolatok elutasítása, SSH-hiba, Azure SSH, SSH-kapcsolatok nem sikerült
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086865"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>A csatlakoztatott virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindításával kapcsolatos hibák

Ha egy Azure-beli virtuális gép (VM) nagy számú csatlakoztatott virtuális Merevlemezrel rendelkezik, amelyek ugyanabban a Storage-fiókban találhatók, akkor előfordulhat, hogy túllépi az egyes Storage-fiókok méretezhetőségi céljait, ami miatt a virtuális gép váratlanul újraindul. Keresse meg a Storage-fiók (**TotalRequests**/**TotalIngress**/**TotalEgress**) perc mérőszámait a tárolási fiók skálázhatósági céljait meghaladó tüskéknél. A [metrikák](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) című témakörben megtudhatja, hogy a mérőszámok a percentthrottlingerror értéket mutatnak megtörtént-e a szabályozás során.

Általánosságban elmondható, hogy minden egyes bemeneti vagy kimeneti művelet egy virtuális MEREVLEMEZről lefordítja a **lap beolvasását** vagy az **oldal** műveleteit az alapul szolgáló oldal blobján. Ezért a környezete becsült IOPS használatával beállíthatja, hogy az alkalmazás konkrét viselkedése alapján hány VHD-t használhat egyetlen Storage-fiókban. A Microsoft azt javasolja, hogy a 40 vagy kevesebb lemez legyen egyetlen Storage-fiókban. Tekintse meg az [Azure Storage skálázhatósági és teljesítményi célkitűzéseit](../../storage/common/storage-scalability-targets.md) a Storage-fiókok méretezhetőségi céljaival kapcsolatos részletekért, különösen a teljes kérelmek arányát és a használt Storage-fiókhoz tartozó teljes sávszélességet.

Ha túllépi a tárolási fiók skálázhatósági céljait, helyezze a virtuális merevlemezeket több Storage-fiókba, hogy csökkentse a tevékenységeket az egyes fiókokban.
