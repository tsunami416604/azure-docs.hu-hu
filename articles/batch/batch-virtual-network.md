---
title: Készlet kiépítése virtuális hálózatban – Azure Batch | Microsoft Docs
description: Batch-készlet létrehozása egy Azure-beli virtuális hálózatban, hogy a számítási csomópontok biztonságosan kommunikálhassanak a hálózaton lévő más virtuális gépekkel, például a fájlkiszolgálón.
services: batch
author: ju-shim
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: jushiman
ms.custom: seodec18
ms.openlocfilehash: 06b861f306718068366653685ebe4daf8d5dcfa6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026268"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Azure Batch-készlet létrehozása egy virtuális hálózaton

Azure Batch készlet létrehozásakor kiépítheti a készletet egy Ön által megadott Azure-beli [virtuális hálózat](../virtual-network/virtual-networks-overview.md) (VNet) alhálózatában. Ez a cikk azt ismerteti, hogyan lehet batch-készleteket beállítani egy VNet. 

## <a name="why-use-a-vnet"></a>Miért érdemes VNet használni?

Egy Azure Batch-készlet olyan beállításokkal rendelkezik, amelyekkel a számítási csomópontok kommunikálhatnak egymással – például több példányos feladatok futtatásához. Ezek a beállítások nem igényelnek külön VNet. Alapértelmezés szerint azonban a csomópontok nem tudnak kommunikálni olyan virtuális gépekkel, amelyek nem részei a Batch-készletnek, például a licenckiszolgálót vagy a fájlkiszolgálón. Annak érdekében, hogy a készlet számítási csomópontjai biztonságosan kommunikáljanak más virtuális gépekkel vagy helyszíni hálózattal, a készletet egy Azure-VNet alhálózatán is kiépítheti. 

## <a name="prerequisites"></a>Előfeltételek

* **Hitelesítés**. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört. 

* **Egy Azure-VNet**. A VNet követelményeinek és konfigurációjának megtekintéséhez tekintse meg a következő szakaszt. Egy vagy több alhálózattal rendelkező VNet előkészítéséhez használhatja a Azure Portal, Azure PowerShell, az Azure parancssori felületét (CLI) vagy más metódusokat is.  
  * Azure Resource Manager-alapú VNet létrehozásával kapcsolatban lásd: [virtuális hálózat létrehozása](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A Resource Manager-alapú VNet új központi telepítések esetén ajánlott, és csak a virtuális gép konfigurációjában található készletekben támogatott.
  * Klasszikus VNet létrehozásával kapcsolatban lásd: [virtuális hálózat (klasszikus) létrehozása több alhálózattal](../virtual-network/create-virtual-network-classic.md). A klasszikus VNet csak a Cloud Services konfigurációjában található készletekben támogatott.

## <a name="vnet-requirements"></a>A virtuális hálózat követelményei

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Készlet létrehozása VNet a portálon

Miután létrehozta a VNet, és hozzárendelt egy alhálózatot, létrehozhat egy batch-készletet az adott VNet. A következő lépésekkel hozhat létre egy készletet a Azure Portalből: 

1. Az Azure portálon lépjen Batch-fiókjára. Ennek a fióknak ugyanabban az előfizetésben és régióban kell lennie, mint a használni kívánt VNet tartalmazó erőforráscsoport. 
2. A bal oldali **Beállítások** ablakban válassza a **készletek** menüpontot.
3. A **készletek** ablakban válassza a **Hozzáadás** parancsot.
4. A **készlet hozzáadása** ablakban válassza ki a használni kívánt beállítást a **rendszerkép típusa** legördülő listából. 
5. Válassza ki a megfelelő **közzétevőt/ajánlatot/SKU-** t az egyéni rendszerképhez.
6. Megadhatja a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a **célként kijelölt csomópontokat**és az **alacsony prioritású csomópontokat**, valamint a kívánt választható beállításokat.
7. A **Virtual Network**területen válassza ki a használni kívánt virtuális hálózatot és alhálózatot.
  
   ![Készlet hozzáadása virtuális hálózattal](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>A kényszerített bújtatáshoz tartozó felhasználó által megadott útvonalak

Előfordulhat, hogy a szervezete követelményei szerint a rendszer átirányítja (kényszeríti) az internetre irányuló forgalmat az alhálózatról a helyszíni helyre a vizsgálat és a naplózás érdekében. Lehetséges, hogy engedélyezte a kényszerített bújtatást a VNet alhálózatai számára. 

Annak biztosítása érdekében, hogy a Azure Batch-készlet számítási csomópontjai olyan VNet működjenek, amelyeken engedélyezve van a kényszerített bújtatás, hozzá kell adnia a következő, [felhasználó által definiált útvonalakat](../virtual-network/virtual-networks-udr-overview.md) ehhez az alhálózathoz:

* A Batch szolgáltatásnak kommunikálnia kell a készlet számítási csomópontjaival az ütemezési feladatokhoz. A kommunikáció engedélyezéséhez adjon hozzá egy felhasználó által megadott útvonalat a Batch szolgáltatás által használt egyes IP-címekhez abban a régióban, ahol a Batch-fiók létezik. További információ a Batch szolgáltatás IP-címeinek beszerzéséről: [szolgáltatások címkéi a helyszínen](../virtual-network/service-tags-overview.md)

* Győződjön meg arról, hogy az Azure Storage-ba irányuló kimenő adatforgalom (pontosabban az űrlap URL-címei `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`és `<account>.blob.core.windows.net`) nincs letiltva a helyszíni hálózati berendezésen keresztül.

Felhasználó által definiált útvonal hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjához, és állítsa be a **következő ugrás típusát** az **Internet**értékre. Lásd a következő példát:

![Felhasználó által megadott útvonal](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Következő lépések

- A Batch részletes áttekintése: [nagy léptékű párhuzamos számítási megoldások létrehozása a Batch szolgáltatással](batch-api-basics.md).
- A felhasználó által megadott útvonal létrehozásával kapcsolatos további információkért lásd: [felhasználó által definiált útvonal-Azure Portal létrehozása](../virtual-network/tutorial-create-route-table-portal.md).
