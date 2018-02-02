---
title: "Azure Batch-készlet része virtuális hálózatnak kiépítése |} Microsoft Docs"
description: "A Batch-készlet létrehozhat egy virtuális hálózatot, hogy a számítási csomópontok is biztonságos kommunikáció valósítható meg a többi virtuális gép a hálózaton, például egy fájlkiszolgálón."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/16/2017
ms.author: danlep
ms.openlocfilehash: 647a123e403b0ed6458bc26e7b26a73f3c1f8e91
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Azure Batch-készlet létrehozása a virtuális hálózat


Azure Batch-készlet létrehozásakor megadhat egy alhálózat, a készlet egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md) (VNet), amely akkor adja meg. Ez a cikk azt ismerteti, hogyan állíthatja be a Batch-készlet a Vneten belül. 



## <a name="why-use-a-vnet"></a>Miért érdemes használni a virtuális hálózatot?


Azure Batch-készlet van a beállítások lehetővé teszik a számítási csomópontok kommunikálnak egymással – például, többpéldányos feladatok futtatásához. Ezek a beállítások nem igényelnek külön hálózatok. Azonban alapértelmezés szerint a csomópontok nem tud kommunikálni, amelyek nem szerepelnek a Batch-készlet, például a licenckiszolgáló vagy a fájlkiszolgáló virtuális gépeket. Ahhoz, hogy a készlet számítási csomópontok biztonságos kommunikáció valósítható meg más virtuális gépekkel, vagy egy helyszíni hálózattal, megadhat egy Azure virtuális hálózat alhálózatának készletbe. 



## <a name="prerequisites"></a>Előfeltételek

* **Hitelesítés**. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört. 

* **Egy Azure virtuális hálózatot**. Készítse elő a virtuális hálózat egy vagy több alhálózattal rendelkező előre, használhatja az Azure portál, Azure PowerShell, az Azure parancssori felület (CLI) vagy más módszerrel. Az Azure Resource Manager-alapú virtuális hálózat létrehozásához lásd: [hozzon létre egy virtuális hálózatot, több alhálózattal](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). A klasszikus virtuális hálózat létrehozásához lásd: [hozzon létre egy virtuális hálózat (klasszikus), több alhálózattal](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>A virtuális hálózat követelményei
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Készlet létrehozása a virtuális hálózaton, a portálon

Egyszer hozott létre a virtuális hálózat és rendelve egy alhálózatot, létrehozhat egy Batch-készlet, hogy a virtuális hálózaton. Kövesse az alábbi lépéseket a készlet létrehozása az Azure-portálon: 



1. Az Azure portálon lépjen Batch-fiókjára. Ez a fiók a használni kívánt VNet tartalmazó erőforráscsoportot azonosnak kell lennie az előfizetés és a régióban. 
2. Az a **beállítások** ablak bal oldalán válassza ki a **készletek** menüpont.
3. Az a **készletek** ablakban válassza ki a **Hozzáadás** parancsot.
4. A a **a készlet hozzáadása** ablakban válassza ki a használni kívánt beállítás a **képtípust** legördülő menüből. 
5. Válassza ki a megfelelő **Publisher/ajánlat/Sku** a egyéni lemezkép számára.
6. Adja meg a fennmaradó szükséges beállításokkal, többek között a **csomópont méretének**, **céloz dedikált csomópontok**, és **alacsony prioritású csomópont**, valamint minden szükséges, választható beállítások.
7. A **virtuális hálózati**, válassza ki a virtuális hálózati és alhálózati szeretné használni.
  
  ![Címkészlet, amely a virtuális hálózat hozzáadása](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Felhasználó által definiált útvonalak a kényszerített bújtatás

Lehetséges, hogy követelmények a szervezet átirányítási (kényszerített) internetre irányuló forgalomnak a alhálózatról a vizsgálati és naplózási helyszíni helyre. Előfordulhat, hogy engedélyezte a kényszerített bújtatás a vnet alhálózatainak. 

Győződjön meg arról, hogy működik-e az Azure Batch-készlet számítási csomópontokat, amely rendelkezik a kényszerített bújtatás engedélyezve van a Vneten belül, hozzá kell adnia a következő [felhasználó által definiált útvonalak](../virtual-network/virtual-networks-udr-overview.md) az alhálózaton:

* A Batch szolgáltatás kell kommunikálnia a készlet számítási csomópontok feladatütemezésre. Ahhoz, hogy ez a kommunikáció, adjon hozzá egy felhasználó által megadott útvonalat a régióban, ahol a Batch-fiók létezik-e a Batch szolgáltatás által használt IP-címek. A Batch szolgáltatás IP-címek listájának beszerzéséhez forduljon Azure támogatási szolgálatához.

* Győződjön meg arról, hogy az Azure Storage kimenő forgalom (pontosabban URL-címei az űrlap `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, és `<account>.blob.core.windows.net`) nincs letiltva a helyszíni hálózati készülék segítségével.

Amikor egy felhasználó által megadott útvonal, mindegyik kapcsolódó kötegelt IP-cím előtagján útvonal megadása, és állítsa be **a következő ugrás típusa** való **Internet**. Tekintse meg a következő példát:

![Felhasználó által megadott útvonal](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>További lépések

- Kötegelt, áttekintéséért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md).
- Felhasználó által megadott útvonal létrehozása kapcsolatban bővebben lásd: [hozzon létre egy felhasználó által megadott útvonal - Azure-portálon](../virtual-network/create-user-defined-route-portal.md).
