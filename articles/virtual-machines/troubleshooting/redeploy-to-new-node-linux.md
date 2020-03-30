---
title: Linux os virtuális gépek újratelepítése az Azure-ban | Microsoft dokumentumok
description: Linuxos virtuális gépek újratelepítése az Azure-ban az SSH-kapcsolati problémák csökkentése érdekében.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 18e96f9463176b0fce04252492eea6dbede416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531107"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Linux rendszerű virtuális gép ismételt üzembe helyezése egy új Azure-csomóponton
Ha nehézségekbe ütközik az SSH vagy az alkalmazások Azure-beli virtuális géphez (VM) való hozzáférésének hibaelhárítása során, a virtuális gép újratelepítése segíthet. Virtuális gép újratelepítésekor a virtuális gép egy új csomópontaz Azure-infrastruktúrán belül, és majd bekapcsolja azt. Az összes konfigurációs lehetőség és a kapcsolódó erőforrások megmaradnak. Ez a cikk bemutatja, hogyan helyezheti üzembe újra a virtuális gépeket az Azure CLI vagy az Azure Portal használatával.

> [!NOTE]
> A virtuális gép újratelepítése után az ideiglenes lemez elvész, és a virtuális hálózati adapterhez társított dinamikus IP-címek frissülnek. 


## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Telepítse a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és jelentkezzen be Azure-fiókjába [az az bejelentkezéssel.](/cli/azure/reference-index)

Telepítse újra a virtuális gép [az vm újratelepítése.](/cli/azure/vm) A következő példa újratelepíti a *myVM* nevű virtuális gép a *myResourceGroup*nevű erőforráscsoportban:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Az Azure klasszikus CLI használata

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Telepítse a [legújabb klasszikus Azure CLI-t,](../../cli-install-nodejs.md) és jelentkezzen be Azure-fiókjába. Győződjön meg arról, hogy`azure config mode arm`Erőforrás-kezelő módban van ( ).

A következő példa újratelepíti a *myVM* nevű virtuális gép a *myResourceGroup*nevű erőforráscsoportban:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>További lépések
Ha problémái vannak a virtuális géphez való csatlakozással, az [SSH-kapcsolatok hibaelhárításával](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kapcsolatos konkrét segítséget vagy [az SSH hibaelhárítási lépéseit](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)találhatja. Ha nem tud hozzáférni a virtuális gépen futó alkalmazáshoz, elolvashatja [az alkalmazáshibaelhárítási problémákat](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)is.


