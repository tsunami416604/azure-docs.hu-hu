---
title: Az Azure Firewall távoli munkahelyi támogatása
description: Ez a cikk bemutatja, hogy az Azure Firewall hogyan támogatja a távoli munkaerő-követelmények.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289640"
---
# <a name="azure-firewall-remote-work-support"></a>Az Azure Firewall távoli munkahelyi támogatása

Az Azure Firewall egy felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely védi az Azure virtuális hálózati erőforrásait. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel. 

## <a name="firewall-rules"></a>Tűzfalszabályok

Az Azure Firewall segítségével az Azure Firewall [DNST-szabályaival](rule-processing.md)biztosíthatja a virtuális asztali infrastruktúra (VDI) bejövő RDP-hozzáférését az Azure virtuális hálózatához. A Windows virtuális asztal (WVD) nem követeli meg a virtuális hálózathoz való bejövő hozzáférés megnyitását. A virtuális hálózatban futó WVD virtuális gépek számára azonban engedélyeznie kell a kimenő hálózati kapcsolatok készletét. További információ: [Mi a Windows virtuális asztal?](../virtual-desktop/overview.md#requirements)

Ezt a kimenő hozzáférést az Azure Firewall alkalmazásszabályok használatával konfigurálhatja. További információ: [Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása az Azure Portalhasználatával.](tutorial-firewall-deploy-portal.md)

## <a name="next-steps"></a>További lépések

További információ a [Windows virtuális asztalról.](https://docs.microsoft.com/azure/virtual-desktop/)