---
title: Azure Bastion-gazdagép létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: 7838e1930a8ccd190c95740a6a8e3dadc68149f0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422115"
---
# <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózaton, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az üzembe helyezés virtuális hálózatonként értelmezendő, nem pedig előfizetések/fiókok vagy virtuális gépek alapján.

A megerősített gazdagépek erőforrásait kétféleképpen lehet létrehozni:

* Hozzon létre egy megerősített erőforrást a Azure Portal használatával.
* Hozzon létre egy megerősített erőforrást a Azure Portalban meglévő virtuálisgép-beállítások használatával.

## <a name="before-you-begin"></a>Előkészületek

A bástya a következő nyilvános Azure-régiókban érhető el:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt egy új Azure Bastion-erőforrás létrehozásához a Azure Portal.

1. A [Azure Portal](https://portal.azure.com) menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.

1. Az **új** lap *Keresés a piactéren* mezőjébe írja be a következőt: **Bastion**, majd kattintson az **ENTER** gombra a keresési eredmények eléréséhez.

1. Az eredmények között kattintson a **Bastion**elemre. Győződjön meg arról, hogy a közzétevő a *Microsoft* , a kategória pedig *hálózatkezelés*.

1. A **megerősített** lapon a **Létrehozás** gombra kattintva nyissa meg a **megerősített szolgáltatás létrehozása** lapot.

1. A **bástya létrehozása** lapon állítson be egy új megerősített erőforrást. Adja meg a megerősített erőforrás konfigurációs beállításait.

    ![megerősített szolgáltatás létrehozása](./media/bastion-create-host-portal/settings.png)

    * **Előfizetés**: az új megerősített erőforrás létrehozásához használni kívánt Azure-előfizetés.
    * **Erőforráscsoport**: az az Azure-erőforráscsoport, amelyben az új megerősített erőforrás jön létre. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat.
    * **Name (név**): az új megerősített erőforrás neve
    * **Régió**: az Azure nyilvános régiója, amelyhez az erőforrást létre kívánja hozni.
    * **Virtual Network (virtuális hálózat**): az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva. A folyamat során létrehozhat egy új virtuális hálózatot a portálon abban az esetben, ha nem rendelkezik meglévő virtuális hálózattal, vagy nem szeretne használni. Ha meglévő virtuális hálózatot használ, ellenőrizze, hogy a meglévő virtuális hálózat rendelkezik-e elegendő szabad hellyel a megerősített alhálózat követelményeinek kielégítéséhez.
    * **Alhálózat**: a virtuális hálózat azon alhálózata, amelyhez az új megerősített gazdagép erőforrása telepítve lesz. Létre kell hoznia egy alhálózatot a **AzureBastionSubnet**név érték használatával. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Legalább egy/27 vagy nagyobb alhálózat (/27,/26 stb.) alhálózatot kell használnia. Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, olvassa el a következőt: a [NSG](bastion-nsg.md)használata.
    * **Nyilvános IP-cím**: a megerősített erőforrás nyilvános IP-címe, AMELYEN az RDP/SSH elérhető (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
    * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
    * **Nyilvános IP-cím SKU**: alapértelmezés szerint a **standard**értékre van feltöltve. Az Azure Bastion csak a standard nyilvános IP-SKU-t használja/támogatja.
    * **Hozzárendelés**: alapértelmezés szerint a **statikus**értékre van feltöltve.

1. Ha végzett a beállítások megadásával, kattintson a **felülvizsgálat + létrehozás**gombra. Ezzel érvényesíti az értékeket. Az ellenőrzés után megkezdheti a létrehozási folyamatot.
1. A bástya létrehozása lapon kattintson a **Létrehozás**gombra.
1. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. A megerősített erőforrás létrehozásához és üzembe helyezéséhez 5 perc szükséges.

## <a name="createvmset"></a>Megerősített gazdagép létrehozása a virtuálisgép-beállítások használatával

Ha egy meglévő virtuális gép használatával hoz létre egy megerősített gazdagépet a portálon, a különböző beállítások automatikusan a virtuális gép és/vagy a virtuális hálózat megfelelő alapértelmezett beállításai lesznek.

1. Nyissa meg az [Azure portált](https://portal.azure.com). Nyissa meg a virtuális gépet, majd kattintson a **Kapcsolódás**elemre.

   ![VM-kapcsolat](./media/bastion-create-host-portal/vmsettings.png)
1. A jobb oldali oldalsávon kattintson a **Bastion**elemre, majd **használja a Bastion**-t.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. A megerősített lapon töltse ki a következő beállítások mezőket:

   * **Name (név**): a létrehozni kívánt megerősített állomás neve.
   * **Alhálózat**: a virtuális hálózaton belüli alhálózat, amelyre a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. Ez lehetővé teszi, hogy az Azure tudja, melyik alhálózaton helyezi üzembe a megerősített erőforrást. Ez nem más, mint az átjáró alhálózata. Az Azure megerősített alhálózat létrehozásához kattintson az **alhálózat konfigurációjának kezelése** lehetőségre. Javasoljuk, hogy legalább egy/27 vagy nagyobb alhálózatot használjon (/27,/26 stb.). Hozza létre a **AzureBastionSubnet** hálózati biztonsági csoportok, útválasztási táblák vagy delegálások nélkül. A **Létrehozás** gombra kattintva hozza létre az alhálózatot, majd folytassa a következő beállításokkal.
   * **Nyilvános IP-cím**: a megerősített erőforrás nyilvános IP-címe, AMELYEN az RDP/SSH elérhető (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
   * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
1. Az érvényesítési képernyőn kattintson a **Létrehozás**gombra. Várjon körülbelül 5 percet a megerősített erőforrás létrehozásához és üzembe helyezéséhez.

## <a name="next-steps"></a>Következő lépések

A [megerősített GYIK](bastion-faq.md) áttekintése