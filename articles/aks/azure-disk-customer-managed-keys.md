---
title: Ügyfél által felügyelt kulcs használata Azure-lemezek titkosításához az Azure Kubernetes szolgáltatásban (ak)
description: Saját kulcsok (BYOK-EK) használatával titkosíthatja az AK-OS operációs rendszert és az adatlemezeket.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 96e7c401578ca8311bfe0e6b5477a9d8cab1a24e
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912731"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Saját kulcsok (BYOK) használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az [ügyfél által felügyelt kulcsokat][customer-managed-keys] az AK-fürtökhöz tartozó operációs rendszer és az adatlemezek titkosításához.

> [!NOTE]
> A Linux-és Windows-alapú AK-fürtök egyaránt támogatottak.

## <a name="before-you-begin"></a>Előzetes teendők

* Ez a cikk azt feltételezi, hogy *új AK-fürtöt*hoz létre.  A titkosítási kulcsok tárolásához a Azure Key Vault egy példányát is használni kell, vagy létre kell hoznia.

* Ha Key Vault használatával titkosítja a felügyelt lemezeket, engedélyeznie kell a Soft Delete (Törlés) és a kiürítési *Azure Key Vault* védelmét.

* Szüksége lesz az Azure CLI 2.0.79 vagy újabb verziójára, valamint az AK-előnézet 0.4.26-bővítményre

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>A legújabb AK CLI előnézet bővítmény telepítése

Az ügyfél által felügyelt kulcsok használatához szüksége lesz az *AK-előnézeti CLI-* bővítmény 0.4.26 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Azure Key Vault-példány létrehozása a kulcsok tárolásához

Igény szerint az [ügyfél által felügyelt kulcsok konfigurálására][byok-azure-portal] is használhatja a Azure Portal Azure Key Vault

Hozzon létre egy új *erőforráscsoportot*, majd hozzon létre egy új *Key Vault* példányt, és engedélyezze a Soft delete és a kiürítés védelmet.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet-példány létrehozása

A következő lépések elvégzéséhez szüksége lesz egy Azure Key Vault tárolt *kulcsra* .  Tárolja a meglévő kulcsot a létrehozott Key Vaultban, vagy [hozzon létre egy kulcsot][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>A Key vaulthoz való hozzáférés biztosítása a DiskEncryptionSet-erőforráshoz

Használja az előző lépésekben létrehozott DiskEncryptionSet és erőforráscsoportokat, és adja meg a DiskEncryptionSet-erőforráshoz való hozzáférést a Azure Key Vaulthoz.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Hozzon létre egy új AK-fürtöt, és titkosítsa az operációsrendszer-lemezt egy ügyfél-összekeveredésű kulccsal

Hozzon létre egy új erőforráscsoportot és egy AK-fürtöt, majd használja a kulcsot az operációsrendszer-lemez titkosításához. Az ügyfél által felügyelt kulcs csak a 1,17-nál nagyobb kubernetes-verziók esetén támogatott.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0
```

Ha új csomópont-készleteket ad hozzá a fent létrehozott fürthöz, a létrehozás során megadott ügyfél által felügyelt kulcs az operációsrendszer-lemez titkosítására szolgál.

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Az AK-alapú fürt adatlemezének titkosítása ügyfél által felügyelt kulccsal

Az AK-adatlemezeket a saját kulcsaival is titkosíthatja.  Cserélje le a myResourceGroup és a myDiskEncryptionSetName értéket a valós értékekre, és alkalmazza a YAML.

Győződjön meg arról, hogy megfelelő AK-beli hitelesítő adatokkal rendelkezik. Az egyszerű szolgáltatásnak közreműködői hozzáféréssel kell rendelkeznie ahhoz az erőforráscsoporthoz, ahol a diskencryptionset megtalálható. Ellenkező esetben hibaüzenet jelenik meg, amely arra utal, hogy az egyszerű szolgáltatásnév nem rendelkezik engedéllyel.

Hozzon létre egy **byok-Azure-Disk. YAML** nevű fájlt, amely az alábbi információkat tartalmazza.  Cserélje le a myResourceGroup és a myDiskEncrptionSetName értékeket az értékekre.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Ezután futtassa ezt az üzembe helyezést az AK-fürtben:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Korlátozások

* Az operációsrendszer-lemez titkosítása a 1,17-es és újabb verziójú Kubernetes-verzióval támogatott   
* Csak azokon a régiókban érhető el, ahol a BYOK támogatott
* Ez jelenleg csak az új AK-fürtök esetében érhető el, a meglévő fürtöket nem lehet frissíteni
* A Virtual Machine Scale Setst használó AK-fürtök szükségesek, a virtuális gépek rendelkezésre állási csoportjai nem támogatottak


## <a name="next-steps"></a>Következő lépések

[Az AK-fürtök biztonságával kapcsolatos ajánlott eljárások][best-practices-security] áttekintése

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
