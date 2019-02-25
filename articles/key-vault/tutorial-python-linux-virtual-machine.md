---
title: Oktatóanyag – Linux rendszerű virtuális gép és a egy Python-alkalmazás használatával az Azure Key Vaultban titkos kulcsok tárolására |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan konfigurálhatja egy Python-alkalmazást az Azure Key vault titkos olvasásához.
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
ms.openlocfilehash: b7077653ec959f99491cecd71573c091772448f4
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749630"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux rendszerű virtuális gép és a egy Python-alkalmazás titkos kulcsok tárolására az Azure Key Vault használatával

Az Azure Key Vault segítségével az API-kulcsok például titkainak védelmére és adatbázis-kapcsolati karakterláncok az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges.

Ebben az oktatóanyagban beállította egy Azure-webalkalmazás Azure-Kulcstartóból származó információk olvasásához által felügyelt identitások használatával az Azure-erőforrásokhoz. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * A key vaultban lévő titkos Store
> * Linuxos virtuális gépek létrehozása
> * Engedélyezze a [identitás](../active-directory/managed-identities-azure-resources/overview.md) a virtuális gép
> * A szükséges a Konzolalkalmazás adatokat olvasni a key vault-engedélyek megadására
> * A key vault titkos kulcs lekérése

Előtt minden további, győződjön meg arról, hogy megértette a [Key Vault alapfogalmaival](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* [Az Azure CLI 2.0.4-es vagy újabb](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy az Azure Cloud Shellben.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Felügyeltszolgáltatás-identitás ismertetése

Az Azure Key Vault is tárolja biztonságosan hitelesítő adatokat, így ezek nem a kódban. Kérheti le azokat, az Azure Key Vaultba hitelesítést kell. Azonban hitelesítést a Key Vault, kell hitelesítő adatokat. Klasszikus rendszerindítási hiba. Az Azure és az Azure Active Directory (Azure AD) a Felügyeltszolgáltatás-identitás (MSI) felhatalmazást egyszerűbbé teszi a rendszer-indításkori identitást biztosít.

MSI engedélyezésekor például a virtuális gépek, az App Service-ben vagy a függvények az Azure-szolgáltatások az Azure létrehoz egy egyszerű szolgáltatást a service-példány az Azure ad-ben. A hitelesítő adatokat, kódtárba a szolgáltatásnévhez tartozó, a szolgáltatás példánya.

![MSI](media/MSI.png)

Ezután a kód meghívja a egy helyi metadata szolgáltatás érhető el a az Azure-erőforráshoz hozzáférési jogkivonatot kapjon. A kód a hozzáférési jogkivonatot, amely a helyi MSI-végpontról lekérdezi azt egy Azure Key Vault szolgáltatással való hitelesítésre használja.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-bA az Azure CLI-vel, írja be:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a használatával a `az group create` parancsot az USA nyugati régiójában, az alábbi kódra. Cserélje le `YourResourceGroupName` a választott nevét.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ez az oktatóanyag során az erőforráscsoport használja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Key vault ezután az erőforráscsoportban, amelyet az előző lépésben hozott létre. Adja meg az alábbi információkat:

* A Key vault neve: A név 3 – 24 karakter karakterláncnak kell lennie, és csak a 0-9, tartalmaznia kell a – z A – Z és kötőjelet (-) tartalmazhat.
* Az erőforráscsoport neve.
* Hely: **USA nyugati RÉGIÓJA**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Érdemes tárolni egy SQL-kapcsolati karakterlánc, vagy bármely egyéb olyan információkat, amelyek a kell lennie mindkét tartanak, biztonságos és az alkalmazás számára elérhető.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az *AppSecret* nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gépek létrehozása

A virtuális gép létrehozása a `az vm create` parancsot.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter automatikusan hoz létre az SSH-kulcsot, és hozzáadja a kulcsok alapértelmezett helyén (**~/.ssh**). Ehelyett egy meghatározott készletének a kulcsok létrehozásához használja a `--ssh-key-value` lehetőséget.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. Az alábbi példa kimenetében látható, hogy sikeres volt-e a virtuális gép létrehozása:

```azurecli
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

Jegyezze fel a saját `publicIpAddress` a kimenetben a virtuális gépről. Ezt a címet fogja használni a későbbi lépésekben a virtuális gép eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális gép

A virtuális gépet a rendszer által hozzárendelt identitás létrehozása a következő parancs futtatásával:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenete a következőképpen történik.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Jegyezze fel a `systemAssignedIdentity`. Használhatja azt a következő lépéssel.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép identitása engedélyt ad a Key Vault

Most már engedélyt adhat a Key Vault létrehozott identitásra. Futtassa az alábbi parancsot:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Jelentkezzen be a virtuális Gépre

Jelentkezzen be a virtuális gép egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>A virtuális gépen telepítse a Python-kódtár

Töltse le és telepítse a [kérelmek](https://pypi.org/project/requests/2.7.0/) Python-kódtár HTTP GET hívásokat.

## <a name="create-edit-and-run-the-sample-python-app"></a>Létrehozása, szerkesztése és a Python-mintaalkalmazás futtatása

Hozzon létre egy Python-fájlt nevű **Sample.py**.

Nyissa meg a Sample.py, és szerkessze a következő kódot tartalmazza:

```python
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

A fenti kód egy kétlépéses folyamat hajtja végre:

   1. A jogkivonat olvas be a helyi MSI-végpontot a virtuális gépen. A végpont majd lekéri a egy tokent, az Azure Active Directoryból.
   1. Továbbítja a jogkivonatot a key vaultban, és a titkos kód lekéri.

Futtassa a következő parancsot. A titkos értéket kell megjelennie.

```console
python Sample.py
```

Ebben az oktatóanyagban megtudhatta, hogyan Azure Key Vault használata egy Linux rendszerű virtuális gépen futó Python-alkalmazások.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölje az erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot a virtuális Gépet, majd válassza a **törlése**.

Törölje a key vault használatával a `az keyvault delete` parancsot:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Key Vault REST API-val](https://docs.microsoft.com/rest/api/keyvault/)
