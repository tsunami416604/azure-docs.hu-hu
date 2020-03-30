---
title: Windows-alapú virtuális gépek újratelepítése az Azure-ban | Microsoft dokumentumok
description: A Windows virtuális gépek újratelepítése az Azure-ban az RDP-kapcsolati problémák csökkentése érdekében.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058627"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Windows rendszerű virtuális gép ismételt üzembe helyezése egy új Azure-csomóponton
Ha nehézségekbe ütközik a Távoli asztali (RDP) kapcsolat vagy a Windows-alapú Azure virtuális gép (VM) alkalmazás-hozzáférésének hibaelhárítása során, a virtuális gép újratelepítése segíthet. Virtuális gép újratelepítésekor az Azure leállítja a virtuális gép, helyezze át a virtuális gép egy új csomópont az Azure-infrastruktúrán belül, majd kapcsolja be újra, megtartva az összes konfigurációs lehetőségek és a kapcsolódó erőforrások. Ez a cikk bemutatja, hogyan helyezheti üzembe újra a virtuális gépeket az Azure PowerShell vagy az Azure Portal használatával.

> [!NOTE]
> A virtuális gép újratelepítése után az ideiglenes lemez elvész, és a virtuális hálózati adapterhez társított dinamikus IP-címek frissülnek. 


## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Győződjön meg arról, hogy a legújabb Azure PowerShell 1.x telepítve van a számítógépen. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

A következő példa telepíti `myVM` a névvel `myResourceGroup`ellátott erőforráscsoportban megnevezett virtuális gép:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>További lépések
Ha problémái vannak a virtuális géphez való csatlakozással, az [RDP-kapcsolatok hibaelhárításával](troubleshoot-rdp-connection.md) kapcsolatos konkrét súgót vagy [az RDP részletes hibaelhárítási lépéseit.](detailed-troubleshoot-rdp.md) Ha nem tud hozzáférni a virtuális gépen futó alkalmazáshoz, elolvashatja [az alkalmazáshibaelhárítási problémákat](../windows/troubleshoot-app-connection.md)is.

