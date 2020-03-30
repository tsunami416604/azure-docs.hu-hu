---
title: Az Azure NetApp-fájlok integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure NetApp-fájlok az Azure Kubernetes szolgáltatással
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273748"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Az Azure NetApp-fájlok integrálása az Azure Kubernetes szolgáltatással

[Az Azure NetApp Files][anf] egy nagyvállalati szintű, nagy teljesítményű, forgalmi díjas fájltárolási szolgáltatás, amely az Azure-on fut. Ez a cikk bemutatja, hogyan integrálhatja az Azure NetApp-fájlokat az Azure Kubernetes szolgáltatással (AKS).

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

> [!IMPORTANT]
> Az AKS-fürtnek olyan régióban kell [lennie, amely támogatja az Azure NetApp-fájlokat.][anf-regions]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

### <a name="limitations"></a>Korlátozások

Az Azure NetApp-fájlok használatakor a következő korlátozások érvényesek:

* Az Azure NetApp Files csak [bizonyos Azure-régiókban][anf-regions]érhető el.
* Az Azure NetApp-fájlok használata előtt hozzáférést kell biztosítani az Azure NetApp Files szolgáltatáshoz. A hozzáférés igényléséhez használhatja az [Azure NetApp Files várólisták beküldési űrlapját.][anf-waitlist] Az Azure NetApp Files szolgáltatás addig nem érhető el, amíg meg nem kapja az Azure NetApp Files csapatának hivatalos visszaigazoló e-mailjét.
* Az Azure NetApp Files szolgáltatást ugyanabban a virtuális hálózatban kell létrehozni, mint az AKS-fürt.
* Az AKS-fürt kezdeti üzembe helyezése után csak az Azure NetApp-fájlok statikus kiépítése támogatott.
* A dinamikus kiépítés azure NetApp Files használatával telepítse és konfigurálja a [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07-es vagy újabb verzióját.

## <a name="configure-azure-netapp-files"></a>Azure NetApp-fájlok konfigurálása

> [!IMPORTANT]
> A *Microsoft.NetApp* erőforrás-szolgáltató regisztrálása előtt ki kell töltenie az [Azure NetApp Files várólistára vonatkozó beküldési űrlapot][anf-waitlist] az előfizetéséhez. Nem regisztrálhatja az erőforrás-biztosít, amíg meg nem kapja a hivatalos visszaigazoló e-mailt az Azure NetApp Files csapat.

Regisztrálja a *Microsoft.NetApp* erőforrás-szolgáltatót:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Ez eltarthat egy ideig.

Amikor létrehoz egy Azure NetApp-fiókot az AKS-hez, létre kell hoznia a fiókot a **csomópont** erőforráscsoportban. Először az erőforráscsoport nevét az [az aks][az-aks-show] `--query nodeResourceGroup` show paranccsal kapja meg, és adja hozzá a lekérdezési paramétert. A következő példa a *myResourceGroup*nevű AKS-fürt csomóponterőforrás-csoportját kapja: *myAKSCluster*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Hozzon létre egy Azure NetApp Files fiókot a **csomópont** erőforráscsoportjában és az AKS-fürttel azonos régióban [az az netappfiles fiók létrehozása használatával.][az-netappfiles-account-create] A következő példa létrehoz egy *myaccount1* nevű fiókot a *MC_myResourceGroup_myAKSCluster_eastus* erőforráscsoportban és *az Eastus* régióban:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Hozzon létre egy új kapacitáskészletet [az az netappfiles készlet létrehozása][az-netappfiles-pool-create]segítségével. A következő példa létrehoz egy új kapacitáskészlet nevű *mypool1* 4 TB méretű és *prémium szintű* szolgáltatás:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Hozzon létre egy alhálózatot az [Azure NetApp-fájlok nak az][anf-delegate-subnet] az hálózati [virtuálishálózat-alhálózat létrehozása][az-network-vnet-subnet-create]használatával. *Ennek az alhálózatnak ugyanabban a virtuális hálózatban kell lennie, mint az AKS-fürtnek.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Hozzon létre egy kötetet az [az netappfiles kötet létrehozása][az-netappfiles-volume-create]használatával.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>A PersistentVolume létrehozása

A kötet részleteinek listázása az [netappfiles kötetmegjelenítés használatával][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Hozzon `pv-nfs.yaml` létre egy definiáló persistentvolume. Cserélje `path` le a `server` *creationToken* és *ipAddress* az előző parancsból. Példa:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Frissítse a *kiszolgálót* és az előző lépésben létrehozott NFS (Network File System) kötet értékeinek *elérési útját.* Hozza létre a PersistentVolume-t a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pv-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolume *állapota* *elérhető-e* a [kubectl describe][kubectl-describe] paranccsal:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>A PersistentVolumeClaim létrehozása

Hozzon `pvc-nfs.yaml` létre egy definiáló persistentvolume. Példa:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Hozza létre a PersistentVolumeClaim-et a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pvc-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolumeClaim *állapota* meg *van kötve* a [kubectl describe][kubectl-describe] paranccsal:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Rögzítés egy hüvely

Hozzon `nginx-nfs.yaml` létre egy definiáló pod, amely a PersistentVolumeClaim. Példa:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Hozza létre a pod a [kubectl alkalmazni][kubectl-apply] parancs:

```console
kubectl apply -f nginx-nfs.yaml
```

Ellenőrizze, hogy a pod *fut-e* a [kubectl leírás][kubectl-describe] paranccsal:

```console
kubectl describe pod nginx-nfs
```

Ellenőrizze, hogy a kötet csatlakoztatva van-e a podsegítségével [kubectl exec][kubectl-exec] csatlakozni a pod, majd `df -h` ellenőrizze, hogy a kötet csatlakoztatva van.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>További lépések

Az Azure NetApp-fájlokról a [Mi az Azure NetApp-fájlok című témakörben talál][anf]további információt. Az NFS AKS-sel való használatáról az [NFS (Network File System) Linux Server-kötet manuális létrehozása és használata az Azure Kubernetes szolgáltatással (AKS)][aks-nfs]című témakörben talál további információt.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
