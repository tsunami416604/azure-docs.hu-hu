---
title: Ügyfél által felügyelt kulcs használata Azure-lemezek titkosításához az Azure Kubernetes szolgáltatásban (ak)
description: Saját kulcsok (BYOK-EK) használatával titkosíthatja az AK-OS operációs rendszert és az adatlemezeket.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 054113da7fe06436fab0cdfff6c4257f1263de97
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290359"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Saját kulcsok (BYOK) használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az [ügyfél által felügyelt kulcsokat][customer-managed-keys] az AK-fürtökhöz tartozó operációs rendszer és az adatlemezek titkosításához.

> [!NOTE]
> A Linux-és Windows-alapú BYOK-fürtök az Azure Managed Disks kiszolgálóoldali titkosítását támogató [Azure-régiókban][supported-regions] érhetők el.

## <a name="before-you-begin"></a>Előzetes teendők

* Ez a cikk azt feltételezi, hogy *új AK-fürtöt*hoz létre.

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

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault példány létrehozása

A kulcsok tárolásához használjon Azure Key Vault-példányt.  Igény szerint az [ügyfél által felügyelt kulcsok konfigurálására][byok-azure-portal] is használhatja a Azure Portal Azure Key Vault

Hozzon létre egy új *erőforráscsoportot*, majd hozzon létre egy új *Key Vault* példányt, és engedélyezze a Soft delete és a kiürítés védelmet.  Ügyeljen arra, hogy az egyes parancsokhoz ugyanazt a régiót és erőforráscsoport-nevet használja.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet-példány létrehozása

Cserélje le a *myKeyVaultName* nevet a kulcstartó nevére.  A következő lépések elvégzéséhez szüksége lesz egy Azure Key Vault tárolt *kulcsra* is.  Tárolja a meglévő kulcsot az előző lépésekben létrehozott Key Vaulton, vagy [hozzon létre egy új kulcsot][key-vault-generate] , és cserélje le az alábbi *myKeyName* a kulcs nevével.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>A DiskEncryptionSet hozzáférésének engedélyezése a Key vaulthoz

Használja az előző lépésekben létrehozott DiskEncryptionSet és erőforráscsoportokat, és adja meg a DiskEncryptionSet-erőforráshoz való hozzáférést a Azure Key Vaulthoz.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Új AK-fürt létrehozása és az operációsrendszer-lemez titkosítása

Hozzon létre egy **új erőforráscsoportot** és egy AK-fürtöt, majd használja a kulcsot az operációsrendszer-lemez titkosításához. Az ügyfél által felügyelt kulcsok csak a 1,17-nél nagyobb kubernetes-verziókban támogatottak. 

> [!IMPORTANT]
> Győződjön meg arról, hogy létrehoz egy új erőforrások-csoportot az AK-fürthöz

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Ha új csomópont-készleteket ad hozzá a fent létrehozott fürthöz, a létrehozás során megadott ügyfél által felügyelt kulcs az operációsrendszer-lemez titkosítására szolgál.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Az AK-fürt adatlemezének titkosítása

Az AK-adatlemezeket a saját kulcsaival is titkosíthatja.

> [!IMPORTANT]
> Győződjön meg arról, hogy megfelelő AK-beli hitelesítő adatokkal rendelkezik. Az egyszerű szolgáltatásnak közreműködői hozzáféréssel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a diskencryptionset telepítve van. Ellenkező esetben hibaüzenet jelenik meg, amely arra utal, hogy az egyszerű szolgáltatásnév nem rendelkezik engedéllyel.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Hozzon létre egy **byok-Azure-Disk. YAML** nevű fájlt, amely az alábbi információkat tartalmazza.  Cserélje le az értékeket a myAzureSubscriptionId, a myResourceGroup és a myDiskEncrptionSetName értékekre, és alkalmazza a YAML.  Ügyeljen arra, hogy az erőforráscsoportot használja, ahol a DiskEncryptionSet telepítve van.  Ha a Azure Cloud Shell használja, akkor ez a fájl a VI vagy a nano használatával hozható létre, mintha virtuális vagy fizikai rendszeren dolgozik:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Ezután futtassa ezt az üzembe helyezést az AK-fürtben:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Korlátozások

* A BYOK jelenleg csak a GA-ban érhető el, és az előzetes verzió bizonyos [Azure-régiókban][supported-regions]
* Az operációsrendszer-lemez titkosítása a 1,17-es és újabb verziójú Kubernetes-verzióval támogatott   
* Csak azokon a régiókban érhető el, ahol a BYOK támogatott
* Az ügyfél által felügyelt kulcsokkal való titkosítás jelenleg csak az új AK-fürtök esetében lehetséges, a meglévő fürtök nem frissíthetők.
* A Virtual Machine Scale Setst használó AK-fürtök szükségesek, a virtuális gépek rendelkezésre állási csoportjai nem támogatottak


## <a name="next-steps"></a>Következő lépések

[Az AK-fürtök biztonságával kapcsolatos ajánlott eljárások][best-practices-security] áttekintése

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
