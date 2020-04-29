---
title: Azure Firewall távoli munkahelyi támogatás
description: Ez a cikk bemutatja, hogyan támogathatja a Azure Firewall a távoli munkahelyi kényszerített követelményeket.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289640"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall távoli munkahelyi támogatás

A Azure Firewall felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely az Azure-beli virtuális hálózati erőforrásokat védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel. 

## <a name="firewall-rules"></a>Tűzfalszabályok

A Azure Firewall használatával biztonságossá teheti a virtuális asztali infrastruktúra (VDI) bejövő RDP-hozzáférését az Azure-beli virtuális hálózathoz Azure Firewall [DNAT-szabályok](rule-processing.md)segítségével. A Windows rendszerű virtuális asztal (WVD) nem igényli a virtuális hálózatra irányuló bejövő hozzáférés megnyitását. A virtuális hálózatban futó WVD virtuális gépekhez azonban engedélyeznie kell a kimenő hálózati kapcsolatok készletét. További információ: [Mi a Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

Ezt a kimenő hozzáférést Azure Firewall alkalmazás-szabályok használatával konfigurálhatja. További információ: [oktatóanyag: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>További lépések

További információ a [Windows rendszerű virtuális asztali környezetről](https://docs.microsoft.com/azure/virtual-desktop/).