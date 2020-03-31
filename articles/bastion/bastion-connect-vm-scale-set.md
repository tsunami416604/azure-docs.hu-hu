---
title: Csatlakozás Windows virtuálisgép-méretezési csoporthoz az Azure Bastion használatával | Microsoft dokumentumok
description: Ebben a cikkben megtudhatja, hogyan csatlakozhat egy Azure virtuálisgép-méretezési csoporthoz az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988090"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Csatlakozás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával

Ez a cikk bemutatja, hogyan biztonságosan és zökkenőmentesen RDP a Windows virtuális gép méretezési készlet példányát egy Azure virtuális hálózat azure-bastion használatával. Csatlakozhat egy virtuális gép méretezési csoport példányát közvetlenül az Azure Portalon. Az Azure Bastion használatakor a virtuális gépek nem igényelnek ügyfelet, ügynököt vagy további szoftvert. Az Azure Bastionról további információt az [Áttekintés című témakörben talál.](bastion-overview.md)

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállított egy Azure-bastion gazdagépet a virtuális hálózathoz, amelyben a virtuális gép méretezési készlet található. További információ: [Create an Azure Bastion host](bastion-create-host-portal.md). Miután a bastion szolgáltatás kivan építve és üzembe helyezve a virtuális hálózatban, használhatja, hogy csatlakozzon egy virtuális gép méretezési csoport példánya ebben a virtuális hálózatban. A Bástya feltételezi, hogy RDP-t használ a Windows virtuálisgép-méretezési csoporthoz való csatlakozáshoz, és az SSH-t a Linux-virtuálisgép-méretezési készlethez való csatlakozáshoz. A Linux virtuális géphez való csatlakozásról a [Csatlakozás virtuális géphez - Linux](bastion-connect-vm-ssh.md)című témakörben talál további információt.

## <a name="connect-using-rdp"></a><a name="rdp"></a>Csatlakozás rdp használatával

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuálisgép-méretezési készletet, amelyhez csatlakozni szeretne.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Nyissa meg azt a virtuálisgép-méretezési példányt, amelyhez csatlakozni szeretne, majd válassza a **Csatlakozás**lehetőséget. RdP-kapcsolat használata esetén a virtuálisgép-méretezési csoportnak Windows virtuálisgép-méretezési készletnek kell lennie.

   ![virtuális gép méretezési készlete](./media/bastion-connect-vm-scale-set/2.png)
3. Miután **kiválasztotta**a Csatlakozás lehetőséget, megjelenik egy oldalsáv, amely három lapot – RDP, SSH és Bastion . Az oldalsávon válassza a **Bástya** lapot. Ha nem létesített hidasztiszt a virtuális hálózathoz, kiválaszthatja a bástya konfigurálásához. A konfigurációs utasításokat a [Bastion konfigurálása](bastion-create-host-portal.md)című témakörben találja.

   ![Bástya lap](./media/bastion-connect-vm-scale-set/3.png)
4. A Bástya lapon adja meg a virtuálisgép-méretezési csoport felhasználónevét és jelszavát, majd válassza a **Csatlakozás**lehetőséget.

   ![csatlakozásra](./media/bastion-connect-vm-scale-set/4.png)
5. Az RDP-kapcsolat a virtuális gép hez keresztül Bastion nyílik meg közvetlenül az Azure Portalon (HTML5 felett) a 443-as port és a Bástya szolgáltatás használatával.

## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK](bastion-faq.md).