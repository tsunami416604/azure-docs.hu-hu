---
title: 'Gyors útmutató: NAT-átjáró létrehozása – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót a Azure Portal használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 8913c956554fa7bf1d0362b44dc6f8031ffd74f9
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429704"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Gyors útmutató: NAT-átjáró létrehozása a Azure Portal használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

>[!NOTE] 
>Az Azure Virtual Network NAT jelenleg nyilvános előzetes verzióként érhető el, és korlátozott számú [régióban](./nat-overview.md#region-availability)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

> [!IMPORTANT]
> Ha Virtual Network NAT [előzetes verziója engedélyezve van](./nat-overview.md#enable-preview) az előfizetésben, az https://aka.ms/natportal használatával férhet hozzá a portálhoz.

Jelentkezzen be az [Azure Portal](https://aka.ms/natportal).


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Egy virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni az erőforráscsoportot és a virtuális hálózatot.  

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget, vagy keressen rá a **virtuális hálózatra** a piactéren.

2. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myVNet** értéket. |
    | Címtér | Adja meg a **192.168.0.0/16**értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a Create New- **myResourceGroupNAT**(új létrehozása) lehetőséget. |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|
    | Alhálózat – név | Adja meg a **mySubnet**. |
    | Alhálózat – címtartomány | Adja meg a **192.168.0.0/24**értéket. |

3. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás**lehetőséget.

### <a name="create-a-vm-to-use-the-nat-gateway"></a>Virtuális gép létrehozása a NAT-átjáró használatához

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához. Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését. A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **számítás** > **Ubuntu Server 18,04 LTS**lehetőséget, vagy keressen rá az **Ubuntu Server 18,04 LTS** kifejezésre a piactéren.

2. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > **erőforráscsoporthoz**: válassza a **myResourceGroupNAT**lehetőséget.
   - **Példány részletei** > **virtuális gép neve**: Type **myVM**.
   - **Példány részletei** > régióban > válassza az **USA 2. keleti** **régiója** lehetőséget.
   - **Rendszergazdai fiók** > **Hitelesítés típusa**: válassza a **jelszó**lehetőséget.
   - **Rendszergazdai fiók** > adja meg a **felhasználónevet**, a **jelszót**és a **Jelszó megerősítése** információt.
   - **Bejövő portszabályok** > **nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > a **bejövő portok kiválasztása**: az **SSH kiválasztása (22)**
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.

3. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnet**
   - **Alhálózat**: **mySubnet**
   - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget.  A **nyilvános IP-cím létrehozása** ablakban írja be **a MyPublicIPVM** **nevet a név** mezőbe, és válassza a **standard** értéket az **SKU**számára.  Kattintson az **OK** gombra.
   - **NIC hálózati biztonsági csoport**: válassza az **alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: az **SSH** megerősítése lehetőség van kiválasztva.

4. A felügyelet **lap** **figyelés**területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva**értékre.

5. Válassza az **Áttekintés + létrehozás** lehetőséget. 

6. Tekintse át a beállításokat, és kattintson a **Létrehozás**gombra.

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt. Hozzáadunk egy nyilvános IP-erőforrást, egy nyilvános IP-előtagot és egy NAT-átjáró erőforrást.

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **nyilvános IP-cím**lehetőséget, vagy keressen **nyilvános IP-címet** a piactér keresésében.

2. A **nyilvános IP-cím létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | IP-verzió | Válassza az **IPv4**elemet.
    | SKU | Válassza a **standard**lehetőséget.
    | Név | Adja meg a **myPublicIP**. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroupNAT**lehetőséget. |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|

3. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás**lehetőséget.

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **nyilvános IP-előtag**lehetőséget, vagy keressen **nyilvános IP-előtagot** a piactér keresésében. 

2. A **nyilvános IP-cím létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > **erőforráscsoporthoz**: válassza a **myResourceGroupNAT**>
   - **Példány részletei** > **neve**: Type **myPublicIPprefix**.
   - **Példány részletei** > régió: válassza az **USA 2. keleti** **régióját**.
   - **Példány részletei** > **előtag mérete**: Select **/31 (2 cím)**

3. Hagyja változatlanul az alapértelmezett beállításokat, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.

4. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.
   

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > a **NAT-átjáró**lehetőséget, vagy keressen rá a **NAT-átjáróra** a piactéren.

2. A **hálózati címfordítási (NAT-) átjáró létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > **erőforráscsoporthoz**: válassza a **myResourceGroupNAT**lehetőséget.
   - **Példány részletei** > **NAT-átjáró neve**: Type **myNATgateway**.
   - **Példány részletei** > régió: válassza az **USA 2. keleti** **régióját**.
   - **Példány részletei** > **Üresjárati időkorlát (perc)** : Type **10**.
   - Válassza a **nyilvános IP-cím** lapot, vagy válassza a **Tovább: nyilvános IP-cím**elemet.

3. A **nyilvános IP-cím** lapon írja be vagy válassza ki a következő értékeket:
   - **Nyilvános IP-címek**: válassza a **myPublicIP**lehetőséget.
   - **Nyilvános IP-előtagok**: válassza a **myPublicIPprefix**lehetőséget.
   - Válassza az **alhálózat** lapot, vagy válassza a **Tovább: alhálózat**elemet.

4. Az **alhálózat** lapon írja be vagy válassza ki a következő értékeket:
   - **Virtual Network**: válassza a **myResourceGroupNAT** > **myVnet**lehetőséget.
   - **Alhálózat neve**: válassza a **mySubnet**melletti jelölőnégyzetet.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felderítése

1. A portál bal oldalán válassza az **erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT**lehetőséget.
3. Válassza a **myVM**lehetőséget.
4. Az **Áttekintés**területen másolja a **nyilvános IP-cím** értéket, és illessze be a Jegyzettömbbe, így a virtuális gép eléréséhez használhatja azt.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy hozzáférjen a virtuális géphez.

## <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépre

Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatára. 

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrásainak kimerítése további nyilvános IP-cím-erőforrások vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel foglalkozik.


- Tudnivalók az [Azure Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.
- [Visszajelzés küldése a nyilvános előzetes](https://aka.ms/natfeedback)verzióról.
> [!div class="nextstepaction"]

