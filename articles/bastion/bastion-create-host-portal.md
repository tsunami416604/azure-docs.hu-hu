---
title: 'Hozzon létre egy Azure Bastion host: portál'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure-bastion gazdata a portál használatával
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366143"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Hozzon létre egy Azure-bástya-állomást a portál használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-bastion gazdagép az Azure Portalhasználatával. Miután kiépítette az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH élmény az azonos virtuális hálózat összes virtuális gépe számára elérhető. Az Azure Bastion központi telepítése virtuális hálózatonként, nem előfizetésenként/fiókonként vagy virtuális gépenként.

Létrehozhat egy új megerősített gazdaerőforrást a portálon az összes beállítás manuális megadásával, vagy egy meglévő virtuális gépnek megfelelő beállítások használatával. Ha egy megerősített gazdagép virtuális gép beállításaival hozzon létre, olvassa el a [rövid útmutató](quickstart-host-portal.md) cikket. Szükség esetén az [Azure PowerShell](bastion-create-host-powershell.md) segítségével azure-bastion gazdat.

## <a name="before-you-begin"></a>Előkészületek

A bástya a következő nyilvános Azure-régiókban érhető el:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion-gazdagép létrehozása

Ez a szakasz segít egy új Azure-bástya-erőforrás létrehozásában az Azure Portalról.

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

1. Az **Új** lap *Keresés a Piactéren* mezőjébe írja be a **Bastion (Bastion**) kifejezést, majd a keresési eredmények megtekintéséhez kattintson az **Enter** gombra.

1. Az eredmények között kattintson a **Bástya gombra.** Győződjön meg arról, hogy a közzétevő a *Microsoft,* és a kategória *a Hálózat*.

1. A **Megerősített** lapon kattintson a **Létrehozás** gombra a **Megerősítettoldal létrehozásához.**

1. A **Megerősített oldal létrehozása** lapon állítson be egy új megerősített erőforrást. Adja meg a megerősített erőforrás konfigurációs beállításait.

    ![hozzon létre egy bástya](./media/bastion-create-host-portal/settings.png)

    * **Előfizetés**: Az új bástya-erőforrás létrehozásához használni kívánt Azure-előfizetés.
    * **Erőforráscsoport:** Az Az Azure erőforráscsoport, amelyben az új bástya-erőforrás jön létre. Ha nem rendelkezik meglévő erőforráscsoporttal, létrehozhat egy újat.
    * **Név**: Az új megerősített erőforrás neve
    * **Régió**: Az azure-beli nyilvános régió, amelyben az erőforrás létre jön.
    * **Virtuális hálózat**: Az a virtuális hálózat, amelyben a megerősített erőforrás létrejön. A folyamat során létrehozhat egy új virtuális hálózatot a portálon, vagy használhat egy meglévő virtuális hálózatot. Ha meglévő virtuális hálózatot használ, győződjön meg arról, hogy a meglévő virtuális hálózat rendelkezik elegendő szabad címterülettel a bástya-alhálózat követelményeinek kielégítéséhez.
    * **Alhálózat:** Az az alhálózat a virtuális hálózatban, amelyre az új megerősített gazdaállomás-erőforrás telepítve lesz. Létre kell hoznia egy alhálózatot az **AzureBastionSubnet**névérték használatával. Ez az érték lehetővé teszi az Azure számára, hogy melyik alhálózatra telepítse a Bástya-erőforrásokat. Ez eltér az átjáró alhálózatátótól. Legalább /27 vagy nagyobb (/27, /26 és így tovább) alhálózatot kell használnia.
    
       Hozza létre az **AzureBastionSubnet-et** útvonaltáblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ az **AzureBastionSubnet,** tekintse meg az [NSG-k használata](bastion-nsg.md) cikket.
    * **Nyilvános IP-cím**: Annak a megerősített erőforrásnak a nyilvános IP-je, amelyen az RDP/SSH-t hozzá fogják férni (a 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozott megerősített erőforrásnak.
    * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrás neve.
    * **Nyilvános IP-cím Termékváltozat**: Ez a beállítás alapértelmezés szerint a Szokásos értékre **van**feltöltve. Az Azure Bastion csak a standard nyilvános IP-termékváltozatot használja/támogatja.
    * **Hozzárendelés**: Ez a beállítás alapértelmezés szerint a Static beállításra **van**feltöltve.

1. Ha befejezte a beállítások megadását, kattintson a **Véleményezés + Létrehozás gombra.** Ez ellenőrzi az értékeket. Az érvényesítés sikeresse, megkezdheti a létrehozási folyamatot.
1. A **Megerősített oldal létrehozása** lapon kattintson a **Létrehozás gombra.**
1. Megjelenik egy üzenet, amely tudatja, hogy a telepítés folyamatban van. Az állapot ezen a lapon jelenik meg az erőforrások létrehozásakor. A bástya erőforrás létrehozása és üzembe helyezése körülbelül 5 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

* További információkért olvassa el a [Bástya GYIK-et.](bastion-faq.md)

* Ha hálózati biztonsági csoportokat szeretne használni az Azure Bastion alhálózattal, olvassa el a [Munka az NSG-kkel című témakört.](bastion-nsg.md)