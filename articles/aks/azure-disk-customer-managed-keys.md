---
title: Ügyfél által felügyelt kulcs használata az Azure-lemezek titkosításához az Azure Kubernetes szolgáltatásban (AKS)
description: Saját kulcsokkal (BYOK) titkosíthatja az AKS operációs rendszert és az adatlemezeket.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596504"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Saját kulcsok (BYOK) és Azure-lemezek az Azure Kubernetes-szolgáltatás (AKS)

Az Azure Storage titkosítja az összes adatot egy tárolófiókban inaktív. Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat az operációs rendszer és az AKS-fürtök adatlemezei számára is, hogy [az ügyfél által felügyelt kulcsokat][customer-managed-keys] inaktív titkosításhoz használhassa.

> [!NOTE]
> A BYOK Linux és a Windows alapú AKS-fürtök olyan [Azure-régiókban][supported-regions] érhetők el, amelyek támogatják az Azure felügyelt lemezeinek kiszolgálóoldali titkosítását.

## <a name="before-you-begin"></a>Előkészületek

* Ez a cikk feltételezi, hogy *új AKS-fürtöt*hoz létre.

* Engedélyeznie kell a helyreállítható törlést, és meg kell tisztítania az *Azure Key Vault* védelmét a Key Vault használatával a felügyelt lemezek titkosításához.

* Szüksége van az Azure CLI 2.0.79-es vagy újabb verziójára és az aks-preview 0.4.26 kiterjesztésre

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgáló opt-in. Az előzetes verziók "adott verzióban" és "ahogy elérhetők", és nem tartoznak a szolgáltatási szintre vonatkozó szerződések és a korlátozott jótállás hatálya alá. Az AKS-előzeteseket részben fedezi az ügyfélszolgálat a legjobb erőfeszítés alapján. Mint ilyen, ezek a funkciók nem célja a termelés használatra. További infromációért lásd a következő támogatási cikkeket:
>
> * [Az AKS támogatási irányelvei](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>A legújabb AKS CLI előzetes bővítmény telepítése

Az ügyfél által felügyelt kulcsok használatához az *aks-preview* CLI bővítmény 0.4.26-os vagy újabb verziójára van szükség. Telepítse az *aks-preview* Azure CLI bővítményt az [az bővítmény hozzáadása][az-extension-add] paranccsal, majd ellenőrizze az elérhető frissítéseket az az extension [update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault-példány létrehozása

Azure Key Vault-példány használatával tárolja a kulcsokat.  Az Azure-portálon igény szerint [konfigurálhatja az ügyfelek által felügyelt kulcsokat az Azure Key Vault segítségével][byok-azure-portal]

Hozzon létre egy új *erőforráscsoportot,* majd hozzon létre egy új Key Vault-példányt, és engedélyezze a helyreállítható törlést és a védelem törlését. *Key Vault*  Győződjön meg arról, hogy minden parancshoz ugyanazokat a régió- és erőforráscsoportneveket használja.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet példányának létrehozása

Cserélje le *a myKeyVaultName-t* a kulcstartó nevére.  A következő lépések végrehajtásához az Azure Key Vaultban tárolt *kulcsra* is szüksége lesz.  Vagy tárolja a meglévő kulcsot az előző lépésekben létrehozott Key Vaultban, vagy [hozzon létre egy új kulcsot,][key-vault-generate] és cserélje le az alábbi *myKeyName* nevet a kulcs nevére.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>A DiskEncryptionSet hozzáférés megadása a kulcstartóhoz

Használja a DiskEncryptionSet és az erőforráscsoportok at létrehozott a korábbi lépésekben, és megadja a DiskEncryptionSet erőforrás-hozzáférést az Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Új AKS-fürt létrehozása és az operációs rendszer lemezének titkosítása

Hozzon létre egy **új erőforráscsoportot** és AKS-fürtöt, majd a kulcs segítségével titkosítsa az operációsrendszer-lemezt. Az ügyfél által felügyelt kulcsokat csak az 1.17-nél nagyobb Kubernetes-verziók támogatják. 

> [!IMPORTANT]
> Annak biztosítása, hogy új resoruce csoportot hozzon létre az AKS-fürthöz

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Amikor új csomópontkészleteket ad hozzá a fent létrehozott fürthöz, a létrehozás során megadott ügyfél által felügyelt kulcs az operációs rendszer lemezének titkosítására szolgál.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Az AKS-fürt adatlemezének titkosítása

Az AKS-adatlemezeket saját kulcsokkal is titkosíthatja.

> [!IMPORTANT]
> Győződjön meg arról, hogy rendelkezik a megfelelő AKS hitelesítő adatokkal. Az egyszerű szolgáltatásnak közreműködői hozzáféréssel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a diskencryptionset telepítve van. Ellenkező esetben hibaüzenet jelenik meg, amely arra utal, hogy a szolgáltatásnév nem rendelkezik engedélyekkel.

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

Hozzon létre egy **byok-azure-disk.yaml** nevű fájlt, amely a következő információkat tartalmazza.  Cserélje le a myAzureSubscriptionId, a myResourceGroup és a myDiskEncrptionSetName értékeket az Ön értékeire, és alkalmazza a yaml.Replace myAzureSubscriptionId, myResourceGroup, and myDiskEncrptionSetName with your values, and apply the yaml.  Győződjön meg arról, hogy azt az erőforráscsoportot használja, amelyben a DiskEncryptionSet telepítve van.  Ha az Azure Cloud Shellt használja, ez a fájl vi vagy nano használatával is létrehozható, mintha virtuális vagy fizikai rendszeren dolgozna:

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
Ezután futtassa ezt a központi telepítést az AKS-fürtben:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Korlátozások

* A BYOK jelenleg csak bizonyos [Azure-régiókban][supported-regions] érhető el a GA-ban és az előzetes verzióban
* A Kubernetes 1.17-es és újabb verzióival támogatott operációsrendszer-lemeztitkosítás   
* Csak azokban a régiókban érhető el, ahol a BYOK támogatott
* Az ügyfél által felügyelt kulcsokkal való titkosítás jelenleg csak új AKS-fürtökre szolgál, a meglévő fürtök nem frissíthetők
* A Virtuálisgép-méretezési csoportokat használó AKS-fürt szükséges, nincs támogatás a virtuálisgép rendelkezésre állási készleteihez


## <a name="next-steps"></a>További lépések

Az [AKS-fürtbiztonsággal kapcsolatos gyakorlati tanácsok áttekintése][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
