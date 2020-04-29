---
title: Privát hivatkozás beállítása
description: Privát végpont beállítása egy tároló-beállításjegyzékben és privát hivatkozás engedélyezése egy helyi virtuális hálózaton
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498918"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Azure Private-hivatkozás konfigurálása Azure Container registryhez 

Beállíthat egy [privát végpontot](../private-link/private-endpoint-overview.md) az Azure Container registryhez, hogy az Azure-beli virtuális hálózat ügyfelei biztonságosan hozzáférjenek a beállításjegyzékhez [privát kapcsolaton](../private-link/private-link-overview.md)keresztül. A magánhálózati végpont egy IP-címet használ a beállításjegyzékhez tartozó virtuális hálózati címtartomány alapján. A virtuális hálózat és a beállításjegyzék ügyfelei közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán lévő privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget.

Megadhatja a magánhálózati végpont [DNS-beállításait](../private-link/private-endpoint-overview.md#dns-configuration) , hogy a beállítások a beállításjegyzék lefoglalt magánhálózati IP-címére legyenek feloldva. A DNS-konfigurációval a hálózatban lévő ügyfelek és szolgáltatások továbbra is hozzáférhetnek a beállításjegyzékhez a beállításjegyzék teljes tartománynevénél, például *myregistry.azurecr.IO*.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry SKU](container-registry-skus.md)-i.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások](#preview-limitations) érvényesek. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Jelenleg nem állíthat be privát végponttal rendelkező magánhálózati kapcsolatot egy [földrajzilag replikált beállításjegyzékben](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Előfeltételek

* A cikkben szereplő Azure CLI-lépések használatához ajánlott az Azure CLI-es vagy újabb verziója. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Ha még nem rendelkezik tároló-beállításjegyzékkel, hozzon létre egyet (prémium szintű csomag szükséges), és küldjön `hello-world` le egy minta képet, például a Docker hub-ból. A beállításjegyzék létrehozásához például használja az [Azure Portal][quickstart-portal] vagy az [Azure CLI][quickstart-cli] -t.
* Ha egy másik Azure-előfizetésben lévő privát hivatkozás használatával szeretné konfigurálni a beállításjegyzék-hozzáférést, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésben lévő Azure Container Registryhoz. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

A cikkben szereplő Azure CLI-példák az alábbi környezeti változókat használják. A környezetnek megfelelő helyettesítő értékeket. Az összes példa a bash-rendszerhéjra van formázva:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Tesztelési célból használjon Docker-kompatibilis Ubuntu virtuális gépet az Azure Container Registry eléréséhez. Ha Azure Active Directory hitelesítést kíván használni a beállításjegyzékben, telepítse az [Azure CLI][azure-cli] -t is a virtuális gépre. Ha már rendelkezik Azure-beli virtuális géppel, ugorja át ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális géphez és a tároló-beállításjegyzékhez is. Ez a beállítás leegyszerűsíti a tisztítást a végén, de nem kötelező. Ha úgy dönt, hogy külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa az [az Group Create][az-group-create]parancsot:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Most helyezzen üzembe egy alapértelmezett Ubuntu Azure-beli virtuális gépet az [az VM Create][az-vm-create]paranccsal. A következő példa egy *myDockerVM*nevű virtuális gépet hoz létre.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs befejeződik, jegyezze fel az Azure `publicIpAddress` CLI által megjelenített adatmennyiséget. Ezzel a címtől SSH-kapcsolatokat hozhat a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futása után létesítsen SSH-kapcsolatokat a virtuális géppel. Cserélje le a *publicIpAddress* -t a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa az alábbi parancsokat a Docker telepítéséhez az Ubuntu virtuális gépen:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális gépen:

```bash
sudo docker run -it hello-world
```

Kimenet:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure CLI az Ubuntu rendszerű virtuális gépen való telepítéséhez kövesse az Azure CLI az [apt-vel](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) való telepítésének lépéseit. Például:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Lépjen ki az SSH-kapcsolatban.

## <a name="set-up-private-link---cli"></a>Privát hivatkozás beállítása – parancssori felület

### <a name="get-network-and-subnet-names"></a>Hálózati és alhálózatok nevének lekérése

Ha még nem rendelkezik velük, szüksége lesz egy virtuális hálózat és alhálózat nevére a privát hivatkozás beállításához. Ebben a példában ugyanazt az alhálózatot használja a virtuális géphez és a beállításjegyzék privát végpontja számára. Számos esetben azonban a végpontot külön alhálózatban kell beállítania. 

Amikor létrehoz egy virtuális GÉPET, az Azure alapértelmezés szerint ugyanahhoz az erőforráscsoporthoz hoz létre egy virtuális hálózatot. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gép *myDockerVM*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, és egy *myDockerVMSubnet*nevű alhálózattal rendelkezik. Állítsa be ezeket az értékeket környezeti változókba az az [Network vnet List][az-network-vnet-list] parancs futtatásával:

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Hálózati házirendek letiltása az alhálózatban

[Tiltsa le a hálózati házirendeket](../private-link/disable-private-endpoint-network-policy.md) , például a hálózati biztonsági csoportokat a magánhálózati végponthoz tartozó alhálózatban. Az alhálózat konfigurációjának frissítése az [az Network vnet subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása

Hozzon létre egy privát DNS-zónát a priviate Azure Container Registry-tartományhoz. A későbbi lépések során DNS-rekordokat hoz létre a beállításjegyzék-tartományhoz ezen a DNS-zónán belül.

Ha privát zónát szeretne használni az Azure Container Registry alapértelmezett DNS-feloldásának felülbírálásához, akkor a zónának **privatelink.azurecr.IO**nevűnek kell lennie. A privát zóna létrehozásához futtassa a következő az [Network Private-DNS Zone Create][az-network-private-dns-zone-create] parancsot:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Társítási hivatkozás létrehozása

Az az [Network Private-DNS link vnet Create][az-network-private-dns-link-vnet-create] paranccsal társíthatja a saját zónáját a virtuális hálózattal. Ez a példa egy *myDNSLink*nevű hivatkozást hoz létre.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Privát beállításjegyzékbeli végpont létrehozása

Ebben a szakaszban a beállításjegyzék saját végpontját hozza létre a virtuális hálózaton. Először szerezze be a beállításjegyzék erőforrás-AZONOSÍTÓját:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Futtassa az az [Network Private-Endpoint Create][az-network-private-endpoint-create] parancsot a beállításjegyzék privát végpontjának létrehozásához.

A következő példa létrehozza a végpont *myPrivateEndpoint* és a szolgáltatáshoz kapcsolódó *myConnection*. A végponthoz tartozó tároló beállításjegyzék-erőforrásának megadásához adja `--group-ids registry`meg a következőt:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Magánhálózati IP-címek lekérése

Futtassa az [az Network Private-Endpoint show][az-network-private-endpoint-show] parancsot a hálózati adapter azonosító végpontjának lekérdezéséhez:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

A hálózati adapterhez társított két magánhálózati IP-cím a tároló-beállításjegyzékhez: egy a beállításjegyzékhez, egy pedig a beállításjegyzék adatvégpontja. A tároló-beállításjegyzék és a beállításjegyzék adatvégpontjának magánhálózati IP-címeinek lekéréséhez futtassa a következőt az [Resource show][az-resource-show] paranccsal:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>DNS-rekordok létrehozása a privát zónában

A következő parancsok DNS-rekordokat hoznak létre a saját zónában a beállításjegyzék-végponthoz és az adatvégponthoz. Ha például egy *myregistry* nevű beállításjegyzék található a *westeurope* régióban, a végpontok nevei a következők `myregistry.azurecr.io` : és. `myregistry.westeurope.data.azurecr.io` 

Először futtassa az az [Network Private-DNS Record-set a Create (létrehozás][az-network-private-dns-record-set-a-create] ) lehetőséget, hogy üres rekordhalmazt hozzon létre a beállításjegyzék-végpont és az adatvégpont számára:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Futtassa az az [Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] parancsot a beállításjegyzék-végpont és az adatvégpont rekordjainak létrehozásához:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

A magánhálózati kapcsolat már konfigurálva van, és használatra kész.

## <a name="set-up-private-link---portal"></a>Privát hivatkozás beállítása – portál

A következő lépések feltételezik, hogy már rendelkezik virtuális hálózattal és alhálózattal a teszteléshez. [Létrehozhat egy új virtuális hálózatot és alhálózatot](../virtual-network/quick-create-portal.md)is.

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások**területen válassza a **privát végponti kapcsolatok (előzetes verzió)** lehetőséget.
1. Válassza a **+ privát végpont**lehetőséget.
1. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Adja meg egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | **Példány részletei** |  |
    | Name (Név) | Adjon meg egy egyedi nevet. |
    |Régió|Válasszon régiót.|
    |||
5. Válassza a **Tovább: erőforrás**elemet.
6. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban**lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. ContainerRegistry/nyilvántartások**lehetőséget. |
    | Erőforrás |Válassza ki a beállításjegyzék nevét|
    |Cél alerőforrás |**Beállításjegyzék** kiválasztása|
    |||
7. Válassza a **Tovább: konfigurálás**lehetőséget.
8. Adja meg vagy válassza ki az adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki azt a virtuális hálózatot, amelyben a virtuális gép telepítve van, például *myDockerVMVNET*. |
    | Alhálózat | Válasszon ki egy alhálózatot, például a *myDockerVMSubnet* , ahol a virtuális gép telepítve van. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |saját DNS zóna |Select *(új) privatelink.azurecr.IO* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
2. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

A magánhálózati végpont létrehozása után a magánhálózati zónában a DNS-beállítások a végpont **Áttekintés** lapján jelennek meg.

![Végpont DNS-beállításai](./media/container-registry-private-link/private-endpoint-overview.png)

A magánhálózati kapcsolat konfigurálva van, és használatra kész.

## <a name="validate-private-link-connection"></a>Privát kapcsolati kapcsolat ellenőrzése

Győződjön meg arról, hogy a privát végpont alhálózatán belüli erőforrások magánhálózati IP-címmel csatlakoznak a beállításjegyzékhez, és a saját DNS-zónák megfelelő integrációja szükséges.

A magánhálózati kapcsolat kapcsolatának ellenőrzéséhez SSH-kapcsolatot kell létesítenie a virtuális hálózatban beállított virtuális géppel.

Futtassa a `nslookup` parancsot a beállításjegyzék IP-címének feloldásához a privát hivatkozáson keresztül:

```bash
nslookup $registryName.azurecr.io
```

Például a kimenet a beállításjegyzék IP-címét jeleníti meg az alhálózat címterület:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Hasonlítsa össze ezt az eredményt a `nslookup` kimenetben lévő nyilvános IP-címmel egy nyilvános végponton keresztül:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Beállításjegyzékbeli műveletek privát kapcsolaton keresztül

Győződjön meg arról is, hogy az alhálózaton található virtuális gépről beállításjegyzék-műveleteket is végrehajthat. Létesítsen SSH-kapcsolatokat a virtuális géppel, és futtassa az [ACR login][az-acr-login] parancsot a beállításjegyzékbe való bejelentkezéshez. A virtuális gép konfigurációjától függően előfordulhat, hogy a következő parancsokat kell előadnia a `sudo`alkalmazásban.

```bash
az acr login --name $registryName
```

Olyan beállításjegyzék-műveleteket `docker pull` hajthat végre, mint például egy minta rendszerkép lekérése a beállításjegyzékből. Cserélje `hello-world:v1` le a t a beállításjegyzék megfelelő képére és címkéjére, és írja be a beállításjegyzék bejelentkezési kiszolgálójának nevét (minden kisbetűs):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a rendszerképet a virtuális gépre.

## <a name="manage-private-endpoint-connections"></a>Magánhálózati végpontok kapcsolatainak kezelése

Felügyelheti a beállításjegyzék saját végpont-kapcsolatait a Azure Portal használatával, vagy az az [ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] paranccsal elérhető parancsok használatával. A műveletek közé tartozik a beállításjegyzék privát végpont-kapcsolatainak jóváhagyása, törlése, listázása, elutasítása vagy megjelenítése.

Ha például egy beállításjegyzék privát végpont-kapcsolatait szeretné kilistázni, futtassa az az [ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] parancsot. Például:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Ha a jelen cikkben ismertetett lépésekkel állít be egy privát végponti kapcsolatot, a beállításjegyzék automatikusan fogadja a RBAC-engedélyekkel rendelkező ügyfelek és szolgáltatások kapcsolatait a beállításjegyzékben. Beállíthatja a végpontot a kapcsolatok manuális jóváhagyásának megköveteléséhez. További információ a privát végpontok kapcsolatainak jóváhagyásáról és elutasításáról: [privát végponti kapcsolat kezelése](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoporthoz hozta létre, és már nincs rájuk szükség, akkor az erőforrásokat egyetlen [az Group delete](/cli/azure/group) paranccsal törölheti:

```azurecli
az group delete --name $resourceGroup
```

A portálon található erőforrások törléséhez navigáljon az erőforráscsoporthoz. Miután betöltötte az erőforráscsoportot, kattintson az **erőforráscsoport törlése** elemre az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a privát hivatkozásokról, tekintse meg az [Azure Private link](../private-link/private-link-overview.md) dokumentációját.
* A privát hivatkozás helyett a beállításjegyzék-hozzáférés korlátozására szolgáló hálózati hozzáférési szabályok állíthatók be. További információ: Azure [Container Registry-hozzáférés korlátozása Azure-beli virtuális hálózati vagy tűzfalszabályok használatával](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
