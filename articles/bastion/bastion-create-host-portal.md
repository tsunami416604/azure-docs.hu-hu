---
title: Azure Bastion-gazdagép létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 881d89170c82731fca3f6977da486eac5be772f8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466993"
---
# <a name="create-an-azure-bastion-host-preview"></a>Azure Bastion-gazdagép létrehozása (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózaton, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az üzembe helyezés virtuális hálózatonként értelmezendő, nem pedig előfizetések/fiókok vagy virtuális gépek alapján.

A megerősített gazdagépek erőforrásait kétféleképpen lehet létrehozni:

* Hozzon létre egy megerősített erőforrást a Azure Portal használatával.
* Hozzon létre egy megerősített erőforrást a Azure Portalban meglévő virtuálisgép-beállítások használatával.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió a következő nyilvános Azure-régiókra korlátozódik:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt egy új Azure Bastion-erőforrás létrehozásához a Azure Portal.

1. A [Azure Portal-Bastion Preview](https://aka.ms/BastionHost)kezdőlapján kattintson az **+ erőforrás létrehozása**elemre. Győződjön meg arról, hogy a megadott hivatkozást használja a portál eléréséhez ehhez az előzetes verzióhoz, nem pedig a normál Azure Portal.

1. Az **új** lap *Keresés a piactéren* mezőjébe írja be a következőt: **Bastion**, majd kattintson az **ENTER** gombra a keresési eredmények eléréséhez.

1. Az eredmények között kattintson a **Bastion (előzetes verzió)** elemre. Győződjön meg arról, hogy a közzétevő a *Microsoft* , a kategória pedig *hálózatkezelés*.

1. A **megerősített (előzetes verzió)** lapon a **Létrehozás** gombra kattintva nyissa meg a **Bastion létrehozása** lapot.

1. A **bástya létrehozása** lapon állítson be egy új megerősített erőforrást. Adja meg a megerősített erőforrás konfigurációs beállításait.

    ![megerősített szolgáltatás létrehozása](./media/bastion-create-host-portal/settings.png)

    * **Előfizetés**: Az új megerősített erőforrás létrehozásához használni kívánt Azure-előfizetés.
    * **Erőforráscsoport**: Az az Azure-erőforráscsoport, amelyben az új megerősített erőforrás jön létre. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat.
    * **Név**: Az új megerősített erőforrás neve
    * **Régió**: Az Azure nyilvános régiója, amelyben az erőforrás létrejön.
    * **Virtuális hálózat**: Az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva. A folyamat során létrehozhat egy új virtuális hálózatot a portálon abban az esetben, ha nem rendelkezik meglévő virtuális hálózattal, vagy nem szeretne használni. Ha meglévő virtuális hálózatot használ, ellenőrizze, hogy a meglévő virtuális hálózat rendelkezik-e elegendő szabad hellyel a megerősített alhálózat követelményeinek kielégítéséhez.
    * **Alhálózat**: Annak a virtuális hálózatnak az alhálózata, amelyhez az új megerősített gazdagép erőforrása telepítve lesz. Létre kell hoznia egy alhálózatot a **AzureBastionSubnet**név érték használatával. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Javasoljuk, hogy legalább egy/27 vagy nagyobb alhálózatot használjon (/27,/26 stb.). Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, olvassa el a következőt: a [NSG](bastion-nsg.md)használata.
    * **Nyilvános IP-cím**: Az RDP/SSH elérésére használt megerősített erőforrás nyilvános IP-címe (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
    * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrásának neve.
    * **Nyilvános IP-cím SKU**: Alapértelmezés szerint a **standard**értékre van feltöltve. Az Azure Bastion csak a standard nyilvános IP-SKU-t használja/támogatja.
    * **Hozzárendelés**: Alapértelmezés szerint a **statikus**értékre van feltöltve.

1. Ha végzett a beállítások megadásával, kattintson a **felülvizsgálat + létrehozás**gombra. Ezzel érvényesíti az értékeket. Az ellenőrzés után megkezdheti a létrehozási folyamatot.
1. A bástya létrehozása lapon kattintson a **Létrehozás**gombra.
1. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. A megerősített erőforrás létrehozásához és üzembe helyezéséhez 5 perc szükséges.

## <a name="createvmset"></a>Megerősített gazdagép létrehozása a virtuálisgép-beállítások használatával

Ha egy meglévő virtuális gép használatával hoz létre egy megerősített gazdagépet a portálon, a különböző beállítások automatikusan a virtuális gép és/vagy a virtuális hálózat megfelelő alapértelmezett beállításai lesznek.

1. A [Azure Portal-Bastion előzetes](https://aka.ms/BastionHost)verziójában navigáljon a virtuális géphez, majd kattintson a **Kapcsolódás**elemre.

    ![VM-kapcsolat](./media/bastion-create-host-portal/vmsettings.png)

1. A jobb oldali oldalsávon kattintson a **Bastion**elemre, majd **használja a Bastion**-t.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. A megerősített lapon töltse ki a következő beállítások mezőket:

    * **Név**: A létrehozni kívánt megerősített állomás neve.
    * **Alhálózat**: A virtuális hálózaton belüli alhálózat, amelybe a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. Ez lehetővé teszi, hogy az Azure tudja, melyik alhálózaton helyezi üzembe a megerősített erőforrást. Ez nem más, mint az átjáró alhálózata. Az Azure megerősített alhálózat létrehozásához kattintson az **alhálózat konfigurációjának kezelése** lehetőségre. Javasoljuk, hogy legalább egy/27 vagy nagyobb alhálózatot használjon (/27,/26 stb.). Hozza létre a **AzureBastionSubnet** hálózati biztonsági csoportok, útválasztási táblák vagy delegálások nélkül. A **Létrehozás** gombra kattintva hozza létre az alhálózatot, majd folytassa a következő beállításokkal.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Nyilvános IP-cím**: Az RDP/SSH elérésére használt megerősített erőforrás nyilvános IP-címe (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
    * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrásának neve.
1. Az érvényesítési képernyőn kattintson a **Létrehozás**gombra. Várjon körülbelül 5 percet a megerősített erőforrás létrehozásához és üzembe helyezéséhez.

## <a name="next-steps"></a>További lépések

A [megerősített GYIK](bastion-faq.md) áttekintése
