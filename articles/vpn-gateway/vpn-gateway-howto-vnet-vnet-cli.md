---
title: "Virtuális hálózat csatlakoztatása másik virtuális hálózathoz: Azure CLI | Microsoft Docs"
description: "Ez a cikk lépésről lépésre bemutatja, hogyan csatlakoztathatók a virtuális hálózatok egymáshoz az Azure Resource Manager és az Azure CLI használatával."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: ff859bd9dbbf30c461cdba8409c77b04ff97b1f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Virtuális hálózatok közötti VPN Gateway-kapcsolat konfigurálása az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan hozható létre VPN Gateway-kapcsolat virtuális hálózatok között. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat, az előfizetéseket nem kell társítani ugyanazzal az Active Directory-bérlővel. 

A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak, és az Azure CLI-t használják. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Ha a virtuális hálózatai azonos régióban találhatóak, fontolja meg az összekötésüket virtuális hálózatok közötti társviszony útján. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal egyesítik, ahogyan azt a következő diagram mutatja:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-cli/aboutconnections.png)

### <a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál.

### <a name="which-set-of-steps-should-i-use"></a>Melyik eljárást használjam?

Ebben a cikkben kétféle lépéssorozatot láthat. Az egyik lépéssorozat az [azonos előfizetésben található virtuális hálózatokra](#samesub), a másik az [eltérő előfizetésekben található virtuális hálózatokra](#difsub) vonatkozik.

## <a name="samesub"></a>Azonos előfizetésben található virtuális hálózatok összekapcsolása

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

### <a name="before-you-begin"></a>Előkészületek

A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI 2.0-s verziójának telepítése](/cli/azure/install-azure-cli).

### <a name="Plan"></a>Az IP-címtartományok megtervezése

A következő lépések során két virtuális hálózatot fogunk létrehozni az azokhoz tartozó átjáróalhálózatokkal és konfigurációkkal. Ezután létrehozunk egy VPN-kapcsolatot a két virtuális hálózat között. Fontos, hogy megtervezze a hálózati konfiguráció IP-címtartományát. Ügyeljen arra, hogy a virtuális hálózata tartományai és a helyi hálózata tartományai ne legyenek átfedésben egymással. Ezekben a példákban nem szerepel DNS-kiszolgáló. Ha névfeloldással szeretné ellátni a virtuális hálózatokat, tekintse meg a [Névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) című cikket.

A példákban a következő értékeket használjuk:

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Erőforráscsoport: TestRG1
* Hely: East US
* TestVNet1: 10.11.0.0/16 és 10.12.0.0/16
* Előtér: 10.11.0.0/24
* Háttér: 10.12.0.0/24
* Átjáróalhálózat: 10.12.255.0/27
* Átjáró neve: VNet1GW
* Nyilvános IP-cím: VNet1GWIP
* VPN típusa: RouteBased
* Kapcsolat (1–4): VNet1toVNet4
* Kapcsolat (1–5): VNet1toVNet5
* Kapcsolat típusa: VNet2VNet

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* TestVNet2: 10.41.0.0/16 és 10.42.0.0/16
* Előtér: 10.41.0.0/24
* Háttér: 10.42.0.0/24
* Átjáróalhálózat: 10.42.255.0/27
* Erőforráscsoport: TestRG4
* Hely: West US
* Átjáró neve: VNet4GW
* Nyilvános IP-cím: VNet4GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet4toVNet1
* Kapcsolat típusa: VNet2VNet


### <a name="Connect"></a>1. lépés – Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>2. lépés – A TestVNet1 létrehozása és konfigurálása

1. Hozzon létre egy erőforráscsoportot.

  ```azurecli
  az group create -n TestRG1  -l eastus
  ```
2. Hozza létre a TestVNet1-et és az alhálózatait. Ez a példa létrehoz egy TestVNet1 nevű virtuális hálózatot és egy FrontEnd nevű alhálózatot.

  ```azurecli
  az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
  ```
3. Hozzon létre egy új címteret a háttérrendszer alhálózatának. Megfigyelheti, hogy ebben a lépésben a korábban létrehozott címteret és a hozzáadni kívánt új címteret is meghatározzuk. Ennek az az oka, hogy az [az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_update) parancs felülírja az előző beállításokat. Győződjön meg róla, hogy a parancs használatakor az összes címelőtagot meghatározza.

  ```azurecli
  az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
  ```
4. Hozza létre a háttérrendszer alhálózatát.
  
  ```azurecli
  az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
  ```
5. Hozza létre az átjáró-alhálózatot. Figyelje meg, hogy az átjáró alhálózatának neve „GatewaySubnet”. A név megadása kötelező. A példában az átjáróalhálózat /27-es alhálózatot használ. Bár lehetséges akár /29-es átjáróalhálózatot is létrehozni, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: legalább /28-asat vagy /27-eset. Ez elegendő címet biztosít az esetleges későbbi konfigurációkhoz.

  ```azurecli 
  az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
  ```
6. Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Vegye figyelembe, hogy az AllocationMethod értéke Dynamic. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik.

  ```azurecli
  az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
  ```
7. Hozza létre a TestVNet1 virtuális hálózati átjáróját. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. Ha ezt a parancsot a „--no-wait” paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. A „--no-wait” paraméter lehetővé teszi, hogy az átjáró a háttérben jöjjön létre. Ez nem jelenti azt, hogy a VPN-átjáró azonnal elkészült. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró termékváltozatától függően.

  ```azurecli
  az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="TestVNet4"></a>3. lépés – A TestVNet4 létrehozása és konfigurálása

1. Hozzon létre egy erőforráscsoportot.

  ```azurecli
  az group create -n TestRG4  -l westus
  ```
2. Hozza létre a TestVNet4-et.

  ```azurecli
  az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
  ```

3. Hozzon létre további alhálózatokat a TestVNet4-hez.

  ```azurecli
  az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
  az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
  ```
4. Hozza létre az átjáró-alhálózatot.

  ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
  ```
5. Kérjen egy nyilvános IP-címet.

  ```azurecli
  az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
  ```
6. Hozza létre a TestVNet4 virtuális hálózati átjáróját.

  ```azurecli
  az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="createconnect"></a>4. lépés – A kapcsolatok létrehozása

Most már két, VPN-átjáróval rendelkező virtuális hálózata van. A következő lépésként létre kell hoznia a VPN-átjáró kapcsolatait a virtuális hálózat átjárói között. Ha a fenti példákat követte, a virtuális hálózat átjárói eltérő erőforráscsoportokban találhatók. Ha az átjárók eltérő erőforráscsoportokban vannak, a kapcsolatok létrehozásakor meg kell határoznia és azonosítania kell minden átjáró erőforrás-azonosítóját. Ha a virtuális hálózatok ugyanabban az erőforráscsoportban találhatók, követheti a [második útmutatót](#samerg), ugyanis nem kell meghatároznia az erőforrás-azonosítókat.

### <a name="diffrg"></a>Eltérő erőforráscsoportokban található virtuális hálózatok csatlakoztatása

1. Az alábbi parancs kimenetéből olvassa le a VNet1GW erőforrás-azonosítóját:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  A kimenetben keresse meg az „id:” sort. A csatlakozás a következő szakaszban való létrehozásához az idézőjelek közötti értékre lesz szüksége. Másolja ezeket az értékeket egy szövegszerkesztőbe, például a Jegyzettömbbe, így a csatlakozás létrehozásakor könnyen beillesztheti őket.

  Példa a kimenetre:

  ```
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
  "enableBgp": false, 
  "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
  "ipConfigurations":
  ```

  Másolja ki az **„id”:** kifejezés utáni idézőjelek közti értékeket.

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
 ```

2. Olvassa le a VNet4GW erőforrás-azonosítóját, és másolja az értékeket egy szövegszerkesztőbe.

  ```azurecli
  az network vnet-gateway show -n VNet4GW -g TestRG4
  ```

3. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vesz igénybe.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
  ```
4. Hozza létre a TestVNet4–TestVNet1 kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat létrehozása néhány percet vesz igénybe.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
  ```
