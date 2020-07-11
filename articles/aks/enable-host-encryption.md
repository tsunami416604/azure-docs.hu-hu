---
title: Gazdagép-alapú titkosítás engedélyezése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan konfigurálhat gazdagép-alapú titkosítást egy Azure Kubernetes-szolgáltatási (ak-) fürtben
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 6f145750ac9158c0184ac9d4d6c303828ee5bdf4
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225261"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Gazdagép-alapú titkosítás az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)

A gazdagép-alapú titkosítással az AK-ügynök csomópontjainak virtuálisgép-gazdagépén tárolt adatok titkosítva maradnak, és a tárolási szolgáltatásba titkosított folyamatok is titkosítottak. Ez azt jelenti, hogy a temp lemezeket a rendszer a platform által felügyelt kulcsokkal titkosítja. Az operációs rendszer és az adatlemezek gyorsítótára inaktív állapotban van, vagy a platform által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal a lemezeken beállított titkosítási típustól függően. Alapértelmezés szerint az AK-ban az operációs rendszer és az adatlemezek titkosítva vannak a platform által felügyelt kulcsokkal, ami azt jelenti, hogy ezeknek a lemezeknek a gyorsítótárai is alapértelmezés szerint titkosítva vannak a platform által felügyelt kulcsokkal.  Megadhatja saját felügyelt kulcsait a [saját kulcsok (BYOK) Azure-lemezekkel való használatával az Azure Kubernetes szolgáltatásban](azure-disk-customer-managed-keys.md). A lemezek gyorsítótára ezután az ebben a lépésben megadott kulccsal is titkosítva lesz.


## <a name="before-you-begin"></a>Előkészületek

Ez a funkció csak a fürt létrehozásakor vagy a csomópont-készlet létrehozási idején állítható be.

> [!NOTE]
> A gazdagép-alapú titkosítás olyan [Azure-régiókban][supported-regions] érhető el, amelyek támogatják az Azure Managed Disks kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép- [méretekkel][supported-sizes]rendelkeznek.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a `aks-preview` CLI-bővítmény v 0.4.55 vagy újabb verziója van telepítve
- Győződjön meg arról, hogy engedélyezve van a `EncryptionAtHost` funkció jelzője `Microsoft.Compute` .
- Győződjön meg arról, hogy engedélyezve van a `EncryptionAtHost` funkció jelzője `Microsoft.ContainerService` .

### <a name="register-encryptionathost--preview-features"></a>Előzetes verziójú funkciók regisztrálása `EncryptionAtHost`

Ha gazdagép-alapú titkosítást használó AK-fürtöt szeretne létrehozni, engedélyeznie kell a `EncryptionAtHost` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `EncryptionAtHost` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EncryptionAtHost"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EncryptionAtHost')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a `Microsoft.ContainerService` és az erőforrás- `Microsoft.Compute` szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A gazdagép-alapú titkosítással rendelkező AK-fürtök létrehozásához a legújabb *AK-előnézet CLI-* bővítményre van szükség. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal, vagy keresse meg a rendelkezésre álló frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

- Csak új csomópont-készleteken vagy új fürtökön engedélyezhető.
- Csak olyan [Azure-régiókban][supported-regions] engedélyezhető, amelyek támogatják az Azure Managed Disks kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép- [méretekkel][supported-sizes]rendelkeznek.
- A (z) Virtual Machine Scale Sets (VMSS) alapján, virtuálisgép- *készlet típusú*fürt és csomópont-készlet szükséges.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Gazdagép-alapú titkosítás használata új fürtökön (előzetes verzió)

Konfigurálja a Fürtfelügyelő csomópontjait a gazdagép-alapú titkosítás használatára a fürt létrehozásakor. A `--aks-custom-headers` fejléc beállításához használja a jelzőt `EnableEncryptionAtHost` .

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Ha gazdagép-alapú titkosítás nélkül szeretné létrehozni a fürtöket, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Gazdagép-alapú titkosítás használata meglévő fürtökön (előzetes verzió)

A meglévő fürtökön a gazdagép-alapú titkosítást úgy engedélyezheti, ha új csomópont-készletet ad hozzá a fürthöz. Konfigurálja az új csomópont-készletet a gazdagép-alapú titkosítás használatára a `--aks-custom-headers` jelző használatával.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Ha a gazdagép-alapú titkosítási funkció nélkül szeretne új csomópont-készleteket létrehozni, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="next-steps"></a>További lépések

Tekintse át a [gazdagép-alapú titkosításról](/azure/virtual-machines/linux/disk-encryption#host-based-encryption)szóló [ajánlott eljárásokat az AK-fürtök biztonsága][best-practices-security] című cikkből.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[supported-regions]: /azure/virtual-machines/linux/disk-encryption#supported-regions
[supported-sizes]: /azure/virtual-machines/linux/disk-encryption#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
