---
title: 'Másolás és beillesztés egy virtuális gépről: Azure Bastion'
description: Ebből a cikkből megtudhatja, hogyan másolhat és illeszthet be egy Azure-beli virtuális gépről a Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989535"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Másolás és beillesztés virtuális gépre: Azure Bastion

Ebből a cikkből megtudhatja, hogyan másolhat és illeszthet be szöveget virtuális gépekre az Azure Bastion használatakor. A virtuális gép használata előtt győződjön meg arról, hogy követte a [megerősített gazdagép létrehozásának](bastion-create-host-portal.md)lépéseit. Ezután csatlakozzon ahhoz a virtuális géphez, amelyet [RDP](bastion-connect-vm-rdp.md) vagy [SSH](bastion-connect-vm-ssh.md)használatával szeretne használni.

A vágólap speciális API-hozzáférését támogató böngészők esetében a helyi eszköz és a távoli munkamenet között ugyanúgy másolhatja és illesztheti be a szöveget, ahogyan a helyi eszközön lévő alkalmazások között másol és beilleszti. Más böngészők esetében használhatja a megerősített vágólap-hozzáférési eszköz palettáját.

   ![Vágólap engedélyezése](./media/bastion-vm-manage/allow.png)

Csak a szöveg másolása/beillesztése támogatott. A közvetlen másolás és beillesztés esetében előfordulhat, hogy a böngésző a megerősített munkamenet inicializálásakor kéri a vágólap-hozzáférés megadását. A vágólap elérésének **engedélyezése** a weblap számára.

## <a name="to"></a>Másolás távoli munkamenetbe

Miután a [Azure Portal ](https://portal.azure.com)használatával kapcsolódott a virtuális géphez, hajtsa végre a következő lépéseket:

1. Másolja a szöveget/tartalmat a helyi eszközről a helyi vágólapra.
1. A távoli munkamenet során a két nyíl kiválasztásával indítsa el a megerősített vágólap-hozzáférési eszköz palettáját. A nyilak a munkamenet bal középső részén találhatók.

   ![eszközpaletta](./media/bastion-vm-manage/left.png)

   ![vágólapra](./media/bastion-vm-manage/clipboard.png)
1. A másolt szöveg általában automatikusan megjelenik a bástya másolási beillesztési palettáján. Ha a szöveg nem létezik, illessze be a szöveget a paletta szöveg területén.
1. Miután a szöveg szerepel a szövegmezőben, beillesztheti a távoli munkamenetbe.

   ![illessze be](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Másolás távoli munkamenetből

Miután a [Azure Portal ](https://portal.azure.com)használatával kapcsolódott a virtuális géphez, hajtsa végre a következő lépéseket:

1. Másolja a távoli munkamenet szövegét/tartalmát a távoli vágólapra (a CTRL-C használatával).

   ![eszközpaletta](./media/bastion-vm-manage/remote.png)
1. A távoli munkamenet során a két nyíl kiválasztásával indítsa el a megerősített vágólap-hozzáférési eszköz palettáját. A nyilak a munkamenet bal középső részén találhatók.

   ![vágólapra](./media/bastion-vm-manage/clipboard2.png)
1. A másolt szöveg általában automatikusan megjelenik a bástya másolási beillesztési palettáján. Ha a szöveg nem létezik, illessze be a szöveget a paletta szöveg területén.
1. Miután a szöveg szerepel a szövegmezőben, beillesztheti azt a helyi eszközre.

   ![illessze be](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.