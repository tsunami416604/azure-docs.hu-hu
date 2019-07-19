---
title: Windows Azure-beli virtuális gépek közös elhelyezése | Microsoft Docs
description: Ismerje meg, hogyan javíthatja a késést az Azure-beli virtuális gépek erőforrásainak közös elhelyezése.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: a6360e1ee8469f8674685f5975ec09db3e87a4ea
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850443"
---
# <a name="co-locate-resource-for-improved-latency"></a>Erőforrás közös keresése a jobb késés érdekében

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 


## <a name="preview-proximity-placement-groups"></a>Előzetes verzió: Proximity elhelyezési csoportok 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Helyezzen üzembe egy virtuális gépet egy [közeli elhelyezési csoportba](proximity-placement-groups.md) Azure PowerShell használatával.

