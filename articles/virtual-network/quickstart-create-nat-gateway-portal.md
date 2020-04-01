---
title: 'Rövid útmutató: NAT-átjáró létrehozása – Azure portal'
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót az Azure Portal használatával
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
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066388"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Rövid útmutató: NAT-átjáró létrehozása az Azure Portalhasználatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure virtuális hálózati NAT-szolgáltatást. NAT-átjárót hoz létre, hogy kimenő kapcsolatot biztosítson egy virtuális gép számára az Azure-ban. 

Ha szeretné, ezeket a lépéseket az [Azure CLI](quickstart-create-nat-gateway-cli.md), [Az Azure PowerShell](quickstart-create-nat-gateway-powershell.md)használatával, vagy üzembe helyezheti az [ARM-sablon](quickstart-create-nat-gateway-powershell.md) helyett a portálon.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Mielőtt üzembe helyezne egy virtuális gépet, és használhatja a NAT-átjárót, létre kell hoznunk az erőforráscsoportot és a virtuális hálózatot.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupNAT |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régiónév>**          | USA 2. keleti régiója      |
| **\<IPv4-címtér>**   | 192.168.0.0\16          |
| **\<alhálózat-név>**          | mySubnet        |
| **\<alhálózati címtartomány>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Virtuális gép létrehozása a NAT-átjáró használatához

Most hozzon létre egy virtuális gép a NAT szolgáltatás használatához. Ez a virtuális gép egy nyilvános IP-cím ként használható példányszintű nyilvános IP-cím, amely lehetővé teszi a virtuális gép eléréséhez. A NAT-szolgáltatás áramlásirány-függő, és lecseréli az alhálózat alapértelmezett internetes célját. A virtuális gép nyilvános IP-címe nem lesz használva a kimenő kapcsolatokhoz.

1. A portál bal felső részén válassza az Erőforrás létrehozása**Számítási** > **Ubuntu Server 18.04 LTS** **lehetőséget,** > vagy keressen az **Ubuntu Server 18.04 LTS elemre** a Marketplace-keresésben.

2. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget.**
   - **Példány részletei** > **Virtuális gép neve**: Írja be a **myVM**nevet.
   - **A példány részletei** > **régió** > válassza az USA **keleti régióját 2**.
   - **Rendszergazdai fiók** > **hitelesítésének típusa**: Válassza a **Jelszó**lehetőséget.
   - **Rendszergazdai fiók** > Adja meg a **Felhasználónevet**, **a Jelszót**és a **Jelszó megerősítése** adatokat.
   - **Bejövő portszabályok** > **Bejövő portok :** A kijelölt **portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > **Bejövő portok kiválasztása**: Válassza az **SSH (22)** lehetőséget
   - Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget.

3. A **Hálózat** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnet**
   - **Alhálózat**: **mySubnet**
   - **Nyilvános IP >** Válassza **az Új létrehozása lehetőséget.**  A **Nyilvános IP-cím létrehozása** ablakban írja be a **myPublicIPVM parancsot** a **Név** mezőbe, és válassza a **Termékváltozat Szokásos** **elemét.**  Kattintson az **OK** gombra.
   - **Hálózati adapter hálózati biztonsági csoportja:** Válassza **az Alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: Válassza **a Kijelölt portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: Az **SSH** megerősítése van kiválasztva.

4. A **Kezelés** lap **Figyelés**csoportjában állítsa a **rendszerindítási diagnosztikát** **Ki**beállításra.

5. Válassza az **Áttekintés + létrehozás** lehetőséget. 

6. Tekintse át a beállításokat, és kattintson a **Létrehozás gombra.**

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

Használhat egy vagy több nyilvános IP-címerőforrást, nyilvános IP-előtagot vagy mindkettőt. Hozzáadjuk a nyilvános IP-erőforrást, a nyilvános IP-előtagot és a NAT átjáró-erőforrást.

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1.  > A portál bal felső részén válassza az Erőforrás**létrehozása nyilvános** > **IP-cím** **lehetőséget,** vagy keressen **nyilvános IP-címet** a Piactér-keresésben.

2. A **Nyilvános IP-cím létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | IP-verzió | Válassza az **IPv4** lehetőséget.
    | SKU | Válassza a **Normál**lehetőséget.
    | Név | Adja meg **myPublicIP**. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroupNAT lehetőséget.** |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|

3. Hagyja meg a többi alapértelmezést, és válassza a **Létrehozás lehetőséget.**

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

1. A portál bal felső részén válassza az **Erőforrás** > **hálózati** > **nyilvános IP-előtag**létrehozása lehetőséget, vagy keressen **nyilvános IP-előtagot** a Piactér-keresésben. 

2. A **Nyilvános IP-előtag létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget**>
   - **A példány részletei** > **:** Írja be a **myPublicIPprefix nevet.**
   - **Példány részletei** > **Régió**: Válassza az USA **keleti régiója 2**lehetőséget.
   - **Példány** > **részleteielőtag mérete**: Select **/31 (2 címek)**

3. Hagyja a többit az alapértelmezett értékekre, és válassza a **Véleményezés + create lehetőséget.**

4. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**
   

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

1. **Networking** > A portál bal felső részén válassza az **Erőforrás** > hálózati**hálózati hálózati átjáró**létrehozása lehetőséget, vagy keressen **NAT-átjárót** a Piactéri keresésben.

2. A **Hálózati címfordítás (NAT) átjáró létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget.**
   - **A példány részletei** > **NAT-átjáró neve**: Írja be a **myNATgateway nevet.**
   - **Példány részletei** > **Régió**: Válassza az USA **keleti régiója 2**lehetőséget.
   - **A példány részletei** > **tétlen időhosszabbítás (perc):** **10-es**típus.
   - Válassza a **Nyilvános IP** lapot, vagy válassza **a Tovább: Nyilvános IP**lehetőséget.

3. A **Nyilvános IP** lapon írja be vagy válassza ki a következő értékeket:
   - **Nyilvános IP-címek**: Válassza a **myPublicIP**lehetőséget.
   - **Nyilvános IP-előtagok**: Válassza a **myPublicIPprefix lehetőséget.**
   - Válassza az **Alhálózat** lapot, vagy válassza **a Tovább: Alhálózat**lehetőséget.

4. Az **Alhálózat** lapon írja be vagy jelölje ki a következő értékeket:
   - **Virtuális hálózat**: Válassza a **myResourceGroupNAT** > **myVnet lehetőséget.**
   - **Alhálózat neve**: Jelölje ki a **mySubnet melletti jelölőnégyzetet.**

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felfedezése

1. A portál bal oldalán válassza az **Erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT lehetőséget.**
3. Válassza a **myVM**lehetőséget.
4. Az **Áttekintés alkalmazásban**másolja a Nyilvános **IP-cím** értékét, és illessze be a jegyzettömbbe, így a virtuális gép eléréséhez használhatja.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, így használhatja a virtuális gép eléréséhez.

## <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépbe

Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Jelölje ki a NAT-átjárót tartalmazó **myResourceGroupNAT** erőforráscsoportot, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatához. 

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával történik.


- Learn about [Azure Virtual Network NAT](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.
> [!div class="nextstepaction"]

