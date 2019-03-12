---
title: SSH-t az Azure Kubernetes Service (AKS)-fürt csomópontjai
description: Ismerje meg, hogyan hozhat létre az SSH-kapcsolatot az Azure Kubernetes Service (AKS)-fürt csomópontjainak hibaelhárítási és karbantartási feladatokhoz.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538799"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Csatlakozzon SSH-n keresztül az Azure Kubernetes Service (AKS) karbantartási és hibaelhárítási fürtcsomópontok

Az Azure Kubernetes Service (AKS)-fürt életciklusa során szükség lehet egy AKS-csomópont eléréséhez. Ez a hozzáférés karbantartási, a naplógyűjtés vagy egyéb hibaelhárítási művelet lehet. Az AKS-csomópontok Linux rendszerű virtuális gépekhez, így elérheti azokat SSH-val. Biztonsági okokból az AKS-csomópontok nem jelennek meg a az interneten.

Ez a cikk bemutatja, hogyan hozhat létre az SSH-kapcsolatot egy AKS-csomópont privát IP-címeik használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>A nyilvános SSH-kulcs hozzáadása

Alapértelmezés szerint az SSH-kulcsok egy AKS-fürt létrehozásakor jönnek létre. Ha nem adta meg a saját SSH-kulcsokat, az AKS-fürt létrehozása során, a nyilvános SSH-kulcsok hozzáadása az AKS-csomópontok.

Az SSH-kulcs ad hozzá egy AKS-csomópont, hajtsa végre az alábbi lépéseket:

1. Az erőforráscsoport nevét az AKS-fürt használatával erőforrások beolvasása [az aks show][az-aks-show]. Adja meg a saját alapvető erőforrás-csoport és az AKS-fürt nevét:

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Az AKS fürt erőforrás csoport használatával listázni a [az virtuálisgép-lista] [ az-vm-list] parancsot. Ezek a virtuális gépek az AKS-csomópontok:

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    Az alábbi példa kimenetében látható, az AKS-csomópontok:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Az SSH-kulcsokat a csomópont hozzáadásához használja a [az vm user frissítése] [ az-vm-user-update] parancsot. Adja meg az erőforráscsoport nevét, majd az előző lépésben lekért az AKS-csomópontok egyikét. Alapértelmezés szerint az AKS-csomópontok tartozó felhasználónév a *azureuser*. Adja meg a helyét, a saját SSH nyilvános kulcs helyét, például *~/.ssh/id_rsa.pub*, vagy illessze be az SSH nyilvános kulcs tartalmát:

    ```azurecli-interactive
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Az AKS címe beolvasása

Az AKS-csomópontok nyilvánosan nem jelennek meg a az interneten. Az AKS-csomópontok ssh-n használhatja a magánhálózati IP-címet. A következő lépésben létrehozott segítő podot az AKS-fürt, amely lehetővé teszi az SSH a csomópont a magánhálózati IP-címe.

A magánhálózati IP-címe egy AKS fürt csomópont használatával megtekintheti a [az vm list-ip-addresses] [ az-vm-list-ip-addresses] parancsot. Adja meg a saját AKS-fürt erőforrás csoport nevét egy korábbi kapott [az-aks-show] [ az-aks-show] . lépés:

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

Az alábbi példa kimenetében látható, az AKS-csomópontok magánhálózati IP-címei:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Az SSH-kapcsolat létrehozása

Hozzon létre egy SSH-kapcsolatot egy AKS-csomópontra, a futtatásához az AKS-fürt segítő podot. A segítő pod biztosít SSH-hozzáférés a fürthöz, és ezután további SSH-csomópont elérése. Hozzon létre, és ez segítő pod használja, hajtsa végre az alábbi lépéseket:

1. Futtassa a `debian` tároló kép és a egy terminál-munkamenetben csatlakoztatni. Ez a tároló az AKS-fürt bármely csomópontjának egy SSH-munkamenet létrehozásához használható:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. Az alap Debian rendszerképet az SSH-összetevők nem tartalmazza. Után a terminál-munkamenetet a tárolóhoz van csatlakoztatva, egy SSH-ügyfél használatával telepítse `apt-get` módon:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Egy új terminálablakban a listában a podok az AKS fürt használatával nem csatlakozik a tárolóhoz, a [kubectl get pods] [ kubectl-get] parancsot. A pod az előző lépésben létrehozott kezdődik a neve *aks-ssh*, az alábbi példában látható módon:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Ez a cikk első lépésben hozzáadott a nyilvános SSH-kulcsot az AKS-csomópont. Ezután másolja a titkos SSH-kulcs a pod. A titkos kulcsot hozzon létre SSH be az AKS-csomópontok szolgál.

    Adja meg a saját *aks-ssh* podnév az előző lépésben beszerzett. Ha szükséges, módosítsa *~/.ssh/id_rsa* a titkos SSH-kulcs helyét:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Vissza a terminál-munkamenetet a tárolóhoz, frissítse a másolt engedélyeit `id_rsa` titkos SSH-kulcsának úgy, hogy az írásvédett felhasználó:

    ```console
    chmod 0600 id_rsa
    ```

1. Most hozzon létre egy SSH-kapcsolatot az AKS csomópontját. Újra, az alapértelmezett felhasználónév az AKS-csomópontok van *azureuser*. Fogadja el a rendszer kéri, a kapcsolat folytatásához, mivel az SSH-kulcsot az első megbízható. A bash parancssorban az AKS-csomópontját, majd megadva:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH-hozzáférés eltávolítása

Ha elkészült, `exit` az SSH-munkamenetet, majd `exit` a tároló interaktív munkamenet. A tároló munkamenet bezárása után, a rendszer törli a pod az AKS-fürtöt az SSH-hozzáféréshez.

## <a name="next-steps"></a>További lépések

Ha további hibaelhárítási adatokat van szüksége, akkor az [kubelet-naplók megtekintése] [ view-kubelet-logs] vagy [a fő Kubernetes csomópontnaplók megtekintése][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
