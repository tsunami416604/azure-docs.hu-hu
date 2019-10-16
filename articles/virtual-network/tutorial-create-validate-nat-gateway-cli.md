---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure CLI'
titlesuffix: Azure NAT service
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával, és hogyan tesztelheti a NAT szolgáltatást
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.custom: seodec18
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: f97284b3c629dbaab1b0cbb54b1f114342278da7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376489"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure CLI használatával és a NAT szolgáltatás tesztelése

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure NAT szolgáltatást, és hogyan hozhat létre NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. A NAT-átjáró teszteléséhez üzembe kell helyeznie egy forrás-és cél virtuális gépet.  Tesztelje a NAT-átjárót úgy, hogy kimenő kapcsolatokat küld a forrás és a cél virtuális gép között a nyilvános IP-címhez.  Ez az oktatóanyag a forrás és a cél két különböző virtuális hálózatban való üzembe helyezését végzi el, csak az egyszerűség kedvéért.

>[!NOTE] 
>Az Azure NAT szolgáltatás jelenleg nyilvános előzetes verzióként érhető el, és korlátozott számú [régióban](https://azure.microsoft.com/global-infrastructure/regions/)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával végezheti el, vagy a megfelelő parancsokat helyileg futtathatja.  Ha még soha nem használta az Azure Cloud shellt, [Jelentkezzen be most](https://shell.azure.com) , és lépjen be a kezdeti beállításba.

Ha a parancsok helyi futtatását választja, telepítenie kell a CLI-t.  Ehhez az oktatóanyaghoz az Azure CLI 2.0.71 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internethez való hozzáféréshez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPsource** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Egy vagy több nyilvános IP-cím-erőforrást, illetve egy vagy több nyilvános IP-előtagot vagy mindkettőt használhat a NAT-átjáróval. Ehhez a forgatókönyvhöz hozzá fogunk adni egy nyilvános IP-előtag-erőforrást, amely bemutatja.   Az [az Network Public-IP előtag Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) paranccsal hozzon létre egy **myPublicIPprefixsource** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót az [az Network NAT Gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) nevű **myNATgateway** , amely a nyilvános IP- **myPublicIPsource** és a nyilvános IP-előtag **myPublicIPprefixsource** is használja, és az üresjárati időkorlátot a következőre módosítja **10 perc**.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomhoz

A következő lépésekben végigvezeti Önt a teljes tesztkörnyezet konfigurálásán és a tesztek végrehajtásán. Kezdjük a forrással, amely a korábban létrehozott NAT Gateway-erőforrást fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása a forráshoz

Mielőtt üzembe helyez egy virtuális GÉPET, és tesztelni tudja a NAT-átjárót, létre kell hoznia a virtuális hálózatot.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot egy **mySubnetsource** nevű alhálózattal a **myResourceGroupNAT** az [az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet)paranccsal.  A virtuális hálózat IP-címe **192.168.0.0/16** , a virtuális hálózaton belüli alhálózat pedig **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT szolgáltatás konfigurálása a forrás-alhálózathoz

Már létrehoztuk a NAT-átjárót, és most már konfiguráltuk a virtuális hálózati **myVnetsource** található **mySubnetsource** , hogy egy adott NAT Gateway erőforrás- **myNATsource** használják az [az Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)paranccsal.  Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  A UDR konfigurálása nem szükséges.

A NAT-átjáró teszteléséhez létre kell hoznia egy forrásoldali virtuális gépet.  Egy nyilvános IP-cím típusú erőforrást egy példány szintű nyilvános IP-címként rendelünk hozzá, hogy a virtuális gép kívülről is hozzáférhessen. Ez a címe csak a teszthez való hozzáféréshez használatos.  Bemutatjuk, hogy a NAT szolgáltatás milyen elsőbbséget élvez a többi kimenő lehetőséggel szemben.

Azt is megteheti, hogy nyilvános IP-cím nélkül hozza létre ezt a virtuális gépet, és egy olyan virtuális gépet hoz létre, amelyet Jumpbox használ nyilvános IP-cím nélkül.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amely a forrás virtuális gép elérésére szolgál. Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPsourceVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>NSG létrehozása a forrásként szolgáló virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", létre kell hozni egy NSG, amely lehetővé teszi az SSH-hozzáférés bejövő hozzáférését.  Mivel a NAT-szolgáltatás a flow iránya, a NSG nem fogja tudni használni a kimenő forgalmat, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) paranccsal hozzon létre egy **myNSGsource** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont közzététele a forrásoldali virtuális gépen

Hozzunk létre egy szabályt a NSG a forrás virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG-SZABÁLYT a **myNSGsource** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása forrásként szolgáló virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP-címet és a hálózati biztonsági csoportot. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Forrásként szolgáló virtuális gép létrehozása

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-destination-for-outbound-traffic"></a>Cél előkészítése a kimenő forgalomhoz

Most létrehozunk egy célhelyet a NAT szolgáltatás által lefordított kimenő forgalomhoz, hogy tesztelni lehessen.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása célhelyként

A virtuális gép és a célhely üzembe helyezése előtt létre kell hozni egy virtuális hálózatot, amelyben a cél virtuális gép elhelyezhető.  Ezek ugyanazok a lépések, mint a forrás virtuális gép esetében, és néhány kisebb módosítással teszi elérhetővé a cél végpontot.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **mySubnetdestination** nevű alhálózattal a **myResourceGroupNAT** az [az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet)paranccsal.  A virtuális hálózat IP-címe **192.168.0.0/16** , a virtuális hálózaton belüli alhálózat pedig **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP-cím létrehozása a cél virtuális géphez

Létrehozunk egy nyilvános IP-címet, amely a forrás virtuális gép elérésére szolgál. Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPdestinationVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM
  --sku standard
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>NSG létrehozása a cél virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", létre kell hozni egy NSG, amely lehetővé teszi az SSH-hozzáférés bejövő hozzáférését.  Mivel a NAT-szolgáltatás a flow iránya, a NSG nem fogja tudni használni a kimenő forgalmat, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) paranccsal hozzon létre egy **myNSGdestination** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-végpont közzététele a cél virtuális gépen

Hozzunk létre egy szabályt a NSG a cél virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG-SZABÁLYT a **myNSGdestination** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-végpont közzététele a cél virtuális gépen

Létre kell hozni egy szabályt a NSG a cél virtuális géphez való HTTP-hozzáféréshez. Az [az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) paranccsal hozzon létre egy **http** nevű NSG-SZABÁLYT a **myNSGdestination** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Hálózati adapter létrehozása a cél virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP- **myPublicIPdestinationVM** és a hálózati biztonsági csoport **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM
    --network-security-group myNSGdestination
    --location westus
```

### <a name="create-a-destination-vm"></a>Cél virtuális gép létrehozása

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    --location westus
```
Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló és tesztelési tartalom előkészítése a célként megadott virtuális gépen

Először fel kell deríteni a cél virtuális gép IP-címét.  A cél virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)lehetőséget. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a cél virtuális gépre

