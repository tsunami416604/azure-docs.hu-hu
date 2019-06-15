---
title: Az Azure Kubernetes Service (AKS) fürt Windows Server-csomópontok RDP
description: Ismerje meg, egy RDP-kapcsolat létrehozása az Azure Kubernetes Service (AKS)-fürt Windows Server-csomópontok hibaelhárítási és karbantartási feladatokhoz.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688632"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Kapcsolódás RDP-vel az Azure Kubernetes Service (AKS) karbantartási és hibaelhárítási fürtcsomópontok a Windows Server

Az Azure Kubernetes Service (AKS)-fürt életciklusa során szükség lehet egy AKS Windows Server-csomópont eléréséhez. Ez a hozzáférés karbantartási, a naplógyűjtés vagy egyéb hibaelhárítási művelet lehet. Az AKS a Windows Server-csomópontok RDP-vel is elérheti. Azt is megteheti, ha hozzáfér az ugyanazon kulcspár, amelyet a fürt létrehozásakor használt SSH az AKS a Windows Server-csomópontok eléréséhez használandó, követheti a lépések [SSH-t az Azure Kubernetes Service (AKS) fürtcsomópontok] [ssh-steps]. Biztonsági okokból az AKS-csomópontok nem jelennek meg a az interneten.

Windows Server-csomópont támogatása jelenleg az aks-ben előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan hozzon létre RDP-kapcsolatot egy AKS-csomópont privát IP-címeik használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt egy Windows Server-csomópont. Ha egy AKS-fürtre van szüksége, tekintse meg a cikket a [egy Windows-tárolót az Azure CLI használatával az AKS-fürt létrehozása][aks-windows-cli]. A Windows rendszergazdai felhasználónevet és jelszót kell a Windows Server-csomópont hibaelhárítása szeretné. Is szükség van egy RDP-ügyfelet például [Microsoft távoli asztal][rdp-mac].

Emellett az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Virtuális gép ugyanazon az alhálózaton, a fürt üzembe helyezése

Az AKS-fürt Windows Server csomópontjai nem rendelkezik a kívülről elérhető IP-címeket. Ahhoz, hogy egy RDP-kapcsolatot, telepíthet egy nyilvánosan elérhető IP-címmel rendelkező virtuális gép ugyanazon az alhálózaton, a Windows Server-csomópontként.

Az alábbi példa létrehoz egy virtuális gép nevű *myVM* a a *myResourceGroup* erőforráscsoportot.

Először kérje le a a Windows Server csomópontkészletek által használt alhálózat. Az alhálózati azonosító lekéréséhez szükséges az alhálózat neve. Az alhálózat nevének kell a virtuális hálózat nevét. A virtuális hálózat nevének lekérése a fürtöt a hálózatok listájában lekérdezésével. Lekérdezés a fürthöz, annak neve van szükség. Az Azure Cloud shellben a következő futtatásával ezek mindegyikét kaphat:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Most, hogy a SUBNET_ID, futtassa a következő parancsot a virtuális Gépet hoz létre ugyanabban az Azure Cloud Shell ablakban:

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

Az alábbi példa kimenetében látható, a virtuális gép sikeresen létrejött, és megjeleníti a virtuális gép nyilvános IP-címét.

```console
13.62.204.18
```

Jegyezze fel a virtuális gép nyilvános IP-címét. Ez a cím egy későbbi lépésben fogja használni.

## <a name="get-the-node-address"></a>Csomópont-címének lekéréséhez

Kubernetes-fürtök kezeléséhez használja [kubectl][kubectl], a Kubernetes parancssori ügyfelét. Ha az Azure Cloud Shellben használja `kubectl` már telepítve van. A telepítendő `kubectl` helyileg, használja a [az aks install-cli] [ az-aks-install-cli] parancsot:
    
```azurecli-interactive
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori Felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A belső IP-cím használatával a Windows Server-csomópontok listázása a [kubectl get] [ kubectl-get] parancsot:

```console
kubectl get nodes -o wide
```

Az alábbi példa kimenetében látható a fürtöt, a Windows Server-csomópontok a belső IP-címek az összes csomópont.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezze fel a Windows Server-csomópont hibaelhárítása kívánt belső IP-címe. Ez a cím egy későbbi lépésben fogja használni.

## <a name="connect-to-the-virtual-machine-and-node"></a>Csatlakoztassa a virtuális gép és a csomópont

Csatlakozhat a nyilvános IP-cím a virtuális gép, korábban létrehozott egy RDP-ügyfelet például [Microsoft távoli asztal][rdp-mac].

![Csatlakozás a virtuális gép RDP-ügyfelet használ képe](media/rdp/vm-rdp.png)

Csatlakozás után a virtuális gép csatlakozott, a *belső IP-cím* hibaelhárítás használatával a virtuális gépre egy RDP-ügyfelet szeretné a Windows Server-csomópont.

![Kapcsolódni a Windows Server-csomóponthoz RDP-ügyfelet használó képe](media/rdp/node-rdp.png)

Ezzel csatlakozott a Windows Server-csomópontra.

![Cmd ablakot, a Windows Server csomópontban képe](media/rdp/node-session.png)

Hibaelhárítási parancsok mostantól futtathatja a *cmd* ablak. Mivel a Windows Server-csomópontok használata a Windows Server Core, esetén nem egy teljes grafikus felhasználói Felülettel vagy más grafikus eszközök, a Windows Server a csomóponthoz csatlakozás RDP-kapcsolaton keresztül.

## <a name="remove-rdp-access"></a>RDP-hozzáférés eltávolítása

Ha elkészült, lépjen ki az RDP-kapcsolat a Windows Server-csomópontra, majd lépjen ki az RDP-munkamenetből a virtuális géphez. Miután kilépett mindkét RDP-munkamenetet, törölje a virtuális gép a [az virtuális gép törlése] [ az-vm-delete] parancsot:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>További lépések

Ha további hibaelhárítási adatokat van szüksége, akkor az [a fő Kubernetes csomópontnaplók megtekintése] [ view-master-logs] vagy [Azure Monitor][azure-monitor-containers].

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
