---
title: "Telepítse újra a Windows virtuális gépek Azure-ban |} Microsoft Docs"
description: "Hogyan kell telepíteni a Windows virtuális gépek RDP kapcsolati problémák elhárítása érdekében az Azure-ban."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 1a171aa07160fd68e3ee73a1606ae048e8e6e1b8
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Új Azure csomópontra Windows virtuális gép újbóli üzembe helyezése
Ha Ön rendelkezik lett nehézségekkel hibaelhárítás a távoli asztal (RDP) kapcsolat vagy alkalmazás elérésére Windows-alapú Azure virtuális gép (VM), a virtuális gép újbóli segíthet. A virtuális gép újbóli telepítésének, a virtuális gép áthelyezése az Azure-infrastruktúra belül egy másik csomópontra, és majd bekapcsolja azt vissza, a konfigurációs beállításokat és a kapcsolódó erőforrások megőrzése. Ez a cikk bemutatja, hogyan telepítse újra a virtuális gépek Azure PowerShell vagy az Azure-portál használatával.

> [!NOTE]
> Után a virtuális gép újbóli telepítésének, az ideiglenes lemez, és dinamikus, virtuális hálózati interfészhez társított IP-címek frissülnek. 


## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Győződjön meg arról, hogy a legújabb Azure PowerShell 1.x van telepítve a számítógépre. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

A következő példa telepíti a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Következő lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, található segítséget [RDP-kapcsolatok hibáinak elhárítása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [részletes hibaelhárítási RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ha nem fér hozzá a virtuális gép futó alkalmazást, is olvasható [problémák elhárítása alkalmazás](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

