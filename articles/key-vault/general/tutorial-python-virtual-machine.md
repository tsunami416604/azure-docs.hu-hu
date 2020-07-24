---
title: Oktatóanyag – Azure Key Vault használata virtuális géppel a Pythonban | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazást konfigurál, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101656"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Oktatóanyag: Azure Key Vault használata virtuális géppel a Pythonban

Azure Key Vault segítséget nyújt a titkok, például az API-kulcsok, az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati karakterláncok védelemmel való ellátásához.

Ebből az oktatóanyagból megtudhatja, hogyan szerezhet be egy konzolos alkalmazást a Azure Key Vault információk olvasásához. Ehhez felügyelt identitásokat kell használnia az Azure-erőforrásokhoz. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Adjon hozzá egy titkos kulcsot a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-beli virtuális gépet.
> * Felügyelt identitás engedélyezése.
> * Rendeljen engedélyeket a virtuális gép identitásához.

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux rendszerekhez:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz helyileg kell futtatnia az Azure CLI-t. Telepítenie kell az Azure CLI 2.0.4 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. A titkos kód lehet egy SQL-kapcsolódási sztring, vagy bármely más olyan információ, amelyet a biztonságos és elérhetővé kell tenni az alkalmazás számára.

Ha a **AppSecret**nevű kulcstartóban szeretne titkos kulcsot létrehozni, írja be a következő parancsot:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titok a **keresési kifejezésként**értéket tárolja.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A következő módszerek egyikével hozhat létre virtuális gépet:

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [A Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez
Ebben a lépésben létrehoz egy rendszerhez rendelt identitást a virtuális géphez a következő parancs futtatásával az Azure CLI-ben:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Jegyezze fel a rendszer által hozzárendelt identitást, amely a következő kódban látható. Az előző parancs kimenete a következő lesz: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek kiosztása a virtuális gép identitásához
Most a következő parancs futtatásával rendelheti hozzá a korábban létrehozott identitási engedélyeket a kulcstartóhoz:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](../../virtual-machines/windows/connect-logon.md)című témakör utasításait.

## <a name="create-and-run-a-sample-python-app"></a>Minta Python-alkalmazás létrehozása és futtatása

A következő szakaszban egy *sample.py*nevű fájl látható. A [kérések](https://2.python-requests.org/en/master/) KÖNYVTÁRÁVAL HTTP Get hívásokat hajt végre.

## <a name="edit-samplepy"></a>Sample.py szerkesztése

Miután létrehozta a *sample.py*, nyissa meg a fájlt, majd másolja a kódot ebben a szakaszban. 

A kód két lépésből álló folyamatot jelent:
1. A virtuális gép helyi MSI-végpontján lévő jogkivonat beolvasása.  
  Ezzel az Azure AD-ből is lekéri a tokent.
1. Továbbítsa a tokent a kulcstartóba, majd olvassa be a titkot. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

A titkos értéket a következő kód futtatásával jelenítheti meg: 

```console
python Sample.py
```

Az előző kód azt mutatja be, hogyan végezheti el a műveleteket a Azure Key Vault egy Windows rendszerű virtuális gépen. 

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a kulcstartót.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
