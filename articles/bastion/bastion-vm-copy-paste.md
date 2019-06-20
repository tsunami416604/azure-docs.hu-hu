---
title: 'Másolja és illessze be a és a virtuális gépről: Az Azure megerősített |} A Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan másolja és illessze be a és a megerősített használata Azure virtuális gépből.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191810"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Másolja és illessze be a virtuális géphez: Az Azure megerősített (előzetes verzió)

Ez a cikk segít másolja és illessze be a szöveget, és a virtuális gépek Azure megerősített használatakor. Egy virtuális gép használata előtt győződjön meg arról, hogy követte a lépéseket [hozzon létre egy bástyagazdagép](bastion-create-host-portal.md). Ezután csatlakozzon a virtuális Géphez, amelyek segítségével a használni kívánt [RDP](bastion-connect-vm-rdp.md) vagy [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

A speciális vágólapra API-hozzáférést támogató böngészőkben másolja, és illessze be a szöveget a helyi eszköz és a távoli munkamenet között, közötti másolást és beillesztést a helyi eszközön alkalmazások ugyanúgy. Más böngészők használhatja a megerősített vágólapra hozzáférés eszköz palettáját.

  ![A vágólap engedélyezése](./media/bastion-vm-manage/allow.png)

Csak szöveg másolási és beillesztési használata támogatott. A közvetlen másolás és beillesztés a böngésző kérheti a vágólap hozzáférésének a megerősített munkamenet inicializálásakor. **Lehetővé teszi** a weblap a vágólap elérésére.

## <a name="to"></a>A távoli munkamenet másolása

A virtuális gépet a csatlakozást követően a [az Azure portal](https://aka.ms/BastionHost) a megerősített előzetes verzió:

1. A szöveges tartalom másolja vágólapra a helyi eszközről.
1. A távoli munkamenet során indítsa el a két nyíl kiválasztásával a megerősített vágólapra hozzáférés eszköz palettáját. A nyilak a munkamenet a bal oldalon középen találhatók.

    ![paletta eszköz](./media/bastion-vm-manage/left.png)

    ![a vágólap](./media/bastion-vm-manage/clipboard.png)

1. A másolt szöveget általában automatikusan megjeleníti a megerősített másolás-beillesztés palettán. Ha a szöveg nem létezik, majd illessze be a szöveget a terület a palettán.
1. Ha a szöveget szöveges területén, illessze be azt a távoli munkamenethez.

    ![Beillesztés](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Másolja át a távoli munkamenet

A virtuális gépet a csatlakozást követően a [az Azure portal](https://aka.ms/BastionHost) a megerősített előzetes verzió:

1. A szöveges tartalom másolása a távoli munkamenet távoli vágólapra (Ctrl + C használatával).

    ![paletta eszköz](./media/bastion-vm-manage/remote.png)

1. A távoli munkamenet során indítsa el a két nyíl kiválasztásával a megerősített vágólapra hozzáférés eszköz palettáját. A nyilak a munkamenet a bal oldalon középen találhatók.

    ![a vágólap](./media/bastion-vm-manage/clipboard2.png)

1. A másolt szöveget általában automatikusan megjeleníti a megerősített másolás-beillesztés palettán. Ha a szöveg nem létezik, majd illessze be a szöveget a terület a palettán.
1. Ha a szöveget szöveges területén, illessze be azt a helyi eszközre.

    ![Beillesztés](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített – gyakori kérdések](bastion-faq.md).