---
title: Oktatóanyag – Az Azure Key Vault használata Windows virtuális géppel a Pythonban | Microsoft dokumentumok
description: Ebben az oktatóanyagban konfigurálja a ASP.NET alapalkalmazást a kulcstartó ból egy titkos olvasásra.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c0bb391348548ecca595fd1a6472bafcb22ed4ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472656"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Oktatóanyag: Az Azure Key Vault használata Windows-alapú virtuális géppel a Pythonban

Az Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat, az adatbázis-kapcsolat ihletési karakterláncait, amelyek az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges.

Ebben az oktatóanyagban megtudhatja, hogyan szerezhet be egy konzolalkalmazást az Azure Key Vaultból származó információk olvasásához. Ehhez felügyelt identitásokat kell használnia az Azure-erőforrásokhoz. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titk a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure virtuális gépet.
> * Felügyelt identitás engedélyezése.
> * Engedélyek hozzárendelése a virtuális gép identitásához.

Mielőtt elkezdené, olvassa el [a Key Vault alapfogalmait.](basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux esetén:
  * [Git](https://git-scm.com/downloads)
  * Ez az oktatóanyag megköveteli, hogy az Azure CLI helyileg fusson. Az Azure CLI 2.0.4-es vagy újabb verziójának telepítve kell lennie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Tudnivalók a felügyeltszolgáltatás-identitásról

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem jelennek meg a kódban. Azonban a kulcsok lekéréséhez az Azure Key Vault ban kell hitelesítenie magát. A Key Vault hitelesítéséhez hitelesítő adatokra van szükség. Ez egy klasszikus bootstrap dilemma. A felügyelt szolgáltatásidentitás (MSI) úgy oldja meg ezt a problémát, hogy olyan _rendszerindítási identitást_ biztosít, amely leegyszerűsíti a folyamatot.

Ha engedélyezi az MSI-t egy Azure-szolgáltatáshoz, például az Azure virtuális gépekhez, az Azure App Service-hez vagy az Azure Functionshez, az Azure létrehoz egy [egyszerű szolgáltatást.](basic-concepts.md) AZ MSI ezt a szolgáltatás az Azure Active Directoryban (Azure AD) a szolgáltatás példányát, és beadja a szolgáltatás egyszerű hitelesítő adatait az adott példányban. 

![MSI](media/MSI.png)

Ezután egy hozzáférési jogkivonat beszerzése, a kód meghívja a helyi metaadat-szolgáltatás, amely elérhető az Azure-erőforrás. Az Azure Key Vault-szolgáltatás hitelesítéséhez a kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt. A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Az újonnan létrehozott erőforráscsoportot használja ebben az oktatóanyagban.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ha az előző lépésben létrehozott erőforráscsoportban szeretne létrehozni egy kulcstartót, adja meg a következő információkat:

* Kulcstartó neve: 3–24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.
* Erőforráscsoport neve
* Helyszín: **USA nyugati telephelye**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ezen a ponton az Azure-fiók az egyetlen, amely jogosult műveletek végrehajtására az új kulcstartó.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. A titkos fájl lehet egy SQL-kapcsolati karakterlánc vagy bármely más információ, amely biztonságban és az alkalmazás számára elérhetővé kell tenni.

Ha egy **AppSecret**nevű titkos kulcsot szeretne létrehozni a key vaultban, írja be a következő parancsot:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ez a titkos tárolja az értéket **MySecret**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Virtuális gépet az alábbi módszerek egyikével hozhat létre:

* [Az Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [Powershell](../virtual-machines/windows/quick-create-powershell.md)
* [Az Azure portál](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez
Ebben a lépésben hozzon létre egy rendszer által hozzárendelt identitást a virtuális gép hez a következő parancs futtatásával az Azure CLI-ben:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Vegye figyelembe a rendszer által hozzárendelt identitás, amely megjelenik a következő kódban. Az előző parancs kimenete a következő lenne: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek hozzárendelése a virtuális gép identitásához
Most már hozzárendelheti a korábban létrehozott identitásengedélyeket a kulcstartóhoz a következő parancs futtatásával:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a Csatlakozás és [a Windows rendszerű Azure-alapú virtuális gépre vonatkozó](../virtual-machines/windows/connect-logon.md)utasításokat.

## <a name="create-and-run-a-sample-python-app"></a>Minta Python-alkalmazás létrehozása és futtatása

A következő szakaszban található egy *Sample.py*nevű példafájl. Ez használ egy [kérelem](https://2.python-requests.org/en/master/) könyvtár, hogy http get hívásokat.

## <a name="edit-samplepy"></a>Sample.py szerkesztése

A *Sample.py*létrehozása után nyissa meg a fájlt, majd másolja a kódot ebbe a szakaszba. 

A kód két lépésből áll:
1. Token beolvasása a helyi MSI-végpontból a virtuális gépen.  
  Ezzel is lekéri a jogkivonatot az Azure AD-ből.
1. Adja át a tokent a kulcstartónak, majd olvassa el a titkos kulcsot. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

A titkos értéket a következő kód futtatásával jelenítheti meg: 

```console
python Sample.py
```

Az előző kód bemutatja, hogyan műveleteket az Azure Key Vault egy Windows virtuális gép. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a key vault.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
