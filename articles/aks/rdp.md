---
title: RDP az Azure Kubernetes Szolgáltatás (AKS) fürtwindows Server csomópontjaiba
description: Ismerje meg, hogyan hozhat létre RDP-kapcsolatot az Azure Kubernetes-szolgáltatás (AKS) fürt Windows Server-csomópontjaival hibaelhárítási és karbantartási feladatokhoz.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594481"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Csatlakozás rdp-vel az Azure Kubernetes Service (AKS) fürt Windows Server csomópontjaihoz karbantartás vagy hibaelhárítás érdekében

Az Azure Kubernetes-szolgáltatás (AKS) fürtjének teljes életciklusa során előfordulhat, hogy hozzá kell férnie egy AKS Windows Server-csomóponthoz. Ez a hozzáférés lehet karbantartási, naplógyűjtési vagy egyéb hibaelhárítási műveletekhez. Az AKS Windows Server-csomópontok rdp használatával érhetők el. Másik lehetőségként, ha az SSH használatával szeretné elérni az AKS Windows Server-csomópontokat, és ugyanazhoz a kulcspárhoz van hozzáférése, amelyet a fürt létrehozása során használt, kövesse az SSH lépéseket az [Azure Kubernetes Service (AKS) fürtcsomópontokba.][ssh-steps] Biztonsági okokból az AKS-csomópontok nincsenek kitéve az internetnek.

A Windows Server-csomópont támogatása jelenleg előzetes verzióban érhető el az AKS-ben.

Ez a cikk bemutatja, hogyan hozhat létre RDP-kapcsolatot egy AKS-csomóddal a saját IP-címük használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy egy meglévő AKS-fürttel rendelkezik Windows Server-csomón. Ha AKS-fürtre van szüksége, olvassa el az [AKS-fürt létrehozásáról][aks-windows-cli]szóló cikket az Azure CLI használatával. A hibaelhárításhoz szükséges Windows-rendszergazdai felhasználónévre és jelszóra van szüksége. Szüksége van egy RDP-ügyfélre is, például [a Microsoft Remote Desktop programra.][rdp-mac]

Az Azure CLI 2.0.61-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Virtuális gép üzembe helyezése a fürttel azonos alhálózatra

Az AKS-fürt Windows Server-csomópontjai nem rendelkeznek külsőleg elérhető IP-címekkel. RdP-kapcsolat létesítéséhez nyilvánosan elérhető IP-címmel rendelkező virtuális gépet telepíthet ugyanarra az alhálózatra, mint a Windows Server-csomópontok.

A következő példa létrehoz egy *myVM* nevű virtuális gépet a *myResourceGroup* erőforráscsoportban.

Először a Windows Server-csomópontkészlet által használt alhálózatot kapja meg. Az alhálózat-azonosító lekért, szüksége van az alhálózat nevére. Az alhálózat nevének leéséhez a virtuális hálózat nevére van szükség. A virtuális hálózat nevének lekérdezésével a fürt a hálózatok listáját. A fürt lekérdezéséhez szüksége van a nevére. Ezeket az Azure Cloud Shellben a következők futtatásával szerezheti be:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Most, hogy rendelkezik a SUBNET_ID, futtassa a következő parancsot ugyanabban az Azure Cloud Shell ablakban a virtuális gép létrehozásához:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

A következő példa kimeneti azt mutatja, hogy a virtuális gép sikeresen létrejött, és megjeleníti a virtuális gép nyilvános IP-címét.

```console
13.62.204.18
```

Rögzítse a virtuális gép nyilvános IP-címét. Ezt a címet egy későbbi lépésben fogja használni.

## <a name="allow-access-to-the-virtual-machine"></a>Hozzáférés engedélyezése a virtuális géphez

Az AKS-csomópontkészlet alhálózatai alapértelmezés szerint NSG-kkel (hálózati biztonsági csoportokkal) védettek. A virtuális gép eléréséhez engedélyeznie kell a hozzáférést az NSG-ben.

> [!NOTE]
> Az NSG-ket az AKS szolgáltatás vezérli. Az NSG-n történt módosításokat a vezérlősík bármikor felülírja.
>

Először is, az erőforráscsoport és az nsg neve az nsg hozzáadásához a szabály:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Ezután hozza létre az NSG-szabályt:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>A csomópont címének beszerezése

A Kubernetes-fürt kezeléséhez [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával kell használnia. Ha az Azure Cloud `kubectl` Shell, már telepítve van. A `kubectl` helyi telepítéshez használja az [aks install-cli][az-aks-install-cli] parancsot:
    
```azurecli-interactive
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A Windows Server-csomópontok belső IP-címének felsorolása a [kubectl get][kubectl-get] paranccsal:

```console
kubectl get nodes -o wide
```

A következő példa kimeneta a fürt összes csomópontjának belső IP-címét mutatja, beleértve a Windows Server-csomópontokat is.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Rögzítse a hibaelhárításra kívánt Windows Server-csomópont belső IP-címét. Ezt a címet egy későbbi lépésben fogja használni.

## <a name="connect-to-the-virtual-machine-and-node"></a>Csatlakozás a virtuális géphez és a csomóponthoz

Csatlakozzon a korábban létrehozott virtuális gép nyilvános IP-címéhez egy RDP-ügyfél, például a [Microsoft Távoli asztal][rdp-mac]használatával.

![RdP-ügyfél használatával csatlakozik a virtuális géphez](media/rdp/vm-rdp.png)

Miután csatlakozott a virtuális géphez, csatlakozzon annak a Windows Server-csomópontnak a *belső IP-címéhez,* amelyet a virtuális gépen lévő RDP-ügyfél használatával szeretne elhárítani.

![RdP-ügyfél lelte a Windows Server-csomóponthoz való csatlakozás t](media/rdp/node-rdp.png)

Most már csatlakozik a Windows Server-csomóponthoz.

![A Windows Server-csomópont cmd ablakának képe](media/rdp/node-session.png)

Most már futtathat hibaelhárítási parancsokat a *cmd* ablakban. Mivel a Windows Server-csomópontok a Windows Server Core rendszert használják, nincs teljes grafikus felhasználói felület vagy más grafikus felhasználói felület-eszköz, amikor rdp-n keresztül csatlakozik egy Windows Server-csomóponthoz.

## <a name="remove-rdp-access"></a>RdP-hozzáférés eltávolítása

Ha végzett, lépjen ki a Windows Server-csomópontrdp-kapcsolatból, majd lépjen ki az RDP-munkamenetből a virtuális gépre. Miután kilépett mindkét RDP-munkamenetből, törölje a virtuális gépet az [az vm delete][az-vm-delete] paranccsal:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

És az NSG szabály:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>További lépések

Ha további hibaelhárítási adatokra van szüksége, [megtekintheti a Kubernetes fő csomópontnaplókat][view-master-logs] vagy az [Azure Monitort.][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
