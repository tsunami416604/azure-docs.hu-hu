---
title: Készlet kiépítése virtuális hálózatban - Azure Batch | Microsoft dokumentumok
description: Batch-készlet létrehozása egy Azure virtuális hálózatban, hogy a számítási csomópontok biztonságosan kommunikálhassanak a hálózat többi virtuális gépével, például egy fájlkiszolgálóval.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a653b645fb8713698e8baf283b3ab6226841dfcd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657470"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Azure Batch-készlet létrehozása virtuális hálózatban

Amikor létrehoz egy Azure Batch-készletet, kiépítheti a készletet egy [azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md) (VNet) megadott alhálózatában. Ez a cikk bemutatja, hogyan állíthat be egy kötegkészletet egy virtuális hálózatban.

## <a name="why-use-a-vnet"></a>Miért érdemes használni a virtuális hálózatot?

Az Azure Batch-készlet beállításai lehetővé teszik a számítási csomópontok egymással való kommunikációt – például többpéldányos feladatok futtatásához. Ezek a beállítások nem igényelnek külön virtuális hálózatot. Alapértelmezés szerint azonban a csomópontok nem tudnak kommunikálni olyan virtuális gépekkel, amelyek nem részei a batch készletnek, például licenckiszolgálóval vagy fájlkiszolgálóval. Annak engedélyezéséhez, hogy a készlet számítási csomópontjai biztonságosan kommunikálhassanak más virtuális gépekkel vagy egy helyszíni hálózattal, kiépítheti a készletet egy Azure virtuális hálózat alhálózatában.

## <a name="prerequisites"></a>Előfeltételek

* **Hitelesítés**. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört.

* **Egy Azure-virtuális hálózat**. Tekintse meg a következő szakaszban a virtuális hálózat követelményeinek és konfigurációjának. Egy vagy több alhálózattal rendelkező virtuális hálózat előzetes előkészítéséhez használhatja az Azure Portalt, az Azure PowerShellt, az Azure parancssori felületet (CLI) vagy más módszereket.
  * Az Azure Resource Manager-alapú virtuális hálózat létrehozásáról a [Virtuális hálózat létrehozása](../virtual-network/manage-virtual-network.md#create-a-virtual-network)című témakörben található. Az erőforrás-kezelő-alapú virtuális hálózat használata új központi telepítésekhez ajánlott, és csak a virtuális gép konfigurációjában lévő készletek esetén támogatott.
  * Klasszikus virtuális hálózat létrehozásához [lásd: Virtuális hálózat létrehozása (klasszikus) több alhálózattal.](../virtual-network/create-virtual-network-classic.md) A klasszikus virtuális hálózat csak a Cloud Services-konfigurációban lévő készleteken támogatott.

## <a name="vnet-requirements"></a>A virtuális hálózat követelményei

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Virtuális hálózattal rendelkező készlet létrehozása a portálon

Miután létrehozta a virtuális hálózatot, és hozzárendelt egy alhálózatot, létrehozhat egy batch készletet az adott virtuális hálózattal. Az alábbi lépésekkel hozzon létre egy készletet az Azure Portalon: 

1. Az Azure portálon lépjen Batch-fiókjára. Ennek a fióknak ugyanabban az előfizetésben és régióban kell lennie, mint a használni kívánt virtuális hálózatot tartalmazó erőforráscsoportnak.
2. A bal oldali **Beállítások** ablakban válassza a **Készletek** menüelemet.
3. A **Készletek** ablakban válassza a **Hozzáadás** parancsot.
4. A **Készlet hozzáadása** ablakban válassza ki a használni kívánt beállítást a **Képtípus** legördülő menüből.
5. Válassza ki a megfelelő **Publisher/Offer/Sku** az egyéni lemezképhez.
6. Adja meg a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a **célzott csomópontokat**és **az alacsony prioritású csomópontokat,** valamint a kívánt választható beállításokat.
7. A **Virtuális hálózat ban**válassza ki a használni kívánt virtuális hálózatot és alhálózatot.

   ![Készlet hozzáadása virtuális hálózattal](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Felhasználó által definiált útvonalak kényszerített bújtatáshoz

Előfordulhat, hogy a szervezetben követelmények vannak az internethez kötött forgalom átirányítására (kényszerítése) az alhálózatból a helyszíni helyre ellenőrzés és naplózás céljából. Lehet, hogy engedélyezte a kényszerített bújtatás a virtuális hálózat alhálózatában.

Annak érdekében, hogy az Azure Batch-készlet számítási csomópontjai olyan virtuális hálózaton működjenek, amelynek kötelező a bújtatása engedélyezve van, hozzá kell adnia a következő [felhasználó által definiált útvonalakat](../virtual-network/virtual-networks-udr-overview.md) az adott alhálózathoz:

* A Batch szolgáltatásnak kommunikálnia kell a készlet számítási csomópontjaival a feladatok ütemezéséhez. A kommunikáció engedélyezéséhez adjon hozzá egy felhasználó által definiált útvonalat a Batch szolgáltatás által használt minden egyes IP-címhez abban a régióban, ahol a Batch-fiók létezik. A Batch szolgáltatás IP-címeinek listájának beszerzéséről a [helyszíni Szolgáltatáscímkék](../virtual-network/service-tags-overview.md)című témakörben olvashat. A Batch szolgáltatás IP-címei `BatchNodeManagement` a szolgáltatáscímkéhez (vagy a Batch-fiók régiójának megfelelő regionális változathoz) lesznek társítva.

* Győződjön meg arról, hogy az Azure Storage -ba irányuló kimenő forgalom (különösen az űrlap `<account>.table.core.windows.net`URL-címei , `<account>.queue.core.windows.net`és `<account>.blob.core.windows.net`) nem blokkolva van a helyszíni hálózati berendezésen keresztül.

Amikor felhasználó által definiált útvonalat ad hozzá, határozza meg az útvonalat az egyes kapcsolódó Batch IP-címelőtagokhoz, és állítsa a **Következő ugrás típust** **az internetre.** Lásd a következő példát:

![Felhasználó által megadott útvonal](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> A kötegelt szolgáltatás IP-címei idővel változhatnak. Az IP-cím változása miatti kimaradás elkerülése érdekében javasoljuk, hogy hozzon létre egy rendszeres folyamatot a Batch szolgáltatás IP-címeinek automatikus frissítéséhez, és tartsa őket naprakészen az útvonaltáblában.

## <a name="next-steps"></a>További lépések

- A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.
- A felhasználó által definiált útvonal létrehozásáról további információ: [Create a user-defined route - Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
