---
title: Store biztonságosan férhetnek hozzá a hitelesítő adatokat a Data Science virtuális gépen – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan tárolja biztonságosan eléréséhez szükséges hitelesítő adatokat a Data Science virtuális gépen. Megtudhatja, hogyan használhatók a felügyelt szolgáltatásbeli identitások és a Azure Key Vault a hozzáférési hitelesítő adatok tárolásához.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 1374cbef41f40ea270f3c4d84c68d08e7db095bc
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051617"
---
# <a name="store-access-credentials-securely-on-a-data-science-virtual-machine"></a>A hozzáférési hitelesítő adatok biztonságos tárolása Data Science Virtual Machine

Gyakori, hogy a Felhőbeli alkalmazásokban található kód hitelesítő adatokat tartalmaz a Cloud Services-hitelesítéshez. A hitelesítő adatok kezelése és védelme jól ismert kihívás a felhőalapú alkalmazások létrehozásához. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, vagy bejelentkezhetnek a verziókövetésba.

Az [Azure-erőforrások felügyelt identitásai](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) lehetővé teszik a probléma egyszerűbb megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak a Azure Active Directory (Azure ad) szolgáltatásban. Ezzel az identitással bármely, Azure AD-hitelesítést támogató szolgáltatáshoz végezhet hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

A hitelesítő adatok biztonságossá tételének egyik módja a Windows Installer (MSI) használata a [Azure Key Vaultkal](https://docs.microsoft.com/azure/key-vault/)és egy felügyelt Azure-szolgáltatással együtt, amely biztonságosan tárolja a titkokat és a titkosítási kulcsokat. A Key vaultot a felügyelt identitás használatával érheti el, majd beolvashatja az erre jogosult titkos kulcsokat és titkosítási kulcsokat a Key vaultból.

Az Azure-erőforrások és a Key Vault felügyelt identitásával kapcsolatos dokumentáció átfogó erőforrást tartalmaz a szolgáltatásokkal kapcsolatos részletes információkhoz. Ez a cikk további részének MSI és a Key Vault alapszintű használatát ismerteti a az adatelemzési virtuális gép (DSVM) Azure-erőforrások eléréséhez. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>A dsvm-hez a felügyelt identitás létrehozása 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Key Vault hozzáférési engedélyek kiosztása egy virtuálisgép-rendszerbiztonsági tag számára
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
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
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

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
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>A kulcstartó elérését az Azure CLI-vel

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
