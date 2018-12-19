---
title: Oktatóanyag – Azure Key Vault használata az Azure Windows virtuális gép a Pythonban |} A Microsoft Docs
description: 'Oktatóanyag: ASP.NET Core-alkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 43f1918e7c80fdc04b80583ce374cfb8127dd021
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602562"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-python"></a>Oktatóanyag: Az Azure Key Vault használata az Azure Windows virtuális gép a Pythonban

Az Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat, valamint az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati sztringeket.

Ebben az oktatóanyagban azokat a lépéseket ismerheti meg, amelyekkel Azure-webalkalmazásokat konfigurálhat az Azure Key Vaultban tárolt adatok olvasására felügyelt identitásokkal Azure-erőforrások számára. A továbbiakban az alábbiakat ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titkos kulcs tárolása a kulcstartóban.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure virtuális gépen.
> * Engedélyezze a [identitás](../active-directory/managed-identities-azure-resources/overview.md) a virtuális gép.
> * A Konzolalkalmazás adatokat olvasni a key vault szükséges engedélyeket.
> * Titkos kódok lekérése a Key Vaultból

A folytatás előtt tekintse át az [alapvető fogalmakat](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek
* Összes platform:
  * Git ([letöltés](https://git-scm.com/downloads)).
  * Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
  * Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb verziója. Ez elérhető Windows, Mac és Linux rendszerekhez.

Ebben az oktatóanyagban felhasznál a Felügyeltszolgáltatás-identitást

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Mi az a Managed Service Identity, és hogyan működik?
A folytatás előtt ismerkedjünk meg az MSI-vel. Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem találhatók meg a kódban, azonban a lekérésükhöz hitelesítés szükséges az Azure Key Vaultban. A Key Vaultban való hitelesítéshez hitelesítő adatokra van szükség. Klasszikus rendszerindítási probléma. Az MSI az Azure és az Azure AD révén egy „rendszerindítási identitást” biztosít, amellyel sokkal egyszerűbb nekilátni a feladatoknak.

Lássuk, hogyan működik! Ha engedélyezi az MSI-t egy Azure-szolgáltatás, például a Virtual Machines, az App Service vagy a Functions számára, az Azure létrehoz egy [szolgáltatásnevet](key-vault-whatis.md#basic-concepts) a szolgáltatás Azure Active Directoryban található példánya számára, majd beszúrja a szolgáltatásnév hitelesítő adatait a szolgáltatás példányába. 

![MSI](media/MSI.png)

Következő lépésként a kód meghív egy, az Azure-erőforráson elérhető helyi metaadat-szolgáltatást a hozzáférési jogkivonat beszerzéséhez.
A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példa létrehoz egy erőforráscsoportot az USA nyugati régiójában:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

A cikkben végig az imént létrehozott erőforráscsoportot használjuk majd.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A következő lépésben létre fogunk hozni egy kulcstartót az előző lépésben létrehozott erőforráscsoportban. Adja meg az alábbi információkat:

* A Key vault neve: A név 3 – 24 karakter karakterláncnak kell lennie, és csak tartalmaznia kell (0-9, a – z, A-Z, - és).
* Az erőforráscsoport neve.
* Hely: **USA nyugati RÉGIÓJA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat SQL-kapcsolati sztringeket vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, azonban az alkalmazás számára elérhetővé kell tenni.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az **AppSecret** nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Kövesse az alábbi hivatkozásokat a Windows virtuális gép létrehozása

[Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portál](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Identitás hozzárendelése a virtuális gép
Ebben a lépésben a rendszer a virtuális géphez, az Azure CLI az alábbi parancs futtatásával hozzárendelt identitás létrehozása folyamatban

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Vegye figyelembe az alábbi systemAssignedIdentity. A fenti parancs kimenete lenne. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Virtuális gép azonosítójának engedélyt a Key Vaulthoz
Most már biztosítani tudjuk a fent létrehozott Key Vault identitás engedélyt a következő parancs futtatásával

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Jelentkezzen be a virtuális gép

Kövesse ezt [oktatóanyag](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="create-and-run-sample-python-app"></a>Létrehozása és futtatása a Python-mintaalkalmazás

Az alábbiakban csupán egy példa fájlt neve "Sample.py". Használ [kérelmek](http://docs.python-requests.org/master/) szalagtár-hívások indítása HTTP GET.

## <a name="edit-samplepy"></a>Sample.py szerkesztése
Sample.py nyílt létrehozása a fájl- és másolása után az alábbi kód

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

By running you should see the secret value 
```
Python Sample.py
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
