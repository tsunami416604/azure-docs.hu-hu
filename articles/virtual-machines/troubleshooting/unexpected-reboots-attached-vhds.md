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
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75358526"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>A csatlakoztatott virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindításával kapcsolatos hibák

Ha egy Azure-beli virtuális gép (VM) nagy számú csatlakoztatott virtuális Merevlemezrel rendelkezik, amelyek ugyanabban a Storage-fiókban találhatók, akkor előfordulhat, hogy túllépi az egyes Storage-fiókok méretezhetőségi céljait, ami miatt a virtuális gép váratlanul újraindul. Keresse meg a Storage-fiók (**TotalRequests** / **TotalIngress** / **TotalEgress**) perc mérőszámait a tárolási fiók skálázhatósági céljait meghaladó tüskéknél. A [metrikák](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) című témakörben megtudhatja, hogy a mérőszámok a percentthrottlingerror értéket mutatnak megtörtént-e a szabályozás során.

Általánosságban elmondható, hogy minden egyes bemeneti vagy kimeneti művelet egy virtuális MEREVLEMEZről lefordítja a **lap beolvasását** vagy az **oldal** műveleteit az alapul szolgáló oldal blobján. Ezért a környezete becsült IOPS használatával beállíthatja, hogy az alkalmazás konkrét viselkedése alapján hány VHD-t használhat egyetlen Storage-fiókban. A Microsoft azt javasolja, hogy a 40 vagy kevesebb lemez legyen egyetlen Storage-fiókban. A standard szintű Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](../../storage/common/scalability-targets-standard-account.md)ismertető témakört. A prémium szintű blob Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd: [a prémium szintű oldal blob Storage-fiókok méretezhetőségi célpontjai](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Ha túllépi a tárolási fiók skálázhatósági céljait, helyezze a virtuális merevlemezeket több Storage-fiókba, hogy csökkentse a tevékenységeket az egyes fiókokban.
