---
title: Store biztonságosan férhetnek hozzá a hitelesítő adatokat a Data Science virtuális gépen – Azure |} A Microsoft Docs
description: Hozzáférés Store biztonságos hitelesítő adatok a Data Science virtuális gépen.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 1bf3150fc79f86e196be120fef78b76be8e47f63
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344506"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Hozzáférés Store biztonságos hitelesítő adatok a Data Science virtuális gépen

Közös kihívást felhőalkalmazások létrehozásához, hogyan kell lennie a kódban, a cloud serviceshez hitelesítéséhez hitelesítő adatok kezelésére. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben azok soha nem az fejlesztői munkaállomások jelennek meg, vagy a forráskezelőhöz beadva beolvasása. 

[Felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) révén az Azure, így egyszerűbb a probléma megoldására services, Azure Active Directoryban (Azure AD) automatikusan felügyelt identitást. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés nélkül a hitelesítő adatokat a kódban bármely szolgáltatással való hitelesítésre. 

Hitelesítő adatok védelme az egyik lehetőség az, hogy az MSI használata az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), egy felügyelt Azure szolgáltatás titkosítási kulcsok és titkok biztonságos tárolására. A kulcstartó elérését a felügyelt identitások használatával, és az arra jogosult titkok és titkosítási kulcsok lekérése a key vaultban. 

Az Azure-erőforrások és a Key Vault-dokumentáció a felügyelt identitásokból egy átfogó erőforrás, ezek a szolgáltatások részletes tájékoztatást. Ez a cikk további részének MSI és a Key Vault alapszintű használatát ismerteti a az adatelemzési virtuális gép (DSVM) Azure-erőforrások eléréséhez. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>A dsvm-hez a felügyelt identitás létrehozása 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>A Key Vault hozzáférési engedélyeket rendeljenek hozzá egy virtuális gép egyszerű
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>A key vaultban titkos elérését a dsvm-hez

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>A dsvm-hez való hozzáférés tárkulcsok

```
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>A key vault eléréséhez a Pythonnal

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your key vault URL.
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>A kulcstartó elérését az Azure CLI-vel

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
