---
title: Egy virtuális hálózatot az Azure Batch-készlet kiépítése |} A Microsoft Docs
description: Batch-készlet létrehozhat egy virtuális hálózatot, hogy számítási csomópontok biztonságosan kommunikálhassanak a hálózaton, például a fájlkiszolgáló más virtuális gépekkel.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 9e8bd6819601cc4436e3432ee390f2ae82a0d94a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059170"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Azure Batch-készlet létrehozása egy virtuális hálózaton belül


Azure Batch-készlet létrehozásakor a készlet, alhálózatán, telepíthet egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md) (VNet), amely azt adja meg. Ez a cikk bemutatja, hogyan állítható be a Batch-készlet, egy virtuális hálózaton. 



## <a name="why-use-a-vnet"></a>Miért érdemes használni egy virtuális hálózaton?


Egy Azure Batch-készletben, hogy a számítási csomópontok kommunikálnak egymással – például, hogy a többpéldányos tevékenységek futtatásához beállításokkal rendelkezik. Ezek a beállítások nem igényel külön virtuális hálózatot. Azonban alapértelmezés szerint a csomópontok nem tud kommunikálni, amelyek nem részei a Batch-készlet, például a licenckiszolgáló vagy egy fájlkiszolgáló virtuális gépeket. Ahhoz, hogy a készlet számítási csomópontok használatával biztonságos kommunikáció a más virtuális gépekkel, vagy egy helyszíni hálózattal, akkor helyezhet üzembe a készlet, egy Azure virtuális hálózat alhálózatán. 



## <a name="prerequisites"></a>Előfeltételek

* **Hitelesítés**. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört. 

* **Azure virtuális hálózatba**. Egy vagy több alhálózattal rendelkező virtuális hálózat előzetes előkészítése, használhatja az Azure Portalon, az Azure PowerShell, az Azure parancssori felület (CLI) vagy más módszerekkel. Egy Azure Resource Manager-alapú virtuális hálózat létrehozása: [hozzon létre egy virtuális hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Egy klasszikus virtuális hálózat létrehozása: [több alhálózattal rendelkező virtuális hálózat (klasszikus) létrehozása](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>A virtuális hálózat követelményei
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Készlet létrehozása egy virtuális hálózatot a portálon

Egyszer létrehozta a virtuális hálózathoz, és hozzárendelt egy alhálózathoz, hozhat létre egy Batch-készletet, hogy a virtuális hálózattal. Kövesse az alábbi lépéseket az Azure Portalon hoz létre egy készletet: 



1. Az Azure portálon lépjen Batch-fiókjára. Ez a fiók az erőforráscsoport, amely tartalmazza a használni kívánt virtuális hálózat azonos előfizetésben és régióban kell lennie. 
2. Az a **beállítások** ablak bal oldalán válassza ki a **készletek** menüpontot.
3. Az a **készletek** ablakban válassza ki a **Hozzáadás** parancsot.
4. Az a **készlet hozzáadása** ablakban válassza ki a használni kívánt lehetőséget a **képtípus** legördülő listából. 
5. Válassza ki a megfelelő **közzétevő/ajánlat/Sku** az egyéni rendszerkép.
6. Adja meg a fennmaradó beállításokat, beleértve a **csomópontméret**, **dedikált csomópontok célszáma**, és **alacsony prioritású csomópontok célszáma**, illetve bármely kívánt, nem kötelező beállítások.
7. A **virtuális hálózat**, válassza ki a virtuális hálózatot és alhálózatot kívánja használni.
  
  ![A virtual network készlet hozzáadása](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Felhasználó által megadott útvonalak a kényszerített bújtatás

Előfordulhat, hogy kell követelmények a szervezetben (kényszerített) átirányítási internetre irányuló forgalmat az alhálózatról a helyszíni helyre történő áthaladásra ellenőrzés és naplózás. Előfordulhat, hogy engedélyezte a kényszerített bújtatás a virtuális hálózat alhálózatai. 

Győződjön meg arról, hogy működik-e az Azure Batch-készlet számítási csomópontok egy virtuális hálózatban, amely rendelkezik a kényszerített bújtatás engedélyezve van, adja hozzá a következő [felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md) , alhálózat:

* A Batch szolgáltatás és a készlet számítási csomópontjait ahhoz, hogy feladatütemezés közötti kommunikációhoz szükséges. Ez a kommunikáció engedélyezéséhez adjon hozzá egy felhasználó által megadott útvonal abban a régióban, ahol a Batch-fiók létezik-e a Batch szolgáltatás által használt IP-címeket. A Batch szolgáltatás IP-címeinek beszerzéséhez forduljon az Azure ügyfélszolgálatához.

* Győződjön meg arról, hogy a kimenő forgalmat az Azure Storage (pontosabban az űrlap URL `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, és `<account>.blob.core.windows.net`) nincs letiltva a helyszíni hálózati berendezések keresztül.

Ha egy felhasználó által megadott útvonal hozzáadásakor, adja meg az egyes kapcsolódó Batch IP-címelőtag útvonalát, és állítsa be **következő ugrás típusa** való **Internet**. Tekintse meg a következő példát:

![Felhasználó által megadott útvonal](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>További lépések

- A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).
- Egy felhasználó által megadott útvonal létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy felhasználó által megadott útvonal – Azure portal](../virtual-network/tutorial-create-route-table-portal.md).
