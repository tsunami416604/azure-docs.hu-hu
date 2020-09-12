---
title: A Container Storage Interface (CSI) illesztőprogramjainak engedélyezése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan engedélyezheti az Azure Disk-és Azure Files tároló-illesztőprogramokat az Azure Kubernetes Service-(ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: bd5706d20496e1ff00843f761443d183cf7fcae3
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422041"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>A Container Storage Interface (CSI) illesztőprogramjainak engedélyezése Azure-lemezekhez és Azure Files az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)

A Container Storage Interface (CSI) egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszerek számára teszi elérhetővé a Kubernetes tárolt számítási feladatokat. A CSI segítségével az Azure Kubernetes Service (ak) segítségével írhat, telepíthet és megismételheti a beépülő modulokat a Kubernetes meglévő tárolási rendszereinek új vagy javító lehetőségeit anélkül, hogy az alapvető Kubernetes-kódot kellene érintenie, és várnia kell a kiadási ciklusokra.

A CSI Storage-illesztőprogram támogatása az AK-ban lehetővé teszi, hogy natív módon hasznosítsa a következőket:
- [*Azure-lemezek*](azure-disk-csi.md) – Kubernetes *adatlemez* -erőforrás létrehozásához használható. A lemezek az Azure Premium Storage-t, a nagy teljesítményű SSD-ket, illetve az Azure standard Storage-t is használhatják, a normál HDD-k vagy a standard SSD-k által támogatottak. A legtöbb éles és fejlesztési számítási feladathoz használja a Premium Storage-t. Az Azure-lemezek *ReadWriteOnce*-ként vannak csatlakoztatva, így csak egyetlen Pod számára érhető el. A több hüvelyrel egyidejűleg elérhető tárolási kötetek esetében használja a Azure Files.
- [*Azure Files*](azure-files-csi.md) használható egy Azure Storage-fiók által támogatott SMB 3,0-megosztás csatlakoztatására a hüvelyek számára. A fájlok lehetővé teszik az adatmegosztást több csomóponton és hüvelyen keresztül. A fájlok a normál HDD-k, illetve az Azure Premium Storage által támogatott Azure standard Storage-t is használhatják, nagy teljesítményű SSD-k által támogatottak.

> [!IMPORTANT]
> A Kubernetes 1,21-es verziójától kezdődően a Kubernetes csak a CSI-illesztőprogramokat és alapértelmezés szerint fogja használni. Ezek a Kubernetes-ben a tárolók támogatásának jövője.
>
> A *"beépített illesztőprogramok"* kifejezés az alapszintű kubernetes-kód részét képező aktuális tároló-illesztőprogramokat és a beépülő modulokat tartalmazó új CSI-illesztőprogramokat tartalmazza.

## <a name="limitations"></a>Korlátozások

- Ez a funkció csak fürt létrehozási idején állítható be.
- A CSI-illesztőprogramokat támogató minimális kubernetes-alverzió v 1.17.
- Az előzetes verzióban az alapértelmezett Storage-osztály továbbra is ugyanaz lesz a [fán tárolt tárolási osztályban](concepts-storage.md#storage-classes). Miután ez a funkció általánosan elérhetővé válik, az alapértelmezett tárolási osztály lesz `managed-csi` eltávolítva.
- Az első előzetes fázisban csak az Azure CLI támogatott.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Az `EnableAzureDiskFileCSIDriver` előzetes verzió funkciójának regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely a CSI-illesztőprogramokat az Azure-lemezekhez és Azure Files használhatja, engedélyeznie kell a `EnableAzureDiskFileCSIDriver` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `EnableAzureDiskFileCSIDriver` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Ha egy AK-alapú fürtöt vagy egy olyan csomópont-készletet szeretne létrehozni, amely képes a CSI Storage-illesztőprogramokat használni, szüksége lesz a legújabb *AK-előnézet CLI-* bővítményre. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>A CSI Storage-illesztőprogramokat használó új fürt létrehozása

Hozzon létre egy új fürtöt, amely a következő CLI-parancsokkal képes felhasználni a CSI Storage-illesztőprogramokat az Azure-lemezekhez és Azure Files. A `--aks-custom-headers` szolgáltatás beállításához használja a jelzőt `EnableAzureDiskFileCSIDriver` .

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Hozza létre az AK-fürtöt a CSI Storage-illesztőprogramok támogatásával.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Ha a CSI Storage-illesztőprogramok helyett fürtöket szeretne létrehozni a faszerkezetű tároló-illesztőprogramokban, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .


Győződjön meg arról, hogy hány Azure Disk-alapú kötetet tud csatlakoztatni ehhez a csomóponthoz a következő futtatásával:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Következő lépések

- Az Azure-lemezek CSI-meghajtójának használatával kapcsolatban lásd: az [Azure Disk és a CSI-illesztőprogramok használata](azure-disk-csi.md).
- Az Azure Files-hoz készült CSI-meghajtóval kapcsolatban lásd: az [Azure Files és a CSI-illesztőprogramok használata](azure-files-csi.md).
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-storage]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register