---
title: Bejövő adatkezelő-bővítmény engedélyezése meglévő AK-fürthöz meglévő Azure-Application Gateway
description: Ezzel az Oktatóanyaggal engedélyezheti a meglévő AK-fürt bemenő vezérlő bővítményét meglévő Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 6cbfac4794a685e5858e689c20d6603807edcedf
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987990"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Oktatóanyag: az Azure CLI (előzetes verzió) szolgáltatással meglévő Application Gatewayokkal rendelkező meglévő AK-fürthöz való belépés Application Gateway bejövő vezérlő bővítményének engedélyezése

Application Gateway az Azure CLI-vel engedélyezheti az [Azure Kubernetes Services-(ak-)](https://azure.microsoft.com/services/kubernetes-service/) fürthöz jelenleg előzetes verzióban elérhető [AGIC](ingress-controller-overview.md) -bővítményt. Ebből az oktatóanyagból megtudhatja, hogyan használhatja a AGIC bővítményt a Kubernetes-alkalmazás elérhetővé tétele egy meglévő AK-fürtön egy meglévő, különálló virtuális hálózatokban telepített Application Gateway használatával. Először hozzon létre egy AK-fürtöt egy virtuális hálózatban, és egy különálló virtuális hálózat Application Gateway a meglévő erőforrások szimulálása érdekében. Ezután engedélyezheti a AGIC-bővítményt, a két virtuális hálózat együttes használatát, és üzembe helyezheti a Application Gateway a AGIC bővítmény használatával. Ha a AGIC-bővítményt egy meglévő Application Gateway és egy meglévő AK-fürthöz engedélyezi egy virtuális hálózatban, akkor kihagyhatja az alábbi társítási lépést. A bővítmény sokkal gyorsabban üzembe helyezi a AGIC-t az AK-fürthöz, mint a [korábban a helmon keresztül](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , és teljes körűen felügyelt élményt is biztosít.  

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Erőforráscsoport létrehozása 
> * Új AK-fürt létrehozása 
> * Új Application Gateway létrehozása 
> * Engedélyezze a AGIC bővítményt a meglévő AK-fürtön a meglévő Application Gateway használatával 
> * Egyenrangú a Application Gateway virtuális hálózat az AK-fürt virtuális hálózatával
> * Minta alkalmazás üzembe helyezése az AGIC használatával a bejövő forgalomhoz az AK-fürtön
> * Győződjön meg arról, hogy az alkalmazás elérhető Application Gateway

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Regisztrálja az *AK-IngressApplicationGatewayAddon* funkció jelzőjét az az [Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) paranccsal az alábbi példában látható módon: ezt csak egyszer kell megtennie egy előfizetéshez, amíg a bővítmény még előzetes verzióban érhető el:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

A regisztrált állapot megjelenítéséhez néhány percet is igénybe vehet. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature#az-feature-register) parancs használatával tekintheti meg:
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a Microsoft. Tárolószolgáltatás erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) paranccsal:
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Ügyeljen arra, hogy a jelen oktatóanyaghoz tartozó AK-előnézet bővítményt telepítse/frissítse. a következő Azure CLI-parancsok használata
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *canadacentral* helyen (régió). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Új AK-fürt üzembe helyezése

Most üzembe kell helyeznie egy új AK-fürtöt, hogy szimulálni kelljen egy meglévő AK-fürtöt, amelyhez engedélyezni szeretné a AGIC-bővítményt.  

