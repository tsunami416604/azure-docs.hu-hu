---
title: SSH-t az Azure Kubernetes Service (AKS)-fürt csomópontjai
description: Ismerje meg, hogyan hozhat létre az SSH-kapcsolatot az Azure Kubernetes Service (AKS)-fürt csomópontjainak hibaelhárítási és karbantartási feladatokhoz.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 34f2d11cf4e1fb8e03d037be221e7b18ed4c5ad0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303330"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Csatlakozzon SSH-n keresztül az Azure Kubernetes Service (AKS) karbantartási és hibaelhárítási fürtcsomópontok

Az Azure Kubernetes Service (AKS)-fürt életciklusa során szükség lehet egy AKS-csomópont eléréséhez. Ez a hozzáférés karbantartási, a naplógyűjtés vagy egyéb hibaelhárítási művelet lehet. AKS-csomópontok ssh-t, beleértve a Windows Server-csomópontok (jelenleg előzetes verzióban érhető el az aks-ben) használatával is elérheti. Emellett [kapcsolódni a távoli asztali protokoll (RDP) kapcsolatokat használt Windows Server-csomópontok][aks-windows-rdp]. Biztonsági okokból az AKS-csomópontok nem jelennek meg a az interneten.

Ez a cikk bemutatja, hogyan hozhat létre az SSH-kapcsolatot egy AKS-csomópont privát IP-címeik használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Emellett az Azure CLI 2.0.64 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>A nyilvános SSH-kulcs hozzáadása

Alapértelmezés szerint az SSH-kulcsok kapott, vagy a generált, akkor hozzá csomópontokra, amikor az AKS-fürt létrehozása. Adja meg a különböző, mint az AKS-fürt létrehozásakor használt SSH-kulcsokat kell, ha a nyilvános SSH-kulcsot a Linux-AKS-csomópontok hozzáadása. Szükség esetén is létrehozhat egy SSH kulcs [macOS vagy Linux-alapú][ssh-nix] or [Windows][ssh-windows]. A PuTTY általános használatakor a kulcspár létrehozása, mentse a kulcspár egy OpenSSH formátumban ahelyett, hogy az alapértelmezett PuTTy titkos kulcs formátuma (.ppk fájlt).

