---
title: "Linux virtuális gép pufferallokációs hibák elhárítása |} Microsoft Docs"
description: "Hozzon létre, újraindításakor vagy átméretezésekor egy Linux virtuális Gépet az Azure-ban fellépő lefoglalási hibák elhárítása"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 626968c463d76abe6becaa85813336567f108d0d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Hozzon létre, újraindításakor vagy átméretezésekor Linux virtuális gépek Azure-ban fellépő lefoglalási hibák elhárítása
Hozzon létre egy virtuális Gépet, indítsa újra a leállított (felszabadított) virtuális gépek vagy átméretezni egy virtuális Gépet, a Microsoft Azure előfizetéshez számítási erőforrásokat foglal le. Alkalmanként jelenhet hibák ezeket a műveleteket--az Azure-előfizetésre vonatkozó korlátok eléri előtt is. Ez a cikk ismerteti az egyes a közös pufferallokációs hibák okait, és lehetséges javítási javasol. Az információ is hasznos lehet a szolgáltatásokhoz központi telepítésének tervezése során. Emellett [létrehozása, újraindításakor vagy átméretezésekor Windows-alapú virtuális gépek Azure-ban fellépő lefoglalási hibák elhárítása](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

