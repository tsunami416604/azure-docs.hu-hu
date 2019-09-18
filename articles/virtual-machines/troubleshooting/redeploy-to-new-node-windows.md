---
title: Windows rendszerű virtuális gépek újbóli üzembe helyezése az Azure-ban | Microsoft Docs
description: Windows rendszerű virtuális gépek újbóli üzembe helyezése az Azure-ban az RDP-kapcsolatok problémáinak enyhítése érdekében.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058627"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Windows rendszerű virtuális gép újratelepítése új Azure-csomópontra
Ha problémába ütközik a Windows-alapú Azure-beli virtuális géphez (VM) való Távoli asztal-(RDP-) kapcsolat vagy alkalmazás-hozzáférés hibaelhárítása során, akkor a virtuális gép újbóli üzembe helyezése segíthet. Ha újratelepít egy virtuális gépet, az Azure leállítja a virtuális gépet, áthelyezi a virtuális gépet az Azure-infrastruktúra egy új csomópontjára, majd újra bekapcsolja, megtartva az összes konfigurációs beállítást és a kapcsolódó erőforrásokat. Ez a cikk bemutatja, hogyan telepítheti újra a virtuális gépeket Azure PowerShell vagy a Azure Portal használatával.

> [!NOTE]
> A virtuális gép újbóli üzembe helyezése után az ideiglenes lemez elvész, és a rendszer frissíti a virtuális hálózati adapterhez társított dinamikus IP-címeket. 


## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Győződjön meg arról, hogy a legújabb Azure PowerShell 1. x telepítve van a gépen. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

A következő példa telepíti a nevű virtuális gépet `myVM` a nevű `myResourceGroup`erőforráscsoport-csoportba:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>További lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, az [RDP-kapcsolatok hibaelhárításával](troubleshoot-rdp-connection.md) és a [részletes RDP-hibaelhárítási lépésekkel](detailed-troubleshoot-rdp.md)kapcsolatos súgóban talál. Ha nem fér hozzá a virtuális gépen futó alkalmazáshoz, az [alkalmazások hibaelhárításával kapcsolatos problémákat](../windows/troubleshoot-app-connection.md)is elolvashatja.

