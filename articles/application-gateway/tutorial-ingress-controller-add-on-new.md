---
title: Új Azure-Application Gateway a bejövő adatkezelő beépülő modul engedélyezése az új AK-fürtökhöz
description: Ezzel az Oktatóanyaggal engedélyezheti a bejövő adatkezelő beépülő modult az új AK-fürthöz új Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670935"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Oktatóanyag: az Azure CLI-vel (előzetes verzió) új Application Gateway az új AK-fürthöz való belépés Application Gateway a bejövő vezérlő bővítményének engedélyezése

Application Gateway az Azure CLI-vel engedélyezheti az [Azure Kubernetes Services-(ak-)](https://azure.microsoft.com/services/kubernetes-service/) fürthöz jelenleg előzetes verzióban elérhető [AGIC](ingress-controller-overview.md) -bővítményt. Ebben az oktatóanyagban létre fog hozni egy AK-fürtöt a AGIC-bővítmény engedélyezésével, amely automatikusan létrehoz egy Application Gateway a használatra. Ezután üzembe helyez egy minta alkalmazást, amely a AGIC-bővítmény használatával teszi elérhetővé az alkalmazást a Application Gatewayon keresztül. A bővítmény sokkal gyorsabban üzembe helyezi a AGIC-t az AK-fürthöz, mint a [korábban a helmon keresztül](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , és teljes körűen felügyelt élményt is biztosít.    

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Erőforráscsoport létrehozása 
> * Új AK-fürt létrehozása a AGIC-bővítmény engedélyezésével 
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

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Új AK-fürt üzembe helyezése a AGIC-bővítmény engedélyezésével

Most üzembe kell helyeznie egy új AK-fürtöt a AGIC-bővítmény engedélyezésével. Ha a AGIC-bővítményt engedélyező új AK-fürtöt telepít, és nem biztosít meglévő Application Gateway, akkor a rendszer automatikusan létrehoz és beállít egy új Application Gateway, amely az AK-fürt forgalmának kiszolgálására szolgál.  

> [!NOTE]
> A Application Gateway inporting Controller (AGIC) bővítmény **csak** Application Gateway v2 SKU (standard és WAF), és **nem** a Application Gateway v1 SKU-t támogatja. Amikor új Application Gateway helyez üzembe a AGIC-bővítményen keresztül, csak Application Gateway Standard_v2 SKU-t helyezhet üzembe. Ha engedélyezni szeretné a AGIC-bővítményt egy Application Gateway WAF_v2 SKU-hoz, engedélyezze az WAF-t az Application Gatewayon keresztül a portálon, vagy hozza létre a WAF_v2 Application Gateway először, majd kövesse a [AGIC-bővítmény meglévő AK-fürthöz és meglévő Application Gateway való engedélyezésének](tutorial-ingress-controller-add-on-existing.md)lépéseit. 

A következő példában egy *myCluster* nevű új AK-fürtöt kell üzembe helyeznie az [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) és a [felügyelt identitások](https://docs.microsoft.com/azure/aks/use-managed-identity) használatával, és a AGIC-bővítmény engedélyezve van az Ön által létrehozott erőforráscsoporthoz, a *myResourceGroup*. Mivel egy meglévő Application Gateway megadása nélkül engedélyezte az új AK-alapú fürtöket a AGIC beépülő modulban, egy Standard_v2 SKU Application Gateway automatikus létrehozását fogja megadni a Application Gateway nevével és alhálózati címével. A Application Gateway neve *myApplicationGateway* lesz, és az alhálózati címtartomány 10.2.0.0/16. Győződjön meg arról, hogy hozzáadta/frissítette az AK-előzetes bővítményt az oktatóanyag elején. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Ha további paramétereket szeretne megadni a `az aks create` parancshoz, keresse fel a hivatkozásokat [itt](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> A létrehozott AK-fürt megjelenik a létrehozott erőforráscsoport *myResourceGroup*. Az automatikusan létrehozott Application Gateway azonban abban a csomópont-erőforráscsoportben fog megjelenni, ahol az ügynök készletei vannak. Alapértelmezés szerint a csomópont-erőforráscsoport neve *MC_resource-Group-name_cluster-name_location*lesz, de módosítható. 

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

## <a name="next-steps"></a>Következő lépések
* [További információ a AGIC-bővítmény letiltásáról](./ingress-controller-disable-addon.md)
* [További információ a AGIC által támogatott jegyzetekről](./ingress-controller-annotations.md)
* [AGIC kapcsolatos hibák elhárítása](./ingress-controller-troubleshoot.md)

