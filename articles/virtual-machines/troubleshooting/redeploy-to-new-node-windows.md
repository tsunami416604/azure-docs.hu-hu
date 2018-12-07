---
title: Ismételt üzembe helyezése az Azure Windows virtual machines |} A Microsoft Docs
description: Hogyan lehet RDP-kapcsolatok problémáinak megoldásához az Azure-beli Windows virtuális gépek ismételt üzembe.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c0bf4d1684ca5a0921173dc90f51fec9fb052efc
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993260"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Új Azure csomópontra Windows virtuális gép ismételt üzembe helyezése
Ha Ön rendelkezik lett nehézségekkel hibaelhárítás a távoli asztali (RDP) kapcsolatot vagy alkalmazáshoz való hozzáférés Windows-alapú Azure virtuális gépek (VM), a virtuális gép újbóli segítségére lehetnek. Egy virtuális Gépet, újbóli telepítése során a virtuális Gépet leállítja az Azure a virtuális gép áthelyezése egy másik csomópontra az Azure infrastruktúráján belül, és ezután kapcsolja be újra, a konfigurációs beállításokat és a kapcsolódó erőforrások megőrzése. Ez a cikk bemutatja, hogyan újratelepíteni a virtuális gép az Azure PowerShell vagy az Azure portal használatával.

> [!NOTE]
> Miután egy virtuális gép újbóli telepítése, az ideiglenes lemez elvesztését, és dinamikus, virtuális hálózati adapterhez társított IP-címek frissülnek. 


## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ellenőrizze, hogy az Azure PowerShell legújabb verzióját 1.x telepítve van a gépén. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

Az alábbi példa telepíti a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, találhat segítséget [RDP-kapcsolatok hibaelhárításának](troubleshoot-rdp-connection.md) vagy [hibaelhárítási lépések az RDP részletes](detailed-troubleshoot-rdp.md). Ha a virtuális Gépen futó alkalmazásokhoz nem elérhető, is olvashatja [kapcsolatos hibák elhárítása alkalmazás](../windows/troubleshoot-app-connection.md).

