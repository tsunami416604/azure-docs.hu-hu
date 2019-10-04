---
title: Linux Azure-beli virtuális gépek közös elhelyezése | Microsoft Docs
description: Ismerje meg, hogyan javíthatja a késést az Azure-beli virtuális gépek erőforrásainak közös elhelyezése.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: 81b9dc5059a6ab3e8245acd9c7e7ef8be5abdafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083869"
---
# <a name="co-locate-resources-for-improved-latency"></a>Erőforrások közös elhelyezése a jobb késés érdekében

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

## <a name="preview-proximity-placement-groups"></a>Előzetes verzió: Proximity elhelyezési csoportok

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Helyezzen üzembe egy virtuális gépet [](proximity-placement-groups.md) az Azure CLI használatával a közelségi elhelyezési csoportba.

