---
title: 'Azure Bastion-gazdagép létrehozása: portál'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Azure-beli megerősített gazdagépet a portál használatával
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366143"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Azure-beli megerősített gazdagép létrehozása a portál használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet a Azure Portal használatával. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében.

A-portálon létrehozhat egy új megerősített gazdagép-erőforrást, vagy megadhatja az összes beállítást manuálisan, vagy egy meglévő virtuális géphez tartozó beállítások használatával. Ha a virtuális gép beállításai segítségével szeretne létrehozni egy megerősített gazdagépet, tekintse meg a rövid útmutató [című cikket.](quickstart-host-portal.md) Igény szerint a [Azure PowerShell](bastion-create-host-powershell.md) használatával is létrehozhat egy Azure-beli megerősített gazdagépet.

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
    * **Virtual Network (virtuális hálózat**): az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva. A folyamat során létrehozhat egy új virtuális hálózatot a portálon, vagy használhat egy meglévő virtuális hálózatot is. Ha meglévő virtuális hálózatot használ, ellenőrizze, hogy a meglévő virtuális hálózat rendelkezik-e elegendő szabad hellyel a megerősített alhálózat követelményeinek kielégítéséhez.
    * **Alhálózat**: a virtuális hálózat azon alhálózata, amelyhez az új megerősített gazdagép erőforrása telepítve lesz. Létre kell hoznia egy alhálózatot a **AzureBastionSubnet**név érték használatával. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Legalább/27 vagy nagyobb (/27,/26 stb.) alhálózatot kell használnia.
    
       Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, tekintse meg a [Work with NSG](bastion-nsg.md) című cikket.
    * **Nyilvános IP-cím**: a megerősített erőforrás nyilvános IP-címe, AMELYEN az RDP/SSH elérhető (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
    * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
    * **Nyilvános IP-cím SKU**: Ez a beállítás alapértelmezés szerint a **standard**értékre van beállítva. Az Azure Bastion csak a standard nyilvános IP-SKU-t használja/támogatja.
    * **Hozzárendelés**: Ez a beállítás alapértelmezés szerint a **statikus**értékre van feltöltve.

1. Ha végzett a beállítások megadásával, kattintson a **felülvizsgálat + létrehozás**gombra. Ezzel érvényesíti az értékeket. Az ellenőrzés után megkezdheti a létrehozási folyamatot.
1. A **bástya létrehozása** lapon kattintson a **Létrehozás**gombra.
1. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. A megerősített erőforrás létrehozása és üzembe helyezése körülbelül 5 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések

* További információért olvassa el a [megerősített gyakori kérdések](bastion-faq.md) című témakört.

* Ha hálózati biztonsági csoportokat kíván használni az Azure megerősített alhálózattal, tekintse meg a következő témakört: a [NSG](bastion-nsg.md)használata.