---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával, és hogyan tesztelhatheti a NAT-szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b3e10b3abbe5c9815e51ce67786882dbd294df3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202222"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure CLI használatával, és a NAT-szolgáltatás tesztelése

Ebben az oktatóanyagban egy NAT-átjárót hoz létre, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. A NAT-átjáró teszteléséhez telepítsen egy forrás- és célvirtuális gépet. A NAT-átjárót nyilvános IP-címhez való kimenő kapcsolatokkal tesztelheti. Ezek a kapcsolatok a forrástól a cél virtuális gépig érkeznek. Ez az oktatóanyag csak az egyszerűség kedvéért telepíti a forrást és a célt két különböző virtuális hálózatba ugyanabban az erőforráscsoportban.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával befejezheti, vagy helyileg futtathatja a megfelelő parancsokat.  Ha még nem használta az Azure Cloud Shellt, [most kell bejelentkeznie.](https://shell.azure.com)

Ha úgy dönt, hogy ezeket a parancsokat helyileg futtatja, telepítenie kell a CLI-t.  Ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.71-es vagy újabb verziójának verzióját futtassa. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy **myResourceGroupNAT** nevű erőforráscsoportot az **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internet eléréséhez egy vagy több nyilvános IP-címre van szükség a NAT-átjáróhoz. Használja [az hálózati nyilvános ip create-t](https://docs.microsoft.com/cli/azure/network/public-ip) egy **myPublicIPsource** nevű nyilvános IP-címerőforrás létrehozásához a **myResourceGroupNAT-ban.**

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Egy vagy több nyilvános IP-címerőforrást, nyilvános IP-előtagokat vagy mindkettőt használhatja a NAT átjáróval. Ehhez a forgatókönyvhöz egy nyilvános IP-előtag-erőforrást adunk hozzá.   Használja [az az hálózati nyilvános ip előtagot,](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) hogy hozzon létre egy nyilvános IP-előtag erőforrás nevű **myPublicIPprefixsource** a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a **myNATgateway**nevű [hálózati nat átjáróval.](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) A parancs a nyilvános IP-címet **használja myPublicIP** és a nyilvános IP előtag **myPublicIPprefix**. A parancs az tétlen időoutot is 10 percre módosítja.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Ezen a ponton a NAT-átjáró működőképes, és csak azt kell konfigurálni, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomra

Végigvezetjük a teljes tesztkörnyezet beállításán. A NAT-átjáró ellenőrzéséhez nyílt forráskódú eszközökkel állíthat be tesztet. Kezdjük a forrással, amely a korábban létrehozott NAT átjárót fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása forráshoz

Mielőtt üzembe helyezne egy virtuális gépet, és tesztelheti a NAT-átjárót, létre kell hoznunk a virtuális hálózatot.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot egy **myResourceGroupNAT** nevű alhálózattal a Microsoft Azure Virtuális hálózat létrehozása az [hálózati Microsoft Azure Virtuális hálózat létrehozása használatával.](https://docs.microsoft.com/cli/azure/network/vnet) **mySubnetsource**  A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-szolgáltatás konfigurálása a forrásalhálózathoz

Konfigurálja a forrásalhálózat **mySubnetsource** virtuális hálózati **myVnetsource** egy adott NAT **átjáró-szolgáltatás myNATgateway-t** az [hálózati Microsoft Azure virtuális hálózat alhálózati frissítéssel.](https://docs.microsoft.com/cli/azure/network/vnet/subnet) Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Az internetes célállomásokra irányuló összes kimenő forgalom most már a NAT szolgáltatást használja.  Nem szükséges konfigurálni az UDR-t.

Mielőtt tesztelhetnénk a NAT-átjárót, létre kell hoznunk egy forrás virtuális gép.  A virtuális gép kívülről való eléréséhez egy nyilvános IP-címerőforrást rendelünk hozzá példányszintű nyilvános IP-címként. Ez a cím csak a teszthez való hozzáféréshez használható.  Bemutatjuk, hogy a NAT szolgáltatás hogyan élvez elsőbbséget a többi kimenő beállítással szemben.

A virtuális gépet nyilvános IP-cím nélkül is létrehozhatja, és létrehozhat egy másik virtuális gépet, amelyet nyilvános IP-cím nélkül használhat gyakorlatként.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP létrehozása a forrás virtuális géphez

Létrehozunk egy nyilvános IP-t a forrás virtuális gép eléréséhez. Használja [az hálózati nyilvános ip create-t](https://docs.microsoft.com/cli/azure/network/public-ip) egy **myPublicIPsourceVM** nevű nyilvános IP-címerőforrás létrehozásához a **myResourceGroupNAT-ban.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>NSG létrehozása a forrásvirtuális géphez

Mivel a standard nyilvános IP-címek "alapértelmezés szerint biztonságosak", létre kell hoznunk egy NSG-t, hogy engedélyezzük az ssh-hozzáférés bejövő elérését.  Az Azure NAT-szolgáltatás áramlásirány-tudatos. Ez az NSG nem lesz használható a kimenő, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [hálózati nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) használatával hozzon létre egy **myNSGsource** nevű NSG-erőforrást a **myResourceGroupNAT-ban.**

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont felfedése a forrás virtuális gépén

Létrehozunk egy szabályt az NSG-ben az SSH-hozzáférés a forrás vm.We create a rule in the NSG for SSH access to the source vm. Az [hálózati nsg szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) az **ssh**nevű NSG-szabály létrehozásához. Ez a szabály a **myResourceGroupNAT**erőforráscsoport **myNSGsource** nevű NSG-jében jön létre.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása a forrásvirtuális géphez

Hozzon létre egy hálózati [adaptert az az network nic](/cli/azure/network/nic#az-network-nic-create) létrehozásával, és társítani a nyilvános IP-címet és a hálózati biztonsági csoportot. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Forrásvirtuális gép létrehozása

Hozza létre a virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create)segítségével.  SSH-kulcsokat hozunk létre ehhez a virtuális géphez, és tároljuk a később használandó személyes kulcsot.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Bár a parancs azonnal visszatér, eltarthat néhány percig a virtuális gép üzembe helyezése.

## <a name="prepare-destination-for-outbound-traffic"></a>Úti cél előkészítése a kimenő forgalomra

Most hozzon létre egy céla a kimenő forgalom által lefordított NAT szolgáltatás lehetővé teszi, hogy tesztelje azt.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása a célhoz

 Létre kell hoznunk egy virtuális hálózatot, ahol a cél virtuális gép lesz.  Ezek a parancsok ugyanazokat a lépéseket, mint a forrás virtuális gép kis módosításokat, hogy a célvégpont.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **myResourceGroupNAT** nevű alhálózattal a Microsoft Azure Virtuális hálózat létrehozása az [hálózati microsoft Azure virtuális hálózat létrehozása használatával.](https://docs.microsoft.com/cli/azure/network/vnet) **mySubnetdestination**  A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP létrehozása a célvirtuális géphez

Létrehozunk egy nyilvános IP-t a forrás virtuális gép eléréséhez. Használja [az hálózati nyilvános ip create-t](https://docs.microsoft.com/cli/azure/network/public-ip) egy **myPublicIPdestinationVM** nevű nyilvános IP-címerőforrás létrehozásához a **myResourceGroupNAT-ban.** 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>NSG létrehozása a célvirtuális géphez

A szabványos nyilvános IP-címek "alapértelmezés szerint biztonságosak", létre kell hoznia egy NSG-t, hogy az ssh számára engedélyezze a bejövő hozzáférést. Az Azure NAT szolgáltatás áramlásirány-tudatos. Ez az NSG nem lesz használható a kimenő, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [hálózati nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) használatával hozzon létre egy **myNSGdestination** nevű NSG-erőforrást a **myResourceGroupNAT-ban.**

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-végpont felfedése a célvirtuális gépen

Létrehozunk egy szabályt az NSG-ben az SSH-hozzáféréshez a cél virtuális géphez. Az [hálózati nsg szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) az **ssh**nevű NSG-szabály létrehozásához. Ez a szabály a **myResourceGroupNAT** **erőforráscsoportmyNSGdestination** nevű NSG-jében jön létre.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-végpont felfedése a célvirtuális gépen

Létrehozunk egy szabályt az NSG HTTP-hozzáférés a cél vm. Az [hálózati nsg szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) segítségével hozzon létre egy **http** nevű NSG-szabályt a **myResourceGroupNAT** **myNSGdestination** nevű NSG-ben.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Hálózati adapter létrehozása a cél virtuális géphez

Hozzon létre egy hálózati [adaptert az az network nic létre,](/cli/azure/network/nic#az-network-nic-create) és társítani a nyilvános IP-címet **myPublicIPdestinationVM** és a hálózati biztonsági csoport **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Célvirtuális gép létrehozása

Hozza létre a virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create)segítségével.  SSH-kulcsokat hozunk létre ehhez a virtuális géphez, és tároljuk a később használandó személyes kulcsot.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Bár a parancs azonnal visszatér, eltarthat néhány percig a virtuális gép üzembe helyezése.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló előkészítése és a hasznos adatszolgáltatás tesztelése a célvirtuális gépen

Először is fel kell derítenünk a cél virtuális gép IP-címét.  A célvirtuális gép nyilvános IP-címének leigazolásához használja [az az network public-ip show -t.](/cli/azure/network/public-ip#az-network-public-ip-show) 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a célvirtuális gépre

Az SSH hitelesítő adatokat az előző műveletből származó Felhőrendszerhéjban kell tárolni.  Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez. 

```bash
ssh <ip-address-destination>
```

A bejelentkezés után másolja és illessze be a következő parancsokat.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Ezek a parancsok frissítik a virtuális gépet, telepítik a nginx-et, és létrehoznak egy 100 KBytes fájlt. Ez a fájl a NAT szolgáltatás használatával a forrásvirtuális gépről lesz beolvasva.

Zárja be az SSH-munkamenetet a cél virtuális géppel.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először is meg kell, hogy felfedezzék az IP-címét a forrás virtuális gép.  A forrás virtuális gép nyilvános IP-címének leéséhez használja az [az hálózati nyilvános ip show-t.](/cli/azure/network/public-ip#az-network-public-ip-show) 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a forrás virtuális gép.

### <a name="sign-in-to-source-vm"></a>Bejelentkezés a forrásvirtuális gépbe

Az SSH hitelesítő adatok ismét a Cloud Shellben tárolódnak. Nyisson meg egy új lapot az [Azure Cloud Shell](https://shell.azure.com) böngészőjében.  Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez. 

```bash
ssh <ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT-szolgáltatás tesztelésének előkészítéséhez.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ez a parancs frissíti a virtuális gépet, telepíti az ugrást, telepíti [a hé](https://github.com/rakyll/hey) a GitHubról, és frissíti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT-szolgáltatás ellenőrzése

A forrásvirtuális gépbe bejelentkezve a **curl** és **a hey** segítségével kérelmeket hozhat létre a cél IP-címére.

A göndörség segítségével olvassa be a 100 kbytes fájlt.  Cserélje ** \<** le az alábbi példában megadott ip-cím-cél>a korábban másolt cél IP-címre.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Azt is létrehozhat egy sor kérelmet a **hey**. Ismét cserélje le ** \<az ip-address-destination>** a korábban másolt cél IP-címre.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ez a parancs 100 kérelmet hoz létre, 10-et egyidejűleg, 30 másodperces időhosszabbítással. A TCP-kapcsolat nem lesz újra használatban.  Minden kérelem 100 kbyte-ot fog lekérni.  Végén a futás, **hé** jelentést néhány statisztikát arról, hogy milyen jól a NAT szolgáltatás nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozott egy forrás- és célvirtuális gép, majd tesztelte a NAT-átjárót.

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával.

- További információ a [virtuális hálózati hálózati hálózati kapcsolatról](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.

> [!div class="nextstepaction"]

