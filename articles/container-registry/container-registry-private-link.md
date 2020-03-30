---
title: Privát hivatkozás beállítása
description: Privát végpont beállítása tárolóbeállításjegyzékben, és privát kapcsolat engedélyezése a helyi virtuális hálózatban
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498918"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Az Azure Private Link konfigurálása egy Azure-tároló beállításjegyzékéhez 

Beállíthat egy [privát végpontot](../private-link/private-endpoint-overview.md) az Azure container registry számára, így az Azure virtuális hálózaton lévő ügyfelek biztonságosan hozzáférhetnek a rendszerleíró adatbázishoz egy [privát kapcsolaton](../private-link/private-link-overview.md)keresztül. A magánvégpont a rendszerleíró adatbázis virtuális hálózati címteréből származó IP-címet használ. A virtuális hálózaton lévő ügyfelek és a rendszerleíró adatbázis közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinchálózatán lévő magánkapcsolaton, így kiküszöböli a nyilvános internetről érkező kitettséget.

A saját végpont [DNS-beállításait konfigurálhatja,](../private-link/private-endpoint-overview.md#dns-configuration) így a beállítások a rendszerleíró adatbázis lefoglalt privát IP-címére oldódnak fel. A DNS-konfigurációval a hálózat ügyfelei és szolgáltatásai továbbra is hozzáférhetnek a rendszerleíró adatbázishoz a rendszerleíró adatbázis teljesen minősített tartománynevével, például *myregistry.azurecr.io.*

Ez a funkció a **prémium szintű** tároló beállításjegyzék-szolgáltatásszintjén érhető el. A beállításjegyzék-szolgáltatási szintekről és -korlátozásokról az [Azure Container Registry ska](container-registry-skus.md)című témakörben talál további információt.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások](#preview-limitations) érvényesek. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előnézeti korlátozások

* Jelenleg nem lehet beállítani egy privát kapcsolatot egy privát végpont egy [georeplikált rendszerleíró adatbázisban.](container-registry-geo-replication.md) 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI ebben a cikkben az Azure CLI 2.2.0-s vagy újabb verziója használata ajánlott. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa az [Azure Cloud Shellben.](../cloud-shell/quickstart.md)
* Ha még nem rendelkezik egy tároló beállításjegyzékkel, hozzon létre egy `hello-world` (prémium szintű szükséges) és leküldéses egy mintarendszerkép, például a Docker Hub. Például az [Azure Portalon][quickstart-portal] vagy az [Azure CLI-ben][quickstart-cli] hozzon létre egy beállításjegyzéket.
* Ha egy másik Azure-előfizetésben lévő privát hivatkozás használatával szeretné konfigurálni a beállításjegyzék-hozzáférést, regisztrálnia kell az azure-tároló beállításjegyzékerőforrás-szolgáltatóját az adott előfizetésben. Példa:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Az Azure CLI példák ebben a cikkben a következő környezeti változók at használja. A környezetnek megfelelő értékek helyettesítése. Minden példa a Bash rendszerhéjhoz van formázva:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Tesztelési célokra használja a Docker-kompatibilis Ubuntu virtuális gép egy Azure-tároló beállításjegyzék eléréséhez. Az Azure Active Directory-hitelesítés a beállításjegyzékben, telepítse az [Azure CLI][azure-cli] a virtuális gépre. Ha már rendelkezik egy Azure virtuális géppel, hagyja ki ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális gép és a tároló beállításjegyzék. Ez a beállítás leegyszerűsíti a karbantartást a végén, de nem szükséges. Ha úgy dönt, hogy külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa az [az csoport létrehozása:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Most üzembe helyezhet egy alapértelmezett Ubuntu Azure virtuális gépet [az az vm create][az-vm-create]segítségével. A következő példa létrehoz egy *myDockerVM*nevű virtuális gép.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Amikor a parancs befejeződik, `publicIpAddress` vegye figyelembe az Azure CLI által megjelenített. Ezzel a címmel SSH-kapcsolatokat létesítanek a virtuális géppel.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futtatása után, hogy egy SSH-kapcsolat a virtuális gép. Cserélje le *a nyilvános IpAddress-t* a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsokat a Docker ubuntus virtuális gépre való telepítéséhez:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális számítógépen:

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

Kövesse az [Azure CLI telepítése apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) az Azure CLI telepítéséhez az Ubuntu virtuális gépen. Példa:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Lépjen ki az SSH-kapcsolatból.

## <a name="set-up-private-link---cli"></a>Privát kapcsolat beállítása - CLI

### <a name="get-network-and-subnet-names"></a>Hálózati és alhálózati nevek beszerezése

Ha még nem rendelkezik velük, a privát kapcsolat beállításához szüksége lesz egy virtuális hálózat és alhálózat nevére. Ebben a példában ugyanazt az alhálózatot használja a virtuális géphez és a rendszerleíró adatbázis privát végpontjához. Azonban sok esetben a végpontot egy külön alhálózatban kell beállítani. 

Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz egy virtuális hálózatot ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gépet *myDockerVM-nek*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, *egy myDockerVMSubnet*nevű alhálózattal. Állítsa be ezeket az értékeket a környezeti változókban az [az hálózati virtuális hálózat lista][az-network-vnet-list] parancsának futtatásával:

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

[Tiltsa le a hálózati házirendeket,](../private-link/disable-private-endpoint-network-policy.md) például a hálózati biztonsági csoportokat a magánhálózati végpont alhálózatában. Az alhálózati konfiguráció frissítése [az az hálózati virtuális hálózat alhálózati frissítésével:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása

Hozzon létre egy privát DNS-zónát a priviátus Azure-tároló beállításjegyzék-tartományához. A későbbi lépésekben dns-rekordokat hozhat létre a rendszerleíró tartományhoz ebben a DNS-zónában.

Ha privát zónát szeretne használni az Azure-tároló beállításjegyzékének alapértelmezett DNS-feloldásának felülbírálásához, a zónát **privatelink.azurecr.io**kell nevezni. Futtassa a következő [az hálózati private-dns zone create][az-network-private-dns-zone-create] parancsot a privát zóna létrehozásához:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Társítási hivatkozás létrehozása

[Futtassa az az-hálózat private-dns kapcsolat vnet létrehozása][az-network-private-dns-link-vnet-create] társítani a privát zóna a virtuális hálózathoz. Ez a példa létrehoz egy *myDNSLink*nevű linket.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Saját rendszerleíró adatbázis-végpont létrehozása

Ebben a szakaszban hozza létre a rendszerleíró adatbázis privát végpontját a virtuális hálózatban. Először a rendszerleíró adatbázis erőforrás-azonosítóját kapja meg:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Futtassa az [az hálózati magánvégpont létrehozási][az-network-private-endpoint-create] parancsát a rendszerleíró adatbázis saját végpontjának létrehozásához.

A következő példa létrehozza a *myPrivateEndpoint* és a szolgáltatáskapcsolat *myConnection végpontját.* A végpont tárolóbeállítási erőforrásának megadásához adja át a következőt: `--group-ids registry`

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

### <a name="get-private-ip-addresses"></a>Privát IP-címek begyűjtése

[Futtassa az az hálózati magánvégpont-megjelenítést][az-network-private-endpoint-show] a hálózati csatoló azonosítójának végpontjának lekérdezéséhez:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

A hálózati adapterhez két magánhálózati IP-cím tartozik a tárolóbeállításjegyzékhez: az egyik a rendszerleíró adatbázishoz, a másik pedig a rendszerleíró adatbázis adatvégpontja. Futtassa a következő [az-erőforrás show][az-resource-show] parancsokat a tárolóbeállítás-jegyzék és a rendszerleíró adatbázis adatvégpontjának privát IP-címeinek lekérni:

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

A következő parancsok DNS-rekordokat hoznak létre a rendszerleíró adatbázis végpontjának és adatvégpontjának privát zónájában. Ha például a *westeurope* régióban van egy *myregistry* nevű `myregistry.azurecr.io` rendszerleíró `myregistry.westeurope.data.azurecr.io`adatbázis, a végpontnevek a és a . 

Először futtassa [az az hálózat private-dns rekord-set a create][az-network-private-dns-record-set-a-create] létrehozni üres A rekordhalmazok a rendszerleíró adatbázis végpont és az adatok végpont:

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

Futtassa az [az hálózat private-dns rekord-set egy add-record][az-network-private-dns-record-set-a-add-record] parancsot, hogy megteremtse az A rekordokat a rendszerleíró adatbázis végpontja és az adatok végpontja:

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

A privát kapcsolat most konfigurálva van, és készen áll a használatra.

## <a name="set-up-private-link---portal"></a>Privát kapcsolat beállítása - portál

A következő lépések feltételezik, hogy már rendelkezik egy virtuális hálózat és alhálózat beállítása a virtuális gép tesztelésre. [Új virtuális hálózatot és alhálózatot](../virtual-network/quick-create-portal.md)is létrehozhat.

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

1. A portálon keresse meg a tároló beállításjegyzékét.
1. A **Beállítások csoportban**válassza **a Privát végpontkapcsolatok (előnézet) lehetőséget.**
1. Válassza **a + Privát végpont lehetőséget.**
1. Az **Alapok lapon** adja meg vagy jelölje ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Írja be egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | **Példány részletei** |  |
    | Név | Adjon meg egy egyedi nevet. |
    |Régió|Válasszon régiót.|
    |||
5. Válassza a **Tovább: Erőforrás**lehetőséget.
6. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza **a Csatlakozás egy Azure-erőforráshoz a címtárban**lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft.ContainerRegistry/registryregistry lehetőséget.** |
    | Erőforrás |A rendszerleíró adatbázis nevének kiválasztása|
    |Cél alerőforrás |**Rendszerleíró adatbázis** kiválasztása|
    |||
7. Válassza a **Tovább lehetőséget: Konfiguráció**.
8. Adja meg vagy jelölje ki az adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki azt a virtuális hálózatot, amelyben a virtuális gép telepítve van, például *a myDockerVMVNET.* |
    | Alhálózat | Válasszon ki egy alhálózatot, például *a myDockerVMSubnet,* ahol a virtuális gép telepítve van. |
    |**Privát DNS-integráció**||
    |Integrálás privát DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *ki az (Új) privatelink.azurecr.io* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
2. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

A privát végpont létrehozása után a privát zónában lévő DNS-beállítások megjelennek a végpont **áttekintése** lapon.

![Végpont DNS-beállításai](./media/container-registry-private-link/private-endpoint-overview.png)

A privát kapcsolat most már konfigurálva van, és használatra kész.

## <a name="validate-private-link-connection"></a>Privát kapcsolat ellenőrzése

Ellenőrizze, hogy a privát végpont alhálózatán belüli erőforrások privát IP-címen keresztül kapcsolódnak-e a rendszerleíró adatbázishoz, és a megfelelő privát DNS-zóna integrációval rendelkeznek.You should validate that the resources within the subnet of the private endpoint connect to your registry over a private IP address, and have the correct private DNS zone integration.

A privát kapcsolat ellenőrzéséhez az SSH a virtuális hálózatban beállított virtuális géphez.

Futtassa a `nslookup` parancsot a rendszerleíró adatbázis IP-címének feloldásához a privát hivatkozáson keresztül:

```bash
nslookup $registryName.azurecr.io
```

Példa kimenet mutatja a rendszerleíró adatbázis IP-címét a címtérben az alhálózat:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Hasonlítsa össze ezt az `nslookup` eredményt az ugyanazon beállításjegyzék kimenetében lévő nyilvános IP-címmel egy nyilvános végponton:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Beállításjegyzék-műveletek magánkapcsolaton keresztül

Ellenőrizze azt is, hogy az alhálózat ban lévő virtuális gépről képes-e beállításjegyzék-műveleteket végrehajtani. SSH-kapcsolatot létesítsen a virtuális géppel, és futtassa [az acr bejelentkezést][az-acr-login] a rendszerleíró adatbázisba való bejelentkezéshez. A virtuális gép konfigurációjától függően előfordulhat, hogy `sudo`előtaggal kell előtagoznia a következő parancsokat.

```bash
az acr login --name $registryName
```

Olyan beállításjegyzék-műveletekvégrehajtása, amelyekkel `docker pull` mintalemezképet szeretne levenni a beállításjegyzékből. Cserélje `hello-world:v1` le a rendszerleíró adatbázisnak megfelelő, a rendszerleíró adatbázis bejelentkezési kiszolgálójának (minden kisbetűs) előtaggal ellátott lemezképre és címkére:

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a lemezképet a virtuális gépre.

## <a name="manage-private-endpoint-connections"></a>Privát végpontkapcsolatok kezelése

A beállításjegyzék privát végpontkapcsolatainak kezelése az Azure Portalon keresztül, vagy az [az acr private-endpoint-connection][az-acr-private-endpoint-connection] parancscsoport parancsainak használatával. A műveletek közé tartozik a rendszerleíró adatbázis privát végpontkapcsolatainak jóváhagyása, törlése, listázása, elutasítása vagy megjelenítése.

Például egy beállításjegyzék privát végpontkapcsolatainak listázásához futtassa az [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] parancsot. Példa:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Ha a cikkben ismertetett lépésekkel állít be magánhálózati végponti kapcsolatot, a beállításjegyzék automatikusan elfogadja az RBAC-engedéllyel rendelkező ügyfelek és szolgáltatások kapcsolatait. Beállíthatja, hogy a végpont a kapcsolatok manuális jóváhagyását igényelje. A magánhálózati végpontkapcsolatok jóváhagyásáról és elutasításáról a [Privát végpontkapcsolat kezelése](../private-link/manage-private-endpoint.md)című témakörben talál további információt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoportban hozta létre, és már nincs rájuk szüksége, szükség esetén törölheti az erőforrásokat egyetlen [az csoport törlési](/cli/azure/group) parancsával:

```azurecli
az group delete --name $resourceGroup
```

Az erőforrások karbantartásához a portálon keresse meg az erőforráscsoportot. Az erőforráscsoport betöltése után kattintson az **Erőforráscsoport törlése gombra** az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Privát hivatkozásról, tekintse meg az [Azure Private Link](../private-link/private-link-overview.md) dokumentációját.
* A magánhálózati kapcsolat alternatívája a hálózati hozzáférési szabályok beállítása a rendszerleíró adatbázis hoz való hozzáférés korlátozására. További információ: [Hozzáférés korlátozása egy Azure-tároló beállításjegyzékéhez egy Azure virtuális hálózati vagy tűzfalszabályok használatával.](container-registry-vnet.md)

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
