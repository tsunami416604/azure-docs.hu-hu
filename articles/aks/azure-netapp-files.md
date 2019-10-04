---
title: Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható Azure NetApp Files az Azure Kubernetes Service-szel
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329330"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással

[Azure NetApp Files][anf] az Azure-on futó nagyvállalati szintű, nagy teljesítményű és mért használatú file Storage szolgáltatás. Ez a cikk bemutatja, hogyan integrálhatja Azure NetApp Files az Azure Kubernetes szolgáltatással (ak).

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

> [!IMPORTANT]
> Az AK-fürtnek olyan régióban is kell lennie [, amely támogatja a Azure NetApp Files][anf-regions].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

### <a name="limitations"></a>Korlátozások

A Azure NetApp Files használatakor a következő korlátozások érvényesek:

* Azure NetApp Files csak [a kiválasztott Azure-régiókban][anf-regions]érhető el.
* Azure NetApp Files használata előtt hozzáférést kell biztosítania a Azure NetApp Files szolgáltatáshoz. A hozzáférésre való jelentkezéshez használhatja a [Azure NetApp Files várólista-beküldési űrlapot][anf-waitlist]. A Azure NetApp Files szolgáltatás addig nem érhető el, amíg nem kapja meg a hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól.
* A Azure NetApp Files szolgáltatást ugyanabban a virtuális hálózatban kell létrehozni, mint az AK-fürtöt.
* Csak a Azure NetApp Files statikus kiépítés támogatott az AK-ban.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files konfigurálása

> [!IMPORTANT]
> A *Microsoft. NetApp* erőforrás-szolgáltató regisztrálásához el kell végeznie az előfizetéshez tartozó [Azure NetApp Files várólista-beküldési űrlapot][anf-waitlist] . Az erőforrás nem regisztrálható, amíg meg nem kapja a hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól.

Regisztrálja a *Microsoft. NetApp* erőforrás-szolgáltatót:

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Ez hosszabb időt is igénybe vehet.

Amikor létrehoz egy Azure NetApp-fiókot az AK-val való használatra, létre kell hoznia a fiókot a **csomópont** -erőforráscsoporthoz. Először kérje le az erőforráscsoport nevét az az [az AK show][az-aks-show] paranccsal, és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa lekéri a *myAKSCluster* nevű AK-fürthöz tartozó csomópont-erőforráscsoportot az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Hozzon létre egy Azure NetApp Files fiókot a **csomópont** -erőforráscsoport és az AK-fürttel azonos régióban az [az netappfiles Account Create][az-netappfiles-account-create]paranccsal. A következő példa létrehoz egy *myaccount1* nevű fiókot a *MC_myResourceGroup_myAKSCluster_eastus* erőforráscsoport és a *eastus* régiójában:

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Hozzon létre egy új kapacitási készletet az [az netappfiles Pool Create][az-netappfiles-pool-create]paranccsal. Az alábbi példa egy *mypool1* nevű új kapacitási készletet hoz létre 4 TB méretű és *prémium* szintű szolgáltatási szinten:

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Hozzon létre egy alhálózatot, amelyet [delegálhat Azure NetApp Files][anf-delegate-subnet] az [az Network vnet subnet Create][az-network-vnet-subnet-create]paranccsal. *Ennek az alhálózatnak ugyanabban a virtuális hálózatban kell lennie, mint az AK-fürt.*

```azure-cli
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

Hozzon létre egy kötetet az [az netappfiles Volume Create][az-netappfiles-volume-create]paranccsal.

```azure-cli
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

A kötet részleteinek listázása az [az netappfiles Volume show][az-netappfiles-volume-show] használatával
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

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

Hozzon létre egy `pv-nfs.yaml` PersistentVolume definiálásával. Cserélje le a `path` értéket a *creationToken* és a `server` értékre az előző parancs *IP* -címe alapján. Példa:

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

Frissítse a *kiszolgálót* és az *elérési utat* az NFS-(hálózati fájlrendszer) kötetnek az előző lépésben létrehozott értékére. Hozza létre a PersistentVolume a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pv-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolume *állapota* *elérhető* -e a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>A PersistentVolumeClaim létrehozása

Hozzon létre egy `pvc-nfs.yaml` PersistentVolume definiálásával. Példa:

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

Hozza létre a PersistentVolumeClaim a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pvc-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolumeClaim *állapota* *kötve* van-e a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Csatlakoztatás Pod-mel

Hozzon létre egy `nginx-nfs.yaml` értéket a PersistentVolumeClaim használó Pod definiálásával. Példa:

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f nginx-nfs.yaml
```

Ellenőrizze, hogy *fut* -e a pod a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pod nginx-nfs
```

Ellenőrizze, hogy a kötet csatlakoztatva van-e a pod-hoz a [kubectl exec][kubectl-exec] használatával, majd `df -h` parancs megadásával ellenőrizze, hogy a kötet csatlakoztatva van-e.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>További lépések

További információ a Azure NetApp Filesről: [Mi az Azure NetApp Files][anf]. További információ az NFS-sel való használatáról: [NFS (hálózati fájlrendszer) Linux Server-kötet létrehozása és használata az Azure Kubernetes szolgáltatással (ak)][aks-nfs].


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