5. Ellenőrizze a kapcsolatokat. Lásd: [A kapcsolat ellenőrzése](#verify).

### <a name="samerg"></a>Azonos erőforráscsoportokban található virtuális hálózatok csatlakoztatása

1. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. Figyelje meg, hogy a példák erőforráscsoportjai megegyeznek. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz saját értékeket is használhat, azonban a kulcsnak mindkét kapcsolat esetében azonosnak kell lennie. A kapcsolat létrehozása egy kis időt vesz igénybe.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
  ```
2. Hozza létre a TestVNet4–TestVNet1 kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat létrehozása néhány percet vesz igénybe.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
  ```
3. Ellenőrizze a kapcsolatokat. Lásd: [A kapcsolat ellenőrzése](#verify).

## <a name="difsub"></a>Különböző előfizetésben található virtuális hálózatok összekapcsolása

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)

Ebben a forgatókönyvben csatlakoztatjuk a TestVNet1 és a TestVNet5 virtuális hálózatot. A virtuális hálózatok eltérő előfizetésben találhatók. Az előfizetéseket nem kell társítani ugyanazzal az Active Directory bérlővel. A konfiguráláshoz használt lépések egy további, virtuális hálózatok közötti kapcsolatot hoznak létre a TestVNet1 és a TestVNet5 összekapcsolásához.

### <a name="TestVNet1diff"></a>5. lépés – A TestVNet1 létrehozása és konfigurálása

Ezek az utasítások az előző szakaszok lépéseit folytatják. Az [1. lépés](#Connect) és a [2. lépés](#TestVNet1) elvégzésével hozza létre és konfigurálja a TestVNet1-et, valamint a TestVNet1 VPN-átjáróját. Ehhez a konfigurációhoz nem kell létrehoznia az előző szakaszban a TestVNet4-et, azonban ha már megtette, az sem akadályozza az alábbi lépések végrehajtását. Miután elvégezte az 1. lépést és a 2. lépést, folytassa a 6. lépéssel (lásd alább).

### <a name="verifyranges"></a>6. lépés – Az IP-címtartományok ellenőrzése

A további kapcsolatok létrehozásakor fontos ellenőrizni, hogy az új virtuális hálózat IP-címtere ne legyen átfedésben a többi virtuális hálózati vagy hálózati átjárói tartománnyal. A gyakorlatban a TestVNet5 hálózathoz a következő értékeket használhatja:

**Értékek a TestVNet5-höz:**

* Virtuális hálózat neve: TestVNet5
* Erőforráscsoport: TestRG5
* Hely: Japan East
* TestVNet5: 10.51.0.0/16 és 10.52.0.0/16
* Előtér: 10.51.0.0/24
* Háttér: 10.52.0.0/24
* Átjáróalhálózat: 10.52.255.0.0/27
* Átjáró neve: VNet5GW
* Nyilvános IP-cím: VNet5GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet5toVNet1
* Kapcsolat típusa: VNet2VNet

### <a name="TestVNet5"></a>7. lépés – A TestVNet5 létrehozása és konfigurálása

Ezt a lépést az új előfizetés (5. előfizetés) környezetében kell elvégezni. Ezt a részt azon másik szervezet rendszergazdájának kell elvégeznie, amely az előfizetés tulajdonosa. Ha váltani szeretne az előfizetések között, használja az „az account list --all” parancsot a fiókban elérhető előfizetések listájának megjelenítéséhez, majd az „az account set --subscription <subscriptionID>” parancsot a használni kívánt előfizetésre váltáshoz.

1. Győződjön meg róla, hogy az 5. előfizetéshez csatlakozik, majd hozzon létre egy erőforráscsoportot.

  ```azurecli
  az group create -n TestRG5  -l japaneast
  ```
2. Hozza létre a TestVNet5-öt.

  ```azurecli
  az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
  ```

3. Adjon hozzá alhálózatokat.

  ```azurecli
  az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
  az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
  ```

4. Adja hozzá az átjáró alhálózatát.

  ```azurecli
  az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
  ```

5. Kérjen egy nyilvános IP-címet.

  ```azurecli
  az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
  ```
6. A TestVNet5 átjárójának létrehozása

  ```azurecli
  az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="connections5"></a>8. lépés – A kapcsolatok létrehozása

Ezt a lépést két CLI-munkamenetre osztottuk fel, amelyek jelölése **[1. előfizetés]** és **[5. előfizetés]**, mivel az átjárók eltérő előfizetésekben találhatók. Ha váltani szeretne az előfizetések között, használja az „az account list --all” parancsot a fiókban elérhető előfizetések listájának megjelenítéséhez, majd az „az account set --subscription <subscriptionID>” parancsot a használni kívánt előfizetésre váltáshoz.

1. **[1. előfizetés]** Jelentkezzen be, és csatlakozzon az 1. előfizetéshez. Az alábbi parancs kimenetéből olvassa le az átjáró nevét és azonosítóját:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  Másolja ki az „id:” kifejezés kimenetét. Küldje el e-mailben vagy másképp a VNet-átjáró (VNet1GW) azonosítóját és nevét az 5. előfizetés rendszergazdájának.

  Példa a kimenetre:

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
  ```

2. **[5. előfizetés]** Jelentkezzen be, és csatlakozzon az 5. előfizetéshez. Az alábbi parancs kimenetéből olvassa le az átjáró nevét és azonosítóját:

  ```azurecli
  az network vnet-gateway show -n VNet5GW -g TestRG5
  ```

  Másolja ki az „id:” kifejezés kimenetét. Küldje el e-mailben vagy másképp a VNet-átjáró (VNet5GW) azonosítóját és nevét az 1. előfizetés rendszergazdájának.

3. **[1. előfizetés]** Ebben a lépésben a TestVNet1 felől a TestVNet5 felé irányuló kapcsolatot hozza létre. A megosztott kulcshoz saját értékeket is használhat, azonban a kulcsnak mindkét kapcsolat esetében azonosnak kell lennie. A kapcsolat létrehozása egy kis időt vehet igénybe. Kapcsolódjon az 1. előfizetéshez.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```

4. **[5. előfizetés]** Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet5 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Győződjön meg róla, hogy a megosztott kulcsok egyeznek, valamint arról, hogy az 5. előfizetéshez csatlakozik.

  ```azurecli
  az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```

## <a name="verify"></a>A kapcsolatok ellenőrzése
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections v2v cli](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [Virtual Machines-dokumentációt](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
