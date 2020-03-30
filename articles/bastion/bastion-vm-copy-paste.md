---
title: 'Másolás és beillesztés virtuális gépre és onnan: Azure-bástya'
description: Ebben a cikkben megtudhatja, hogyan másolhat és illesztett be egy Azure-beli virtuális gépba a Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989535"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Másolás és beillesztés egy virtuális gépre: Azure-bástya

Ez a cikk segít a virtuális gépekre és virtuális gépekről a szöveg másolásában és beillesztésében az Azure Bastion használatakor. Mielőtt egy virtuális gép, győződjön meg arról, hogy követte a lépéseket, hogy [hozzon létre egy bástya állomás.](bastion-create-host-portal.md) Ezután csatlakozzon ahhoz a virtuális géphez, amelyet [rdp](bastion-connect-vm-rdp.md) vagy [SSH](bastion-connect-vm-ssh.md)használatával szeretne használni.

A speciális vágólap API-hozzáférést támogató böngészők esetében a helyi eszköz és a távoli munkamenet közötti szövegmásolást és beillesztést ugyanúgy másolhatja és illesztheti be a helyi eszközön lévő alkalmazások között. Más böngészők ben használhatja a Bástya vágólap hozzáférési eszköz palettáját.

   ![Vágólap engedélyezése](./media/bastion-vm-manage/allow.png)

Csak a szöveg másolása/beillesztés támogatott. Közvetlen másolás és beillesztés esetén a böngésző a bástya-munkamenet inicializálásakor kérheti a vágólap elérését. **A** weblap elérésének engedélyezése a vágólapra.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Másolás távoli munkamenetbe

Miután csatlakozott a virtuális géphez az [Azure Portal ](https://portal.azure.com)használatával, hajtsa végre az alábbi lépéseket:

1. Másolja a szöveget/tartalmat a helyi eszközről a helyi vágólapra.
1. A távoli munkamenet során indítsa el a Bastion vágólap hozzáférési eszközpalettáját a két nyíl kiválasztásával. A nyilak a munkamenet bal közepén találhatók.

   ![eszközpaletta](./media/bastion-vm-manage/left.png)

   ![Vágólap](./media/bastion-vm-manage/clipboard.png)
1. A másolt szöveg általában automatikusan megjelenik a Bastion copy paste palettán. Ha a szöveg nincs ott, illessze be a szöveget a paletta szövegterületén.
1. Miután a szöveg a szövegterületen van, beillesztheti a távoli munkamenetbe.

   ![Tészta](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Másolás távoli munkamenetből

Miután csatlakozott a virtuális géphez az [Azure Portal ](https://portal.azure.com)használatával, hajtsa végre az alábbi lépéseket:

1. Másolja a szöveget/tartalmat a távoli munkamenetből a távoli vágólapra (a Ctrl-C billentyűkombinációval).

   ![eszközpaletta](./media/bastion-vm-manage/remote.png)
1. A távoli munkamenet során indítsa el a Bastion vágólap hozzáférési eszközpalettáját a két nyíl kiválasztásával. A nyilak a munkamenet bal közepén találhatók.

   ![Vágólap](./media/bastion-vm-manage/clipboard2.png)
1. A másolt szöveg általában automatikusan megjelenik a Bastion copy paste palettán. Ha a szöveg nincs ott, illessze be a szöveget a paletta szövegterületén.
1. Miután a szöveg a szövegterületen van, beillesztheti a helyi eszközre.

   ![Tészta](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK](bastion-faq.md).