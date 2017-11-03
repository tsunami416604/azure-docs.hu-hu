---
title: "Windows virtuális gép pufferallokációs hibák elhárítása |} Microsoft Docs"
description: "Hozzon létre, újraindításakor vagy átméretezésekor egy Windows Azure-ban fellépő lefoglalási hibák elhárítása"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2016
ms.author: cjiang
ms.openlocfilehash: 9db1987541f2b64a38e6d0efe1b80b18372ff64e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Hozzon létre, újraindításakor vagy átméretezésekor Windows-alapú virtuális gépek Azure-ban fellépő lefoglalási hibák elhárítása
Hozzon létre egy virtuális Gépet, indítsa újra a leállított (felszabadított) virtuális gépek vagy átméretezni egy virtuális Gépet, a Microsoft Azure előfizetéshez számítási erőforrásokat foglal le. Alkalmanként jelenhet hibák ezeket a műveleteket--az Azure-előfizetésre vonatkozó korlátok eléri előtt is. Ez a cikk ismerteti az egyes a közös pufferallokációs hibák okait, és lehetséges javítási javasol. Az információ is hasznos lehet a szolgáltatásokhoz központi telepítésének tervezése során. Emellett [létrehozása, újraindításakor vagy átméretezésekor Linux virtuális gépek Azure-ban fellépő lefoglalási hibák elhárítása](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

