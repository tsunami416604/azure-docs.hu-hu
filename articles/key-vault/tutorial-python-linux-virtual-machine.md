---
title: Oktatóanyag – Azure Key Vault használata egy Azure virtuális gépen a Pythonban |} A Microsoft Docs
description: Ebben az oktatóanyagban konfigurál egy Python-alkalmazás titkos key vault kulcsainak olvasásához
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
ms.openlocfilehash: 8c816d17807432d75b6102190fc37d25a525d7cf
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244171"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Oktatóanyag: Az Azure Key Vault használata egy Azure virtuális gépen a Pythonban

Az Azure Key Vault segítségével az API-kulcsok például titkainak védelmére és adatbázis-kapcsolati karakterláncok az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges.

Ebben az oktatóanyagban kövesse a lépéseket a Azure-webalkalmazások Azure Key Vault származó információk olvasásához az Azure-erőforrások felügyelt identitások használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Titkos kulcs tárolása a kulcstartóban.
> * Hozzon létre egy Azure virtuális gépen.
> * Engedélyezze a [identitás](../active-directory/managed-identities-azure-resources/overview.md) a virtuális gép.
> * A Konzolalkalmazás adatokat olvasni a key vault szükséges engedélyeket.
> * Titkos kulcs lekérése a kulcstartóból.

Előtt minden további, kérjük, olvassa el a [Key Vault alapfogalmaival](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek
Az összes platformra van szüksége:

* Git ([letöltés](https://git-scm.com/downloads)).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb verziója. Érhető el Windows, Mac és Linux rendszereken.

### <a name="managed-service-identity-and-how-it-works"></a>Felügyeltszolgáltatás-identitás- és annak működéséről
Ebben az oktatóanyagban felhasznál a Felügyeltszolgáltatás-identitás (MSI).

Az Azure Key Vault is tárolja biztonságosan hitelesítő adatokat, így ezek nem a kódban. Kérheti le azokat, a Key Vault hitelesítenie kell. Hitelesítést a Key Vault, a hitelesítő adatokat kell. Ez klasszikus rendszerindítási hibát. Az Azure és az Azure Active Directory (Azure AD), MSI egy "rendszer-indításkori identitást biztosít" könnyíti meg a felhatalmazást.

Ha engedélyezi az Azure-szolgáltatások, például a virtuális gépek, az App Service-ben vagy a függvények MSI, az Azure létrehozza egy [szolgáltatásnév](key-vault-whatis.md#basic-concepts) a szolgáltatás az Azure AD-példány. Az Azure egyszerű szolgáltatás számára a hitelesítő adatokat, a szolgáltatás példánya kódtárba. 

![MSI](media/MSI.png)

Ezután a kód meghívja egy helyi metaadat-szolgáltatás, amely a hozzáférési jogkivonatot kapjon az Azure-erőforrás érhető el.
A kód a hozzáférési jogkivonatot, amely a helyi MSI-végpontról lekérdezi azt egy Azure Key Vault szolgáltatással való hitelesítésre használja. 

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

* A Key vault neve: A név 3 – 24 karakter karakterláncnak kell lennie, és csak a 0-9, tartalmaznia kell a – z A – Z és kötőjelet (-) tartalmazhat.
* Az erőforráscsoport neve.
* Hely: **USA nyugati RÉGIÓJA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat SQL-kapcsolati sztringeket vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, azonban az alkalmazás számára elérhetővé kell tenni.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az *AppSecret* nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot.

A következő példa létrehoz egy *myVM* nevű virtuális gépet, és hozzáad egy *azureuser* nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter automatikusan hoz létre az SSH-kulcsot, és hozzáadja a kulcsok alapértelmezett helyén (*~/.ssh*). Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. Az alábbi példa kimenetében látható, hogy sikeres volt-e a virtuális gép létrehozása:

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Vegye figyelembe a saját `publicIpAddress` értékét a kimenet a virtuális gépről. A következő lépésekben ez a cím használható a virtuális gép eléréséhez.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Identitás hozzárendelése a virtuális gép
Ebben a lépésben létrehozzuk a virtuális gép egy rendszer által hozzárendelt identitással. Az Azure CLI-ben futtassa a következő parancsot:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenete a következőképpen történik. Jegyezze fel az értékét a **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>A virtuális gép identitása engedélyt a key vaultban
Most már tudjuk engedélyt adhat az identitás a key vaultban. Futtassa az alábbi parancsot:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Jelentkezzen be a virtuális gép

Jelentkezzen be a virtuális gépet a következő [ebben az oktatóanyagban](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Létrehozása és futtatása a Python-mintaalkalmazás

A következő példa fájlt nevű *Sample.py*. Használja a [kérelmek](https://pypi.org/project/requests/2.7.0/) szalagtár-hívások indítása HTTP GET.

## <a name="edit-samplepy"></a>Sample.py szerkesztése
Miután létrehozta a Sample.py, nyissa meg a fájlt, és másolja az alábbi kódot. A kód két lépésből áll: 
1. Olvassa be a jogkivonatot a helyi MSI-végpontról a virtuális gépen. A végpont majd lekéri a egy tokent, az Azure Active Directoryból.
2. A jogkivonat át a key vaultban, és a titkos kód beolvasása. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

A következő parancs futtatásával a titkos értéket kell megjelennie: 

```
python Sample.py
```

A fenti kód bemutatja, hogyan hajtsa végre a műveleteket az Azure Key Vault egy Windows virtuális gépen. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Key Vault REST API-val](https://docs.microsoft.com/rest/api/keyvault/)
