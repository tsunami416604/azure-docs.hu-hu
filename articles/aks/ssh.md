---
title: SSH-n keresztüli bejelentkezés az Azure Kubernetes Service- (AKS-) fürtcsomópontokba
description: Ismerje meg, hogyan hozhat létre SSH-kapcsolatot az Azure Kubernetes-szolgáltatás (AKS) fürtcsomópontjaival hibaelhárítási és karbantartási feladatokhoz.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593631"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>SSH-n keresztüli csatlakozás az Azure Kubernetes Service- (AKS-) fürtcsomópontokhoz karbantartás és hibaelhárítás céljából

Az Azure Kubernetes-szolgáltatás (AKS) fürtjének teljes életciklusa során előfordulhat, hogy hozzá kell férnie egy AKS-csomóponthoz. Ez a hozzáférés lehet karbantartási, naplógyűjtési vagy egyéb hibaelhárítási műveletekhez. Az AKS-csomópontok ssh-val érhetők el, beleértve a Windows Server-csomópontokat is (jelenleg előzetes verzióban az AKS-ben). [A Windows Server-csomópontokrdp-kapcsolatokkal][aks-windows-rdp]is csatlakozhatnak. Biztonsági okokból az AKS-csomópontok nincsenek kitéve az internetnek. Az SSH az AKS-csomópontok, használja a privát IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre SSH-kapcsolatot egy AKS-csomóddal a saját IP-címük használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Alapértelmezés szerint az SSH-kulcsokat a rendszer beszerzi vagy létrehozza, majd a csomópontokhoz adja hozzá, amikor Létrehoz egy AKS-fürtöt. Ez a cikk bemutatja, hogyan adhat meg különböző SSH-kulcsokat, mint az AKS-fürt létrehozásakor használt SSH-kulcsok. A cikk azt is bemutatja, hogyan határozhatja meg a csomópont privát IP-címét, és csatlakozhat hozzá az SSH használatával. Ha nem kell megadnia egy másik SSH-kulcsot, akkor kihagyhatja az SSH nyilvános kulcs csomóponthoz való hozzáadásának lépéseit.

Ez a cikk azt is feltételezi, hogy rendelkezik egy SSH-kulccsal. SSH-kulcsot [macOS, Linux vagy][ssh-nix] [Windows][ssh-windows]rendszerrel hozhat létre. Ha a PuTTY Gen segítségével hozza létre a kulcspárt, mentse a kulcspárt OpenSSH formátumban, ne pedig az alapértelmezett PuTTy titkos kulcsformátumban (.ppk fájl).

Az Azure CLI 2.0.64-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Virtuálisgép-méretezési csoporton alapuló AKS-fürtök konfigurálása SSH-hozzáféréshez

A virtuálisgép-méretezési készlet ssh-hozzáféréshez való konfigurálásához keresse meg a fürt virtuálisgép-méretezési készletének nevét, és adja hozzá az SSH nyilvános kulcsot a méretezési csoporthoz.

Az [aks show][az-aks-show] parancs segítségével leszeretné késelni az AKS-fürt erőforráscsoport nevét, majd az [az vmss list][az-vmss-list] parancsot a méretezési csoport nevének leéséhez.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

A fenti példa hozzárendeli a *myResourceGroup* *myAKSCluster* fürterőforrás-csoportjának nevét *a CLUSTER_RESOURCE_GROUP.* A példa ezután *CLUSTER_RESOURCE_GROUP* a méretezési halmaz nevének felsorolására és *a SCALE_SET_NAME*való hozzárendelésére.

> [!IMPORTANT]
> Jelenleg csak frissítse az SSH-kulcsokat a virtuális gép méretezési készletalapú AKS-fürtök az Azure CLI használatával.
> 
> Linux-csomópontok esetén az SSH-kulcsok jelenleg csak az Azure CLI használatával adhatók hozzá. Ha SSH használatával szeretne csatlakozni egy Windows Server-csomóponthoz, használja az AKS-fürt létrehozásakor megadott SSH-kulcsokat, és hagyja ki az SSH nyilvános kulcs hozzáadásához szükséges következő parancskészletet. Továbbra is szüksége lesz a hibaelhárításra kívánt csomópont IP-címére, amely a szakasz végső parancsában látható. Másik lehetőségként az SSH használata helyett [rdp-kapcsolatok használatával is csatlakozhat a Windows Server-csomópontokhoz.][aks-windows-rdp]

Az SSH-kulcsok hozzáadása a csomópontok egy virtuális gép méretezési csoport, használja az [az vmss bővítménykészlet][az-vmss-extension-set] és [az vmss update-instances][az-vmss-update-instances] parancsokat.

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

A fenti példa az előző parancsok *CLUSTER_RESOURCE_GROUP* és *SCALE_SET_NAME* változóit használja. A fenti példa a *~/.ssh/id_rsa.pub* webhelyet is használja az SSH nyilvános kulcsának helyeként.

> [!NOTE]
> Alapértelmezés szerint az AKS-csomópontok felhasználóneve *azureuser.*

