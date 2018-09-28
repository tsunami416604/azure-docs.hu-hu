---
title: Ismételt üzembe helyezése Linux rendszerű virtuális gépek az Azure-ban |} A Microsoft Docs
description: Hogyan ismételt üzembe helyezése Linux rendszerű virtuális gépek az Azure-ban az SSH-kapcsolati problémák elhárításához.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: genli
ms.openlocfilehash: 8f8cab985ebc20290a94df34a9268ad2c1f169d3
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413851"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ismételt üzembe helyezése Linux rendszerű virtuális gépet az új Azure csomópontra
Ha SSH hibaelhárítási nehézségek között, vagy segítségére lehetnek az alkalmazás elérését egy Linux rendszerű virtuális gép (VM) az Azure-ban, a virtuális gép újratelepítése. Ha egy virtuális gép újbóli telepítése, a virtuális gép áthelyezése egy másik csomópontra az Azure infrastruktúráján belül, és majd használja újra. A konfigurációs beállításokat és a kapcsolódó erőforrásokat megmaradnak. Ez a cikk bemutatja, hogyan újratelepíteni a virtuális gép Azure parancssori felület vagy az Azure portal használatával.

> [!NOTE]
> Miután egy virtuális gép újbóli telepítése, az ideiglenes lemez elvesztését, és dinamikus, virtuális hálózati adapterhez társított IP-címek frissülnek. 


## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Telepítse a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Ismételt üzembe helyezése virtuális Gépen való [az vm redeploy](/cli/azure/vm#az_vm_redeploy). Az alábbi példa ismét üzembe helyezi a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>A klasszikus Azure parancssori felületének használata
Telepítse a [legújabb Azure klasszikus parancssori felület](../../cli-install-nodejs.md) , és jelentkezzen be az Azure-fiókjával. Győződjön meg arról, hogy biztosan megfeleljen az erőforrás-kezelő módban (`azure config mode arm`).

Az alábbi példa ismét üzembe helyezi a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, találhat segítséget [SSH-kapcsolatok hibaelhárításának](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [részletes hibaelhárítási lépések az SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ha a virtuális Gépen futó alkalmazásokhoz nem elérhető, is olvashatja [kapcsolatos hibák elhárítása alkalmazás](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


