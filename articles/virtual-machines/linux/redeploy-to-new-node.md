---
title: "Telepítse újra a Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Hogyan újratelepíteni a Linux virtuális gépek SSH-kapcsolati problémák elhárítása érdekében az Azure-ban."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.openlocfilehash: 7bf69b2a3c006faa0dc0144313e5ebb64e941e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Telepítse újra a Linux virtuális gép új Azure csomópont
Ha Ön szembesülhetnek SSH hibaelhárítási problémák vagy segíthet a Linux virtuális gép (VM) Azure, a virtuális gép újbóli alkalmazás eléréséhez. A virtuális gép újbóli telepítésének, a virtuális gép áthelyezése az Azure-infrastruktúra belül egy másik csomópontra, és majd bekapcsolja azt vissza. A konfigurációs beállításokat és a kapcsolódó erőforrások jelennek meg. Ez a cikk bemutatja, hogyan telepítse újra a virtuális gépek Azure CLI vagy az Azure-portál használatával.

> [!NOTE]
> Után a virtuális gép újbóli telepítésének, az ideiglenes lemez, és dinamikus, virtuális hálózati interfészhez társított IP-címek frissülnek. 

Egy virtuális Gépet a következő lehetőségek egyikének használatával központilag telepítheti. Csak ki kell választani. újratelepíteni a virtuális Gépet egy lehetőséget:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Az Azure parancssori felület használatával 2.0
Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/#login).

Telepítse újra a virtuális Gépet a [az vm helyezze üzembe újra](/cli/azure/vm#redeploy). Az alábbi példa redeploys nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Az Azure parancssori felület használatával 1.0
Telepítse a [Azure CLI legújabb 1.0](../../cli-install-nodejs.md), jelentkezzen be Azure-fiókkal, és győződjön meg arról, hogy éppen erőforrás-kezelő módban (`azure config mode arm`).

Az alábbi példa redeploys nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Következő lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, található segítséget [SSH-kapcsolatok hibáinak elhárítása](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [részletes hibaelhárítási SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ha nem fér hozzá a virtuális gép futó alkalmazást, is olvasható [problémák elhárítása alkalmazás](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

