---
title: Tároló biztonságosan férhetnek hozzá a hitelesítő adatokat az adatok tudományos virtuálisgép - Azure |} Microsoft Docs
description: Hozzáférés tárolják az adatok tudományos virtuális gép biztonságos hitelesítő adatok.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, a adatok tudományos virtuális gép, a földrajzi analytics, a csapat az tudományos folyamata
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 4eb1d657adc37ef0d1e4055573b174d58baf2e0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Hozzáférés tárolják az adatok tudományos virtuális gép biztonságos hitelesítő adatok

Egy közös kérdés esetén felhőalapú alkalmazások kezelése a hitelesítő adatokat, amelyek kell lenniük a kódban való hitelesítéséhez. Fontos feladat biztosíthatja a rendszer ezen hitelesítő adatok védelmét. Ideális esetben azok soha nem jelennek meg a fejlesztői munkaállomásokon, vagy beolvasása beadja a verziókövetési rendszerrel. 

[Szolgáltatás-identitás (MSI) felügyelt](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) teszi egyszerűbbé válik a probléma megoldásához azzal, hogy az Azure-szolgáltatások az Azure Active Directory (Azure AD) automatikusan felügyelt identitást. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. Hitelesítő adatok védelmét egy közös mintát MSI használandó együtt a [Azure Keyvault](https://docs.microsoft.com/azure/key-vault/), egy felügyelt Azure-szolgáltatásokban is titkok és titkosítási kulcsokat tárolja biztonságos helyen. A felügyelt szolgáltatás identitás használatával kulcstároló eléréséhez, és a jogosult titkos kulcsok és a titkosítási kulcsok lekérése a Key Vault. 

Az MSI-fájl és a Key Vault dokumentáció áll egy átfogó erőforrás, a fenti szolgáltatások részletesebb információk. Ez a cikk többi egy alapszintű ismertetése mutatja be alapszintű MSI és a Key Vault az adatok tudományos virtuális gép (DSVM). 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>Az MSI-fájl használatával biztonságosan férjenek hozzá az Azure-erőforrások bemutatása

## <a name="1-create-msi-on-the-dsvm"></a>1. A DSVM MSI-fájl létrehozása 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2. VM egyszerű Keyvault hozzáférési engedélyek hozzárendelése
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>3. A Keyvault a titkos kulcs elérje a DSVM

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4. A DSVM a tárolási hívóbetűk

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5. A Python hozzáférés Keyvault

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6. Az Azure parancssori felület a hozzáférést a virtuális gép

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
