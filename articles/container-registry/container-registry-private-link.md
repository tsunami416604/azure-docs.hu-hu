---
title: Privát hivatkozás beállítása
description: Hozzon létre egy privát végpontot egy tároló-beállításjegyzékben, és engedélyezze a hozzáférést egy helyi virtuális hálózatban lévő privát kapcsolaton keresztül. A Private link Access a prémium szintű szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 05/19/2020
ms.openlocfilehash: f25f7b94a3008b829340cdaaed247d7ab1203c19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509338"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Azure Private-hivatkozás konfigurálása Azure Container registryhez 

Korlátozza a hozzáférést a beállításjegyzékhez úgy, hogy a virtuális hálózat magánhálózati IP-címeit a beállításjegyzékbeli végpontokhoz rendeli hozzá az [Azure Private link](../private-link/private-link-overview.md)használatával. A virtuális hálózat és a beállításjegyzék ügyfelei közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán lévő privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget.

Megadhatja a magánhálózati végpont [DNS-beállításait](../private-link/private-endpoint-overview.md#dns-configuration) , hogy a beállítások a beállításjegyzék lefoglalt magánhálózati IP-címére legyenek feloldva. A DNS-konfigurációval a hálózatban lévő ügyfelek és szolgáltatások továbbra is hozzáférhetnek a beállításjegyzékhez a beállításjegyzék teljes tartománynevénél, például *myregistry.azurecr.IO*.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry rétegek](container-registry-skus.md).

## <a name="things-to-know"></a>Tudnivalók

* A képvizsgálat jelenleg Azure Security Center használatával nem érhető el egy privát végponttal konfigurált beállításjegyzékben.

## <a name="prerequisites"></a>Előfeltételek

* A cikkben szereplő Azure CLI-lépések használatához ajánlott az Azure CLI-es vagy újabb verziójának használata. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Ha még nem rendelkezik tároló-beállításjegyzékkel, hozzon létre egyet (prémium szintű csomag szükséges), és [importáljon](container-registry-import-images.md) egy minta képet, például `hello-world` a Docker hub-ból. A beállításjegyzék létrehozásához például használja az [Azure Portal][quickstart-portal] vagy az [Azure CLI][quickstart-cli] -t.
* Ha egy másik Azure-előfizetésben lévő privát hivatkozás használatával szeretné konfigurálni a beállításjegyzék-hozzáférést, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésben lévő Azure Container Registryhoz. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

A cikkben szereplő Azure CLI-példák az alábbi környezeti változókat használják. A környezetnek megfelelő helyettesítő értékeket. Az összes példa a bash-rendszerhéjra van formázva:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Privát hivatkozás beállítása – parancssori felület

### <a name="get-network-and-subnet-names"></a>Hálózati és alhálózatok nevének lekérése

Ha még nem rendelkezik velük, szüksége lesz egy virtuális hálózat és alhálózat nevére a privát hivatkozás beállításához. Ebben a példában ugyanazt az alhálózatot használja a virtuális géphez és a beállításjegyzék privát végpontja számára. Számos esetben azonban a végpontot külön alhálózatban kell beállítania. 

Amikor létrehoz egy virtuális GÉPET, az Azure alapértelmezés szerint ugyanahhoz az erőforráscsoporthoz hoz létre egy virtuális hálózatot. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gép *myDockerVM*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, és egy *myDockerVMSubnet*nevű alhálózattal rendelkezik. Állítsa be ezeket az értékeket környezeti változókba az az [Network vnet List][az-network-vnet-list] parancs futtatásával:

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Hálózati házirendek letiltása az alhálózatban

[Tiltsa le a hálózati házirendeket](../private-link/disable-private-endpoint-network-policy.md) , például a hálózati biztonsági csoportokat a magánhálózati végponthoz tartozó alhálózatban. Az alhálózat konfigurációjának frissítése az [az Network vnet subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása

Hozzon létre egy privát DNS-zónát a privát Azure Container Registry-tartományhoz. A későbbi lépések során DNS-rekordokat hoz létre a beállításjegyzék tartományához ebben a DNS-zónában.

Ha privát zónát szeretne használni az Azure Container Registry alapértelmezett DNS-feloldásának felülbírálásához, akkor a zónának **privatelink.azurecr.IO**nevűnek kell lennie. A privát zóna létrehozásához futtassa a következő az [Network Private-DNS Zone Create][az-network-private-dns-zone-create] parancsot:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Társítási hivatkozás létrehozása

Az az [Network Private-DNS link vnet Create][az-network-private-dns-link-vnet-create] paranccsal társíthatja a saját zónáját a virtuális hálózattal. Ez a példa egy *myDNSLink*nevű hivatkozást hoz létre.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Privát beállításjegyzékbeli végpont létrehozása

Ebben a szakaszban a beállításjegyzék saját végpontját hozza létre a virtuális hálózaton. Először szerezze be a beállításjegyzék erőforrás-AZONOSÍTÓját:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Futtassa az az [Network Private-Endpoint Create][az-network-private-endpoint-create] parancsot a beállításjegyzék privát végpontjának létrehozásához.

A következő példa létrehozza a végpont *myPrivateEndpoint* és a szolgáltatáshoz kapcsolódó *myConnection*. A végponthoz tartozó tároló beállításjegyzék-erőforrásának megadásához adja meg a következőt `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Magánhálózati IP-címek lekérése

Futtassa az [az Network Private-Endpoint show][az-network-private-endpoint-show] parancsot a hálózati adapter azonosító végpontjának lekérdezéséhez:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

A példában szereplő hálózati adapterhez két magánhálózati IP-cím van társítva a tároló-beállításjegyzékhez: egy a beállításjegyzékhez, egy pedig a beállításjegyzék adatvégpontja. A következő az [Resource show][az-resource-show] parancsok lekérik a tároló-beállításjegyzék és a beállításjegyzék adatvégpontjának MAGÁNHÁLÓZATI IP-címeit:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Ha a beállításjegyzék [földrajzilag replikálódik](container-registry-geo-replication.md), az egyes beállításjegyzék-replikák további adatvégpontjának lekérdezése.

### <a name="create-dns-records-in-the-private-zone"></a>DNS-rekordok létrehozása a privát zónában

A következő parancsok DNS-rekordokat hoznak létre a saját zónában a beállításjegyzék-végponthoz és az adatvégponthoz. Ha például egy *myregistry* nevű beállításjegyzék található a *westeurope* régióban, a végpontok nevei a következők: `myregistry.azurecr.io` és `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Ha a beállításjegyzék [földrajzilag replikálódik](container-registry-geo-replication.md), hozzon létre további DNS-rekordokat az egyes replikák adatvégponti IP-címéhez.

Először futtassa az az [Network Private-DNS Record-set a Create (létrehozás][az-network-private-dns-record-set-a-create] ) lehetőséget, hogy üres rekordhalmazt hozzon létre a beállításjegyzék-végpont és az adatvégpont számára:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Futtassa az az [Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] parancsot a beállításjegyzék-végpont és az adatvégpont rekordjainak létrehozásához:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

A magánhálózati kapcsolat már konfigurálva van, és használatra kész.

## <a name="set-up-private-link---portal"></a>Privát hivatkozás beállítása – portál

Hozzon létre egy privát hivatkozást a beállításjegyzék létrehozásakor, vagy adjon hozzá egy meglévő beállításjegyzékhez egy privát hivatkozást. A következő lépések feltételezik, hogy már rendelkezik virtuális hálózattal és alhálózattal a teszteléshez. [Létrehozhat egy új virtuális hálózatot és alhálózatot](../virtual-network/quick-create-portal.md)is.

### <a name="create-a-private-endpoint---new-registry"></a>Privát végpont létrehozása – új beállításjegyzék

1. Amikor beállításjegyzéket hoz létre a portálon, az **alapok** lap **SKU**területén válassza a **prémium**lehetőséget.
1. Válassza a **hálózatkezelés** lapot.
1. A **hálózati kapcsolat**területen válassza a **privát végpont**  >  **+ Hozzáadás**lehetőséget.
1. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Adja meg egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | Name | Adjon meg egy egyedi nevet. |
    | Alerőforrás |**Beállításjegyzék** kiválasztása|
    | **Hálózat** | |
    | Virtuális hálózat| Válassza ki azt a virtuális hálózatot, amelyben a virtuális gép telepítve van, például *myDockerVMVNET*. |
    | Alhálózat | Válasszon ki egy alhálózatot, például a *myDockerVMSubnet* , ahol a virtuális gép telepítve van. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |saját DNS zóna |Select *(új) privatelink.azurecr.IO* |
    |||
1. Konfigurálja a fennmaradó beállításjegyzék-beállításokat, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.

  ![Beállításjegyzék létrehozása privát végponttal](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Privát végpont létrehozása – meglévő beállításjegyzék

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások**területen válassza a **hálózatkezelés**lehetőséget.
1. A **privát végpontok** lapon válassza a **+ privát végpont**elemet.
1. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Adja meg egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | **Példány részletei** |  |
    | Name | Adjon meg egy nevet. |
    |Régió|Válasszon régiót.|
    |||
5. Válassza a **Tovább: erőforrás**elemet.
6. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban**lehetőséget.|
    | Előfizetés| Válassza ki az előfizetését. |
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

A magánhálózati végpont létrehozása után a privát zónában a DNS-beállítások a portál **privát végpontok** lapján jelennek meg:

1. A portálon navigáljon a tároló beállításjegyzékéhez, és válassza a **beállítások > hálózatkezelés**lehetőséget.
1. A **privát végpontok** lapon válassza ki a létrehozott privát végpontot.
1. Az **Áttekintés** oldalon tekintse át a hivatkozás beállításait és az egyéni DNS-beállításokat.

  ![Végpont DNS-beállításai](./media/container-registry-private-link/private-endpoint-overview.png)

A magánhálózati kapcsolat konfigurálva van, és használatra kész.

## <a name="disable-public-access"></a>Nyilvános hozzáférés letiltása

Számos esetben tiltsa le a beállításjegyzékhez való hozzáférést a nyilvános hálózatokból. Ez a konfiguráció megakadályozza, hogy a virtuális hálózaton kívüli ügyfelek elérjék a beállításjegyzékbeli végpontokat. 

### <a name="disable-public-access---cli"></a>Nyilvános hozzáférés letiltása – parancssori felület

Az Azure CLI használatával történő nyilvános hozzáférés letiltásához futtassa [az az ACR Update][az-acr-update] parancsot, és állítsa a következőre: `--public-network-enabled` `false` . 

> [!NOTE]
> Az `public-network-enabled` argumentumhoz az Azure CLI 2.6.0 vagy újabb verzió szükséges. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Nyilvános hozzáférés letiltása – portál

1. A portálon navigáljon a tároló beállításjegyzékéhez, és válassza a **beállítások > hálózatkezelés**lehetőséget.
1. A **nyilvános hozzáférés** lapon, a **nyilvános hálózati hozzáférés engedélyezése**területen válassza a **Letiltva**lehetőséget. Kattintson a **Mentés** gombra.

## <a name="validate-private-link-connection"></a>Privát kapcsolati kapcsolat ellenőrzése

Győződjön meg arról, hogy a privát végpont alhálózatán belüli erőforrások magánhálózati IP-címmel csatlakoznak a beállításjegyzékhez, és a saját DNS-zónák megfelelő integrációja szükséges.

A magánhálózati kapcsolat kapcsolatának ellenőrzéséhez SSH-kapcsolatot kell létesítenie a virtuális hálózatban beállított virtuális géppel.

Futtassa a `nslookup` parancsot a beállításjegyzék IP-címének feloldásához a privát hivatkozáson keresztül:

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

Például a kimenet a beállításjegyzék IP-címét jeleníti meg az alhálózat címterület:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Hasonlítsa össze ezt az eredményt a kimenetben lévő nyilvános IP-címmel `nslookup` egy nyilvános végponton keresztül:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Beállításjegyzékbeli műveletek privát kapcsolaton keresztül

Győződjön meg arról is, hogy az alhálózaton található virtuális gépről beállításjegyzék-műveleteket is végrehajthat. Létesítsen SSH-kapcsolatokat a virtuális géppel, és futtassa az [ACR login][az-acr-login] parancsot a beállításjegyzékbe való bejelentkezéshez. A virtuális gép konfigurációjától függően előfordulhat, hogy a következő parancsokat kell előadnia a alkalmazásban `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Olyan beállításjegyzék-műveleteket hajthat végre, mint például `docker pull` egy minta rendszerkép lekérése a beállításjegyzékből. Cserélje le a `hello-world:v1` t a beállításjegyzék megfelelő képére és címkéjére, és írja be a beállításjegyzék bejelentkezési kiszolgálójának nevét (minden kisbetűs):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a rendszerképet a virtuális gépre.

## <a name="manage-private-endpoint-connections"></a>Magánhálózati végpontok kapcsolatainak kezelése

Felügyelheti a beállításjegyzék saját végpont-kapcsolatait a Azure Portal használatával, vagy az az [ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] paranccsal elérhető parancsok használatával. A műveletek közé tartozik a beállításjegyzék privát végpont-kapcsolatainak jóváhagyása, törlése, listázása, elutasítása vagy megjelenítése.

Ha például egy beállításjegyzék privát végpont-kapcsolatait szeretné kilistázni, futtassa az az [ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] parancsot. Például:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Ha a jelen cikkben ismertetett lépésekkel állít be egy privát végponti kapcsolatot, a beállításjegyzék automatikusan fogadja a RBAC-engedélyekkel rendelkező ügyfelek és szolgáltatások kapcsolatait a beállításjegyzékben. Beállíthatja a végpontot a kapcsolatok manuális jóváhagyásának megköveteléséhez. További információ a privát végpontok kapcsolatainak jóváhagyásáról és elutasításáról: [privát végponti kapcsolat kezelése](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Zónaadatok hozzáadása a replikák számára

Ahogy az ebben a cikkben is látható, amikor egy magánhálózati végponti kapcsolattal bővíti a beállításjegyzéket, a zónában lévő DNS-rekordok a beállításjegyzékben `privatelink.azurecr.io` és annak adatvégpontjában jönnek létre azokban a régiókban, ahol a beállításjegyzék [replikálódik](container-registry-geo-replication.md). 

Ha később új replikát ad hozzá, manuálisan kell hozzáadnia egy új zónát az adott régióban lévő adatvégponthoz. Ha például létrehoz egy *myregistry* -replikát a *northeurope* helyen, vegyen fel egy zóna rekordot a következőhöz: `myregistry.northeurope.data.azurecr.io` . További lépések: [DNS-rekordok létrehozása a privát zónában](#create-dns-records-in-the-private-zone) ebben a cikkben.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoporthoz hozta létre, és már nincs rájuk szükség, akkor az erőforrásokat egyetlen [az Group delete](/cli/azure/group) paranccsal törölheti:

```azurecli
az group delete --name $RESOURCE_GROUP
```

A portálon található erőforrások törléséhez navigáljon az erőforráscsoporthoz. Miután betöltötte az erőforráscsoportot, kattintson az **erőforráscsoport törlése** elemre az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a privát hivatkozásokról, tekintse meg az [Azure Private link](../private-link/private-link-overview.md) dokumentációját.
* Ha be kell állítania a beállításjegyzék-hozzáférési szabályokat az ügyfél tűzfala mögött, tekintse meg a [szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)című témakört.

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
