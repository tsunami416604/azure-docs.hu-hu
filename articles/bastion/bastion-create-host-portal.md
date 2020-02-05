---
title: Azure Bastion-gazdagép létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990419"
---
# <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet a Azure Portal használatával. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében.

A-portálon létrehozhat egy új megerősített gazdagép-erőforrást, vagy megadhatja az összes beállítást manuálisan, vagy egy meglévő virtuális géphez tartozó beállítások használatával. Igény szerint az [Azure PowerShell](bastion-create-host-powershell.md) használatával is létrehozhat egy Azure-beli megerősített gazdagépet.

## <a name="before-you-begin"></a>Előzetes teendők

A bástya a következő nyilvános Azure-régiókban érhető el:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Megerősített gazdagép létrehozása – beállítások megadása

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

## <a name="createvmset"></a>Megerősített gazdagép létrehozása – virtuális gép beállításainak használata

Ha egy meglévő virtuális gép használatával hoz létre egy megerősített gazdagépet a portálon, a különböző beállítások automatikusan megegyeznek a virtuális géppel és/vagy a virtuális hálózattal.

1. Nyissa meg az [Azure portált](https://portal.azure.com). Nyissa meg a virtuális gépet, majd kattintson a **Kapcsolódás**elemre.

   ![VM-kapcsolat](./media/bastion-create-host-portal/vmsettings.png)
1. A jobb oldali oldalsávon kattintson a **Bastion**elemre, majd **használja a Bastion**-t.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. A megerősített lapon töltse ki a következő beállítások mezőket:

   * **Name (név**): a létrehozni kívánt megerősített állomás neve.
   * **Alhálózat**: a virtuális hálózaton belüli alhálózat, amelyre a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. Ez lehetővé teszi, hogy az Azure tudja, melyik alhálózaton helyezi üzembe a megerősített erőforrást. Ez nem más, mint az átjáró alhálózata. Legalább/27 vagy nagyobb (/27,/26 stb.) alhálózatot kell használnia. Az alhálózatot hálózati biztonsági csoportok, útválasztási táblák vagy delegálások nélkül hozza létre. Ha később úgy dönt, hogy hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, tekintse meg a következőt: a [NSG](bastion-nsg.md)használata.
   
     A **AzureBastionSubnet**létrehozásához kattintson az **alhálózat konfigurációjának kezelése** elemre.  A **Létrehozás** gombra kattintva hozza létre az alhálózatot, majd folytassa a következő beállításokkal.
   * **Nyilvános IP-cím**: a megerősített erőforrás nyilvános IP-címe, AMELYEN az RDP/SSH elérhető (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
   * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
1. Az érvényesítési képernyőn kattintson a **Létrehozás**gombra. Várjon körülbelül 5 percet a megerősített erőforrás létrehozásához és üzembe helyezéséhez.

## <a name="next-steps"></a>Következő lépések

* További információért olvassa el a [megerősített gyakori kérdések](bastion-faq.md) című témakört.

* Ha hálózati biztonsági csoportokat kíván használni az Azure megerősített alhálózattal, tekintse meg a következő témakört: a [NSG](bastion-nsg.md)használata.