Miután hozzáadja az SSH nyilvános kulcsot a méretezési csoporthoz, az SSH-t az adott méretezési csoport ban lévő csomópont virtuális gépbe az IP-cím használatával is létrehozhatja. Tekintse meg az AKS-fürtcsomópontok privát IP-címét a [kubectl get paranccsal.][kubectl-get]

```console
kubectl get nodes -o wide
```

A következő példa kimeneta a fürt összes csomópontjának belső IP-címét mutatja, beleértve a Windows Server-csomópontot is.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Rögzítse a hibaelhárításra kívánt csomópont belső IP-címét.

Ha SSH használatával szeretné elérni a csomópontot, kövesse [az SSH-kapcsolat létrehozása](#create-the-ssh-connection)című részben leírt lépéseket.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Virtuálisgép rendelkezésre állási készletalapú AKS-fürtjének konfigurálása SSH-hozzáféréshez

A virtuális gép rendelkezésre állási készletalapú AKS-fürt SSH-hozzáféréshez történő konfigurálásához keresse meg a fürt Linux-csomópontjának nevét, és adja hozzá az SSH nyilvános kulcsát az adott csomóponthoz.

Az [az aks show][az-aks-show] parancs segítségével leszeretné késelni az AKS-fürt erőforráscsoport nevét, majd az [az vm listparancs][az-vm-list] a fürt Linux-csomódának virtuálisgép-nevének listázásához.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példa hozzárendeli a *myResourceGroup* *myAKSCluster* fürterőforrás-csoportjának nevét *a CLUSTER_RESOURCE_GROUP.* A példa ezután *CLUSTER_RESOURCE_GROUP* a virtuális gép nevének felsorolására használja. A példakimenet a virtuális gép nevét jeleníti meg:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Az SSH-kulcsok csomóponthoz való hozzáadásához használja az [az vm felhasználói frissítési][az-vm-user-update] parancsot.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A fenti példa a *CLUSTER_RESOURCE_GROUP* változót és a csomópont virtuálisgép-nevét használja az előző parancsokból. A fenti példa a *~/.ssh/id_rsa.pub* webhelyet is használja az SSH nyilvános kulcsának helyeként. Az SSH nyilvános kulcs tartalmát is használhatja az elérési út megadása helyett.

> [!NOTE]
> Alapértelmezés szerint az AKS-csomópontok felhasználóneve *azureuser.*

Miután hozzáadja az SSH nyilvános kulcsot a virtuális csomóponthoz, az SSH-t az IP-cím használatával beviheti a virtuális gépbe. Tekintse meg egy AKS-fürtcsomópont privát IP-címét az [az vm list-ip-addresses][az-vm-list-ip-addresses] paranccsal.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példa az előző parancsokban található *CLUSTER_RESOURCE_GROUP* változókészletet használja. A következő példa kimenetaz AKS-csomópontok privát IP-címeit mutatja be:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Az SSH-kapcsolat létrehozása

SSH-kapcsolat létrehozása egy AKS-csomóponthoz, egy segítő podot futtat az AKS-fürtben. Ez a segítő pod sSH-hozzáférést biztosít a fürthöz, majd további SSH-csomópont-hozzáférést. A segítő egység létrehozásához és használatához hajtsa végre az alábbi lépéseket:

1. Futtasson egy `debian` tárolólemezképet, és csatoljon hozzá egy terminálmunkamenetet. Ez a tároló ssh-munkamenetek létrehozására használható az AKS-fürt bármely csomópontos ával:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Ha Windows Server-csomópontokat használ (jelenleg előzetes verzióban az AKS-ben), adjon hozzá egy csomópontválasztót a parancshoz a Debian tároló Linux-csomópontra történő ütemezéséhez:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Miután a terminálmunkamenet csatlakoztatva van a tárolóhoz, telepítsen egy SSH-ügyfelet a következő használatával: `apt-get`

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Nyisson meg egy új terminálablakot, amely nem csatlakozik a tárolóhoz, másolja a személyes SSH-kulcsot a segítő egységbe. Ez a személyes kulcs az SSH-t az AKS-csomópontba való létrehozására használja. 

   Szükség esetén módosítsa *a ~/.ssh/id_rsa-t* a privát SSH kulcs helyére:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Térjen vissza a terminálmunkamenethez a tárolóhoz, `id_rsa` frissítse a másolt személyes SSH-kulcs engedélyeit, hogy az csak olvasható legyen:

    ```console
    chmod 0600 id_rsa
    ```

1. Hozzon létre egy SSH-kapcsolatot az AKS-csomóponttal. Az AKS-csomópontok alapértelmezett felhasználóneve ismét az *azureuser.* Fogadja el a kérdést, hogy folytassa a kapcsolatot, mivel az SSH-kulcs először megbízható. Ezután az AKS-csomópont bash-parancssora lesz:

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

Ha elkészült, az SSH-munkamenet, `exit` majd `exit` az interaktív tároló munkamenet. Amikor ez a tárolómunkamenet bezárul, az AKS-fürtből származó SSH-hozzáféréshez használt pod törlődik.

## <a name="next-steps"></a>További lépések

Ha további hibaelhárítási adatokra van szüksége, [megtekintheti a kubelet naplókat,][view-kubelet-logs] vagy [megtekintheti a Kubernetes fő csomópontnaplókat.][view-master-logs]

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