> [!NOTE]
> SSH-kulcsokat is jelenleg csak lehet hozzáadni az Azure parancssori felületével Linux-csomópontokat. Ha a Windows Server-csomópontok használata esetén az AKS-fürt létrehozásakor megadott SSH-kulcsok használata, és ugorjon a [beszerzése az AKS címe](#get-the-aks-node-address). Másik lehetőségként [kapcsolódni a távoli asztali protokoll (RDP) kapcsolatokat használt Windows Server-csomópontok][aks-windows-rdp].

A lépéseket a az AKS-csomópontok a magánhálózati IP-címe eltér futtatása AKS-fürt típusa alapján:

* A legtöbb AKS-fürtök esetén kövesse a lépéseket a [IP-címének lekéréséhez rendszeres AKS-fürtök](#add-ssh-keys-to-regular-aks-clusters).
* Az aks-ben, amely használhatja a virtuális gép méretezési csoportokat, például több csomópontkészletek vagy Windows Server-tároló támogatása, minden előzetes verziójú funkciók használatakor [kövesse a lépéseket, a virtuális gép scale set-alapú AKS fürtök](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Adja hozzá az SSH-kulcsok rendszeres AKS-fürtök

Adjon hozzá az SSH-kulcsot a Linux AKS csomóponthoz, hajtsa végre az alábbi lépéseket:

1. Az erőforráscsoport nevét az AKS-fürt használatával erőforrások beolvasása [az aks show][az-aks-show]. A következő változót hozzá van rendelve a fürt neve *CLUSTER_RESOURCE_GROUP*. Cserélje le *myResourceGroup* ahol Ön AKS-fürt is található az erőforráscsoport nevét:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Az AKS fürt erőforrás csoport használatával listázni a [az virtuálisgép-lista][az-vm-list] parancsot. Ezek a virtuális gépek az AKS-csomópontok:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Az alábbi példa kimenetében látható, az AKS-csomópontok:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Az SSH-kulcsokat a csomópont hozzáadásához használja a [az vm user frissítése][az-vm-user-update] parancsot. Adja meg az erőforráscsoport nevét, majd az előző lépésben lekért az AKS-csomópontok egyikét. Alapértelmezés szerint az AKS-csomópontok tartozó felhasználónév a *azureuser*. Adja meg a helyét, a saját SSH nyilvános kulcs helyét, például *~/.ssh/id_rsa.pub*, vagy illessze be az SSH nyilvános kulcs tartalmát:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Adja hozzá az SSH-kulcsok a virtual machine scale set-alapú AKS fürtök

Az SSH-kulcs ad hozzá egy Linux-AKS csomópont, amely része egy virtuálisgép-méretezési csoportot, hajtsa végre az alábbi lépéseket:

1. Az erőforráscsoport nevét az AKS-fürt használatával erőforrások beolvasása [az aks show][az-aks-show]. A következő változót hozzá van rendelve a fürt neve *CLUSTER_RESOURCE_GROUP*. Cserélje le *myResourceGroup* ahol Ön AKS-fürt is található az erőforráscsoport nevét:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Ezután kérdezze le az AKS fürt a virtuális gép méretezési a [az vmss list][az-vmss-list] parancsot. A következő változót hozzá van rendelve a virtuális gép méretezési csoport neve *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Az SSH-kulcsokat a virtuálisgép-méretezési csoportban lévő csomópontok hozzáadásához használja a [az vmss-bővítmény beállítása][az-vmss-extension-set] parancsot. Az az előző parancsokban elérhető az erőforráscsoportot és a virtuálisgép-méretezési csoport nevének beállítása. Alapértelmezés szerint az AKS-csomópontok tartozó felhasználónév a *azureuser*. Ha szükséges, frissítse a saját SSH nyilvános kulcs helyét, helyét például *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Az SSH-kulcsot használ a csomópontok alkalmazni a [az vmss update-instances][az-vmss-update-instances] parancsot:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Az AKS címe beolvasása

Az AKS-csomópontok nyilvánosan nem jelennek meg a az interneten. Az AKS-csomópontok ssh-n használhatja a magánhálózati IP-címet. A következő lépésben létrehozott segítő podot az AKS-fürt, amely lehetővé teszi az SSH a csomópont a magánhálózati IP-címe. A lépéseket a az AKS-csomópontok a magánhálózati IP-címe eltér futtatása AKS-fürt típusa alapján:

* A legtöbb AKS-fürtök esetén kövesse a lépéseket a [IP-címének lekéréséhez rendszeres AKS-fürtök](#ssh-to-regular-aks-clusters).
* Az aks-ben, amely használhatja a virtuális gép méretezési csoportokat, például több csomópontkészletek vagy Windows Server-tároló támogatása, minden előzetes verziójú funkciók használatakor [kövesse a lépéseket, a virtuális gép scale set-alapú AKS fürtök](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>Ssh-n rendszeres AKS-fürtök

A magánhálózati IP-címe egy AKS fürt csomópont használatával megtekintheti a [az vm list-ip-addresses][az-vm-list-ip-addresses] command. Provide your own AKS cluster resource group name obtained in a previous [az-aks-show][az-aks-show] . lépés:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Az alábbi példa kimenetében látható, az AKS-csomópontok magánhálózati IP-címei:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>Ssh-KAPCSOLATOT a virtuális gép set-alapú AKS fürtök méretezése

A belső IP-cím használatával csomópont listában a [kubectl get parancs][kubectl-get]:

```console
kubectl get nodes -o wide
```

Az alábbi példa kimenetében látható a fürtöt, a Windows Server-csomópont a belső IP-címek az összes csomópont.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezze fel a csomópont hibaelhárítása kívánt belső IP-címe. Ez a cím egy későbbi lépésben fogja használni.

## <a name="create-the-ssh-connection"></a>Az SSH-kapcsolat létrehozása

Hozzon létre egy SSH-kapcsolatot egy AKS-csomópontra, a futtatásához az AKS-fürt segítő podot. A segítő pod biztosít SSH-hozzáférés a fürthöz, és ezután további SSH-csomópont elérése. Hozzon létre, és ez segítő pod használja, hajtsa végre az alábbi lépéseket:

1. Futtassa a `debian` tároló kép és a egy terminál-munkamenetben csatlakoztatni. Ez a tároló az AKS-fürt bármely csomópontjának egy SSH-munkamenet létrehozásához használható:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Használatakor a Windows Server-csomópontok (jelenleg előzetes verzióban érhető el az aks-ben), egy csomópont-választó hozzáadása a egy Linux-csomóponton a Debian tároló ütemezni a következő parancsot:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Az alap Debian rendszerképet az SSH-összetevők nem tartalmazza. Után a terminál-munkamenetet a tárolóhoz van csatlakoztatva, egy SSH-ügyfél használatával telepítse `apt-get` módon:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Egy új terminálablakban a listában a podok az AKS fürt használatával nem csatlakozik a tárolóhoz, a [kubectl get pods][kubectl-get] parancsot. A pod az előző lépésben létrehozott kezdődik a neve *aks-ssh*, az alábbi példában látható módon:

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

Ha további hibaelhárítási adatokat van szüksége, akkor az [kubelet-naplók megtekintése][view-kubelet-logs] or [view the Kubernetes master node logs][view-master-logs].

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
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
