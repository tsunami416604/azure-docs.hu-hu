---
title: Csatlakoztatott virtuális gépek váratlan újraindításának hibaelhárítása az Azure virtuális gépeken | Microsoft dokumentumok
description: A virtuális gépek váratlan újraindításának elhárítása.
keywords: ssh kapcsolat elutasítva, ssh hiba, azure ssh, SSH kapcsolat nem sikerült
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358526"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>A csatlakoztatott virtuális gépek váratlan újraindításának elhárítása csatlakoztatott virtuális számítógépekkel

Ha egy Azure virtuális gép (VM) rendelkezik nagy számú csatlakoztatott virtuális számítógép, amely ugyanabban a tárfiókban található, előfordulhat, hogy túllépi az egyes tárfiók méretezhetőségi célokat, ami a virtuális gép váratlanul újraindul. Tekintse meg a tárfiók percmetrikák (**TotalRequests**/**TotalIngress**/**TotalEgress**) a kiugrások, amelyek meghaladják a tárfiók méretezhetőségi célokat. Lásd: [Metrikák százalékos throttlingError növekedését jeleníti](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) meg, ha segítséget nyújt annak meghatározásához, hogy a szabályozás történt-e a tárfiókban.

Általában minden egyes bemeneti vagy kimeneti művelet egy virtuális merevlemezen egy virtuális gépről fordítja **a Lap betöltése** vagy **a Lap betöltése** műveleteket az alapul szolgáló lap blob. Ezért a környezet becsült IOPS-használatával beállíthatja, hogy hány virtuális számítógép rendelkezhet egyetlen tárfiókban az alkalmazás adott viselkedése alapján. A Microsoft azt javasolja, hogy egy tárfiókban 40 vagy kevesebb lemez legyen. A szabványos tárfiókok méretezhetőségi céljairól a [szabványos tárfiókok méretezhetőségi céljai című témakörben](../../storage/common/scalability-targets-standard-account.md)talál további információt. A prémium szintű lapblobstorage-fiókok méretezhetőségi céljairól további információt a [prémium szintű lapblobstorage-fiókok méretezhetőségi céljai című témakörben talál.](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Ha túllépi a tárfiók méretezhetőségi céljait, helyezze el a virtuális gépeket több tárfiókban, hogy csökkentse a tevékenységet az egyes fiókokban.
