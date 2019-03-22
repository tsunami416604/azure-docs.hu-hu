---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 55e46e058bddca717929df61b2bc766b89e0f885
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122757"
---
![Virtuális gépek egy önálló cloud service-ben](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Ha a virtuális gépet helyez egy virtuális hálózatban, megadhatja, hogy a használni kívánt hány cloud services és a rendelkezésre állási csoportok betöltése. Ezenkívül rendezheti a virtuális gépek alhálózatok ugyanúgy a helyszíni hálózat és a virtuális hálózat csatlakoztatása a helyszíni hálózat. Például:

![A virtuális hálózatban a virtuális gépek](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Virtuális hálózatok javasolt módon az Azure-beli virtuális gépek csatlakozni. Az ajánlott eljárás, hogy az alkalmazás mindegyik rétegéhez konfigurálása egy különálló cloud service-ben. Azonban szükség lehet néhány virtuális gépnél a különböző alkalmazásrétegek egyesítendő ugyanazon a felhőszolgáltatáson belül a maximálisan megengedett száma előfizetésenként 200 a cloud services kell maradniuk. Ez és egyéb korlátok áttekintéséhez lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Virtuális gépek csatlakoztatása egy virtuális hálózaton
Csatlakozás virtuális hálózatban lévő virtuális gépek:

1. A virtuális hálózat létrehozása a [az Azure portal](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) , és adja meg a "klasszikus üzembe helyezési".
2. Hozzon létre a felhőalapú szolgáltatások a rendelkezésre állási csoportok a Tervező, és a terheléselosztás a központi telepítéshez. Az Azure Portalon kattintson **erőforrás létrehozása > Számítás > Felhőszolgáltatás** minden felhőszolgáltatás.

   Adja meg az a felhőalapú szolgáltatás részleteit, válassza ki ugyanazt _erőforráscsoport_ használja a virtuális hálózattal.

3. Minden egyes új virtuális gép létrehozásához kattintson a **erőforrás létrehozása > számítás**, majd válassza ki a megfelelő Virtuálisgép-lemezképet, a **kiemelt alkalmazások**.

   A virtuális gép **alapjai** panelen válassza ki ugyanazt _erőforráscsoport_ használja a virtuális hálózattal.

   ![Virtuális gép alapvető beállítások panel egy virtuális hálózat használatakor](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Ahogy, töltse ki a virtuális gép **beállítások**, válassza ki a megfelelő _felhőalapú szolgáltatás_ vagy _virtuális hálózati_ a virtuális gép.

   Az Azure a többi elem a választott beállítás alapján fogja kiválasztani.

   ![Virtuális hálózat használata esetén a virtuális gép beállításainak panel](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Virtuális gépek csatlakoztatása az önálló felhőalapú szolgáltatásként
Csatlakozás virtuális gépek egy önálló cloud service-ben:

1. A felhőszolgáltatás létrehozása a [az Azure portal](http://portal.azure.com). Kattintson a **új > Számítás > Felhőszolgáltatás**. Vagy az első virtuális gép létrehozásakor hozhat létre a felhőalapú szolgáltatás, az üzembe helyezéshez.
2. A virtuális gépek létrehozásakor válassza ki a felhőalapú szolgáltatáshoz használt ugyanabban az erőforráscsoportban.

   ![Virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3. Adja meg az a virtuális gép adatait, mivel az első lépésben létrehozott felhőalapú szolgáltatás nevének kiválasztása.

   ![Virtuális gép egy felhőszolgáltatás kiválasztása](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