Az alábbi példában egy *myCluster* nevű új AK-fürtöt telepítünk az [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) és a [felügyelt identitások](https://docs.microsoft.com/azure/aks/use-managed-identity) használatával a létrehozott erőforráscsoport, a *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Ha további paramétereket szeretne megadni a `az aks create` parancshoz, keresse fel a hivatkozásokat [itt](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Új Application Gateway üzembe helyezése 

Most egy új Application Gateway üzembe helyezésével szimulálhatja azt a meglévő Application Gateway, amelyet a forgalom terheléselosztásához kíván használni az AK-fürtre ( *myCluster*). A Application Gateway neve *myApplicationGateway*lesz, de először létre kell hoznia egy *myPublicIp*nevű nyilvános IP-erőforrást, és egy *myVnet* nevű új virtuális hálózatot, amelynek a címe 11.0.0.0/8, valamint egy olyan alhálózat, amelynek 11.1.0.0/16 nevű alhálózata *mySubnet, és*a mySubnet használatával helyezi *myPublicIp*üzembe *Application Gateway a myPublicIp* . 

Ha AK-alapú fürtöt használ, és Application Gateway külön virtuális hálózatban, akkor a két virtuális hálózat címterület nem fedik át egymást. Az AK-fürtök alapértelmezett 10.0.0.0/8, ezért a Application Gateway virtuális hálózati címek előtagját a 11.0.0.0/8 értékre állítja be. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> A Application Gateway inporting Controller (AGIC) bővítmény **csak** Application Gateway v2 SKU (standard és WAF), és **nem** a Application Gateway v1 SKU-t támogatja. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>AGIC-bővítmény engedélyezése meglévő AK-fürtön meglévő Application Gateway 

Most engedélyezze a AGIC-bővítményt a létrehozott AK-fürtben, *myCluster*, és adja meg a AGIC-bővítményt a létrehozott meglévő Application Gateway használatára, *myApplicationGateway*. Győződjön meg arról, hogy hozzáadta/frissítette az AK-előzetes bővítményt az oktatóanyag elején. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Egyenrangú a két virtuális hálózat együtt

Mivel üzembe helyezte az AK-fürtöt a saját virtuális hálózatában, illetve a Application Gateway egy másik virtuális hálózatban, a két virtuális hálózatot össze kell kapcsolnia ahhoz, hogy a forgalom a Application Gateway a fürtben lévő hüvelybe kerüljön. A két virtuális hálózat társításához az Azure CLI-parancsot két külön alkalommal kell futtatnia, hogy a kapcsolat kétirányú legyen. Az első parancs egy egyenrangú kapcsolatot hoz létre a Application Gateway virtuális hálózatról az AK-beli virtuális hálózatra; a második parancs egy egyenrangú kapcsolatot hoz létre a másik irányban. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Minta alkalmazás üzembe helyezése a AGIC használatával 

Most üzembe kell helyeznie egy minta alkalmazást a létrehozott AK-fürtön, amely a AGIC bővítményt fogja használni a bejövő forgalomhoz, és a Application Gateway csatlakozik az AK-fürthöz. Először a parancs futtatásával hitelesítő adatokat fog kapni a telepített AK-fürthöz `az aks get-credentials` . 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Miután megadta a hitelesítő adatokat a létrehozott fürthöz, futtassa a következő parancsot egy olyan minta alkalmazás beállításához, amely a AGIC-t használja a fürtre való behatoláshoz. A AGIC frissíti a korábban beállított Application Gateway a megfelelő útválasztási szabályokkal a telepített új minta alkalmazásra.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Győződjön meg arról, hogy az alkalmazás elérhető

Most, hogy a Application Gateway úgy van beállítva, hogy kiszolgálja az AK-fürt forgalmát, ellenőrizze, hogy az alkalmazás elérhető-e. Először megkapja a bejövő forgalom IP-címét. 

```azurecli-interactive
kubectl get ingress
```

Győződjön meg arról, hogy a létrehozott minta alkalmazás működik-e, vagy látogasson el a Application Gateway IP-címére, amelyet a fenti parancs futtatásával vagy a szolgáltatással való ellenőrzéssel kapott `curl` . A frissítés megkezdése Application Gateway egy percet is igénybe vehet, így ha a Application Gateway továbbra is "frissítés" állapotban van a portálon, akkor hagyja befejezni az IP-cím elérését. 

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>További lépések
* [További információ a AGIC-bővítmény letiltásáról](./ingress-controller-disable-addon.md)
* [További információ a AGIC által támogatott jegyzetekről](./ingress-controller-annotations.md)
* [AGIC kapcsolatos hibák elhárítása](./ingress-controller-troubleshoot.md)