Az SSH hitelesítő adatait az előző műveletből kell tárolni a Cloud shellben.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh <ip-address-destination>
```

Másolja ki és illessze be az alábbi parancsokat a bejelentkezés után.  

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

Ezek a parancsok frissítik a virtuális gépet, telepítik az Nginx-et, és létrehozunk egy 100 kilobájtos fájlt, amelyet a forrás virtuális gépről a NAT szolgáltatás használatával lehet lekérni.

A célként megadott virtuális géppel zárjuk be az SSH-munkamenetet.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először fel kell deríteni a forrás virtuális gép IP-címét.  A forrás virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)lehetőséget. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépre

Az SSH hitelesítő adatait a Cloud Shell tárolja. Nyisson meg egy új lapot a böngészőben [Azure Cloud Shellhoz](https://shell.azure.com) .  Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh <ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT szolgáltatás tesztelésének előkészítéséhez.

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

Ezzel frissíti a virtuális gépet, telepítheti a Go-t, telepítheti a [Hey](https://github.com/rakyll/hey) -t a githubról, és frissítheti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT szolgáltatás ellenőrzése

Ha bejelentkezett a forrás virtuális gépre, a **curl** és a **Hey** használatával is létrehozhat KÉRÉSEKET a cél IP-címhez.

Az 100 kilobájtos fájl beolvasásához használja a curlt.  Cserélje le a **\<ip-cím-destination >** az alábbi példában az előzőleg másolt cél IP-címmel.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

A **Hey**használatával több kérelem is létrehozható. Újra cserélje le a **\<ip-cím-destination >t** az előzőleg másolt cél IP-címére.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ez 100 kérelmet fog létrehozni, 10 párhuzamosan, 30 másodperces időkorláttal, és a TCP-kapcsolat újbóli használata nélkül.  Minden kérelem 100 KB-ot fog beolvasni.  A Futtatás végén a **Hey** jelentést készít arról, hogy milyen jól teljesíti a NAT-szolgáltatást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozta a forrás és a cél virtuális gépet, majd tesztelte a NAT-átjárót. Ha többet szeretne megtudni az Azure NAT szolgáltatásról, folytassa az Azure NAT szolgáltatással kapcsolatos egyéb oktatóanyagokkal.

A Azure Monitor mérőszámait megtekintheti a NAT szolgáltatás működésének megtekintéséhez és az olyan problémák diagnosztizálásához, mint például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

> [!div class="nextstepaction"]

